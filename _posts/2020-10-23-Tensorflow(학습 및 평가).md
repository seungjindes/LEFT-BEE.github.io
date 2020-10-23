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









