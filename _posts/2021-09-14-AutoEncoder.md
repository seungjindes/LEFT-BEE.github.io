---
title : "AutoEncoder의 이해와 manifold"
categories : "study"
--- 

이전에 읽은 EbGAN은 generator와 discriminator의 관계를 에너지 역학적 관계로 표현하여 성능을 끌어올렸다 이때 사용된 개념이
autoenocer이다. 

------------

### atuo encoder
autoEncoer는 저차원의 representation z를 원본 x로부터 구하여 스스로 네트워크를 학습하는 방법을 의미한다 이것은
Unsupervised learning 방법으로 레이블이 필요 없이 원본 데이터를 레이블로 활용한다.

AE의 주목적은 encoder를 잘 학습하여 유용한 feature extractor로 사용하는 것이다 현재는 잘 사용하지 않지만 기본 구조를 
응용하여 다양한 딥러닝 네트워크에 적용중에 있다 ex) VAE , U-NET , Stacked Hourglass

![image](https://user-images.githubusercontent.com/65720894/133219676-762224ba-5b72-4324-ac45-086ec3de550f.png)

AE의 전체적인 구조는 위 아키텍쳐와 같다 입력 x와 출력 y가 동일한 값을 가지도록 하는 것이 AE의 목적이며 입력의 정보를 압축하느 ㄴ구조를
Encoder라 하고 압축된 정보를 복원하는 구조를 Ecoder그리고 그 사이의 variable을 latent variable Z라고 한다.


![image](https://user-images.githubusercontent.com/65720894/133220268-4f87fa71-3007-4a72-83b7-f05b88053995.png)

AE에서 저차원 표현 Z는 위그림과 같이 원본 데이터의 함축적인 의미를 가지도록 학습이 도니다 물론 어떤 feature를 추출할지는
명확하게 결정할 수 없다 

![image](https://user-images.githubusercontent.com/65720894/133220537-ee05d267-0ed9-4899-9c6d-17032eb9f5a3.png)

E의 Latent Variable은 입력을 압축하여 Feature를 만들기 때문에, Feature Extractor의 역할을 할 수 있다. 따라서 Encoder는
featur Extracter로써 사용하여 다른 머신러닝 classfier로 사용할 수 있게된다. 


### VAE

이러한 AE로 만들 수 있는 구소가 VAE이다 
![image](https://user-images.githubusercontent.com/65720894/133221188-5a84372d-9041-49a0-ab8a-6f7bbabbb17d.png)

vae의 전체 아키텍쳐를 보면 인풋으로 데이터를 받은 뒤 encoder부분에서는 latent variabe을 생성하도록 학습한다
이때 AE와 다르게 latent varuable로 파라미터를 생성한다 이는 가우시안 노이즈와 곱해지고 더해져서 decoder의 
input으로 전달이 된다.


![image](https://user-images.githubusercontent.com/65720894/133221430-dfe4cbfd-0379-4838-a63e-d2d479250d7a.png)

AE와 VAE의 핵심적인 차이점은 latent variable에 대한 표현 방식이다 VAE에서는 latent variable의 distribution을 정규분포 형태로
나타내고 그 분포에서 샘플링을 한다. 중요한 점은 VAE또한 딥러닝 네트워크이기에 
오른쪽과 같이 이분가능한 형태의 아키텍쳐를 구축해야한다는 점이다

![image](https://user-images.githubusercontent.com/65720894/133222955-024f531c-c002-4b33-a359-e97f96d55364.png)


앞에서 설명한 것을 종합하면 학습단계에서의 VAE에서는 위 그림과 같이 Enocder Decoder 그리고 Latent Variable Distrubution을 이용하여
네트워크를 구성하고 네트워크의 입력과 출력이 Recontruction이 잘되도록 학습을 한다 

추론단계에서는 Encoder부분을 떄어내고 latent variable에서 샘플링을 한 후 Decoder를 통해 출력을 하면 새로운 이미지를 출력
할 수 있다.

https://gaussian37.github.io/assets/img/dl/concept/vae/19.png





