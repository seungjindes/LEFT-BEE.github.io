---
layout: post
title: "[CS330] lecture6 review"
categories: summary
tags: summary cs330
comments: true
---

## QnA


## How should task be defined for good meta-learning performace?

이번 강의의 주제는 어떻게 메타러닝의 성능을 끌어올릴 수 있는가? 이다. 

![image](https://user-images.githubusercontent.com/65720894/179387218-c2b3c76a-537a-4f1c-ade2-fa0707d6d94e.png)

다시 정리 해보자면 우리는 지금까지 black-box adaptation = model-based , optimization-adaptation, metric adaptation기법을 배우고 이에 따른 장점과 단점을 
공부하였다. 위 슬라이드의 경우 optimization-based한 모델인데 meata-learner를 embedding함으로써 깊은 네트워크를 구성할 수 있었다. 

![image](https://user-images.githubusercontent.com/65720894/179387270-30b31d82-ec98-42e3-8c43-9e78e35bec16.png)

또한 Non-parametric meta-learning한 방법은 optimize하기 쉽고 연산이 빠르지만 분류문제에 특화되었다는 점이 단점이라고 설명하였다.

![image](https://user-images.githubusercontent.com/65720894/179387329-79a43e8a-33ad-4f00-8d4b-081ca1c38e75.png)

강의에서는 다음과 같이 질문한다. label order를 랜덤으로 주지 않고 고정하면 학습성능이 좋아질 것인가? 이에 대한 답은 metric-based 한 모델을 제외하고는
meta-learing에서는 심각하게 성능이 않좋아진다고한다. 그 이유는 하나의 모델이 모든 문제를 풀수 있게 학습이 되고 이는 일반화가 되지 않으므로 train datsaet에서는
어느정도 성능을 얻을지 몰라도 meta-learing에서는 성능을 기대하기 어렵다고한다.   

즉 학습되는 모델은 train data의 정보를 학습하면 않된다. - mutual exclusive해야한다. 

![image](https://user-images.githubusercontent.com/65720894/179387458-af6ffdeb-6dba-4798-b07e-5673d7dbc8fa.png)

이러한 경우를 일반 supervised learning 의 overfitting과 비교하여 설명하고 있다. 모델 f에 train set의 정보가 너무 많이 학습되어 실제로 test set에서 좋은성능을
보이지 않는 것이기에 비슷한 개념이라 생각하면 될 것 같다.

![image](https://user-images.githubusercontent.com/65720894/179387747-9e48cc5c-4074-4889-bca4-199fc5cd3c30.png)

이를 해결하기 위해 두가지의 솔루션을 제안한다. 이들을 통합하여 정보의 흐름을 컨트롤하는 것이 핵심적인 내용이다.

![image](https://user-images.githubusercontent.com/65720894/179388412-a7b8c494-bfff-44f7-b0e8-d3e832812dbb.png)

이를 구현하기 위해 regulrization 



