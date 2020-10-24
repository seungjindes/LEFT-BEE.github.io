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

