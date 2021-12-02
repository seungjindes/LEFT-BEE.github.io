---
layout: post
title: "[Linear Regression] QR분해"
subtitle: "QR분래란?"
categories: math
tags: math linearRegression
comments: true
---


### 선형대수의 이론인  QR분해에 대해 알아보자

---
## Gram-Schmidt 과정 

이는 기본적으로 이렇게 수행한다.
"Linearly independent한 벡터들이 주어졌을 때 이들을 적절히 변형해서 otrhogonal basis로 만들어주자"   
직교하는 vector set을 얻게 되면 여러가지로 편리한 점이 많지만 특히 중요한 것은 직교하지 않는 기저에 비해 직교하는 기저를 얻게 되는 얻게되면
여러 이점이 생긴다.

![image](https://user-images.githubusercontent.com/65720894/144384309-973a4da0-3bcb-4765-b71b-a74160870e72.png)

만약 선형독립인 두벡터(v1 , v2)가 2차원 실수 공간의 기저라고 해보자, 다음과 같이 표현할 수 있다.

![image](https://user-images.githubusercontent.com/65720894/144384475-a6dbff21-5eea-470c-a8cb-ed5355d85ed3.png)

그런데 이때 만약 두 벡터(w1 , w2)가 2차원 실수 공간 상의 직교하는 기저라고 한다면, 2차원 실수 공간상에 있는 임의의 벡터 v는 다음과 같이
쓸 수 있게 된다. (v1의 내적값 * w1기저벡터) , (v2의 내적값 * w2기저벡터) 

이로써 v를 v1과 v2의 선형결합으로 표현하기 위한 계수를 쉽게 얻을 수 있게 되었다.

## Gram-Schmidt 과정의 프로세스

![image](https://user-images.githubusercontent.com/65720894/144386427-aecfd4e2-9900-4ab0-9cd5-b8ec965f3ce2.png)

결국에는 위와 같은 직교벡터를 만다는 것이 목표이다.

벡터공간 V상에서 주어진 일차 독립인 벡터 a1, . . .ak를 이용하여 다음과 같이 정규 직교기저를 구할 수 있다.

![image](https://user-images.githubusercontent.com/65720894/144387562-4e53d5ad-e6a7-4bb0-acce-18775b454a47.png)

즉 u1벡터에 내적한 u2'벡터와 u2벡터의 차이는 그 정보의 손실을 담고 있는데 당여한게도 그 차이 베터 u2'-u2는 u1에 수직으로 직교하게 된다.
이러한 과정을 모든 기저벡터에 적용한다면 정규 직교 기저를 구할 수 있고 이를 각각의 벡터들의 크기(norm)으로 나누어 주면 정규직교화를 
할 수 있다. 이것이 정규 직교 기저 {q1. q2 ... qk}를 얻을 수 있다.

![image](https://user-images.githubusercontent.com/65720894/144388405-57aedd43-ee6b-45b4-9334-13f534eb6ddd.png)

그림으로 표현하면 다음과 같다. q2를 구하는 것이 그람-슈미트 과정이다.

## QR 분해

이제 QR분해는 그람-슈미트 과정을 이용해 찾아낸 정규직교기저 벡터를 이용해 행렬을 분해하는 과정이다.

그람-슈미트 과정을 통해 얻어낸 정규직교 기저 q1....qn을 모아둔 행렬을 Q라고 한다면 다음이 성립한다.

![image](https://user-images.githubusercontent.com/65720894/144388790-085839ad-3453-448a-a07d-04617fd3474e.png)

![image](https://user-images.githubusercontent.com/65720894/144391817-dca8628c-f462-490d-ace0-e80e911b4de4.png)

여기서 a1 dot q2를 생각해보면 q2는 a1 혹은 q1의 성분이 모두 제거되었기 때문에 값이 0이다.

즉 a_i dot q_j에 대해 i < j인 경우 a_i dot q_j 는 0이다.

왜냐하면 j번째 정규직교기저 q_j에서는 i<j의 성분들을 모두 다 빼버렸기 떄문이다. 따라서 아래의 식이 성립한다.

![image](https://user-images.githubusercontent.com/65720894/144392477-732cab9e-f9f7-4db4-a66a-e6af2e10abf6.png)

## 예제문제

다음 행렬을 QR분해하자

![image](https://user-images.githubusercontent.com/65720894/144394127-5253891c-cdfd-438d-8fcf-2036c0154b39.png)

행렬 A의 각 열 벡터를 a1 ,a2, a3이라고 하면 다음과 같다.

![image](https://user-images.githubusercontent.com/65720894/144394214-5898f7ca-895e-45b8-aff0-2a25cca81609.png) 

QR분해르 ㄹ하기 위해 세 벡터들에 대해 그람-슈미트를 적용하자. 정규화되지는 않고 직교화면 시킨 벡터들은 u1,u2등과 같이 쓰고,
정규직교화 된 벡터들은 e1, e2등과 같이 쓰도록하자.

우선 a1에 대해서 그람-슈미트 과정에 의해 첫번째 벡터는 그 벡터를 그대로 사용한다.

![image](https://user-images.githubusercontent.com/65720894/144394534-37f6bae0-6cdc-4797-813e-79cf00b27fc6.png)

이제 u2르 ㄹ계산해보자 u2는 a2벡터에서 u1방향으로의 성분을 제외해준 벡터이다. 다음과 같이 정리된다.

![image](https://user-images.githubusercontent.com/65720894/144395876-61eacdc9-0721-4b63-bf43-8754cb776f93.png)

u3까지 정라하면 다음과 같다.

![image](https://user-images.githubusercontent.com/65720894/144395952-11357036-b408-4d7b-a74a-fa7a1a3cca78.png)

이를 정규화 하면 

![image](https://user-images.githubusercontent.com/65720894/144396484-b8da01fa-c922-4fbf-bf6c-66ba4f7a7f39.png)

얻은 e1,e2,e3를 A= qr에서의 q1,q2,q3에 대응시키면 아래와 같이 QR분해가능하다.

![image](https://user-images.githubusercontent.com/65720894/144396656-3c7fe0aa-be48-4dbd-984d-23532e7e9364.png)






