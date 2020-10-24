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

* model.save() or tf.keras.models.save_model()

* tf.keras.models.load_model()



