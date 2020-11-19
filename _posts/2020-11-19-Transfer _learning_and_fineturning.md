---
title : "Transfer learning and fine - turning"
---

## Introduction 

전이학습이라는 어느 한 문제에서 학습된 feature를 가지고 비슷하면서 새로운 문제에 이를 활용하는 방법이다 예를들어 너구리를 식별하는 모델로 부터의 features는
tanuki(대충 너구리 비슷한놈)을 구별하는데 유용하다 또한 전이학습은 내가 가지고있는데 큰 규모의 모델을 학습하는데 있어 dataset이 매우 적을때 유용하다.

전이학습의 일반적인 활용은 아래와 같다.

1. 미리 학습된 모델로부터의 layer을 가져온다.

2. 그것들이 가지고 있는 어느 정보가 layer가 앞으로 학습되는데 있어 총둘을 야기시키기 떄문에 Freeze해준다

3. frozen된 layer위에 trainable layers를 더해준다 그것들은 이전의 학습된 특징으로부터 새로운 dataset에 의한 예측값으로 변할 것이다.

4. new layers를 내가 가지고있는 dataset으로 학습시킨다.

마지막으로 선택적인 단계가 있는데 fine-tuning이다 이는 위에서 획득한 전체 모델을 고정해제하는 것으로 구성된다 그리고 다시 새로운 데이터로 매우 느린 학습률을 가진채로 
재학습시킨다. 이는 잠재적으로 의미있는 향상을 보인다 또한 사전 훈련 된 기능을 새 데이터에 점진적으로 적용는 것이다.

우선 Keras trainable API을 자세히 살펴볼 것이다 이는 대부분의 전이학습과 fine-tunung작업의 기초라 할 수 있다.
그리고 특정한 workflow를 증명할 것이다 ImageNet에 미리 학습된 모델을 가져옴으로써 그리고 "cats vs dogs 구별 데이터셋을 재학습 시킴으로써

### freezing layers: understanding the trainable attribute 

Layers & models은 가중치요소를 가지고있다.
* `weight`는 layer에 있는 모든 가중치의 리스트이다

* `trainable_weights`는 학습동안 loss함수의 최소화에 있어 계속되어 업데이트되는 가중치 리스트이다

* `non_trainable_weight`는 학습되지 않는 것들을 의미한다 특히 forward pass동안 모델에 의해 업데이트 된다.

아래 예시를 보자
```
layer = keras.layers.Dense(3)
layer.build((None, 4))  # Create the weights

print("weights:", len(layer.weights))
print("trainable_weights:", len(layer.trainable_weights))
print("non_trainable_weights:", len(layer.non_trainable_weights))


결과
weights: 2
trainable_weights: 2
non_trainable_weights: 0
```
일반적으로 모든 가중치들은 훈련가능한 가중치이다 학습하지 않는 layer는 BatchNormarlization층 밖에 없다 학습하는 동안 input의 mean과 variance를 추적할때 non-trainalble을 사용한다 .

Example: the BatchNormalization layer has 2 trainable weights and 2 non-trainable weights
```
layer = keras.layers.BatchNormalization()
layer.build((None, 4))  # Create the weights

print("weights:", len(layer.weights))
print("trainable_weights:", len(layer.trainable_weights))
print("non_trainable_weights:", len(layer.non_trainable_weights))\

결과
weights: 4
trainable_weights: 2
non_trainable_weights: 2

```
Layers & model은 trainable에 있어 이중적인 특징을 가진다 이것의 값은 바꿀 수 있다 layer.trainable을 False로 세팅하는 것은 layer에 weights들을 trainable 에서 non-tranable
하게 만든다. 이를 두고 "freezing"이라 부른다. 이렇게 frozen되어있는 layer는 학습하는 동안 업데이트 되지 않는다.

EXAMPLE: setting trainable to False
```
layer = keras.layers.Dense(3)
layer.build((None, 4))  # Create the weights
layer.trainable = False  # Freeze the layer

print("weights:", len(layer.weights))
print("trainable_weights:", len(layer.trainable_weights))
print("non_trainable_weights:", len(layer.non_trainable_weights))

결과
weights: 2
trainable_weights: 0
non_trainable_weights: 2
# trainable_weight이 2개가 사라짐
```

trainable weight가 non-trainable하게 될때 그 값은 더이상 학습하는 동안 업데이트 되지 않는다.
```
# Make a model with 2 layers
layer1 = keras.layers.Dense(3, activation="relu")
layer2 = keras.layers.Dense(3, activation="sigmoid")
model = keras.Sequential([keras.Input(shape=(3,)), layer1, layer2])

# Freeze the first layer
layer1.trainable = False

# Keep a copy of the weights of layer1 for later reference
initial_layer1_weights_values = layer1.get_weights()

# Train the model
model.compile(optimizer="adam", loss="mse")
model.fit(np.random.random((2, 3)), np.random.random((2, 3)))

# Check that the weights of layer1 have not changed during training
final_layer1_weights_values = layer1.get_weights()
np.testing.assert_allclose(
    initial_layer1_weights_values[0], final_layer1_weights_values[0]
)
np.testing.assert_allclose(
    initial_layer1_weights_values[1], final_layer1_weights_values[1]
)
```
### recursive setting of the trainable attribute

만약 sublayer를 가지거나 children layer가 있는 layer를 trainable =false로 설정헀다면 이는 하위의 layer들도 non-trainable해진다
example
```
inner_model = keras.Sequential(
    [
        keras.Input(shape=(3,)),
        keras.layers.Dense(3, activation="relu"),
        keras.layers.Dense(3, activation="relu"),
    ]
)

model = keras.Sequential(
    [keras.Input(shape=(3,)), inner_model, keras.layers.Dense(3, activation="sigmoid"),]
)

model.trainable = False  # Freeze the outer model

assert inner_model.trainable == False  # All layers in `model` are now frozen
assert inner_model.layers[0].trainable == False  # `trainable` is propagated recursively
```

### Typical transfer-learning workflow 

Keras에서 특정한 전이학습과정이 어떻게 이루어지는지를 알아보자 

1. 기반 모델을 인스턴트화하고 학습된 weight를 로드하여 모델에 넣는다 

2. 기반 모델에 있는 모든 layers를 고정한다  (trainable = False를 통해)

3. 기반 모델의 output layer위에 새로운 모델을 생성한다.

4. 새로운 데이터로 새로운 모델을 학습하자!

더욱 가벼운 작업에 있어 또한 이러한 대안이 있다.

1. 기반 모델을 인스턴트화하고 학습된 weight를 로드하여 모델에 넣는다.

2. 새로운 데이터를 이용해 원래 모델을 통해 학습시킨다 그리고 결과물중 하나의 layer를 저장한다 이를 feature extraction(특징 추출)이라고 부른다 

3. 이렇게 나온 출력값을 비슷한(가벼운)모델에 input 데이터로 사용한다.











