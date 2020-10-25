---
title : "Making new Layers and Models via subclassing:"
---

## Save and Load Keras models
---------------------------------------------

### 도입부

keras 모델은 다양한 요소들을 포함한다. 

* 모델의 구조(architecture, or configuration구성 , 각 layer가 모델에서 어떻게 연결되어지는지 등 

* 가중치의 값

* optimizer

* 손실함수와 metrics의 구성 

이러한 요소들을 Keras API을 통해 선택적으로 또는 전체를 저장할 수있다. TensorFlow SavedModel형식 (이전에는 Keras H5형식)으로 모든 것을 저장한다 이는 표준방식이다
또한 일반적으로 JSON 파일로 아키텍쳐 및 구성만 저장한다 또한 일반적으로 모델을 학습시킬경우 가중치의 값만을 저장한다.

이러한 설정등을 알아보자 어떤 것을 사용해야하는지 또한 어떻게 사용해야하는지에 대해서 말이다.

---
### The short answer to saving & loading

tensorflow 문서에서는 매우 간단하다고 소개하고 있으니 한번 봐보자 

savning a keras model의 예시이다
```
model = ...  # Get model (Sequential, Functional Model, or Model subclass)
model.save('path/to/location')
```

Loading the model 의 예시이다
```
from tensorflow import keras
model = keras.models.load_model('path/to/location')
```
### whole-model saving & loading 

전체 모델을 단일항목으로 저장할 수 있는데 이는 

1. 모델의 구조와 설정

2. 모델의 가중치값 

3. 모델의 compliation 정보 (if compile() was called)

4. optimizer 와 그것의 상태 

### API

모델을 저장하는 방법은 
* model.save() or tf.keras.models.save_model()

모델을 로드하는 방법은
* tf.keras.models.load_model()

디스크(저장소)에 저장하는데 사용할 수 있는 두가지 형태는 TensorFlow SavedModel 형식과 이전의 KerasH5 형식이다 이중 Tensroflow에서 권장하는 방식은 SavedModel형식이다 따라서 model.save()을 사용했을 떄의 기본값은 SavedModel형식이다. 이때 format = 'h5' 를 save()에 인수로 전달하거나 .h5 또는 .keras로 끝나는 파일명을 save()로 전달하면 H5형식으로 전환하여 저장한다.

#### SavedModel 형식

예시를 보자
```
def get_model():
    # Create a simple model.
    inputs = keras.Input(shape=(32,))
    outputs = keras.layers.Dense(1)(inputs)
    model = keras.Model(inputs, outputs)
    model.compile(optimizer="adam", loss="mean_squared_error")
    return model


model = get_model()

# Train the model.
test_input = np.random.random((128, 32))
test_target = np.random.random((128, 1))
model.fit(test_input, test_target)

# Calling `save('my_model')` creates a SavedModel folder `my_model`.
model.save("my_model")

# It can be used to reconstruct the model identically.
reconstructed_model = keras.models.load_model("my_model")

# Let's check:
np.testing.assert_allclose(
    model.predict(test_input), reconstructed_model.predict(test_input)
)

# The reconstructed model is already compiled and has retained the optimizer
# state, so training can resume:
reconstructed_model.fit(test_input, test_target)
```
get_model()함수는 만든 model의 compile한 값을 반환해준다. 이를 받은 model = get_model()은 fit()에서 학습된다. 이떄 model.save('파일명')을 통해 학습한 모델을 저장하였다 저장된 모델을 keras.model.load_model()을 통해 받아와 데이터를 예측시키고 test데이터를 이용해 확인해본다 .

### What the SavedModel contains 

model.save('my_model')을 호출하면 my_model이라는 폴더를 생성한다 폴더안에는 3가지 파일이 들어있는데
```
assets  saved_model.pb  variables
```

saved_model.pb에는 모델 아키텍쳐 및 training configuration(optimizer , loss , metric)등이 저장된다. 가중치는 variables 디렉토리에 저장된다.

### Keras H5 format 

keras는 singgle로 HDF5 파일로 저장할수 있다 이는 모델의 구조, 가중치의 값, compile()의 정보등이 들어있다.
```
model = get_model()

# Train the model.
test_input = np.random.random((128, 32))
test_target = np.random.random((128, 1))
model.fit(test_input, test_target)

# Calling `save('my_model.h5')` creates a h5 file `my_model.h5`.
model.save("my_h5_model.h5")

# It can be used to reconstruct the model identically.
reconstructed_model = keras.models.load_model("my_h5_model.h5")

# Let's check:
np.testing.assert_allclose(
    model.predict(test_input), reconstructed_model.predict(test_input)
)

# The reconstructed model is already compiled and has retained the optimizer
# state, so training can resume:
reconstructed_model.fit(test_input, test_target)
```
#### Limitation 

SavedModel format에 비하여 두가지가 H5파일에는 포함되어있지 않다.

* External losses & metrics : savedmodel형식과는 다르게 model.add_loss()와 model.add_metrics가 저장되지 않는다 만약 불러온 모델을 다시 학습시키고 싶은경우 다시 추가해줘야한다. 

* The computation graph of custom objects : custom layers같은 경우 파일에 저장되지 않는다

### Saving the architecture

모델의 구조는 모델에 포함된 레이어와 이러한 레이어의 연결방법을 지정한다 모델 구성이 있는 경우 가중치에 대해 새로 초기화된 상태로 컴파일 정보 없이 모델을 작성할 수 있다.

#### Sequential 모델 또는 Functional API 모델의 구성 ★★★★★★★★★★★★★(여기 모르겠음 뭐라는건지)

이러한 유형의 model은 명시적 graph이다 구성은 항상 구조화된 형식으로 제공된다.

APIS

* get_fonfig() 및 from_config()

* tf.keras.model.model_to_json 및
  tf.keras.model.model_from_json()
  
get_config() 및 from_config()
config = model.get_config()을 호출하면 모델 구성이 포함된 Python dict가 반환된다 그런 다음 Sequential.from_config(config)을 통해 (이떄 config는 Sequential 모델이다) 또는 Model.from_config()을 통해 동일한 모델을 재구성할 수 있다.
  
레이어 예제:
```
layer = keras.layers.Dense(3, activation="relu")
layer_config = layer.get_config()
new_layer = keras.layers.Dense.from_config(layer_config)
```
...모르겠다 생략하자

### 모델의 가중치 값만 저장 및 로딩

모델의 가중치 값만 저장하고 로드하도록 선택할 수 있습니다. 다음과 같은 경우에 유용할 수 있습니다.

* 모델을 추론을 위해서만 필요할때: 이런 경우에는 학습을 다시 시작할 필요는 없다 따라서 optimizer상태나 다른 요소들을 바꾸지 않아도 괜찮다

* 전이학습을 위해서 필요할떄 : 이런 경우 이전의 모델의 상태를 다시 사용해 새로운 모델을 학습시키는 것이다 따라서 이전 모델의 다른 요소들을 건들지 않아도 된다

#### APIs for in-memory weight transfer

가중치는 다른 객체들 사이에서 copied 될수 있다 이를 위해 get_weight 그리고 set_weight를 사용한다 :

* `tf.keras.layers.Layer.get_weight()` : numpy array 리스트를 반환한다.

* `tf.keras.layers.Layer.set_weight()` : 모델의 가중치를 설정하는데 

아래 예시를 보자 

Transfering weights from one layer to another, in memory

```
def create_layer():
    layer = keras.layers.Dense(64, activation="relu", name="dense_2")
    layer.build((None, 784))
    return layer


layer_1 = create_layer()
layer_2 = create_layer()

# Copy weights from layer 2 to layer 1
layer_2.set_weights(layer_1.get_weights())
```
요런식으로 layers_1의 가중치를 layers_2 의 가중치에 전달하였다. 다음예시는 모델간의 가중치 전이학습이다

Transfering weights from one model to another model with a compatible architecture, in memory
```
# Create a simple functional model
inputs = keras.Input(shape=(784,), name="digits")
x = keras.layers.Dense(64, activation="relu", name="dense_1")(inputs)
x = keras.layers.Dense(64, activation="relu", name="dense_2")(x)
outputs = keras.layers.Dense(10, name="predictions")(x)
functional_model = keras.Model(inputs=inputs, outputs=outputs, name="3_layer_mlp")

# Define a subclassed model with the same architecture
class SubclassedModel(keras.Model):
    def __init__(self, output_dim, name=None):
        super(SubclassedModel, self).__init__(name=name)
        self.output_dim = output_dim
        self.dense_1 = keras.layers.Dense(64, activation="relu", name="dense_1")
        self.dense_2 = keras.layers.Dense(64, activation="relu", name="dense_2")
        self.dense_3 = keras.layers.Dense(output_dim, name="predictions")

    def call(self, inputs):
        x = self.dense_1(inputs)
        x = self.dense_2(x)
        x = self.dense_3(x)
        return x

    def get_config(self):
        return {"output_dim": self.output_dim, "name": self.name}


subclassed_model = SubclassedModel(10)
# Call the subclassed model once to create the weights.
subclassed_model(tf.ones((1, 784)))

# Copy weights from functional_model to subclassed_model.
subclassed_model.set_weights(functional_model.get_weights())

assert len(functional_model.weights) == len(subclassed_model.weights)
for a, b in zip(functional_model.weights, subclassed_model.weights):
    np.testing.assert_allclose(a.numpy(), b.numpy())
    ````
    





