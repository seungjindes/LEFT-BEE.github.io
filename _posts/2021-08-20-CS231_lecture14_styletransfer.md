---
layout: post
title: "[CS231N] lecture14 review"
categories: summary
tags: summary cs231n
comments: true
---
### style transfer

####  동영상 강의자료가 없으므로 논문리뷰로 대체한다. 모든 자료는 youtube에 나동빈님의 자료를 이용하였습니다.

-------------

![image](https://user-images.githubusercontent.com/65720894/130194889-332d890d-7b4e-4f12-8633-0b9c44eaddc7.png)


style transfer는 한 이미지의 특징분포를 학습하여 다른 이미지에 적용하는 것이라 알고있는 기술이다 이번에 가지고 온 논문은 style transfer에서 처음으로
딥러닝 그리고 convolutional한 기술을 적용한 사례라 할수 있다 본 논문이후 딥러닝을 이용해 styletransfer를 적용하는 논문들이 많이 시작되었다고 한다.

![image](https://user-images.githubusercontent.com/65720894/130195163-88910141-df51-45a9-9f41-a57f674448ef.png)

스타일 전송을 위해 사전학습된 모델을 사용하는데 이떄 모델은 분류모델을 주로 사용한다. 그 모델의 가중치는 고정한 뒤에 이미지를 업데이트하는
방법을 사용한다. 따라서 손실함수에 따라 이미지 자체가 최적화 된다. 몰론 이때 노이즈가 따라가는 모습은 target 이미지가 되겠고 이를 최적화 시킬
손실함수는 이후에 등장한다.

![image](https://user-images.githubusercontent.com/65720894/130195976-ec00ff7b-9fbb-470c-9f25-3e34711e9f56.png)

본 내용에서 이미지를 벡터로 표현한 이유는 좌측상단에서부터 오른쪽 끝까지 일렬로 나열하여 벡터로 만들었다고 보면된다.  
그리고 loss를 content loss와 style loss이 두개 줄이는 방식을 사용함으로써 만들어지는 이미지는 style transfer가 완성된다.

![image](https://user-images.githubusercontent.com/65720894/130196687-3186160e-849f-4d57-a0de-fad604db444e.png)

CNN 필터를 다시한번 살펴보면 앞쪽에서 얻은 activation map은 크기가 크고 channel size가 작은 것을 확인 할 수 있다-> 아마
커다란 특징을 잡아낼 확률이 높을 것이다 반대로 뒤에 layer에서 추출한 activation map은 작고 channel의 갯수가 많다 이는 반대로
디 테일한 특징을 잡을 확률이 높을 것이다. 어찌됬는 이러한 activation map은 서로다른 filter map에의하여 추출된 channel size만큼의
특징들이라고 할 수 있다.

![image](https://user-images.githubusercontent.com/65720894/130197135-66038161-555d-4dd5-96d2-956f5bb39ae1.png)

위의 loss는 content loss를 정의한 부분이다. 두 이미지의 특징의 activation값이 동일하도록 만든다
이때 noise와 content image는 둘다 같은 네트워크를 사용한다 당연하게도 featuremap을 비교할 떄도 같은 레이어의 특징들을 비교한다
위식에서 i는 activation map들의 체널 l은 해달 레이어 그리고 h는 실제 feature map에서의 각 위치를 말하는 것이다 
(연산이 오래걸릴거 같은데.... 뭐 그래봤자 featuremap의 size는 작으므로 상관없을 것이다)

![image](https://user-images.githubusercontent.com/65720894/130198254-b61ac634-f549-48d1-bfe9-f84c2c6ea72e.png)

다음은 style loss를 정의한다 style은 서로 다른 특징간의 상관관계를 의미한다 즉 스타일이 비슷하다는 것은 다시말해
각 이미지의 서로다른 특징간의 상관관계=(feature map)이 유사하다를 의미한다. 이를 정의하기 위해 gram matrix를 생성하는데
각 행렬의 원소는 특징 i 와 j의 내적값이라고 할 수 있다 = 각각의 위치에대한 곱한것을 더한것 이렇게 만들어진 gram matrix는
당연하게도 channel size의 정방행렬이다.또한 이러한 특징간에 상관관계를 나타난 행렬이므로 이 자체를 스타일이라 정의
할 수 있는 것이다.

![image](https://user-images.githubusercontent.com/65720894/130199594-f793bdca-7b0d-440b-b62d-fd1031ede565.png)

noise의 네트워크에서 나온 gram matrix인 G와 target image의 네트워크의 gram matrix A를 서로 비교하여 그 값의 차이가 감소할 수 있도록 업데이트한다. style Loss의 W는 가중치를 두어 레이어에 따라 얼마나 중요도를 줄지 결정 할 수 있다. 아마 cnn의
구조상 깊이가 깊어질수록 디테일한 특징을 잡아내기 때문에 만약 이미지의 스타일을 디테일하게 적용하고 싶으면 가중치를
조절함으로써 해결할 수 있을 것이다.   

아까의 content loss와 조금 다른점은 content loss가 하나의 layer에 있어(예를들어 conv4와 같은 단일층) 그 손실값을 구하지만 style loss는 1~3개의 
layer만 loss를 추출하는데 사용한다. 그리고 특정한 값을 나눠주는(4M^2N^2)은 너무 큰값이 나오지 않게 스케일링한 과정이라고
보면된다.  

![image](https://user-images.githubusercontent.com/65720894/130399561-0a77d81b-361a-4a75-9c97-054c2d020449.png)

 
본 내용은 원래 style loss와 content loss를 혼합시켜 style transfer을 구현하지만 그 loss를 각각 따로 적용시켜본 것이다
content의 경우에서 깊어질수록 디테일한 픽셀의 정보는 사라진다는 것을 알 수 있다.

![image](https://user-images.githubusercontent.com/65720894/130400150-c8096cdc-9a99-4481-b40a-86ca15129cd3.png)

지금까지의 과정을 하나의 구조로 나타낸 것이다. 즉 styletransfer는 두가지의 loss를 사용하는데 style loss와 content loss가
그것이다 content loss는 하나의 층에서 그 loss를 구할 수 있는데 이는 단순하게 특징맵을 비교함으로써 구할 수 있다.
style loss는 activation map들의 gram matrix를 생성해 이를 합성곱하여 스타일을 만들어 낸다 이렇게 만든 스타일을
비교함으로써 손실을 정의하는데 이는 여러개의 layer에서 이루어져 이를 합친 것을 loss라 할 수 있다. 두가지의 loss를
줄임으로써 styletransfer를 수행할 수있다.












