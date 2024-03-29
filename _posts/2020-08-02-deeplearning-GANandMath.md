---
layout: post
title: "[GAN] 일반적인 GAN에 대한 심화이해"
subtitle: "generative model에 대한 이해를 위해 작성한 글"
categories: deeplearning
tags: tech  
comments: true
---

## Gan의 심화이해

이전에도 공부했지만 GAN모델은 생성자와 구분자의 적대적 학습의 결과라고 할 수 있다. 우리의 목표는 real data x를 이용해 생성기의 분포 p_g를 학습시키는 것이다. 이를 위해 생성기의 input이 될 noise z~p_z(z)를 몇개 만들어 이를 생성기에 넣어 fake data G를 만든다 구분기는 input X가 realdata에서 왔을 확률 D를 구한다.

생성기 G와 구분기 D의 목표는 상반된다. 생성기는 구분기를 최대한 속이도록 학습해야하고 구분기는 이를 최대한 잘 구분할 수 있도록 학습해야 한다.

다시말하면, 구분기 D는 real data X ~ P_data(X)에 대해서는 log_D(x)를 최대화 해야하고 z~p_z(Z)에 대해서는 
log(1-D(G(z)))를 최대화 시켜야 한다. 즉 (log_D(G(z))를 최대화 시켜야한다.  

그리고 생성기 G는 log(1-D(G(z)))를 최소화 시켜야한다.

이렇게 되면, 이 문제는 G와 D에 대한 minimax game이된다.

(![sample](https://user-images.githubusercontent.com/65720894/89097844-9f4d3a00-d41d-11ea-9232-bd536be30eaa.PNG)

Global optimun은 p_g == p_data일떄 가진다. 

-------------------------

### 학습

학습은 두단계로 이루어진다.

1.생성기의 parameter를 고정시키고 realdata와 fake data를 넣어 구분기를 학습시킨다.
2.구분기의 parameter를 고정시키고 fake data를 구분기에 넣은 값을 이용해 생성기를 학습시킨다.

![1](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F999BD7405B36467918)

1.Minibatch 수 만큼 real data와 fake data를 뽑아 구분기를 학습시킨다.

![2](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99B3CF405B3646792D)

2.Minibatch 수 만큼 fakedata를 생성해 생성기를 학습시킨다. 이때 real data는 생성기에 영향을 주지 않으므로 굳이 넣지 않는다. 

### 학습과정

![학습과정](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F996C5B4F5B363E472B)

논문에 실린 그래프인데 GAN의 학습과정을 상당히 잘 보여준다, 여기서 검은 점선은 real data distribtion p_data, 초록 실선은 generatibe distribution p_g, 파란 점건은 discriminative distrinution을 나타낸다, 그리고 아래 x와 z는 생성기 G가 noise z를 data sapce의 x로 mapping 하는 것을 나타낸다.

(a): 맨 처음에는 생성기의 결과가 실제 데이터와 상당히 차이가 많이 나고 구분기 성능도 썩 좋지 않다. 

(b): G를 고정하고 D를 학습시켜 가짜 데이 구분할 수 있게 한다, 이때, D_G(x)는 p_data(x) / (p_data(x) + p_g(x)) 에 가까워진다. 

(C): D를 고정하고 D의 graduent를 이용해 G를 학습시킨다. 생성기는 더 그럴싸한 데이터를 만들 수 있게 된다.

(d): 위의 (b) , (c) 과정을 반복하게 되면 생성기는 완전한 데이터를 만들 수 있게 되고(p_g == p_data) 학습이 더이상 이루어지지 않게된다. 구분기는 이제 둘을 아예 구분할 수 없어 D(x) = 1/2가 된다.
 
 
 ---------------------------------------------------
 
 ## Conditional GANs (cGANs)
 
 좋아 분위기를 타고 Conditional GANS 까지 자세히 알아보도록 하자.  
 conditional GANs 는 학습시에 condition y도 같이 넣어줘서 conditional distribution을 학습힌다. Generation 과정에서 condition을 넣어주면 해당 label을 가질 만한 output을 내놓는다.
 
 ![1](https://user-images.githubusercontent.com/65720894/89120107-85c7f300-d4ee-11ea-8c67-ca7f6fa1a354.PNG)

가 일반적인 cGAN의 object이고, 여기에 l_1 distance를 regularizer로 활용할 수 있다. 여기서 y가 Target Domain의 Real Image이고, x가 Source Domain의 Real Image이다. 밑에식의 마지막항은 Reconstruction Loss로서, 기존의 CNN기반 학습 방법에서 사용하던 Loss이다.

![2](https://user-images.githubusercontent.com/65720894/89120125-b3ad3780-d4ee-11ea-875a-33b41673cec0.PNG)

GAN 은 실제 같은 Data를 만들어내서 Discriminator를 통해 학습이 되는 것에 초점을 맞추었고 다른 방식에 비해 확실한 선택을 하기에 결과가 매우 잘 나온다는 특징이있다. 기존의 GAN에서 의 차이점은 일반적으로 noise distribution으로 부터 Data Distribution을 뽑아내는 학습을 하게된다면 여기서는 한 image domain 으로부터 또다른 image domain로 부터의 전환을 학습하게되고 여기서부터 Discriminator를 통해서 진짜인지 아닌지를 검사받게 되는 것이다.





C_GAN의 구조는 아래 그림과 같다.

![c_gan](https://t1.daumcdn.net/cfile/tistory/21152734595761630F)

이렇게 학습을 해주면 generate할 때, 내가 원하는 것 만 골라서 generation 시킬 수가 있다. 


---------------------------------------

## pix2pix

![black](https://taeoh-kim.github.io/img/img1.PNG)

컬러영상은 누구나 쉽게 모을 수 있고, 이와 pair를 이루는 흑백 영상은 간단히 만들 수 있으므로, pair로 있는 data를 모아서 CNN을 기반으로 학습시켜서 문제에 적용시켰다 라고 볼 수 있는데, 그렇다면 pair로 되었있는 다른 Datasetdmf dldydgotj Image to Image Transelation을 할 수 있지 않을까?

![dd](https://taeoh-kim.github.io/img/img2.PNG)

이를 pix2pix라 이름붙였고 위의 예제와 같이 다양한 방면으로 활용가능하다는 것이다. 이때 Generator Network 에 U-net을 사용했다고 한다.

![u-net](https://taeoh-kim.github.io/img/img4-2.PNG)

pix2pix의 성능을 더욱 향상시키기 위해 아래 그림과 같이 encoder-decoder구조를 사용하게 되면 정보의 손실이 발생하는데 이를 Skip-Connection을 사용해서 연결해준것이 U-net구조이다. 또한 Discriminator에서 Path단위 판별을 하는 PatchGAN라는 것을 사용했다.

![color)[https://taeoh-kim.github.io/img/img7.PNG]

위는 Colorization의 결과인데, 여기에는 없지만 Pix2Pix 구조는 일반화된 문제를 풀기 위한 구조이기 때문에 사실 Colorization만 하고 싶다면 Coloful Image Colorization 논문이 더 잘 되는 것을 확인할 수 있다.

![cgan](https://taeoh-kim.github.io/img/img8-3.PNG)

위의 이미지들은 Pix2Pix의 여러 결과물들이다. 각각의 Traning Data에서 최대 3000장 정도가 사용되었으며, Architecture에서 Photo로 바꾸는 경우 400장밖에 사용하지 않았다. 또한, training 시 batch size를 1 또는 4로 작게 가져가고 4일때는 Batch Normalization을, 1일때는 Instance Normalization을 사용한다.


pix2pix 코드를 보면서 network와 학습과정을 살펴보도록 하자. 아래그림은 각각 Generator(U-net) 과 Discriminator(PathGAN)을 표시한것이다.

![code](https://taeoh-kim.github.io/img/code1.PNG)

일반적인GAN과 비슷해보이지만 역시 Generator부분에서 확실히 차이를 보인다. Training 부분(pix2pix.py)을 살펴보면 다음과 같다.

```
discriminator.zero_grad()

# Forward
real_A = to_variable(input_A)
fake_B = generator(real_A)
real_B = to_variable(input_B)

pred_fake = discriminator(real_A, fake_B) #진짜 데이터와 가짜데이터를 구분하는데 있어 구분자를 점점 똑똑하게 만들어준다 즉 데이터들끼리의 페어가 가짜인지 진짜인지 구별하는것 
pred_real = discriminator(real_A, real_B)#진짜 데이테들의 페이를 학습시킨다.

# Loss (CriterionGAN: Cross Entropy)
loss_D_fake = GAN_Loss(pred_fake, False, criterionGAN) # Fake-Fake Loss
loss_D_real = GAN_Loss(pred_real, True, criterionGAN) # Real-Real Loss
loss_D = (loss_D_fake + loss_D_real) * 0.5

# Optimize
loss_D.backward(retain_graph=True)
d_optimizer.step()

# ===================== Train G =====================#
generator.zero_grad()

# Forward
pred_fake = discriminator(real_A, fake_B)

# Loss
loss_G_GAN = GAN_Loss(pred_fake, True, criterionGAN) # Fake-Real Loss
loss_G_L1 = criterionL1(fake_B, real_B) # Recon Loss
loss_G = loss_G_GAN + loss_G_L1 * args.lambda_A

# Optimize
loss_G.backward()
g_optimizer.step()

```


각 Training Iteration 안에서 Discriminator와 Generator가 번갈아 가면서 학습하고 각각 Input에 대한 Forward Pass, Loss 구하고 Optimize라는 단순한 구성으로 되어 있다.

### 추가로 이해해야하는 점

![cgna](https://taeoh-kim.github.io/img/img4.PNG)

위 이미지에서 묻고 있는것은 두 데이터가 비슷하냐를 묻는 것이아닌 어울리는 한쌍인지를 묻고 있다. 즉 positive examples에서는 real data의 페어 이용해 구분자가 정확한 페이러를 구분할 수 있게 학습한다. 반면에 Negative examples에서는 생성자(Generator)가 만든 fake data와 real data의 페어쌍을 이용해 구분자가 페어가 거짓인지 아닌지 학습한다. 

즉 구분자(Discriminator)는 2번 학습을 하게 되있다. 이후 포스트는 pix2pix의 예제코드를 살펴보도록하겠다.






 
 
 
 
 
 

