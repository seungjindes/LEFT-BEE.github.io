---
layout: post
title: "[Machine Learning] Random Forest"
categories: deeplearning
tags: tech machinelearning
comments: true
---

Randomforest는 ensemble(양상블) machine learning 모델이다. 여러개의 decision tree를 형성하고 새로운 데이터 포인트를 각 트리에 동시에 통과 시키며
각 트리가 분류한 결과에서 민주적이게도? 투표를 실시하여 가장 많이 득표한 결과가 최종 분류결과로 결정된다. 랜덤포레스트가 생성한
일부 트리는 과적합 될 수 있지만 많은 수의 트리를 생성함으로써 과적합이 예측하는데 있어 큰 영향을 미치지 못하도록 예방한다- 큰 수의 법칙?   

-------------------------------------------


Decision Tree는 과적합될 가능성이 높은 약점*이있다. 이러한 약점을 보안하기 위해 많은 알고리즘이 생겨났으며 랜덤포레스트는 그 중 하나이다. 이후
adaboost , lgbm등 많은 알고리즘을 다루고 싶다. 어쨋든 가지치기를 통해 트리의 최대높이를 설정 해줄 수 있지만 충분한 효과를 보기는 어렵다.

Random forest는 앙상블모델인데 앙상블이란 여러모델이 힘을 합쳐 성능을 향상시키는 기법이다. 
![rdf](https://miro.medium.com/max/1638/1*9Cw6GyeSKOpWqnT91lygdA.png)

## Bagging 

랜덤포레스트는 제일먼저 bagging 이라는 과정을 거친다 bagging은 트리를 만들 때 training set의 부분집합을 활용하여 형성하는 것을 의미한다 예를들어
training set에 1000개의 데이터가 존재한다 가정하면 각 트리를 생성할 때 100개의 데이터만 임의로 선택하여 트리를 만드는데 활용 할 수 있다는 것이다
즉 모든 트리는 각기 다른 데이터를 바탕으로 형성되지만 모두 training set의 부분집합이다.

![bag](https://miro.medium.com/max/1678/1*Wf91XObaX2zwow7mMwDmGw.png)

이렇게 100개의 데이터를 sampling할떄 중요한 점은 중복샘플링을 한다는 것이다 이렇게 중복을 허용함으로써 1000개의 trainingset에서 100개만 뽑기보다
1000개씩 매번 뽑아도 unique한 데이터셋을 형성할 수 있다.

### Bagging features

랜덤포레스트는 트리를 형성할때 dataset에만 변화를 주는 것이 아닌 feature를 선택하는데 있어도 변화를 줄 수 있다. feature를 선택할때도 기존에 
존재하는 feature의 부분집합을 이용한다 예를 들어 집값을 예시로 들어보자 아래는 집값이 책정되는 특징들이다. 집값의 label은 (very high , high,
normal , low)라 하자.

1. 집넓이
2. 방의 개수
3. 아파트 층수
4. 역세권
5. 보안 시설 평가지수
등등

이때 집넓이가 label를 결정하는데 있어 가장 큰 영향력을 가지고 있다 가정하자 그렇다면 아무리 많은 모델을 만들어도 결국에는 집넓이라는 변수하나가 모델들을
비슷하게 만들어 버린다(사실 너무크면 그 특징만으로 학습하면 되지만 그렇지 않다는 가정하에...) 따라서 특징또한 랜덤으로 선택하게된다. 즉 하나의 모델에서는
1 ,2 ,3 의 특징으로 가지고 모델이 학습하고 다른 모델에서는 3,4,5의 특징을 가지고 학습한다는 것이다. 

## classify
여러개의 트리를 형성하였다면 이제 분류를 수행한다. 예를들어 8개의 트리를 형성하고 집값데이터를 각 트리에 전달하였을 때 나온결과가 
veryhigh가 1표 high가 5표 normal이 1표라면 집값은 high로 구분될 것이다.





