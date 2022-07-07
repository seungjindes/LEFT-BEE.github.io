---
layout: post
title: "[CS330] lecture3 review"
categories: summary
tags: summary cs330
comments: true
---

## Question

![image](https://user-images.githubusercontent.com/65720894/177471474-a9c84994-bf85-42c2-80fa-2b8d0e062d97.png)

1. 다음과 같이 메타러닝을 할때 훈련 데이터에 들어가는 이미지셋은 배치당 7개의 이미지가 맞는지?
2. training classes가 많을 수록 학습 성능이 좋아지는지?

![image](https://user-images.githubusercontent.com/65720894/177710740-2328c020-d66c-4c52-801d-4a67877ae53b.png)
여기서 파이가 이 네트워크의 파라미터들을 그대로 가져오는건지
아니면 문맥벡터? 를 의미하는지?

------ 

## Transfer & Meta-Learning

2강에서 살펴보았던 multi-task learning 과 transfer learning의 차이를 설명하고 있다.  
가장 큰 차이점은 multi-task learning 은 한번에 여러개의 task를 동시에 학습하고 transfer learning은 먼저 task b를 학습하고 여기서 얻은 
지식을 통해 task a를 순차적으로 학습한다는 것에 차이가 있다

또한 multi-task learning이 task a 와 task b를 동시에 학습할 수 있다는 점과 transfer-learning에서는 결국에는 task b만을 학습하게 된다는 것이 차이점이다.

![image](https://user-images.githubusercontent.com/65720894/177678922-5f8dad4c-7025-497d-8f21-a2423d284d9a.png)


보통 transfer learning에서는 fine-tuning을 통해 이후 task를 학습하게 되는데 이는 먼저 학습하게 된 task a를 통해 weight를 initiation 하여 이후 학습에 더욱 빠르고 정확하게 학습이 가능하다.

일반적으로 pretrained 모델을 사용하면서 fine tuning할 때 사용하는 규칙 같은게 있는데,  
보통 finetuning을 할 때는 pretrained된 것보다 작은 learning rate부터 시작하여 점점 올리는 형식으로 파라미터를 찾는다.
그리고 pretrained 된 모델을 freeze 하고 점점 unfreeze하면서 학습을 하는데 이때 earlie layer에서부터 하는 것이 일반적이다.
그리고 마지막 layer를 reinitialze 하고 학습을 한다.
그리고 하이퍼파라미터를 찾을 때 교차검증을 통해 알아내는 것, 그리고 task에 맞는 pretrained model를 찾는 것 또한 중요하다.


![image](https://user-images.githubusercontent.com/65720894/177685366-d3cd6753-d697-4273-a413-f51bdb985d6f.png)


### Meta-learning work

아래 그림을 보면 task에 따라 batch를 나누어 학습을 진행한다. meta test 데이터셋에서는 train dataset 에서 보지 못한 새로운 클래스에 대해 예측을 수행한다.
위 그림과 같은 경우 1-shot 5 classes 의 경우라고 볼 수 있다.   

이러한 task는 image classification 뿐 아니라 regression, language generation, skill learning과 같은 다른 ml problem에도 적용가능하다.

![image](https://user-images.githubusercontent.com/65720894/177696669-2015fc28-5a34-442c-800a-8f5cccf765bb.png)

마찬가지로 이전의 task를 학습시키어 test task를 빠르게 해결할 수 있음을 설명하고 있다. 이때 meta learning을 학습하는 방법을 학습한다고 설명해준다.

![image](https://user-images.githubusercontent.com/65720894/177700694-740a55d2-4bfc-408a-bb45-1599d1ed2267.png)


meta learning 에서 mnist와 같은 샘플인 omniglot dataset은 많은 종류의 적은 데이터 갯수를 가지고 있다. 이는 현실에서 많이 볼 수 있는 케이스라고 한다.

![image](https://user-images.githubusercontent.com/65720894/177707443-831dab94-d532-486b-a577-6b29a0296d08.png)


이제 알고리즘의 경우 제일 먼저 black-box adaptation을 소개한다. 구조는 아래와 같은데 RNN과 같은 sequential 한 모델에 D_tr traian dataset을 입력으로 받아 pi를 얻는다.
이 pi 는 이후 test set의 label값을 예측하는데 사용된다. 즉 이전에 train datset 으로 학습한 지식이라고도 볼 수 있다.

오른쪽 아래와 같이 학습을 한다. D_tr을 통해 pi를 얻고 D_test를 pi 함수에 입력으로 넣어 나온 값과 비교해서 loss를 구한다. 

![image](https://user-images.githubusercontent.com/65720894/177709451-8eecbb7c-8d4c-461c-8450-ab32ac4332cb.png)


구체적인 순서는 다음과 같다.


![image](https://user-images.githubusercontent.com/65720894/177709646-105aae64-47f2-4c42-b70e-d8d4994012e6.png)


여기서 네트워크의 모든 파라미터는 필요없다고 한다. 따라서 작업의 context를 추출할 만한 저 차원의 벡터만을 추출하게 되는데 이를 h라고 하자.
이는 이전에 살펴 본 것 처럼 학습이 공유되는 파라미터라고도 볼 수 있다, - 조금더 일반적인 모델을 만들 수 있을 것이다. 

![image](https://user-images.githubusercontent.com/65720894/177710355-54c226eb-9178-49a6-aac1-e7668174fe01.png)

물론 이러한 구조는 rnn에 구애받지 않아도 되고 오히려 순서가 상관없는 경우 feedforward + avg한 모델을 사용하는게 성능이 좋다고한다, 그 외에도 
cnn과 attention과 같은 요즘 많이 사용하는 모델도 효과적으로 학습이된다고한다.

![image](https://user-images.githubusercontent.com/65720894/177710922-624cd02c-4eda-4f09-9901-c4d37cf4f238.png)






















