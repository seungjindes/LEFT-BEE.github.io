---
layout: post
title: "[CS330] lecture2 review"
categories: summary
tags: summary cs330
comments: true
---

## Multi-Task learning

다중 task를 학습하기 위해서는 공유되는 theta를 가지는 것을 공통으로 하면서 다양한 구조를 가질 수 있다.  
일반적으로 아래와 같이 작업을 정의한다.  

Task 마다 batch를 나눠 batch에 맞게 loss를 줄이고(이때 일반적인 딥러닝 학습처럼 mse, cross entropy와 같은 손실함수 사용), batch 이후 최종적으로 전체 loss를
줄이는 것을 목표로한다. 



![image](https://user-images.githubusercontent.com/65720894/177447575-a9ebf0e0-57b0-40d7-a977-d7ce5903f75d.png)


여기서 표현식에 대해 좀더 자세히 알아보자면 D_tr 와 D_test는 각 배치마다의 train데이터와 test데이터이다 i는 물론 batch의 index를 의미한다.  
만약 task가 다양한 글자의 학습에 있다면 p(x) 는 그 글자의 갯수 즉, 영어의 알파벳 26개 y는 그 글자에 따른 레이블이라고 할 수 있다. 

multi-task 학습은 도메인이 같은 경우, p(x)가 같은 경우 그리고 p(X)와 y의 형태가 같은 경우도 있다. 결론적으로 multi-task learning은 어느 정도 같은 도메인을 지닌
task끼리 통용된다는 것을 알려준다.



![image](https://user-images.githubusercontent.com/65720894/177448743-c46e6102-9748-42ad-bd0f-efb3d29d6fe6.png)


그렇다면 전체 loss를 어떻게 줄이는가로 강의 내용은 넘어간다. 보통 전체 모델구조에서 일부 공유되는 weight만을 업데이트함으로써 전체 loss를 학습한다고 정의한다.  
우리는 이를 z라 두기로 한다.

물론 아래 처럼 앙상블 기법처럼 보이는 모델 하나를 전체학습 하는 방향도 있다. 좋은 방식은 아닌듯 하다.



![image](https://user-images.githubusercontent.com/65720894/177449092-cb0dd05c-7021-45d1-bc6b-7230dfeaf397.png)


아래 그림은 아까 말하였듯이 공유되는 weight를 지니어 이를 linaer 층을 통해 임베딩시킨다. 강의에서는 두 구조가 결국에는 같은 구조를 가진다고 하였는데 
이는 아래 concate된 input이 결국 각각 반개의 가중치들에 대해 행렬곱 연산을 하기 때문이다. 



![image](https://user-images.githubusercontent.com/65720894/177449623-aa85f12a-774b-414a-90c2-c4c8d7bc18f7.png)



계속해서 multi-head architecture 와 multiplicative conditioning의 방법이 있다. 
task마다 haed를 두어 학습하는 방법 - (통째로 학습하는 것 같은데 성능이 궁금하다) 그리고 일반적으로 아까의 additive방식과 비슷한데 이는 일반화적인 성능에서 
multiplicate한 방식이 더 뛰어나다고 한다.



![image](https://user-images.githubusercontent.com/65720894/177451227-0df3227e-6306-449e-81b8-0b208cf69ab0.png)



다음은 W를 학습하는 방식이다. 여기서 w는 아래 그림에서 보이다 싶이 각 task마다 중요도가 있어 이를 학습함으로써 작업의 우선도를 결정할 수 있다는 점에서
더 좋은 성능을 기대할 수 있을 것이다.

첫번째는 varius heuristics한 방식인데 weight의 기울기를 비슷하게 만들고 어떠한 한 task가 지배적이지 않게 만든다. 
두번째는 task uncertatinly를 추가하는 방식인데 더 작은 불확실성에 대해 더 높은 작업 가중치로 큰 불확실성을 가지는 task에 대해 더 낮은 작업 가중치를 가지게 한다.
세번째는 pareto optimal 한 문제를 해결하는 방식으로 가는데 이는 하나의 작업에 대해 이와 대조되게 학습되는 또 다른 작업들 사이에서 이를 최소화하는 방향으로 학습한다.
마지막으로 worst-case task loss를 해결하는 방식으로 학습이 되는데 이는 이들 가운데 가장 작업들간 공평하게 학습이 된다.


![image](https://user-images.githubusercontent.com/65720894/177453626-7f6742a7-3403-4ac4-b5f9-db081b4884f4.png)


optimizing은 아래와 같이 이루어진다.

![image](https://user-images.githubusercontent.com/65720894/177453808-74f372e9-d2ec-4fb9-b0d7-71b5d0562ea5.png)


마지막으로 multi-task learning모델의 문제점을 말하는데

첫번쨰는 nagative transfer이다 이는 멀티태스크 모델이 일반적인 모델보다 성능이 좋지 않을 수 있다는 문제인데 
이는 optimization을 바꾸거나 모델의 용량자체를 더 크게 만들어 어느정도 완화할 수 있다고한다.    
또한 task간의 공유되는 weight들을 줄임으로써 또한 완화가 될 수 있다. 



![image](https://user-images.githubusercontent.com/65720894/177455428-7030da80-0815-4f09-8819-f84dffe84cfb.png)



또한 overfitting 문제인데 
이는 공유되는 weight를 늘림으로써 해결가능하다.


![image](https://user-images.githubusercontent.com/65720894/177455664-754f447f-8100-440c-884c-1fe8ce67719a.png)
