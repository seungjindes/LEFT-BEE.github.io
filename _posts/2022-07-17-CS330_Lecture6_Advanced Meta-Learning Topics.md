---
layout: post
title: "[CS330] lecture6 review"
categories: summary
tags: summary cs330
comments: true
---

## QnA

-----
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

이를 구현하기 위해 가우스 분포형태의 regulrization 추가하여 정보에 noise를 준다. 이를 통해 theta에 전달되는 train set의 info를 
컨트롤 할 수 있다. 

![image](https://user-images.githubusercontent.com/65720894/179391368-54b0973a-49ec-44f3-b74f-c6c981ad16b6.png)

성능을 확인해보았는데 MR(Meta regularization)-MAML위 경우 성능이 매우 좋아짐을 확인 할 수 있다. 또한 pose prediction taks
의 경우에도 성능감소가 별로 일어나지 않는 것을 확인할 수 있다.

-----

## A general recipe for unsupervised meta-learning

![image](https://user-images.githubusercontent.com/65720894/179391438-350b8d23-81d4-460a-a508-0ff16c5cc17d.png)

label이 없는 data를 메타러닝하기 위해서는 다음과 같이 데이터를 받으면 사용자가 지정한 task를 통해 meata learing을 시킨다.
이때 task를 선정하기 위해 diverse(얼마나 버용성 있게 test task를 수행하는가?) 와 structed(few-shot learning이 가능한가?)을 고려해야만
한다. 

![image](https://user-images.githubusercontent.com/65720894/179391915-dea7bfd2-07e6-43db-b875-11fe16d68494.png)

예시는 다음과 같다. unlabled 데이터를 nusupervised learning중 하나인 클러스터링을 통해 분류해준다. 그리고 task마다 
클러스터링 한 레이블로 구분해준다. 이는 분류문제를 풀고 있는 것이 아닌 범용적인 meta laerning을 하는 것이기 떄문에 class는 
클러스터링한 값들이 무의미해보일 수도 있다. (예시에서는 긴거 or 동그란거 인듯함) 이렇게 만든 dataset을 train과 test 데이터로 나누고
메타러닝을 실시한다. 

결과적으로 downsteam task에 맞는 표현방식을 얻어 낼 수 있다. ex) 분류문제 

![image](https://user-images.githubusercontent.com/65720894/179392054-7983649c-e6ed-4fae-8be5-30745855f41d.png)

다음과 같이 self-supervised learing과 유사해 보이는 방법도 사용가능하다. dataset에서 augumentation을 통해 그 positive pair를 
만들고 positive pair의 거리를 최대로 유사하게 만드는 특징 추출기를 만든다. 이러한 경우 마찬가지로 meta-learning이 간능하다.

이러한 예제들은 얼핏 supervised learning과 유사해보이지만 항상 meta-learing은 많은 class에 적은 example에서 유리하다는 것을 기억하자. 또한 train data를 학습하는 것이 아닌 학습하는 방법을 학습하는 것또한 잊어서는 않된다.

![image](https://user-images.githubusercontent.com/65720894/179392144-eb0eb527-e12e-40f1-b11e-4da3990783fc.png)

language model에서도 좋은 성능을 보인다. smllmt가 본 강의에서 나오는 mask를 사용한 meta-learing language model 인데 


![image](https://user-images.githubusercontent.com/65720894/179392447-b8c718d8-34c5-4b14-a783-24c2f6e5c5db.png)


마찬가지로 언어모델에서 특징을 표현하는 것을 학습한다. 방금 생각 난 것은 language model은 어마어마하게 많은 데이터를 학습하게 될 것인데 결국에는 supservised learning도 마찬가지로 일반화가 되지 않을까 한다. meat-laring과 마찬가지로 근데 위에 성능표를 보니 meta-learning이 몇몇 task에서 앞서는 것을 확인할 수 있으니 case by case일 것 같다. 








