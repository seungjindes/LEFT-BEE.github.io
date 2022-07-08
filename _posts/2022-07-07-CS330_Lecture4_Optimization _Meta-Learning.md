---
layout: post
title: "[CS330] lecture4 review"
categories: summary
tags: summary cs330
comments: true
---

## Question 
pi* D_i test 를 통해 optimize한 결과가 맞는지?
MAML 요약 한게 맞는지?
update는 batch 마다 이루어지는지 아니면 task마다 이루어지는 것이 맞는지?
theta를 업데이트 할때 사용되는 loss함수는 벡터의 유사도를 비교함으로써 loss를 구하는건지?
![image](https://user-images.githubusercontent.com/65720894/177984373-f0506385-9b80-41e1-ab50-20dd7b21f865.png)



## Add Point
- maml gradient 연산을 할때 full - hesse(헤시안) 행렬을 구하지 않고도 일부분을 구해 계산하는 기술이 존재한다
- 


## Optimization Meta-Learning

black-box adaptation에서 pi를 구하여 이를 통해 test와 함께 loss를 구하였다. 이번 강의에서는 optimization을 최적화하여 어떤 task에서도 빠르게 학습될 수 있는 파라미터를 찾으려고한다.

![image](https://user-images.githubusercontent.com/65720894/177952796-59a01445-6560-4cc7-a1a5-90ca01f80e44.png)

### MAML

maml을 이해하기 위한 간단한 표식이다. 
결론적으로 theta는 task1, task2, task3 전부에 대하여 가장 빨리 학습할 수 있는 theta값을 찾는 것이 목표이다. 아래 그림을 보면 theta 가 task에 의해 
L1, L2, L3로 optimizing 된다 이 optimize 된 결과를 pi라고 할때 theta는 이 pi와 이후 update를 위해 test set 에서 optimize한 결과인 pi* 의 차이를 최소화하는 것을
목표로 삼는다. 결구 pi = pi* 가 되어야지만, 즉 모든 test task에 대해 최소한의 optimize를 통해 학습되는 theta를 찾는 방향으로 움직이게 되고

아래 그림의 빨간색 동그라미가 그 지점인 것이다. 현재 task마다 theta가 update 되는 것인지 아니면 

![image](https://user-images.githubusercontent.com/65720894/177984362-14a5da61-cd57-486e-a728-46615f41418f.png)

