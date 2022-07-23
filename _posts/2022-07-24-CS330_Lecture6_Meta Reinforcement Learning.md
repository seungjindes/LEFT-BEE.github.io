---
layout: post
title: "[CS330] lecture9 review"
categories: summary
tags: summary cs330
comments: true
---

## QnA

-----

![image](https://user-images.githubusercontent.com/65720894/180614496-b2eb3313-d2dd-40e5-9115-45d1af13fc1f.png)

이번 강의는 강화학습을 도메인으로 하는 메타러닝에 대해 공부한다. 먼저 강화학습의 정의에 대해 그리고 용어에 대해 설명해주고 있다.   

강화학습에서 학습은 어떠한 상황속에서 t라는 순서를 따라 진행된다. 만약 위와 같이 학습이 실제 환경에서 이루어진다고 가정하면, 
학습은 실제 환경에 영향을 받을 것이고 또한 영향을 줄 것이다. 

여기서는 state , observation, action이라는 용어를 사용하고 있다. 먼저 S는 state인데 환경에 존재하는
객체의 상태라고 볼 수 있다. 이때 t에 종속되므로 t시점의 객체의 상태라고 할 수 있다.    

이러한 객체의 속성은 실제 환경에서 수만가지가 될 수 있다. 따라서 우리는 객체의 특징중 관측할 수 있는 일부의
속성만을 가져와야한다. 이렇게 가져온 것을 우리가 t시점에서 객체를 관측한 Observation이라고한다.

가져온 obsevation을 특징으로 하여 모델을 학습할 수 있는데, 모델을 통해 나온 결과를 State에 적용시칸다.(여기서
적용 시킨다는 것은 실제 객체와의 비교, 회귀, 방향 등이 될 수 있다) 즉 이는 State에 어떠한 Action을 적용시켰다고
볼 수 있고 이를 t시점의 action이라고 정의한다. 



![image](https://user-images.githubusercontent.com/65720894/180615478-60e9d797-4109-4258-9193-0d5607cbb475.png)



위 그림을 보면 S에서 O를 추출하여 우리의 policy pi(a|o)를 구하는 것을 확인 할 수 있다. 이후 구한 a를 적용한 s_prime의 
분포를 계산하여 현재 S를 update한다. 이때 중요한점은 현재 상태는 다른 시점의 상태와 완전히 독립적인 것이다.   



![image](https://user-images.githubusercontent.com/65720894/180616668-2d659ead-3499-402e-984d-1cd5201b34d2.png)


다음과 같은 예제가 있다 로봇은 해당하는 물체를 알맞은 곳에 넣는 일종의 분류문제이다. 로봇은 상단의 카메라 이미지를 통해
observation을 얻고 이를 통해 객체를 분류하게 된다. 이러한 경우 제한적인 정보만이 있기 떄문에 학습이 어려울 수도 있다.
또한 이러한 경우 잘못 분류했을 경우 reward를 0으로 잘 분류 했을때 reward를 1로 주면서 학습하게 될 것이다. 

![image](https://user-images.githubusercontent.com/65720894/180616789-f0a1d458-f8af-4bff-81a8-b49b00783531.png)


다시 돌아와 위에서 말한 과정을 수식으로 표현하면 다음과 같이 모든 시점의 reward를 최대화 하는 방향으로 theta를 업데이트 하려고하는 것이다. 


![image](https://user-images.githubusercontent.com/65720894/180617092-f52fada8-f0d7-4585-92da-74e1e2206743.png)


강의에서는 다음과 같이 강화학습을 정의한다. state spcae , aciton space를 정의한다. 각각 loss 함수, 딥러닝 모델을
의미한다고 봐도 이해가 좋을 듯 하다. 그리고 state의 첫번째 상태 그리고 s,a 에대한 s_prime의 분포를 가진다. 마지막으로
reward또한 task 내부에서 정의된다.

