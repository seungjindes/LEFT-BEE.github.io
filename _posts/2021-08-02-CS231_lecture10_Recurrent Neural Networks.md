---
layout: post
title: "[CS231N] lecture10 review"
categories: summary
tags: summary cs231n
comments: true
---

### Recurrent Neural Networks
----

RNN에 대해서는 어렴풋이 회귀모델로만 아주조금 알고 있었을 뿐이다 지금까지 배운 네트워크는 one to one model로 하나의 
입력에 대해서 하나의 출력을 가지는 구조이다 
하지만 하나뿐 아니라 아래왜 같이 여러 입력 또는 출력을 가지는 network도 필요하다. 여기서
RNN은 이러한 것을 가능하게 해준다 RNN이란 Sequence를 따라 node사이의 연결의 형태가 방향 그래프인 인공 신경망의 한 종류이다.

![image](https://user-images.githubusercontent.com/65720894/127803593-dcc4f8cb-f4c3-425b-b53f-f205d4919e01.png)

----

### RNN의 기본 구조와 수식


![image](https://user-images.githubusercontent.com/65720894/127803653-6d569110-b919-4aee-ad68-2cb013fab1c7.png)

기본적인 rnn수식은 위와 같다. t는 시간 W는 weight값을 나타낸다. RNN은 모든 함수와 parameter값을 모든 시간에서 동일하게
사용한다 이러한 점이 RNN와 mlp의 차이라고 한다.

![image](https://user-images.githubusercontent.com/65720894/127803903-dc796365-09fc-4850-abde-ce95a6297b47.png)

위 수식은 전 hidden state의 값에 대한 W_hh와 현재 입력값에 대한 W_xh weight의 값을 각각 곱해준다 

### RNN의 활성화 함수 비교

1. sigmoid는 평균이 sigmoid는 평균이 0.5인데 비해서 tanh는 평균이 0이므로 연속적으로 같은 활성화 함수를 쓰는 RNN 입장에서 입력의 noramliaztion 입장에서 더 좋다. 
또한 값이 0일때 기울기가 1인 것이 linear model과 비슷한 효과도 있다.
2. relu는 0 미만 값에 대해서는 큰 양수 값들이 점점더 치웃쳐서 exploding 현상이 일어납니다.
3. exploding 현상은 gradient clipping 으로 gradient의 threshold값을 줘 해결 가능하다.
4. 다만 vanshing 문제는 해결이 불가능 하다.

### RNN Computational Graph

![image](https://user-images.githubusercontent.com/65720894/127806943-6bdaca2e-90b4-42ba-8275-1470880b8f0f.png)


RNN의 Computational Graph는 위와같은데 각 RNN구조를 순차적으로 쌓아올려, 여러 입력을 받아드일 수 있다.
각 hidden state에서 다른 네트워크에 들어가서 y_t을 나타낼 수 있다. 각 스텝에서 같은 가중치를 사용하는 것도 볼 수 있다.

#### many-to-many

![image](https://user-images.githubusercontent.com/65720894/127807170-ced39a6a-ed23-49e5-ba86-c64672226639.png)

![image](https://user-images.githubusercontent.com/65720894/127807689-a23016ab-fbb0-4d19-847e-e169b956fd70.png)



#### many-to-one

![image](https://user-images.githubusercontent.com/65720894/127807765-eee1f8da-f03d-4377-9d0b-146c4908adde.png)

이 모델은 감정분석 같이 여러 입력을 받고 하나의 출력을 가지는 경우 사용된다고 한다. 최종 hidden state에서만 결과값이 나온다.


#### one-to-many

![image](https://user-images.githubusercontent.com/65720894/127807842-71f9d64a-7d6b-487e-b06e-46e676c6480f.png)

#### seq2seq - many=to-one + one-to-many
![image](https://user-images.githubusercontent.com/65720894/127807897-f98e0647-91f7-4e49-b4f8-fc9c1abfdc67.png)
다음과 같이 다른 구조인 두 모델을 조합해 사용할 수 도 있다. 이를 Seq2seq라고 한다. encoder 와 decoder의 조합으로 
encoder는 영어문장과 같은 가변 입력을 받아들여 final hidden state에 전체 sentence를 요약한다.

여기서 Decoder는 이sentence를 받아 다른언어로 번역된 문장을 출력할 수 있다.
End와 같은 임의에 토큰을 지정하여 출력 길이를 조절할 수 있으며 입력과 같은 경우 0을 집어 넣는 경우도 있다.

---
### RNN 예제 Character level Language Model

아래는 하나의 문자를 입력받고 다음으로 올 문자열을 예측하는 언어 모델의 예시이다 예시로, hell을 입력받아 다음에 올 o를 출력해서 hello 라는 단어를 추측하는 모델이다 즉 앞에 맥락상 가장 올만한 
,확률이 높은 단어를 선택하는 모델이다 먼저 각 단어를 One-hot-encodeing으로 변환하여 입력으로 넣어준다.

![image](https://user-images.githubusercontent.com/65720894/127808909-e1cb4edb-982d-40e9-9e80-a32722238669.png)

![image](https://user-images.githubusercontent.com/65720894/127809061-d7bec1a0-c03e-4672-97ca-757b49c54a51.png)

학습은 다음과 같다 h값이 들어가면 다음에 나올 값이 e를 예측한다 마지막으로 우리가 원하는 4번쨰 요소인 o의 값이 가장 큰값을 
가지고 올바르게 예측된다. 순서를 외운다고 생각한다.

![image](https://user-images.githubusercontent.com/65720894/127809491-70711ecc-27d4-41f4-bec9-a0816f367138.png)

각 입력의 대한 출력을 sampling하여 다음 입력으로 넣어주는 방식도 있다. softmax를 추가하여 다른 값보다 얼마나 높은지 추정하고
다른 값들과 상호 베타적으로 비교 가능하다 이를 CrossEntropy 오차로 사용 

RNN은 앞에 hidden state를 이용하여 진행하여 계산시간과 학습 시간이 느리다 또한 rnn은 많은 메모리를 사용한다.

### RNN Truncated Backpropagation

![image](https://user-images.githubusercontent.com/65720894/127809747-bdfb6d8a-866c-478a-8f08-f3aa5b5acb63.png)

일반적으로 RNN은 모든 출력을 구하면서 학습을 진행하면 너무 느리다 이러한 문제를 해결하기 위해 배치별로 나누어서 학습을 
진행하는 방법도 사용한다. 

### RNN 예제 lmage captionaing 

![image](https://user-images.githubusercontent.com/65720894/127810003-79399d9d-6bed-430f-98be-4a5dd3f67923.png)

image captioning은 cnn에서 나오는 하나의 출력값을 rnn의 입력으로 사용하고 이 정보로 부터 문장을 만들어 내는 방법이다.

![image](https://user-images.githubusercontent.com/65720894/127810030-69c155ac-ae43-4532-b13e-2e5dbe00faef.png)

다음과 같이 VCC19모델이 있다고 했을때 softmax와 FC레이어 하나를 제거한다. 제거한 레이어 전에 나온 립력을 hidden layer의 이전 
hidden state값으로 사용한다 그리고 입력값으로 <start>토큰을 준다. 이후 입력값은 rnn의 출력값이다.
  
![image](https://user-images.githubusercontent.com/65720894/127811036-6a9a7a15-9ae6-47e6-8f03-3338910339e2.png)
  
![image](https://user-images.githubusercontent.com/65720894/127811085-7aec2a69-0c3f-417b-8042-3163efd51568.png)
마지막 출력을 <END> 토큰으로 학습 시켜 마지막 출력을 알 수 있도록 한다 이모델은 Supervised learning으로 학습시킨다

 위처럼 , 이미지를 통째로 사용하여 얻어진 요점을 언어로 변경하는 Top-Down Approach라고 한다.
  
  ### RNN 예제 Image Captioning with Attention
  
  ![image](https://user-images.githubusercontent.com/65720894/127811417-5b3aef33-c2d7-4db9-b94e-a15ecbf8d0e9.png)

  
  더 발전된 방법으로 Attention이라는 방법이 있다. 이 방법은 caption을 생성할 때 이미지의 다양한 부분을 집중해서 볼 수 있다.
  위에서 사용한 Top-Down Approach는 이미지의 디테일한 부분들에 집중하는 것이 상대적으로 어렵다는 단점을 가진다. 하지만
  이 Attention 기법을 이용하면 이미지의 모든 부분으로 부터 단어를 뽑아내어 디테일에 신경을 써줄 수 있다. 이러한 방법을 
  Bottom-Up-Approach라고 한다.
  
  
  이 Attention 기법은 아까와 다르게 CNN의 출력으로 하나의 벡터를 만드는 것이 아니라 각 벡터가 공간정보를 가지고 있는 grid
  of vector을 만들어 낸다.
![image](https://user-images.githubusercontent.com/65720894/127811547-6c9f23e2-d12c-41ad-a18f-331b0b52bd6a.png)

  grid of vector값을 입력으로 넣어서 a1값을 생성한다 이 a1의 요소로 grid of vector에 각
  depth에 곱하여 scaling해준다. 즉 a1은 주의깊게 봐야하는 부분을 feature에 적용시켜 이를 입력으로 삼는 z를 통해 
  우리가 원하는 값으 ㄹ얻는다.
  
  ![image](https://user-images.githubusercontent.com/65720894/127822731-8281f283-b666-40ca-89d4-b29ca056b87f.png)

 아래는 soft attention 과 hard attention의 차이를 보ㅓ여준다 soft attention은 0~1부터 다양한 범위 값을 부드럽게 사용한다 
  hard attention 0또는 1의 값을 사용하여 정확하게 몇 부분만을 집중해서 본다.
  ![image](https://user-images.githubusercontent.com/65720894/127823341-726f96a8-0c86-46fe-bf6e-3ef1c9b1c9ce.png)
   
  추가적으로 CNN에서 어떻게 attention할 부분을 추출하는지에 대한 공부가 필요
  
### RNN Backpropagation Through time
  RNN의 BPTT(Backpropagation Through time)에 대해서 알아봅시다. 먼저 예시로 기본적인 RNN의 구조가 아래와 같다고 가정합시다.
  ![image](https://user-images.githubusercontent.com/65720894/127823980-09ba173a-b4c8-4633-8eb7-453ab075a654.png)

 이 RNN의 BPTT은 아래와 같다 
  ![image](https://user-images.githubusercontent.com/65720894/127824047-b0998b4f-c563-4e8c-8f21-a18e2bc59318.png)
  
  ![image](https://user-images.githubusercontent.com/65720894/127824251-9b7aa231-3064-4df4-ad9b-a14e7bcf4b2b.png)
![image](https://user-images.githubusercontent.com/65720894/127824310-68d2663d-36e8-4039-91c7-8484b8894314.png)

  
  rnn의 BPTT는 h4에서 시작해서 h0까지 loss를 구하기 위해서는 W의 transpose요소를 모두 곱해야하는 비효율적인 연산이 반복
  된다.또한 이러한 곱셈과정에서 값이 1보다 크거나 1보다 작은 경우 각각 exploding 같은 문제가 발생할 수 있다. 마지막으로
  장기간에 걸쳐 패턴이 반복하면 장시간의 패턴을 학습할 수 없는 문제를 가지고 있다.
  
  ### LSTM ☆
  
  ![image](https://user-images.githubusercontent.com/65720894/127825147-3666809c-858a-43b6-b5ce-b83f21649925.png)

  
위에서 RNN이 가지고 있던 Vanishing gradients의 문제를 완하시키기 위해서 Long Short Term Memory(LSTM)구조가 등장한다.
  
 LSTM은 f,i,o,g 4개의 값이 사용된다 
  
  i -> 현재입력값을 얼마나 반영할 것인지
  f -> 이전 입력값 을 얼마나 기억할 것인지
  0 -> 현재 셀 안에서의 값을 얼마나 보여줄 것인지
  g -> input cell을 얼마나 포함 시킬지 결정하는 가중치 얼마나 학습시킬지
  
  i,f,o는 잊기 위해서는 0에 가까운 값을 기억하기 위해서는 1에 가까운 는 1에 가까운 값을 사용하기 위해서 sigmoid를 사용 하였습니다. 마치 스위치와 같은 역할을 한다고 합니다. g에서 tanh는 0~1의 크기는 강도, -1 ~ 1은 방향을 나타낸다고 생각하면 됩니다.
  
  ![image](https://user-images.githubusercontent.com/65720894/127825714-9aa528a9-1591-40d5-ac68-1a7a4110d77e.png)
![image](https://user-images.githubusercontent.com/65720894/127825731-d9753ccc-15e7-46de-9431-8a0a1964487a.png)
  
  #### LSTM의 장점
  
  forget gate의  elementwise multiplication이 matrix multiplication보다 계산적 효율성을 가진다 또한 forget gate의 값을 
  곱하여 사용하므로 항상 같은 weight값을 곱해주던 위 형태와 다르게 입력에 따라 다른 값으 ㄹ곱해주어 expliding또는
  다양한 문제를 피하는 이점을 가진다.
  
  ![image](https://user-images.githubusercontent.com/65720894/127826085-1f28d295-dfbb-4e5e-ac5b-2f61eaf2e519.png)

  
  forget gate의 sigmoid 값으로 bias를 1에 가깝게 만들어주면 vanishing gradient를 많이 약화시킬 수 있다. gradient를 구하기 위해서
  W값이 곱해지지 않아되기 때문에 마치 고속도로 처럼 gradient를 위한 빠른 처리가 가능하다 이는 이전에 배운 ResNet의
  형태와 유사한 성격을 띈다 - 연산량이 준다는 관점에서  
  
  아래 그림은 LSTM의 역전파 과정이다.
  
  ![image](https://user-images.githubusercontent.com/65720894/127826573-57ff2855-9c6e-49e8-a8cd-de3cf7ce8b3b.png)

  #### LSTM 핍홀 peephole연결
  
  ![image](https://user-images.githubusercontent.com/65720894/127826811-c2935be5-6e6e-4b85-b4fb-eb8d178a9d2c.png)

  
  핍홀 연결은 LSTM의 변종으로 기존 LSTM의 gate controller가 입력 x_t , h_t-1을 가지는데 비해 아래의 그림과 같이 
  다른 입력들도 볼 수 있도록 연결시켜주어 이전 입력 c_t-1 , c_t를 추가 시킨다 이는 더 다양한 맥락을 인식할 수 있다.
  
  ![image](https://user-images.githubusercontent.com/65720894/127826805-d1adb72d-4295-4c23-bd3e-610474c19425.png)

  cf) 참고로 RNN의 학습 최적화 방법은 skip connetion을 residual block 과 비슷하게 구현하고 dropout은 사용하지
  않는다고 한다.
  
  

  
  
  
  













