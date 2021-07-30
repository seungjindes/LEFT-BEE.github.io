---
title : ""Training Neural Networks, Part2 / CS231n 7강 정리"
categories : "cs231"
---

## Training Nerual Networks Part2

----

### 이전강의 preview

![image](https://user-images.githubusercontent.com/65720894/127647448-1194637b-4dd0-49a4-b16f-ba61300e5529.png)

하이퍼 파라미터의 최적값을 서치하는 방법론에 대해서도 언급한다 넓은 범위에서 시작하고 싶을 때 쓰면 좋다. 몇 번씩 반복하면서 하이퍼 파라미터의
범위를 좁히는 방법을 사용한다 파라미터들마다 각각의 sensitivity가 다르다 예를들어 learning rate는 굉장히 sensitive하지만
regularization은 그렇지 않다  


----

## optimization

### SGD

![image](https://user-images.githubusercontent.com/65720894/127647709-ee66a885-6bb3-46e8-9f2a-938573158d67.png)

optimization의 목표는 최소의 loss값을 갖는 w를 찾는 것이다 SGD는 매우 간단하지만 문제를 갖는다 위 그림 처럼 W1 ,W1인 2차원으로 생각
할때 빨간점에서 최적점으로 갈 때 수평으로 이동하면 매우 느리고 수직으로 이동하면 비교적 빠르다 LOSS가 수직 방향으로만 SENSITIVE해서 동일하게 
이동하지 못하고 지그재그로 이동하게 되는 것이다 high demension일 수록 더욱 문제가 심각해져서 큰 문제이다.

![image](https://user-images.githubusercontent.com/65720894/127648313-502412d5-71be-4b4e-8f70-2673f0ec485e.png)

또한 파라미터w와 loss function의 관계함수가 구불구불해서 기울기가 0이 되는 지점이 극소값이 아닌 경우의 문제이다 이는 위처럼 2개의 문제로 
분류가 된다.

1.local minima

슬라이드 중 위에 있는 사진의 경우입니다. 극대값들 사이의 극소값(기울기가 0이 되는 지점)에 안착하게 되는 경우이다.

2. saddle point
슬라이드의 아래있는 사진의 경우입니다. 얘는 local minima 보다 더욱 골치 아픈 문제가 되는데 데이터가 더욱 고차원일수록 잘 일어나고, 기울기 0의 주변 지점의 기울기가 매우 작아져서 update도 굉장히 느려지는 문제가 발생합니다.

![image](https://user-images.githubusercontent.com/65720894/127648496-a431956e-d5d6-470e-9dfd-fbedea736a52.png)

마지막 3번째 문제점은 SGD의 S인 Stochastic이 문제를 일으킨다고 한다 미니 배치를 쓰는 SGD알고리즘은 미니배치마다 loss를 계산하여 전진해나가는데
이는 강의에서 expensive라고 하는데 비료율적이라는 의미이다 


### SGD + Momentum

![image](https://user-images.githubusercontent.com/65720894/127649154-b519c014-01e0-42f1-84bd-4b149b9472a7.png)

어느 한 점에서 특정 포인트를 찾는 과정을 물리적인 관점으로 볼 수 있다. 이는 운동량을 개입하는 시키는 것인데 위의 vx코드를 보면 rho*vx로 
step이 이루어져서 기울기가 0인 지점에서도 update가 되는 알고리즘이다.

여기서 v는 가속도를 의미하며 rho는 보통 0.9 , 0.99를 이용하여 가속도에 약간의 마찰값을 넣어주는 파라미터이다.
매 step에서 old velocity에 friction으로 감소시키고 현재의 gradient를 더해준다.

![image](https://user-images.githubusercontent.com/65720894/127649413-fe411670-1925-4203-9a8a-33052ab56c01.png)

슬라이드 공은 기울기가 0인 지점에서도 가속도로 step하기 때문에 update가 진행된다 또 하나의 장점은 tacoshell의 문제를 어느정도 해결해줍니다.

기존 SGD는 지그재그로 최적화가 되어 poor conditioning이지만, 모멘텀 + SGD는 모멘텀이 수평방향으로는 가속도를 유지하여 민감한 방향으로의 총합을 줄여주는데 gradient의 추정값들에서의 noise들을 평균화 시켜주는 역할을한다.


### Nesterov Momentum 

![image](https://user-images.githubusercontent.com/65720894/127649539-03ceecc3-dacb-4a5e-9132-1cd3129eff37.png)

쪽 그림을 먼저 보면, 실제로 이 두 벡터 방향의 average로 업데이트를 하는데 이게 바로 gradient의 추정값의 noise를 줄여주는 것이다.
오른쪽 장면에서 빨간 스타트점에서 velocity방향으로 출발한 뒤 거기서 gradident를 게산한다. 
그리고 다시 원점에서 actual step으로 최적화를 진행한다. 계산 순서의 차이가 있는데, Convex optimization에서는 잘 작동하지만, Neural Network와 같은 non-convex의 문제에서는 보장된 방식은 아니다.





