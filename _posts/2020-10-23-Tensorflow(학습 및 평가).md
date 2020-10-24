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

fit()을 사용하기 위해 손실(loss)함수 , optimizer , optionly, some metrucs to moniter등을 인수로 넣어준다 이때 metrics함수는 list의 형태로 넘겨줘야한다  ex) `metrics=[keras.metrics.SparseCategoricalAccuracy()]` 또한 만약 모델이 다중출력의 형태를 지닌다면 각각의 출력에 대한
loss함 함수와 metrics를 인수로 널어줘야한다 위의 방식이 아닌 
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

-------------------------------------------------------------------------------

## 모르는 

### Using Kras,utlis,Sequence object as input

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
#### DataGenerator.getitem()


batch 프로세싱이 주어진 index에 따라 호출 될 때 generator는 `__getitem__`을 호출함
결국 batch size만큼의 entry를 계산해서 리턴해줌
예를 들어 batch size가 2이고 index가 10이라면 아래 코드에 의해 indexes에 10, 11이 리턴되고 이에 상응하는 list_IDs[10], list_IDs[11]이 list_IDs_temp에 리턴됨 이를 통해 `__data_generation(list_IDs_temp)`를 통해 알맞은 X, y가 구해짐

#### DataGenerator.len()

각 call request는 배치 index 0 ~ 총 batch 크기 만큼 될 수 있다.
이부분이` __len__`을 통해 컨트롤 된다.

위 부분은 아마 내가 원하는 batchisze data를 만드는 것에 의미가 있는것 같지만 아직 무슨의미인지 모르겠다...

---------------------------------------------------------------

### Using sample weighting and class weighting

freqeuncy에 의해 가중치를 세팅하는 두가지 방법이 있다 class weight 와 sample weight이다 

#### class weight 

Model.fit()에 있는 class_weight인수자리에 class_weight dictionary를 넣어준다.
이는 특정 class에 중요도를 분석하여 train model에 있어 차이를 둘 수 있다.예를 들어 0번 class가 1번 class에 비해 절반정도의 중요도를 가질경우 
`Model.fit(..., class_weight={0: 1., 1: 0.5})`라고 사용가능하다. 아래에 예시를 보자 

```
import numpy as np

class_weight = {
    0: 1.0,
    1: 1.0,
    2: 1.0,
    3: 1.0,
    4: 1.0,
    # Set weight "2" for class "5",
    # making this class 2x more important
    5: 2.0,
    6: 1.0,
    7: 1.0,
    8: 1.0,
    9: 1.0,
}

print("Fit with class weight")
model = get_compiled_model()
model.fit(x_train, y_train, class_weight=class_weight, batch_size=64, epochs=1)
```
dictionary 형태의 class_weight를 만들고 난 후 (5번 class에는 다른 항목에 비해 2배더 중요하다고 두었다) fit()에서 class_weight인수에 전달해 주었다.

#### sample weight

가중치의 세밀한 제어를 위해 또는 분류기를 작성하지 않는 경우 "sample weight"를 사용할 수 있다. 이떄 학습데이터가 Numpy data일 경우 smaple_weight 요소를 Model.fit()함수에 전달한다.:"sample weights"는 숫자로 이루어진 배열이다 예시를 보도록 하자
```
sample_weight = np.ones(shape=(len(y_train),))
sample_weight[y_train == 5] = 2.0

print("Fit with sample weight")
model = get_compiled_model()
model.fit(x_train, y_train, sample_weight=sample_weight, batch_size=64, epochs=1)
```
데이터가 tf.data일 경우에는 아래의 예시를 따른다
```
sample_weight = np.ones(shape=(len(y_train),))
sample_weight[y_train == 5] = 2.0

# Create a Dataset that includes sample weights
# (3rd element in the return tuple).
train_dataset = tf.data.Dataset.from_tensor_slices((x_train, y_train, sample_weight))

# Shuffle and slice the dataset.
train_dataset = train_dataset.shuffle(buffer_size=1024).batch(64)

model = get_compiled_model()
model.fit(train_dataset, epochs=1)
```
from_tensorf_slices에서 sample_weight를 지정해주면 된다.

### passing data to multi-input , multi-output models 

앞서 봤던 것들은 하나의 input과 하나의 output을 가진다 그렇다면 다중 입력과 출력에 대해서는 어떻게 해야할까? 아래의 예시를 보자
```
image_input = keras.Input(shape=(32, 32, 3), name="img_input")
timeseries_input = keras.Input(shape=(None, 10), name="ts_input")

x1 = layers.Conv2D(3, 3)(image_input)
x1 = layers.GlobalMaxPooling2D()(x1)

x2 = layers.Conv1D(3, 3)(timeseries_input)
x2 = layers.GlobalMaxPooling1D()(x2)

x = layers.concatenate([x1, x2])

score_output = layers.Dense(1, name="score_output")(x)
class_output = layers.Dense(5, name="class_output")(x)

model = keras.Model(
    inputs=[image_input, timeseries_input], outputs=[score_output, class_output]
)
```
예시는 두개의 input을 가지고있다. 하나는 image의 크기, 다른 하나는 timeseries input_shape = (None , 10) 이다. 각기 다른 input값은
각각의 모델을 가지고 layers.concatenate([x1 , x2])을 통해 합쳐졌다. 이후 score_output 과 class_output 두가지 출력 layers를 만들었다.

```
keras.utils.plot_model(model , "multi_input_and_output_model.png", show_shapes=True)
```
![이미지](https://www.tensorflow.org/guide/keras/train_and_evaluate_files/output_ac8c1baca9e3_0.png)

모델을 학습하기 위해 서로다른 손실함수를 선언해주었다.(optimizer는 같음) 만약 하나의 loss함수만을 전달해 주었다면 두개의 모델은 같은 손실함수를 사용한다.
```
model.compile(
    optimizer=keras.optimizers.RMSprop(1e-3),
    loss=[keras.losses.MeanSquaredError(), keras.losses.CategoricalCrossentropy()],
)
```
complie에서 metrics또한 따로 지정가능하다 이때 2개 이상의 output을 가진다면 아래와 같이 dictionary형태로 선언해주는 것이 좋다

```
model.compile(
    optimizer=keras.optimizers.RMSprop(1e-3),
    loss={
        "score_output": keras.losses.MeanSquaredError(),
        "class_output": keras.losses.CategoricalCrossentropy(),
    },
    metrics={
        "score_output": [
            keras.metrics.MeanAbsolutePercentageError(),
            keras.metrics.MeanAbsoluteError(),
        ],
        "class_output": [keras.metrics.CategoricalAccuracy()],
    },
)
```
마찬가지로 loss_weights 요소를 통해 각각의 특정한 손실함수에 각기다른 가중치를 전달할 수 있다. 
```
model.compile(
    optimizer=keras.optimizers.RMSprop(1e-3),
    loss={
        "score_output": keras.losses.MeanSquaredError(),
        "class_output": keras.losses.CategoricalCrossentropy(),
    },
    metrics={
        "score_output": [
            keras.metrics.MeanAbsolutePercentageError(),
            keras.metrics.MeanAbsoluteError(),
        ],
        "class_output": [keras.metrics.CategoricalAccuracy()],
    },
    loss_weights={"score_output": 2.0, "class_output": 1.0},
)
```
만일 모델에 있어 학습을 시키지 않고싶을때 이런식으로 구현가능하다.
```
# List loss version
model.compile(
    optimizer=keras.optimizers.RMSprop(1e-3),
    loss=[None, keras.losses.CategoricalCrossentropy()],
)

# Or dict loss version
model.compile(
    optimizer=keras.optimizers.RMSprop(1e-3),
    loss={"class_output": keras.losses.CategoricalCrossentropy()},
)
```

```
model.compile(
    optimizer=keras.optimizers.RMSprop(1e-3),
    loss=[keras.losses.MeanSquaredError(), keras.losses.CategoricalCrossentropy()],
)

# Generate dummy NumPy data
img_data = np.random.random_sample(size=(100, 32, 32, 3))
ts_data = np.random.random_sample(size=(100, 20, 10))
score_targets = np.random.random_sample(size=(100, 1))
class_targets = np.random.random_sample(size=(100, 5))

# Fit on lists
model.fit([img_data, ts_data], [score_targets, class_targets], batch_size=32, epochs=1)

# Alternatively, fit on dicts
model.fit(
    {"img_input": img_data, "ts_input": ts_data},
    {"score_output": score_targets, "class_output": class_targets},
    batch_size=32,
    epochs=1,
)
```
fit()에서 더미 데이터를 만들어 dictionary형태로 넣어준다. 아래는 dataset의 경우이다
```
train_dataset = tf.data.Dataset.from_tensor_slices(
    (
        {"img_input": img_data, "ts_input": ts_data},
        {"score_output": score_targets, "class_output": class_targets},
    )
)
train_dataset = train_dataset.shuffle(buffer_size=1024).batch(64)

model.fit(train_dataset, epochs=1)
```

### Using callback

keras에서 callback 객체는 가중치가 학습하는 동안 다른요소들을 호출할 수 있게 한다 예를들어 epoch가 시작될떄, batch반복이 종료될떄, epoch의 반복이 마지막일떄 등이 있다 이는 아래와 같이 사용된다.

1. 학습하는동안 각기 다른 지점에서 검증을 할떄 
2. 정기적으로 또는 특정 정확도 임계 값을 초과할때 모델 체크포인트
3. 훈련이 정체되는 것처럼 보일떄 모델의 학습률 변경
4. 훈련이 정체되는 것처럼 보일때 최상위 계층의 미세조정 수행
5. 교육이 종료되거나 특정 성능 임계값이 초과된 경우 이메일 또는 인스턴트 메세지 알림 보내기

등이 있다 callback은 fit()함수에 인수로 전달가능하다. 아래 예시를 보자
```
model = get_compiled_model()

callbacks = [
    keras.callbacks.EarlyStopping(
        # Stop training when `val_loss` is no longer improving
        monitor="val_loss",
        # "no longer improving" being defined as "no better than 1e-2 less"
        min_delta=1e-2,
        # "no longer improving" being further defined as "for at least 2 epochs"
        patience=2,
        verbose=1,
    )
]
model.fit(
    x_train,
    y_train,
    epochs=20,
    batch_size=64,
    callbacks=callbacks,
    validation_split=0.2,
)
```
위 예시는 만일 검증시 그 성능이 개선되지 않을경우 학습을 중단하는 예시이다 위의 moniter = "val_loss" val_loss가 개선되지 않는다면 학습을 중
단하겠다는 의미이다 min_delta  = 1e-4는 1e-4만큼 개선되야지만 학습을 계속해나간다는 의미이다. 이러한 요소들은 학습을 조기종료하는 `EarlyStopping` 모듈에서의 인수들이다

이러한 모듈들은 

* ModelCheckpoint : 주기적으로 모델을 저장한다.

* EarlyStopping : 훈련이 더이상 검증 지표를 개선하지 않을 때 훈련을 중지한다 

* TensorBoard : 학습과정을 시각화할 수 있다.

* CSVLogger : 손실 및 metrics data를 CSV파일로 스트리밍한다.

### CheckPoint Model

비교적 큰 데이터 세트에서 모델을 학습하는 경우 모델의 체크 포인트를 빈번한 간격으로 저장하는 것이 중요하다 이를 구현하는 가장 쉬운 방법은
`ModelCheckpoint` callback을 사용하는 것이다. 매 epochs마다 모델이 저장되는 아래 예시를 보자

```
model = get_compiled_model()

callbacks = [
    keras.callbacks.ModelCheckpoint(
        # Path where to save the model
        # The two parameters below mean that we will overwrite
        # the current checkpoint if and only if
        # the `val_loss` score has improved.
        # The saved model name will include the current epoch.
        filepath="mymodel_{epoch}",
        save_best_only=True,  # Only save a model if `val_loss` has improved.
        monitor="val_loss",
        verbose=1,
    )
]
model.fit(
    x_train, y_train, epochs=2, batch_size=64, callbacks=callbacks, validation_split=0.2
)
```
ModelCheckpoint callback은 훈련이 무작위로 중단되는 경우 모델의 마지막 저장된 상태에서 훈련을 다시 시작 할 수 있는 기능이다 
```
import os

# Prepare a directory to store all the checkpoints.
checkpoint_dir = "./ckpt"
if not os.path.exists(checkpoint_dir):
    os.makedirs(checkpoint_dir)


def make_or_restore_model():
    # Either restore the latest model, or create a fresh one
    # if there is no checkpoint available.
    checkpoints = [checkpoint_dir + "/" + name for name in os.listdir(checkpoint_dir)]
    if checkpoints:
        latest_checkpoint = max(checkpoints, key=os.path.getctime)
        print("Restoring from", latest_checkpoint)
        return keras.models.load_model(latest_checkpoint)
    print("Creating a new model")
    return get_compiled_model()


model = make_or_restore_model()
callbacks = [
    # This callback saves a SavedModel every 100 batches.
    # We include the training loss in the saved model name.
    keras.callbacks.ModelCheckpoint(
        filepath=checkpoint_dir + "/ckpt-loss={loss:.2f}", save_freq=100
    )
]
model.fit(x_train, y_train, epochs=1, callbacks=callbacks)
```

























