---
layout: post
title: "[Model Review] Yolo Review"
categories: deeplearning
tags: paper model
comments: true
---

## Abstract

- Yolo 연구진은 객체 인식을 하나의 파이프라인으로 연결하여 end-to-end 방식으로 문제를 해결하였다.
- YOLO는 이미지에 대해서 하나의 회귀문제로 정의하였다.
- YOLO 모델은 굉장히 빠르다는 특징을 가지고 있다.


## Introduction

사람은 이미지를 보고 한번에 객체가 무엇인지 인지 할 수 있지만 머신에게는 어려운 과제이다. 배경이나 시간 그리고 조명등에 의해
똑같은 객체임에도 불구하고 다르다고 판단하거나 하는 식의 문제가 발생한다.   

YOLO이전의 모델들은 DPM 과 R-CNN등이 있었는데 이는 객체검출을 통해 객체의 위치정보를 파악하고 파악된 객체가 어떤 클래스인지판
판별하는 구조로 이루어져있다.

DPM은 이미지 전체를 슬라이딩 윈도우 형식으로 객체검출을 하는 모델이다. R-CNN은 이미지 안에서 bounding box를 생성하기 위해
region proposal이라는 방법을 사용한다. bounding box에 classifer를 적용하여 분류하게 되는 것이다. 이후 분류된 bounding box를 조정하고
중복된 검출을 제거하고 객체에 따라 box의 점수를 재산정하기 위해 후처리(post-processing)을 하게 된다 이러한 복잡한은 R-CNN을 
느리게 하는 원인이 되었다.

따라서 YOLO 연구원들은 이를 해결하기 위해 box를 찾고 class를 판별하는 과정을 하나의 회귀문제로 정의하여 이러한 정차를 간편화 시켰다.
이러한 시스템을 통해 YOLO는 이미지 내에 어떤 문체가 있고 그 물체가 어떤 카테고리인지 하나의 파이프라인으로 빠르게 구해준다. 따라서 이미지를
한번보면 객체를 검출 할 수 있다고 하여 이름이 YOLO(You only look once)인 것이다.

![image](https://user-images.githubusercontent.com/65720894/148089037-14032368-deff-40ec-9f71-e19c44fb2d2d.png)

yolo는 하나의 convolution network가 여러 bounding box와 각각의 클래스 확률을 동시에 계산해 준다. YOLO는 이미지 전체를 학습하여 
곧바로 성능을 최적화할 수 있다 - 하나의 파이프라인으로 이루어져있기에 가능한듯?   

이러한 YOLO는 굉장히 빠르다 기존의 객체검출 프로세스를 하나의 회귀문제로 바꾸었기 떄문이다. 그리고 단순히 테스트 단계에서는 새로운 이미지를
YOLO신경망에 넣어 주기만 하면 쉽게 객체검출이 가능하디 또한 YOLO의 기본네트워크는 1초에 45프레임을 처리하는데 이는 동영상을 실시간으로 처리할 수 있다는 것을 의미한다.


그리고 YOLO는 예측을 할 때 이미지 전체를 보는데 이전의 방식과다르게 훈련과 테스트 단계에서 이미지 전체를 본다.     
이러한 방식으로 클래스의 모양에 대한 정보 뿐 아니라 주변의 정보까지 학습하여 처리한다. 하지만 이전의 R-CNN와 같은 모델은 배경을 같이
학습하지 못하여 배경에 노이즈가 낄 시 객체로 인식하고는 하였는데 이러한 점을 극복하였다.

또한 YOLO는 물체의 일반적인 부분을 학습한다 따라서 다른 모델에 비해 일반화 성능이 좋고 즉 이는 훈련단계에서 보지 못한 새로운 이미지에 대해
더욱 Robust하다는 특징이 있다.

하지만 최신 SOTA모델에 비해 정확도가 다소 떨어진다는 단점이 있다. 빠르게 객체를 검출할 수 있다는 장점은 있지만 정확성이 다소 떨어진다는 것이다.

## Unified Detection 

YOLO의 학습과정을 살펴보자.   
YOLO는 입력 이미지를 S X S grid로 나눈다. 만약 어떤 객체의 중심이 특정 grid cell안에 위치 한다면 그 grid cell이 해당 객체를 검출해야 한다.
각각의 grid cell은 B개의 bounding box와 그 bounding box에 대한 confidence score를 예측한다. 이때 confidence score는 bounding box가 
객체를 포함한다는 것이 얼마나 믿을만한지,  그리고 만들어진 bounding box가 얼마나 정확한지를 나타낸다. 따라서 이는 아래와 같이 정의된다.

![image](https://user-images.githubusercontent.com/65720894/148142177-4ee13434-1252-47a6-bab1-bd80c39484da.png)

이때 IOU는 intersection over union의 약자로 예측한 bounding box가 실제 객체를 담고있는 bouding box와 얼마나 겹쳐있는지 합집합
면적 대비 교집합면적의 비율을 뜻한다 즉 자카드 유사도와 같은 알고리즘을 따른다

만약 셀에 아무 객체가 없다면 Pr(objcet)가 0이므로 confidence score도 0이된다.   
각각의 boudning box는 5개의 예측치를 가지는데 x,y,w,h,confidence가 그 요소이다 x,y는 bounding box의 상대적인 위치로 이미지 사이즈내에서
존재하므로 0~1사이의 값을 가질 것이고 w,h도 마찬가지이다 그리고 confidence는 위에서 정의한 것과 같다.

그리고 각 그리드 셀은 conditional class probablites(c)를 예측한다. 이는 아래와 같이 정의된다.

![image](https://user-images.githubusercontent.com/65720894/148142970-4ade8c49-990e-4929-a5f0-ce2dbfb4cc82.png)

이는 grid cell안에 객체가 존재한다는 가정하에 각 클래스인지에 대한 조건부 확률이다. grid cell에는 몇개의 bounding box가 있던지 간에 하나의 클래스에 대한 확률 값만을 구한다. 즉 하나의 grid cell에서 b개의 bouding box를 가진다고 하였는데 이 개수와 무관하게 하나의 class만을
예측한다는 것이다.

테스트 단계에서는 conditional class probability(c)와 bounding box의 confidence score를 곱해주는데 이를 각 bounding box에 대한
class-specific confidence score라고 부른다.

![image](https://user-images.githubusercontent.com/65720894/148143512-38faffc0-4267-4824-80f7-6bd7a55750ba.png)

즉 이 score는 bounding box에 특정 클래스 객체가 나타날 확률과 예측된 boudning box가 그 클래스 객체에 얼마나 잘 들어맞는지를 나타낸다.

![image](https://user-images.githubusercontent.com/65720894/148143640-dd9156f3-30f4-4369-a000-52ac9ce9ad29.png)

## Network Design 

![image](https://user-images.githubusercontent.com/65720894/148144091-089f2e60-453b-46ed-b9c0-5a09394226b5.png)

모델의 구조는 위와 같다 yolo는 여러개의 convolution 구조와 마지막에 전결합층을 지나 S X S X ( B * 5 + C)의 차원을 가진다.   
YOLO의 신경망 모델은 GoogleNET에서 가져왔으며 YOLO는 총 24개의 convolution layers와 2개의 전결합 계층으로 구성되어있다. convolution 
계층은 이미지의 특징을 추출하고 fully conncected layer는 클래스의 확률과 bounding box의 좌표를 에측한다.

## Training

1000개의 클래스를 갖는 imageNet 데이터 셋에서 pretrain한 결과 88%의 정확도를 기록하였다.
ImageNet은 분류를 위한 데이터 셋이다 따라서 사전 훈련된 분류모델을 객체 검출 모델로 바꾸어야한다. 연구진들은 사전훈련된 모델뒤에
4개의 convolution layer그리고 2개의 전결합 계층을 추가하여 성능을 향상 시켰다. 이떄 추가된 layer의 가중치는 랜덤하게 초기화 하였다.
또한 객체 검출을 위해서는 이미지 정보의 해상도가 높아야 하므로 입력 이미지의 해상도를 224 x224dptj 448 x 448로 증가시켰다.

이 신경망의 최종 예측값은 class probablities와 bouding box의 위치정보이다 bounding box의 위치정보에는 w,h,x,y가 있다.
YOLO의 마지막 계층에는 linear activation function을 적용하였고 나머지 모든 계층에는 leaky ReLu를 적용하였다.

YOLO의 loss는 SSE(sum-squared error)를 기반으로 한다. 따라서 이를 최적화 해야한다. SSE를 사용한 이유는 최적화 하기 쉽기 때문이다 하지만
SSE를 최적화 하는 것이 YOLO의 최종목적인 mAP(평균 정확도)을 높이는 것과 완벽하게 일치하지는 않는다.

이는 객체 검출이 bouding box의 위치를 찾는 것 그리고 클래스를 올바르게 예측했는지 에 대해 같은 가중치를 두고 학습하는 것이 좋은 
방법이 아니지만 SSE를 최적화하는 방식은 이 두 LOSS의 가중치를 동일하게 취급한다.

또한 이미지의 대부분이 객체가 검출되지 않아 confidence score가 0이 되도록 학습하게 되는데 이는 모델의 불균형을 초래한다고 한다.

이를 개선하기 위해 객체가 존재하는 bouding box좌표에 대한 loss의 가중치를 증사기키고 객체가 존재하지않는
bounding box의 ocnfidence loss에 대한 가중치는 감소시켰다. 이를 위해 두개의 파라미터를 사용하였는데 lambda_cord 와 lambda_noobj이다
각각 5, 0.5로 주었다.

SSE는 또 다른 문제를 지니고 이는데 큰 bounding box와 작은 bounding box에 대해 모두 동일한 가중치로 loss를 계산한다 하지만 작은 bouding box가 큰 bouding box보다 위치변화에 민감하다. 이를 개선하기 위해 bounding box의 너비와 높이에 square root를 취해주었다. 너비와 높이에
square root를 취해주면 너비와 높이가 커짐에 따라 그 증가율이 감소해 loss에 대한 가중치를 감소시키는 효과가 있기 떄문이다.

YOLO는 하나의 grid cell마다 여러개의 bounding box를 예측한다. 이떄 객체 하나당 하나의 bounding box와 매칭을 시켜야한다
따라서 여러 개의 bounding box중 하나만 선택해야한다. 이를 위해 예측된 여러 bounding box중 실제 객체를 감싸는 ground -truth bouding box와의 iou가 가장 큰 것을 선택한다. 

이렇게  보완하게 되면 loss는 아래와 같이 된다.

![image](https://user-images.githubusercontent.com/65720894/148146916-3133cfdd-73c5-4a23-8eeb-c0bb7ba0fbb2.png)

이 loss각 하나의 grid cell에서 적용되며 1_i^obj는 grid cell안에 객체가 존재하는지 여부를 의미한다. 따라서 객체가 존재하지 않으면
0 존재하면 1이다. 그리고  1_ij^obj는 그리드 셀 i의 j번째 bounding box predictor가 사용되는지 여부를 의미한다.

위식은 다음을 정의한건데 

- Object가 존재하는 그리드 셀 i의 bounding box predictor j에 대해, x와 y의 loss를 계산
- Object가 존재하는 그리드 셀 i의 bounding box predictor j에 대해, w와 h의 loss를 계산
- Object가 존재하는 그리드 셀 i의 bounding box predictor j에 대해, confidence score의 loss를 계산
- Object가 존재하지 않는 그리드 셀 i의 bounding box predictor j에 대해, confidence score의 loss를 계산
- Object가 존재하는 그리드 셀 i에 대해, conditional class probability의 loss를 계산. (p_i(c)=1 if class c is correct, otherwise: p_i(c)=0)

파라미터는 batch size = 64 , momentum은 0.9 decay 는 0.0005로 설정하였다 초반에는 learning rate을 0.001에서 0.001로 천천히
상승시켰다. 이후 75 epoch 동안에는 0.01, 30 epoch 동안에는 0.001, 그리고 마지막 30 epoch 동안은 0.0001로 learning rate를 설정한다.

overfitting을 막기 위해 dropout과 data agumentation을 적용하였다 이떄 dropout의 비율은 0.5 agumentation은 20%까지 랜덤 스케일링과
랜덤 이동을 적용하였다.

## Inference 

훈련단계와 마찬가지로 추론단계에서도 이미지로 부터 객체를 검출하는 데에는 하나의 신경망 게산만 하면된다.
YOLO는 한 임지당 98개의 boudning box를 에측해주고 그 box마다 class 확률을 구해준다. 

## Limitations of YOLO

YOLO는 하나의 grid cell마다 두개의 boudning box를 예측한다 그리고 하나의 grid cell마다 오직 하나의 객체만을 검출할 수 있으므로
이는 공간적 제약을 야기한다. 이는 하나의 grid cell은 오직 하나의 객체만을 검출하므로 하나의 grid셀 안에 여러개의 객체가
존재할때 이를 잘 검출하지 못한다는 한계를 말한다. 즉 객체가 몰려있다면 그 성능이 떨어지게된다.

또한 모델이 데이터로 부터 bounding box를 예측하는 것이기 때문에 훈련단계에서 학습하지 못한 형태의 bounding box는 잘 예측하지 못한다.




