---
layout: post
title: "[Deep Learning] Normalization"
categories: deeplearning
tags: tech 
comments: true
---



Normalization이란?  
머신러닝 또는 딥러닝에서 정규화의 목표는 값 범위의 차이를 왜곡시키지 않고 데이터 세트를 공통 스케일로 변경하는 것이다. 데이터처리에 있어 각 특성의 스케일을 조정한다는 의미로, feature scaling 이라고도 불린다. 이러한 과정을 통해 모델의 훈련을 더욱 효과적으로 할 수 있게 만들어 준다. 

본 내용에서는 다양한 normalizaion기법들에 대해서 공부해보도록 하자

### Normalizaion vs Regularization

![image](https://user-images.githubusercontent.com/65720894/132445428-a615dded-a495-476d-829c-4b6ed7a8f55b.png)


1. Normalzation

Normalizaion은 정규화라고 불린다 머신러닝 또는 딥러닝에서 정규화의 목표는 값 범위의 차이를 왜곡시키지 않고 데이터 세트를 공통
스케일로 변경하는 것이다.

2. Regularization
 
 정규화라고 한다 보통 모델의 선명도를 유지하면서 모델의 복잡도를 줄이는 방식을 말한다.


### 1-1 Batch Normalzation 

이는 Gradient Vanishing / Gradient Explding이 일어나지 않도록 하는 아이디어 중의 하나이다
- Gradient Vanishing 신경망에서 손실 함수의 gradient값이 0에 근접하여 훈려을 하기 힘들어지는 문제
- Gradient Expoding 심층 신경망 또는 RNN에서 error gradient가 축척되어 가중치 업데이터시 overflow되거나 NAN값이 되는 형상'

이러한 문제들은 Activation 함수를 ReLU로 변환 초깃값 튜닝 작은 learing rate값으로 해결했다 BatchNormalization 방법은 traing과정 자체를 안정화하여 학습속도를 가속화 하기 위해 만들어졌다.

논문에서는 이러한 학습의 불안정이 internal Convariance shift라는 문제라고 주장한다 즉 매번의 학습마다 데이터분포가 변동성이
심해 학습이 불안정하다고 보면 된다. 이는 배치단위로 볼 수 있고 레이어 그리고 하나의 task에대해서도 볼 수 잇다.

minibatch x의 평균을 u라고 할때 분산과 표준편차를 구하고 이를 정규화 시킴으로 스케일을 조정하고 분포를 조정하였다. (대신 편차의 제곱에 앱실론이 더해진 값으로 나누어졌다) 
당연하게도 앱실론은 분모에 0을 만들지 않기 위해서이다.

이러한 방법은 propagation에서 parameter의 scale영향을 받지 않으므로 learning rate를 크게 설정할 수 있다 
-> 왜냐하면 activation fuc을 지나기 전에 항상 정규화가 되므로 하이퍼파라미터와는 영향을 받지 않는다.
또한 batchnormalzation은 dropout이나 weight regularation과 같은 추가 작업을 해주지 않아도 모델이 스케일링 되기 떄문에 
학습속도가 더 빨라진다.

BN = batchnormalization이 왜 딥러닝 모델에서 효과적인지는 의견이 나뉘지만 결론적으로 어떤 방향이든 BN은 효과적이다.
그 이유로는 다음과 같은 이유가 있다.

신경망에서 가중치의 변경은 그와 연결된 layer에 영향을 미친다. 또한 가중치의 변화는 매우 복잡하고 긴밀하게 신경망에 영향을
따라서 vanishing 또는 expliding을 막기 위해 small learning rate 혹은 활성화 함수사용을 선택한다 - 특징을 흐름을 작게 만듬
batch Normalization은 최적화를 하기 쉽게 만들기 위해 네트워크를 다시 매개변수화 시킵니다 이러한 방법은 모든 레이어의 평균
크기 , 활성화 함수를 독립적으로 조정할 수 있게 만드는데 이러한 조정들은 가중치가 미치는 영향을 규제한다.   

아래 그림은 감마가 스케일링을하고 베타가 bias역할을 하는데 이 또한 backpropagation을 통해 학습을 하게 된다 즉 위 네트워크를
매개변수화 시킨다는 것은 아래의 Batch Normalization의 설명이다.

![image](https://user-images.githubusercontent.com/65720894/132445101-667b758a-cc0d-4bb8-80fb-f1b1b671228a.png)

### 학습단계의 배치 정규화

배치 정규화 이므로 평균과 분산을 구할때에서는 배치별로 계산이 되어야 의미가 있다. 이때 학습단계에서
모든 Feature에 정규화를 해주게 되면 정규화로 인하여 Feature가 동일한 Scale이 되어 learningrate결정에 유리해진다. 왜냐하면 Feature의 scale이 다르면 gradient descent를 하였을때 같은 learning rate에 대하여 가중치마다 반응하는 정도가 다르기 떄문에 다루기가 힘들다

하지만 정규화를 해 주면 gradient descent에 대한 가중치의 반응이 같아지므로 학습에 유리해진다 
또한 감마와 베타의 값또한 중요한다 만약 정규화를 해주고 activation fuc인 relu에 들어간다고 가정하면 기껏 정규화한 분포가 반절이 
사라질 것이다 따라서 감마와 베타값이 정규화 같에 곱해지고 더해져서 Relu가 적용되더라도 기존의 음수 부분이 모두 0으로 되지 않도록 방지해 주고 있다. 


### 추론단계의 배치정규화

매우 강조하는 점은 학습단계의 정규화와 추론단계의 정규화는 방식이 다르다는 점이다 추론 과정에서는 학습단계에서 사용하였던 분산과 평균을 사용하는데 이는 학습을 하였을때 최근 N개에 대한 평균값을 고정값으로 사용하는 것이다. 

### (regularization 효과)

미니배치에 들어가는 평균과 분산이 지속적으로 변화는 과정속에서 분포가 조금씩 바뀌게 되고 학습하는 과정에 weight에 뎡향을 주기 때문에 규제효과가 있다고 한다 만약 평균과 분산이 고정이라면 학습하는 동안 계속 고정된 값이연산되기 떄문에 특정 가중치에 큰 값이 발생 할 수 있다. 하지만 batch Normalization에서 평균과 분산이 지속적으로 변하고 weight 업데이트에도 계속 영향을 주어 한 weight에 가중치가 
크게 편향되지 않으므로 Regularization effect를 얻을 수 있다. 

이와 같은 효과를 얻기 위해 적용하는 dropout을 batchNormalzation을 통하여 얻을 수 있기 때문에 이론적으로는 안써도 된다고 하지만
때에 따라 더 좋은 효과를 보이기도 한다.


### Convolution Layer와 Batch Normalization

![image](https://user-images.githubusercontent.com/65720894/132447585-58904ec9-239f-4637-afa5-960f107e2b83.png)

왼쪽이 fully connected layer에 적용된 batch Noarmalzation이고 오른쪽은 Convolution layer에 적용된 Batch Normalization이다
Fully connected layer에서는 Normalization되는 대상이 뉴런별로 정규화가 된다 반면 Convolution layer에서는 채널별로 정규화가 된다.
즉, Batch , Height , Width 에대해 평균과 분산을 구한다. 

#### bn의 문제점

normalization에서 데이터를 표준화시키는 가장 좋은 방법은 전체 데이터에 대해 평균과 분산을 사용하는 것이다 하지만 각계층에 대해 계속 연산을 한다면 너무 많은 자원을
사용한다 그렇기에 다른 효율적인 방법으로 우리가 구하고 싶은 평균과 분산에 근사한 값을 찾아야 한다.

bn에서는  mini-batch의 평균과 분산을 사용한다 약간의 오차는 있겠지만 나쁘지 않은 추정 방법이다 하지만 이런 방법은 문제가 발생한다

1. batch-size가 작은경우: 당연하게도 데이터가적어 batch가 작다면 평균과 분산의 오차가 클것이다 

2. rnn에서는 각 단계마다 서로 다른 통계치를 가진다 이는 즉 매 단계마다 레이어에 별도의 bn을 적용해야한다 이는 모델을 더 복잡하게 만들어 계속 새롭게 형성된 통계치를 저장해야한다는 접이다
이 상황에서 BN은 매우 비효율적인 방법이다


### Pytorch에서의 사용 방법

Pytorch에서 BatchNormalization을 사용하는 대표적인 방법은 torch.nn.BatchNormid와 torch.nn.BatchNorm2d를 사용하는 것이다 두가지 방법 모두 아래식을 따른다 특히 감마와 베타는 학습되는 파라미터이며 감마는 1 베타는 0을 초기값으로 학습을 시작한다 

이 때, 차이점은 BatchNorm1d의 경우 Input과 Output이 (N, C) 또는 (N, C, L)의 형태를 가지고 BatchNorm2d의 경우 Input과 Output이 (N, C, H, W)의 형태를 가진다. 여기서 N은 Batch의 크기를 말하고 C는 Channel을 말한다. BatchNorm1d에서의 L은 Length을 뜻하고 BatchNorm2d에서의 H와 W 각각은 height와 width를 뜻한다 -batch norm은 체널별로 정규화가 되므로 

```
# With Learnable Parameters
m = nn.BatchNorm1d(100)
input = torch.randn(20, 100)
output = m(input)

# With Learnable Parameters
m = nn.BatchNorm2d(100)
input = torch.randn(20, 100, 35, 45)
output = m(input)
```

### 여러가지 정규화 방법


![image](https://user-images.githubusercontent.com/65720894/132448559-6c5ca929-0e8c-43e5-9c7c-e0ca46bc831a.png)

### Instance Normalization

여러가지 정규화 방법이 있지만 Instance normalizaiton부터 보도록 하겠다 그전에 LN Layer Normalization은 BN의 batch단위로 정규화를 했던것에 비해 
featur차원에서 정규화 라는 차이가 있다 각특성에 대하여 따로 계산이 이뤄지며 각 특성에 독립적으로 게산한다. 이는 RNN에서 매우 좋은 성능을 보여준다

IN은 LN과 유사하지만 한 단계 더 나아가 평균과 표준 편차를 구하여 각 example의 각 채널에 정규화를 진행한다 style transfer을 위해 고안된 Instance Normalization은 network가 원본 이미지와 변형된 이미지가 불가지론(구분할 수 없음)을 가지길 바라며 설계되었습니다. 따라서 이미지에 국한된 정규화이며, RNN에서는 사용할 수 없습니다.

style transfer 또는 GAN에서 BN을 대체하여 사용하며, real-time generation에 효과적입니다.
