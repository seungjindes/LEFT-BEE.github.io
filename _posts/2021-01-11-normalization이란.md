Normalization이란?  
머신러닝 또는 딥러닝에서 정규화의 목표는 값 범위의 차이를 왜곡시키지 않고 데이터 세트를 공통 스케일로 변경하는 것이다. 데이터처리에 있어 각 특성의 스케일을 조정한다는
의미로, feature scaling 이라고도 불린다.

### 1-1 Batch Normalzation 

이는 Gradient Vanishing / Gradient Explding이 일어나지 않도록 하는 아이디어 중의 하나이다
- Gradient Vanishing 신경망에서 손실 함수의 gradient값이 0에 근접하여 훈려을 하기 힘들어지는 문제
- Gradient Expoding 심층 신경망 또는 RNN에서 error gradient가 축척되어 가중치 업데이터시 overflow되거나 NAN값이 되는 형상

이러한 문제들은 Activation 함수를 ReLU로 변환 초깃값 튜닝 작은 learing rate값으로 해결했다 BatchNormalization 방법은 traing과정 자체를 안정화하여
학습 속도를 가속화 하기 위해 만들어졌다.

minibatch x의 평균을 u라고 할때 그냥 정규화를 하였다 (대신 편차의 제곱에 앱실론이 더해진 값으로 나누어졌다)  

이러한 방법은 propagation에서 parameter의 scale영향을 받지 않으므로 learning rate를 크게 설정할 수 있다

BN = batchnormalization이 왜 딥러닝 모델에서 효과적인지는 의견이 나뉘지만 결론적으로 어떤 방향이든 BN은 효과적이다.
1. loss surface를 보다 쉽게 찾을 수 있고  
2. 최적화를 쉽게 만들며  
3. 여러 작업에서 모델 성능을 향상시킨다.

#### bn의 문제점

normalization에서 데이터를 표준화시키는 가장 좋은 방법은 전체 데이터에 대해 평균과 분산을 사용하는 것이다 하지만 각계층에 대해 계속 연산을 한다면 너무 많은 자원을
사용한다 그렇기에 다른 효율적인 방법으로 우리가 구하고 싶은 평균과 분산에 근사한 값을 찾아야 한다.

bn에서는  mini-batch의 평균과 분산을 사용한다 약간의 오차는 있겠지만 나쁘지 않은 추정 방법이다 하지만 이런 방법은 문제가 발생한다

1. batch-size가 작은경우: 당연하게도 데이터가적어 batch가 작다면 평균과 분산의 오차가 클것이다 

2. rnn에서는 각 단계마다 서로 다른 통계치를 가진다 이는 즉 매 단계마다 레이어에 별도의 bn을 적용해야한다 이는 모델을 더 복잡하게 만들어 계속 새롭게 형성된 통계치를 저장해야한다는 접이다
이 상황에서 BN은 매우 비효율적인 방법이다



### 여러가지 정규화 방법

### Instance Normalization

여러가지 정규화 방법이 있지만 Instance normalizaiton부터 보도록 하겠다 그전에 LN Layer Normalization은 BN의 batch단위로 정규화를 했던것에 비해 
featur차원에서 정규화 라는 차이가 있다 각특성에 대하여 따로 계산이 이뤄지며 각 특성에 독립적으로 게산한다. 이는 RNN에서 매우 좋은 성능을 보여준다

IN은 LN과 유사하지만 한 단계 더 나아가 평균과 표준 편차를 구하여 각 example의 각 채널에 정규화를 진행한다 style transfer을 위해 고안된 Instance Normalization은 network가 원본 이미지와 변형된 이미지가 불가지론(구분할 수 없음)을 가지길 바라며 설계되었습니다. 따라서 이미지에 국한된 정규화이며, RNN에서는 사용할 수 없습니다.

style transfer 또는 GAN에서 BN을 대체하여 사용하며, real-time generation에 효과적입니다.
