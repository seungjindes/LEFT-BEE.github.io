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

![image](https://user-images.githubusercontent.com/65720894/180629114-d3e6905b-54e0-4a4e-b33a-7f414e58d8b8.png)

이후는 강화학습의 multi-task에 대해서 소개한다. 우리는 S를 다시 S = (S_orgin , condition = Z_i) 와 같이 정의한다. 
이를 통해 S가 특정한 task라는 것을 조건을 주게된다. 이때 Condition은 ont-hot-encoding일 수도 있고 laguage discription
일 수도 있다. 

보상측면에서는 이전과 같이 보상을 최대화 하는 방향으로 학습, 그리고 distance(S , S_g)를 최소화 하는 것과 같다.

![image](https://user-images.githubusercontent.com/65720894/180629854-dd6034ff-4759-4952-b3be-6b20644a78fa.png)

이전에 도식을 다시 정리하면 다음과 같이 표현가능하다. 알고리즘은 3단계로 표현하며 각 gradient , Q-learnig, model-based 하게 설명하고있다.   
이때 policy는 정책으로 우리가 최적화 해야하는 것이다. 

이번 강의에서는 policy gradient한 방식과 Q-learnig의 방식에 집중하고 이후 많은 강의에서 model-based한 방법에 대해 다룬다고 한다.  


---- 

### policy gradient 

![image](https://user-images.githubusercontent.com/65720894/180630333-0f93dafe-f404-4c56-8a2c-7f69c775a72e.png)

policy에 대해 데이터가 무조건 policy의 흐름을 따라야 하는 것인지 아니면 그럴 필요가 없는 작업인지 나뉜다. om-policy 는 대부분의 rl 학습에 적용되는 알고리즘이고
이는 이전에 사용하였던 데이터는 다시 쓸 수 없는 방식이다 하지만 off-policy의 경우 어떤 시점이던 다시 사용하던, 심지어 다른 policy의 정보라도 사용한다 이는 
특수한 task에서 사용된다고 한다.

![image](https://user-images.githubusercontent.com/65720894/180630439-335d7ab7-78c6-44c9-b0c6-b87a7d33ce5e.png)

오른쪽 그림을 보면 화살표 하나가 policy라고 생각하면 좋을 것 같다 (아닐 수도 있다..)

위 수식을 보면 p_theta(tau)가 있다. tau는 위필기 처럼 최대 우도 추정에 의해 각 시점에서 pi라는 policy를 따르는 분포를 의미한다. 따라서 이러한 분포를 따르면서도,
보상을 최대화 하는 방향으로 업데이트 해야만 하고 이때 손실함수를 다음과 같이 정의할 수 있다. 

우리는 pi policy의 모든 값을 가져올 수 없으므로 샘플링을 통해 평균을 낸다. 

![image](https://user-images.githubusercontent.com/65720894/180630830-08899b28-8a42-403e-b41c-9493dc6c6a74.png)

이후 증명을 통해 최종적으로 기울기가 다음과 같이 수식이 정의됨을 강의에서는 보인다. 우리는 이를 통해 theta를 update할 것이다
supervised learning과 매우 유사한것 같다. 여기서 중요한 점은 pi_theta(a_t | s_t)에서 샘플 i개를 뽑아서 이에대해 수식을 적용하는데 있는 것 같다.



![image](https://user-images.githubusercontent.com/65720894/180631582-0b296148-dddb-4066-be28-1690db81ce0e.png)


일반적인 ML 문제의 최대 우도 솔루션과 RL에서의 gradient 솔류션을 비교하고 있다. 보이는 것처럼 매우 유사한 구조를 가진다. 하지만 RL learning에서는 reward라는 
변수가 존재하여 만약 reward가 0에 가까울 시 해당 policy는 update를 하지 못하고 1에 가까울 수록 그 방향으로 학습하게 될 것이다. 즉, 궁극적으로 보상을 최대한 
많이 받게 되는 pi 분포를 찾는 것이 목표가 될 것이다. 

![image](https://user-images.githubusercontent.com/65720894/180631672-2a314be4-e52d-40db-9312-0bb7efc27c4e.png)


Policy Gradient 방식을 정리하고 있다.    

이는 매우 간단하고 다른 메타 러닝 알고리즘, 그리고 멀티테스크에 쉽게 적용할 수 있다는 장점이 있다    

하지만 기울기가 high-variance 하게 제공되기 때문에 학습하기가 어렵고 오직 on-policy의 데이터만을 사용해야 하므로
기울기를 예측할때 다시 데이터를 사용할 수 없다는 점에서 효율성이 낮다.

---

### Value-Based RL 

![image](https://user-images.githubusercontent.com/65720894/180632158-2da05711-da70-4e0a-a589-463e4004353c.png)

또다른 방법은 동적할당법으 사용하여 순차적으로 최대의 value를 얻는 방법이 있다.

여기서는 두가지 함수를 소개하는데 하나는 state만을 인자로 받아 보상을 추정하는 vlaye function 그리고
state-action pair가 얼마나 좋은 결과를 보이는지에 대해 Q-function가 그것이다.    


이 강의에서는 Q function에 대해 주로 소개하고 특정 policy pi_star에 대해 학습은 Q_star를 찾는 것이라고 마한다.
Q_star는 Q_1 부터 시작하여 모든 경로에서 얻은 값의 평균으로 최종 보상값? 정도로 보면 좋을 것같다. 

![image](https://user-images.githubusercontent.com/65720894/180632311-ee40f100-7ba5-410a-ba5e-6760f915c79e.png)



이를 설명하기 위해 다음과 같은 예시를 든다. 드럼을 배우고 싶은 사람의 경로 3가지를 샘플링 할 수 있을 것이다. 

이떄 value function은 아무 연습도 하지 않았으므로 0이 된다. 구리고 Q function에서 a_t가 1일 a1일경우 0 , a3일 경우 한 0.3정도 될 것이다.   
그리고 Q_star의 경우 이러한 경로의 총합이므로 at = a3 일경우 거의 1에 수렴할 것이다. 그리고 이떄의 V_star는 연습을 열심히 했으므로 1이 될 것이다. 

![image](https://user-images.githubusercontent.com/65720894/180632706-de0bf88c-8d23-469b-b855-28681f6de26d.png)

알고리즘은 다음과 같다. 우리는 gradient descent방식이 아닌 방법으로 Q value를 최대화 하는 방향으로 학습한다 이때 어떤 policy를 추출하여
데이터 셋을 추출하고 y를 set하는데 Q값이 큰값이 되는 action을 찾아낸다. Q가 최대화 하는 방향은 r이 곧 최대화 하는 방향과 같다. 이는 
3번째 순서에서 명확하게 나오는데 Q-fuction을 우리의 target 즉 y에 맞추기 위해 두 거리를 최소화 하는 방향으로 pi를 set한다. 

결론적으로 우리는 Q-fuction을 통해 policy pi(a|s)를 얻을 수 있다. 



![image](https://user-images.githubusercontent.com/65720894/180633200-266ac236-7f3b-43b4-875e-a77d96665142.png)


위 그림과 같은 예시를 든다 . 우리는 처음 normal한 distributuin을 가지고 있으며 이중에서 왼쪽 그림과 같이 어떠한 policy를 통해 data를 sampling 한다.
그리고 이중에서 Q가 최대가 되는 데이터만을 가지와 이를 선택한다. 이후 선택된 데이터에 맞추어 pi를 수정하여 다음과 같이 원이 변형된 거을 확인 가능하다.



![image](https://user-images.githubusercontent.com/65720894/180633310-ff61a6b8-b2e0-4b05-950f-7d7fe5572a84.png)


이렇게 학습하는 방법은 smaple efficient하다. 즉 데이터를 넓게 사용할 수 있는 것이다. 또한 이는 reward가 없더라도 update가 될 수 있고 , 상대적으로
다른 병렬적으로 학습할 수 있다. 

하지만 다른 메타 알고리즘에 적용하기 어렵다는 단점이 있다.

![image](https://user-images.githubusercontent.com/65720894/180633372-578b97aa-10b1-4bd0-b2c5-87e8638e3df5.png)

강화 학습의 multi-task를 정의한다. 

![image](https://user-images.githubusercontent.com/65720894/180633470-c40ee7e2-b9d1-4fdc-bcbe-04473a4c7f5a.png)

task 에대해 condition을 준다면 해결 될 것 같지만 축구를 예로 들자면 패스를 하려했던 것이 슛으로 들어가 reward를 받는 경우도 존재한다. 이를 반영하기 위해 경험을 통해
데이터를 만나고 이 데이터를 labeling해주는 기법을 사용한다 이를 사후레이블링이라고 한다 

또한 이전에 배운 gradient 방식과 비교하여 gradient방식은 rward가 0일 시 그 정보를 아애 배재해 버리지만 Q fucntoin은 하지 말아야할 행동에 대해 더욱 강경하게 하지
말아야한다고 주의를 줄 수 있다. 




















