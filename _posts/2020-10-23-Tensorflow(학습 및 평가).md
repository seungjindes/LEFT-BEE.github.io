---
title : "학습 및 평가"
---

## 학습 및 평가
----------------------
### API개요 :첫 번째 end to end 예제

모델에 데이터를 학습 시킬경우 Numpy arrays(데이터가 작고 메모리에 맞을때) 또는 tf.data 객체를 이용할 것이다 이번엔는 MNIST데이터를 이용할 것이다.
아래의 예제를 보자 
```
inputs = keras.Input(shape=(784,), name="digits")
x = layers.Dense(64, activation="relu", name="dense_1")(inputs)
x = layers.Dense(64, activation="relu", name="dense_2")(x)
outputs = layers.Dense(10, activation="softmax", name="predictions")(x)

model = keras.Model(inputs=inputs, outputs=outputs)
```
```
(x_train, y_train), (x_test, y_test) = keras.datasets.mnist.load_data()

# Preprocess the data (these are NumPy arrays)
x_train = x_train.reshape(60000, 784).astype("float32") / 255
x_test = x_test.reshape(10000, 784).astype("float32") / 255

y_train = y_train.astype("float32")
y_test = y_test.astype("float32")

# Reserve 10,000 samples for validation
x_val = x_train[-10000:]
y_val = y_train[-10000:]
x_train = x_train[:-10000]
y_train = y_train[:-10000]
```
학습을 구성한다(optimizer , loss , metrics)

```
model.compile(
    optimizer=keras.optimizers.RMSprop(),  # Optimizer
    # Loss function to minimize
    loss=keras.losses.SparseCategoricalCrossentropy(),
    # List of metrics to monitor
    metrics=[keras.metrics.SparseCategoricalAccuracy()],
)
```
fit()함수를 호출하여 모델을 학습시킨다 이떄 batch_size에 맞게 나눠서 학습키면서 epochs만큼 반복한다 .

```
print("Fit model on training data")
history = model.fit(
    x_train,
    y_train,
    batch_size=64,
    epochs=2,
    # We pass some validation for
    # monitoring validation loss and metrics
    # at the end of each epoch
    validation_data=(x_val, y_val),
)
```
이떄 history 객체는 이전의 loss value가 저장되있어 값을 불러올수 있다.
```
history.history
결과
{'loss': [0.3424801528453827, 0.16180744767189026],
 'sparse_categorical_accuracy': [0.903439998626709, 0.9512799978256226],
 'val_loss': [0.18268445134162903, 0.12816601991653442],
 'val_sparse_categorical_accuracy': [0.9477999806404114, 0.9650999903678894]}

```
또한 evaluate()함수를 통해 구축한 모델의 점수를 매길수 있다. prediction은 모델에 의해 산출된 결과값을 의미한다.
```
# Evaluate the model on the test data using `evaluate`
print("Evaluate on test data")
results = model.evaluate(x_test, y_test, batch_size=128)
print("test loss, test acc:", results)

# Generate predictions (probabilities -- the output of the last layer)
# on new data using `predict`
print("Generate predictions for 3 samples")
predictions = model.predict(x_test[:3])
print("predictions shape:", predictions.shape)
```
#### The compile() method

fit()을 사용하기 위해 손실(loss)함수 , optimizer , optionly, some metrucs to moniter등을 인수로 넣어준다 이때 metrics함수는 list의 형태로 
넘겨줘야한다  ex) `metrics=[keras.metrics.SparseCategoricalAccuracy()]` 또한 만약 모델이 다중출력의 형태를 지닌다면 각각의 출력에 대한
loss함 함수와 metrucs를 인수로 널어줘야한다 위의 방식이 아닌 
```
model.compile(
    optimizer="rmsprop",
    loss="sparse_categorical_crossentropy",
    metrics=["sparse_categorical_accuracy"],
)
```
위와 같이 string의 형태로 전달해주어도 된다. 아래는 위의 내용을 정리한 코드이다.
```
def get_uncompiled_model():
    inputs = keras.Input(shape=(784,), name="digits")
    x = layers.Dense(64, activation="relu", name="dense_1")(inputs)
    x = layers.Dense(64, activation="relu", name="dense_2")(x)
    outputs = layers.Dense(10, activation="softmax", name="predictions")(x)
    model = keras.Model(inputs=inputs, outputs=outputs)
    return model


def get_compiled_model():
    model = get_uncompiled_model()
    model.compile(
        optimizer="rmsprop",
        loss="sparse_categorical_crossentropy",
        metrics=["sparse_categorical_accuracy"],
    )
    return model
```
get_uncompiled_model()은 x의 모델을 구축하고 이를 반환한다 get_compiled_model()은 위 모델을 받아와 compile한것을 반환해준다.

#### Automatically setting apart a validation holdout set

검증 데이터르 만들떄 이전에는 Numpy Array를 모델에 적용했던 것과 다르게 훈련데이터를 학습시킬때 즉 fit()함수에서 바로 검증데이터로
적용할 수 있다 뿐만아니라 그 비율과 검증데이터의 정확도 또한 바로 나온다 아래 예시를 보자
```
model = get_compiled_model()
model.fit(x_train, y_train, batch_size=64, validation_split=0.2, epochs=1)

결과
625/625 [==============================] - 1s 2ms/step - loss: 0.3753 - sparse_categorical_accuracy: 0.8927 - val_loss: 0.2252 - val_sparse_categorical_accuracy: 0.9344

<tensorflow.python.keras.callbacks.History at 0x7f755826f160>
```

### Training & evaluation from tf.data Datasets

앞서 보았던 것은 NumPy Array일 경우이다 그렇다면 tf.data.Dataset 객체의 데이터를 가지고 있다면 어떻게 모델을 학습시킬까?
아래 예시를 보자
```
model = get_compiled_model()

# First, let's create a training Dataset instance.
# For the sake of our example, we'll use the same MNIST data as before.
train_dataset = tf.data.Dataset.from_tensor_slices((x_train, y_train))
# Shuffle and slice the dataset.
train_dataset = train_dataset.shuffle(buffer_size=1024).batch(64)

# Now we get a test dataset.
test_dataset = tf.data.Dataset.from_tensor_slices((x_test, y_test))
test_dataset = test_dataset.batch(64)

# Since the dataset already takes care of batching,
# we don't pass a `batch_size` argument.
model.fit(train_dataset, epochs=3)

# You can also evaluate or predict on a dataset.
print("Evaluate")
result = model.evaluate(test_dataset)
dict(zip(model.metrics_names, result))
```

tf.data.Dataset.from_tensor_slices((x_data , y_data))를 통해 tf.data형태인 train_data와 test_data를 만들었다. 이를 fit()함수로 
학습시고 evaluate() , pedict() 함수를 이용해 평가하고 예측하였는데 이는 전과 같은 형태이다.

epochs마다 모든 batch_size가 학습이 끝나면 data는 초기화되어 다시 사용가능한데 이때 steps_per_epochs 함수를 이용하면 하나의 Epochs마다 학습하는 batch_size의 양을 결정할 수 있다 예를들어 
```
model = get_compiled_model()

# Prepare the training dataset
train_dataset = tf.data.Dataset.from_tensor_slices((x_train, y_train))
train_dataset = train_dataset.shuffle(buffer_size=1024).batch(64)

# Only use the 100 batches per epoch (that's 64 * 100 samples)
model.fit(train_dataset, epochs=3, steps_per_epoch=100)
```
위 예시는 각 epochs마다 100개의 batch_size를 학습하란 의미로 64 * 100개의 샘플이미지를 학습한다 이렇게 학습되면 epcosh는 리셋되지 않고 다음 epochs에 이후에 학습되어야 하는 batch_size가 학습된다.

#### Using a validation datset 

검증데이터를 사용할 경우 fit() 함수에 tf.data의 형태로 넣어준다. (validation_data = val_dataset부분)
```
model = get_compiled_model()

# Prepare the training dataset
train_dataset = tf.data.Dataset.from_tensor_slices((x_train, y_train))
train_dataset = train_dataset.shuffle(buffer_size=1024).batch(64)

# Prepare the validation dataset
val_dataset = tf.data.Dataset.from_tensor_slices((x_val, y_val))
val_dataset = val_dataset.batch(64)

model.fit(train_dataset, epochs=1, validation_data=val_dataset)
```
각 epochs가 끝날때마다 모델은 검증데이터의 loss값과 validation metircs를 게산한다. 만약 위의 steps_per_epoch처럼 각 epochs마다 검증
데이터의 크기를 지정하고 싶다면 validation_steps() 인수를 이용해 명시한다 
```
model.fit(
    train_dataset,
    epochs=1,
    # Only run validation using the first 10 batches of the dataset
    # using the `validation_steps` argument
    validation_data=val_dataset,
    validation_steps=10,
)
```
이때 validation_data는 각 epochs마다 reset되며 따라서 각 epochs마다 같은 data를 이용하여 검증된다 

#### Other input formats supported

데이터에는 많은 종류가 있지만 특별하게 다중처리와 shuffled가 가능한 Python data generators 을 제공하는 keras.utils.Sequence class를 볼것이다 우선 각 데이터의 설명을 보도록하자

1. NumPy input data if your data is small and fits in memory

2. Dataset objects if you have large datasets and you need to do distributed training

3. Sequence objects if you have large datasets and you need to do a lot of custom Python-side processing that cannot be done in TensorFlow (e.g. if you rely on external libraries for data loading or preprocessing).

#### Using Kras,utlis,Sequence object as input

keras.utils.Sequence는 다중처리에 능하고 데이터를 shuffle할 수 있는 특징을 가진 Subclass이다. Sequence는 두가지 함수를 명시하는데
하나는 `__getitem__` 과 다른 하나는 `__len__` 이다. `__getitem__` 은 완전한 batch를 반환한다 만약 epochs사이에서 dataset을 변형하고 시ㅍ다면 `on_epoch_end`를 실행하면 된다. 아래 예시를 보자
```
from skimage.io import imread
from skimage.transform import resize
import numpy as np

# Here, `filenames` is list of path to the images
# and `labels` are the associated labels.

class CIFAR10Sequence(Sequence):
    def __init__(self, filenames, labels, batch_size):
        self.filenames, self.labels = filenames, labels
        self.batch_size = batch_size

    def __len__(self):
        return int(np.ceil(len(self.filenames) / float(self.batch_size)))

    def __getitem__(self, idx):
        batch_x = self.filenames[idx * self.batch_size:(idx + 1) * self.batch_size]
        batch_y = self.labels[idx * self.batch_size:(idx + 1) * self.batch_size]
        return np.array([
            resize(imread(filename), (200, 200))
               for filename in batch_x]), np.array(batch_y)

sequence = CIFAR10Sequence(filenames, labels, batch_size)
model.fit(sequence, epochs=10)
```


















