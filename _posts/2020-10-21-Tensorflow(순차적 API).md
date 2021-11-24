---
layout: post
title: "[Tensorflow] 순차적 API"
categories: deeplearning
tags: tech
comments: true
---

### tensorflow 순차적 API실습

## Sequential 모델

```
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers
```

예시를 들어보자
```
# Define Sequential model with 3 layers
model = keras.Sequential(
    [
        layers.Dense(2, activation="relu", name="layer1"),
        layers.Dense(3, activation="relu", name="layer2"),
        layers.Dense(4, name="layer3"),
    ]
)
# Call model on a test input
x = tf.ones((3, 3))
y = model(x)
```
이렇게 만든 모델은 아래와 같은 구조이다

```
# Create 3 layers
layer1 = layers.Dense(2, activation="relu", name="layer1")
layer2 = layers.Dense(3, activation="relu", name="layer2")
layer3 = layers.Dense(4, name="layer3")

# Call layers on a test input
x = tf.ones((3, 3))
y = layer3(layer2(layer1(x)))
```
이렇게 만든 레이어는 model.layers를 통해 접근 할 수 있다.

add()메서드를 통해 Sequential모델을 점진적으로 작성 할 수도 있다.
```
model = keras.Sequential()
model.add(layers.Dense(2, activation="relu" , name = "layers 1")) 
model.add(layers.Dense(3, activation="relu"))
model.add(layers.Dense(4))
```
name인수를 허용하기때문에 유의미한 이름으로 주석을 달 수 있다.

레이어를 제거하는 pop()메서드도 있다 
```
model.pop()
print(len(model.layers))  # 2

결과:

2
```

일반적으로 Keras의 모든 레이어는 가중치를 만들려면 입력의 형상을 알아야 합니다. 따라서 다음과 같은 레이어를 만들면 처음에는 가중치가 없습니다
```
layer = layers.Dense(3)
layer.weights  # Empty
```
가중치는 모양이 입력의 형상에 따라 달라지기 때문에 입력에서 처음 호출될 때 가중치를 만든다.
```
# Call layer on a test input
x = tf.ones((1, 4))
y = layer(x)
layer.weights  # Now it has weights, of shape (4, 3) and (3,)
```
즉
```
model = keras.Sequential(
    [
        layers.Dense(2, activation="relu"),
        layers.Dense(3, activation="relu"),
        layers.Dense(4),
    ]
)  # No weights at this stage!

# At this point, you can't do this:
# model.weights

# You also can't do this:
# model.summary()

# Call the model on a test input
x = tf.ones((1, 4))
y = model(x)
print("Number of weights after calling the model:", len(model.weights))  # 6


결과
Number of weights after calling the model: 6
```

모델을 생성할때에는 가중치가 존재하지 않치만 x입력값을 넣어줄시 가중치가 생성된다는 것이다
또한 모델이'build'되면 그 내용을 표시하기 위해 summary()메서드를 호출할 수 있다.

```
model.summary()

Model: "sequential_3"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
dense_7 (Dense)              (1, 2)                    10        
_________________________________________________________________
dense_8 (Dense)              (1, 3)                    9         
_________________________________________________________________
dense_9 (Dense)              (1, 4)                    16        
=================================================================
Total params: 35
Trainable params: 35
Non-trainable params: 0
_________________________________________________________________

```

추가적으로 Input객체를 모델에 전달하여 모델의 시작형상을 알 수 있도록 모델을 시작해야한다.


### 일반적인 debugging workflow 

```
model = keras.Sequential()
model.add(keras.Input(shape=(4,)))
model.add(layers.Dense(2, activation="relu"))

model.summary()

Model: "sequential_4"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
dense_10 (Dense)             (None, 2)                 10        
=================================================================
Total params: 10
Trainable params: 10
Non-trainable params: 0
_________________________________________________________________
```

```
model = keras.Sequential()
model.add(layers.Dense(2, activation="relu", input_shape=(4,)))

model.summary()
```

이렇게 inputdata를 인수로 놓는다면 항상 가중치를 가지며 항상 정의된 출력 형상을 갖는다 그러니 일반적으로 input인수를 주는것이 좋다.

```
model = keras.Sequential()
model.add(keras.Input(shape=(250, 250, 3)))  # 250x250 RGB images
model.add(layers.Conv2D(32, 5, strides=2, activation="relu"))
model.add(layers.Conv2D(32, 3, activation="relu"))
model.add(layers.MaxPooling2D(3))

# Can you guess what the current output shape is at this point? Probably not.
# Let's just print it:
model.summary()

# The answer was: (40, 40, 32), so we can keep downsampling...

model.add(layers.Conv2D(32, 3, activation="relu"))
model.add(layers.Conv2D(32, 3, activation="relu"))
model.add(layers.MaxPooling2D(3))
model.add(layers.Conv2D(32, 3, activation="relu"))
model.add(layers.Conv2D(32, 3, activation="relu"))
model.add(layers.MaxPooling2D(2))

# And now?
model.summary()

# Now that we have 4x4 feature maps, time to apply global max pooling.
model.add(layers.GlobalMaxPooling2D())

# Finally, we add a classification layer.
model.add(layers.Dense(10))

결과

Model: "sequential_6"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
conv2d (Conv2D)              (None, 123, 123, 32)      2432      
_________________________________________________________________
conv2d_1 (Conv2D)            (None, 121, 121, 32)      9248      
_________________________________________________________________
max_pooling2d (MaxPooling2D) (None, 40, 40, 32)        0         
=================================================================
Total params: 11,680
Trainable params: 11,680
Non-trainable params: 0
_________________________________________________________________
Model: "sequential_6"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
conv2d (Conv2D)              (None, 123, 123, 32)      2432      
_________________________________________________________________
conv2d_1 (Conv2D)            (None, 121, 121, 32)      9248      
_________________________________________________________________
max_pooling2d (MaxPooling2D) (None, 40, 40, 32)        0         
_________________________________________________________________
conv2d_2 (Conv2D)            (None, 38, 38, 32)        9248      
_________________________________________________________________
conv2d_3 (Conv2D)            (None, 36, 36, 32)        9248      
_________________________________________________________________
max_pooling2d_1 (MaxPooling2 (None, 12, 12, 32)        0         
_________________________________________________________________
conv2d_4 (Conv2D)            (None, 10, 10, 32)        9248      
_________________________________________________________________
conv2d_5 (Conv2D)            (None, 8, 8, 32)          9248      
_________________________________________________________________
max_pooling2d_2 (MaxPooling2 (None, 4, 4, 32)          0         
=================================================================
Total params: 48,672
Trainable params: 48,672
Non-trainable params: 0
_________________________________________________________________

```

일반적으로 새로운 Sequential아키텍쳐를 만들떄에는 레이어를 점진적으로 쌓고 모델요약을 자주 인쇄하는것이 유용하다.

### 모델이 완성되면 해야 할 일

1. 모델을 훈련시키고 평가하며 추론을 실행한다 

2.모델을 디스크에 저장하고 복구한다 

3. 다중 GPU를 활용해서 모델의 훈련속도를 향상한다.

### 전이학습 과정

전이학습과정은 두 가지가 일반적으로 있는데 먼제 Senqumtail 모델이 있고 마지막 모델을 제외한 모든 레이어를 동결하려고한다고 가정하자 이경우 다음과 같이 단순히
model.layers를 반복하고 마지막 레이어를 제외하고 각 레이어에서 layer.trainable = False를 설정한다.

```
model = keras.Sequential([<br>    keras.Input(shape=(784))<br> 
layers.Dense(32, activation='relu'),<br>    layers.Dense(32, activation='relu'),<br> 
layers.Dense(32, activation='relu'),<br>    layers.Dense(10),<br>])<br> <br>
```

이상으로 마치도록 한다.


















