---
layout: post
title: "[GAN] CGAN 예제코드를 통한 이해"
subtitle: "Tensorflow example code"
categories: deeplearning
tags: code 
comments: true
---

## Tensorflow example code

```
import tensorflow as tf

import os
import time
from matplotlib import pyplot as plt
from IPython import display

#Load the dataset

_URL = 'https://people.eecs.berkeley.edu/~tinghuiz/projects/pix2pix/datasets/facades.tar.gz'

path_to_zip = tf.keras.utils.get_file('facades.tar.gz',
                                      origin=_URL,
                                      extract=True)
#256 x 256 size
PATH = os.path.join(os.path.dirname(path_to_zip), 'facades/')
```

기본적인 세팅과 학습에 필요한 데이터를 가져온다.

```
BUFFER_SIZE = 400#의문
BATCH_SIZE = 1
IMG_WIDTH = 256
IMG_HEIGHT = 256

def load(image_file):
  image = tf.io.read_file(image_file)
  image = tf.image.decode_jpeg(image)# 이미지파일을 잘손질

  w = tf.shape(image)[1] #w = tf.Tensor(512, shape=(), dtype=int32) 의 형태이다
  w = w// 2 # 256으로 변환
   #// 나누기 연산후 소수점 이하의 수를 버리고, 정수부분의 수만 구함
  real_image = image[:, :w, :]#앞에있는게 real데이터
  input_image = image[: ,w:, :] #데이터자체가 반으로 나뉘어진것일것  아 걍 그렇다고 하자 데이터가

  input_image = tf.cast(input_image, tf.float32)
  real_image = tf.cast(real_image, tf.float32)

  return input_image, real_image #input image = 내가가진 데이터 , real_data =  워너비 실제데이터
  ```
  
BUFFER_SIZE는 무슨의미인지 아직 불명이다..   
load()함수는 이미지 파일을 입력받아 데이터를 반으로 나누어 real_data와 input_data로 나눈다 
  
  ```
  inp,re = load(PATH+'train/100.jpg')
# casting to int for matplotlib to show the image
plt.figure()
plt.imshow(inp/255.0)#값을 바꾸면서 본 결과로 밝기를 조절할수 있다 즉 0으로 갈수록 어두워지며 1로 갈수록 밝아진다
plt.figure()
plt.imshow(re/255.0)
```

데이터를 확인해 보도록 하자.

![2](https://user-images.githubusercontent.com/65720894/89540699-5e4b9000-d838-11ea-95df-9bd993e6d629.PNG)

위가 input_image 아래가 real_image이다. 255.0으로 나눠주는 것은 각 픽셀의 밝기가 0~1부터의 밝기로 이루어져서이다.

```
def resize(input_iamge , real_image, height, width):
  input_image = tf.image.resize(input_image, [height, width],
                              method=tf.image.ResizeMethod.NEAREST_NEIGHBOR)
  real_image = tf.image.resize(real_image, [height, width],
                               method=tf.image.ResizeMethod.NEAREST_NEIGHBOR)
  
  return input_image, real_image #학습에 알맞게 resize해준다
#위 함수는 지터링을 위해 더 큰 높이(286pix)와 너비(286pix)로 이미지 크기를 조정한다. 
```

```
def random_crop(input_image, real_image):
  stacked_image = tf.stack([input_image, real_image], axis=0)#stack 함수는  행렬를 합친다 즉 두 데이터를 합침
  cropped_image = tf.image.random_crop(
      stacked_image, size=[2, IMG_HEIGHT, IMG_WIDTH, 3])# 마지막은 체널이고 첫번쨰는?
  
# tf.random_crop 함수는 첫 번째 파라미터로 받은 텐서타입의 이미지들을 
#두 번째 파라미터로 받은 크기로 무작위로 잘라 첫 번째 받은 파라미터와 같은 rank의 텐서 형태로 돌려준다.

  return cropped_image[0], cropped_image[1]
#256pix인 대상크기로 무작위 자르기를 한다. - 이를jitter라고 하는데 논문에서 소개하고있으니 해보도록 하자
```

```
 # normalizing the images to [-1, 1]

def normalize(input_image, real_image):
  input_image = (input_image / 127.5) - 1
  real_image = (real_image / 127.5) - 1

  return input_image, real_image
  ```
  
  ```
  def random_jitter(input_image, real_image):
  # resizing to 286 x 286 x 3
  input_image, real_image = resize(input_image, real_image, 286, 286)#286으로 재조정

  # randomly cropping to 256 x 256 x 3
  input_image, real_image = random_crop(input_image, real_image)#256사이즈로 랜덤하게 자른다. 아마 학습
  #률을 높이기위해(분포값을 늘리기위해?)

  if tf.random.uniform(()) > 0.5:#균일 한 분포로부터의 출력값을 랜덤.
    # random mirroring
    input_image = tf.image.flip_left_right(input_image)
    real_image = tf.image.flip_left_right(real_image)#이미지 d

  return input_image, real_image
  ```
  
  코드가 길어졌는데 사실 내용은 별거 없다. jitter와 mirriring을 해주었는데 그 이유는 CGAN 논문에서 학습률에 도움을 준다고 하여 집어넣었다. 예상하는 바로는 각 이미지 데이터의 분포를 항상 랜덤하게 만들어 학습이 더 효과적으로 이루어질 수 있게 하는것이라 생각한다.
  ![1](https://user-images.githubusercontent.com/65720894/89540695-5d1a6300-d838-11ea-804f-87aec51fcd59.PNG)
  
  
  위는 눈문의 내용중을 캡쳐하였다.
  
  ![3](https://user-images.githubusercontent.com/65720894/89541357-3ad51500-d839-11ea-9775-64cf239123f5.PNG)
  하지만 현재 위의 오류가 뜨면서 나의 진행을 방해하고있다. 문제의 해결법을 찾았는데... 단순한 오타였다 하하
  
  ```
   plt.figure(figsize=(6, 6))
for i in range(4):
  rj_inp, rj_re = random_jitter(inp, re)
  plt.subplot(2, 2, i+1)
  plt.imshow(rj_inp/255.0)
  plt.axis('off')
plt.show()
```
![캡처](https://user-images.githubusercontent.com/65720894/89653837-ad5df780-d902-11ea-91b1-b6d2e1842cc1.PNG)

이미지 데이터로 시험해보니 잘 작동하는것 같다.

```
 def load_image_train(image_file):
  input_image, real_image = load(image_file)
  input_image, real_image = normalize(input_image, real_image)

  return input_image, real_image
  #image train 용 데이터  호출
  
  def load_image_test(image_file):
  input_image, real_image = load(image_file)
  input_image, real_image = resize(input_image, real_image,
                                   IMG_HEIGHT, IMG_WIDTH)
  input_image, real_image = normalize(input_image, real_image)

  return input_image, real_image #test용 이미지 호출 애는 왜 resize를 할까요?
  ```
  
  이미지의 test_data와 train_data는 다른방식으로 로드된다. 
  
  ```
  #입력 파이프라인
train_dataset = tf.data.Dataset.list_files(PATH+'train/*.jpg')
train_dataset = train_dataset.map(load_image_train,
                                  num_parallel_calls=tf.data.experimental.AUTOTUNE)#살짝 이해안감
train_dataset = train_dataset.shuffle(BUFFER_SIZE)#섞어준다
train_dataset = train_dataset.batch(BATCH_SIZE)#batch size로 배열을 나눠준다 reminder = true 설정은 남는 배열값을
#무시하고 만든다.

test_dataset = tf.data.Dataset.list_files(PATH+'test/*.jpg')
test_dataset = test_dataset.map(load_image_test)
test_dataset = test_dataset.batch(BATCH_SIZE)#test용 데이터셋을 만들어준다
```

각각의 데이터 셋을 만드는 과정인데 데이터를 이후 사용할 수 있게 잘 다듬어 준다.

```
 OUTPUT_CHANNELS = 3
  
   def downsample(filters, size, apply_batchnorm=True):
  initializer = tf.random_normal_initializer(0., 0.02)#0이 평균 0.02가 표준편차인 텐서 생성

  result = tf.keras.Sequential()
  result.add(
      tf.keras.layers.Conv2D(filters, size, strides=2, padding='same',
                             kernel_initializer=initializer, use_bias=False))#input image가 없는건가 이후 
                             #tf.keras.latyers.Input(shape) 함수에서 입력값을 넣어준다.
                             #kenel_initalizer는 정규 분포와 텐서를 생성한다.
                             #근데 왜 생성해서 매개변수에 넣어주는걸까..?
                             #답은 가중치 초기화에 있었다.

  if apply_batchnorm:
    result.add(tf.keras.layers.BatchNormalization())

  result.add(tf.keras.layers.LeakyReLU())

  return result
  
 #인코더의 각블록은 (Conv - Batchnorm - Leaky ReLU)이다.
 #블록은 즉 result 하나의 layer라 할수 있지 않을까?
 ```
 
 downsample 데이터배열을 합성곱(convolution)하여 feature map을 만든다 이때 하나의 layer에는 conv2d - Batchnormalization - Leaky Relu 순으로 구성되어있다.
 
 ```
down_model = downsample(3, 4)
down_result = down_model(tf.expand_dims(inp, 0))
print (down_result.shape)
```

`result : (1, 128, 128, 3)`

```
def upsample(filters, size, apply_dropout=False):
  initializer = tf.random_normal_initializer(0., 0.02)

  result = tf.keras.Sequential()
  result.add(
    tf.keras.layers.Conv2DTranspose(filters, size, strides=2,
                                    padding='same',#패딩그대로 
                                    kernel_initializer=initializer,
                                    use_bias=False))
  

  result.add(tf.keras.layers.BatchNormalization())

  if apply_dropout:
      result.add(tf.keras.layers.Dropout(0.5))

  result.add(tf.keras.layers.ReLU())

  return result
 #디코더의 블록은 (Transposed Conv - Batchnorm - Dropout) - ReLU 으로 구성되있다.
 ```
 
 upsample 은 Transepose_Conv2d로 구현 가능하며 이와 관련된 개념은 이후 차차 알아볼것이다. 여기서는 이미지 데이터를 확장 시키므로 Conv2d와 반대의 입장이라 이해하면 쉽다. 각각의 배열은 (Transposed Conv - Batchnorm - Dropout)-ReLU로 구성된다. RELU는 처음 3 개의 블록에 적용됨
 
 ```
 def Generator():
  inputs = tf.keras.layers.Input(shape=[256,256,3]) #keras 모델에 input데이터 값을 입력해준다. 

  down_stack = [
    downsample(64, 4, apply_batchnorm=False), # (bs, 128, 128, 64) 첫번쨰만 배치정규화를 안했다.
    downsample(128, 4), # (bs, 64, 64, 128)
    downsample(256, 4), # (bs, 32, 32, 256)
    downsample(512, 4), # (bs, 16, 16, 512)
    downsample(512, 4), # (bs, 8, 8, 512)
    downsample(512, 4), # (bs, 4, 4, 512)
    downsample(512, 4), # (bs, 2, 2, 512)
    downsample(512, 4), # (bs, 1, 1, 512)
  ]

  up_stack = [
    upsample(512, 4, apply_dropout=True), # (bs, 2, 2, 1024)
    upsample(512, 4, apply_dropout=True), # (bs, 4, 4, 1024)
    upsample(512, 4, apply_dropout=True), # (bs, 8, 8, 1024)
    upsample(512, 4), # (bs, 16, 16, 1024)
    upsample(256, 4), # (bs, 32, 32, 512)
    upsample(128, 4), # (bs, 64, 64, 256)
    upsample(64, 4), # (bs, 128, 128, 128)
  ]#이 개념은 나중에 다시 세세히 알아보자 - 위 데이터 shape값은 downsample의 데이터값이 concatenate된 값으로 
  #실제의 두배의 데이터의 차원이 있다.

  initializer = tf.random_normal_initializer(0., 0.02)
  last = tf.keras.layers.Conv2DTranspose(OUTPUT_CHANNELS, 4,
                                         strides=2,
                                         padding='same',
                                         kernel_initializer=initializer,
                                         activation='tanh') # (bs, 256, 256, 3)

  x = inputs

  # Downsampling through the model
  skips = []
  for down in down_stack:
    x = down(x)
    skips.append(x)

  skips = reversed(skips[:-1])# 파이썬에서 배열의 -1값은 맨 마지막 데이터이므로 마지막 데이터를 제외한 모든 값이다.

  # Upsampling and establishing the skip connections
  for up, skip in zip(up_stack, skips):
    x = up(x)
    x = tf.keras.layers.Concatenate()([x, skip])# 위의 데이터를 합치는데 이게바로 U-NET방식이다 학습률을 높여주지

  x = last(x)

  return tf.keras.Model(inputs=inputs, outputs=x)
  ```
  생성자(Generator)를 구현하였고 이는 이 그림을 통해 이해하면 쉽다
  
  ![generator](https://camo.githubusercontent.com/6bae649298f16ed7645a607615c9e97470cd873f/68747470733a2f2f7461656f682d6b696d2e6769746875622e696f2f696d672f636f6465312e504e47)</center>
  
downsample로 256x256x3의 이미지 데이터를 1x1x512 feature변환 시킨후 이를 upsample시켜 원래의 이미지 크기로 다시 변환한다.  
이때 upsample에는 ` x = tf.keras.layers.Concatenate()([x, skip])` 이전에 downsample했던 이미지 데이터가 역배치로 함께 들어가게된다. 또한 upsample의 마지막은 따로 Cnnv2d를 설정해준다.

```
generator = Generator()
tf.keras.utils.plot_model(generator, show_shapes=True, dpi=64)
``` 
위코드를 실행하면 generator 모델의 학습과정이 나타난다.  

![generator](https://user-images.githubusercontent.com/65720894/89734156-5177a800-da95-11ea-878f-f5084bcf4c16.png)

```
gen_output = generator(inp[tf.newaxis,...], training=False)
plt.imshow(gen_output[0,...])
```

input을 넣고 generator의 학습과정을 볼 수도 있다 = gen_output

![3](https://user-images.githubusercontent.com/65720894/89734198-a61b2300-da95-11ea-822b-d828a99566bc.PNG)

```
LAMBBDA = 100

 def generator_loss(disc_generated_output, gen_output, target):#생성자의 손실함수 정의?
  gan_loss = loss_object(tf.ones_like(disc_generated_output), disc_generated_output)#이줄 진짜 모르겠다...
  #loss object는 그냥 Sigmoid Cross Entropy 기법이라고 생각하자 
  #  loss = loss_object(labels, predictions)
  #tf.ines_like - 입력값의 형태와 동일한 텐서(1)을 생성
  # mean absolute error

  l1_loss = tf.reduce_mean(tf.abs(target - gen_output))

  total_gen_loss = gan_loss + (LAMBDA * l1_loss)
  #V(G,D)=minGmaxDEy[log(D(y)]+Ex[log(1−D(G(x))]+Ex,y[∥y−G(x)∥
  return total_gen_loss, gan_loss, l1_loss

  #gen_output = generator(input_image, training=True) -이미지 생성결과가 나옴
  #disc_generated_output = discriminator([input_image, gen_output], training=True) 
  # 위에는 fake_data와 input_data을 구분 한것을 반환한다

  #target이 내가 지향하는바
  #Gen_output이 G(y) 즉 source real image를 생성자로 변환한것 
  
  ```
  
  모르는 점을 정리해보자면 
  1. loss_object란 함수자체를 모르겠다 - 하지만 sigmoid loss의 개념인듯 싶다.  - 얼추 해결
  2. 그 인자에 disc_generated_output shape 의 배열과 disc_generated_output가 동시에 들어가는지 - 얼추 해결
  
 아래 코드는 위 코드의 수정본?? 노트필기형식으로 모르는 것에 관하여 해답을 적어놓은것이다.  
 참고로 `gen_output[0]` 의 형태는 `<tf.Tensor: shape=(256, 256, 3), dtype=float32, numpy=''' `의 형태로 이미지 배열을 나타낸다.
 
 
 ```
  def generator_loss(disc_generated_output, gen_output, target):#생성자의 손실함수 정의?
  gan_loss = loss_object(tf.ones_like(disc_generated_output), disc_generated_output)
  
  
  # loss_object = tf.keras.losses.BinaryCrossentropy(from_logits=True) 밑에 있음...
  # predictions = model(input) 즉 모델학습값이다(예측값)
  #  loss = loss_object(labels, predictions) =
  #tf.ines_like - 입력값의 형태와 동일한 텐서(1)을 생성

  # 이렇게 생각해보자 input이 label값으로 향해야 한다고 즉 label이 전부 1로 맞춰져 있으므로
  # 생성자가 만든 값이 구분자가 구분했을떄 참(1)으로 구분하게 만든다고

  #binary cross-entorpy loss 함수는 만약 label이 1일경우 
  #disc_generated_ouput 값이 1일시 최저값을 가지므로 label인 1을 향해 손실이 줄어든다.
  #따라서 Ey[log(D(y)]+Ex[log(1−D(G(x))] 에서 G(x) 가 진짜라고 하는것이 loss가 된다.
  

  l1_loss = tf.reduce_mean(tf.abs(target - gen_output))

  total_gen_loss = gan_loss + (LAMBDA * l1_loss)
  #V(G,D)=minGmaxDEy[log(D(y)]+Ex[log(1−D(G(x))]+Ex,y[∥y−G(x)∥
  return total_gen_loss, gan_loss, l1_loss

  #disc_generated_output = discriminator([input_image, gen_output], training=True) 
  # 위에는 생성자가 만든 fakedata와 input데이터가 한쌍인지 아닌지를 판단한 결과이다
  ```
 
 주저리 주저리 써놓았지만 결론적으로는 위 코드는 아래 수식을 구현한 것이다.
 
 ![ㅡㅁ소](https://user-images.githubusercontent.com/65720894/89906573-85ce9e00-dc26-11ea-9e8f-fccfa9ca87b1.PNG)
 
losses.BinaryCrossentropy가 손실함수일 경우 label값이 1인 경우 prediction값들이 경사하강법에 따라 1즉 TRUE값으로 학습된다.  
loss function과 활성화함수등은 다시 한번 공부하도록 하자..

```
def Discriminator():
  initializer = tf.random_normal_initializer(0., 0.02)

  inp = tf.keras.layers.Input(shape=[256, 256, 3], name='input_image')
  tar = tf.keras.layers.Input(shape=[256, 256, 3], name='target_image')
  #real을 구분하기 위한 discriminator
  # input이 재료 target이 목표

  x = tf.keras.layers.concatenate([inp, tar]) # (bs, 256, 256, channels*2)

  down1 = downsample(64, 4, False)(x) # (bs, 128, 128, 64)#batchnorm 은 falee
  down2 = downsample(128, 4)(down1) # (bs, 64, 64, 128)
  down3 = downsample(256, 4)(down2) # (bs, 32, 32, 256)

  zero_pad1 = tf.keras.layers.ZeroPadding2D()(down3) # (bs, 34, 34, 256) zero패딩으로 pix수가 늘었다
  conv = tf.keras.layers.Conv2D(512, 4, strides=1,
                                kernel_initializer=initializer,
                                use_bias=False)(zero_pad1) # (bs, 31, 31, 512) zero패딩 이후에 conv를 하면 pix만 살짝 줄어든다

  batchnorm1 = tf.keras.layers.BatchNormalization()(conv)

  leaky_relu = tf.keras.layers.LeakyReLU()(batchnorm1)

  zero_pad2 = tf.keras.layers.ZeroPadding2D()(leaky_relu) # (bs, 33, 33, 512) 

  last = tf.keras.layers.Conv2D(1, 4, strides=1,
                                kernel_initializer=initializer)(zero_pad2) # (bs, 30, 30, 1)

  return tf.keras.Model(inputs=[inp, tar], outputs=last)
#모든 순서는 논문에 나와있는데로 실시하였다.
```
discriminator 모델을 생성한것이다 위 형태는 논문에서 나온 discriminator형태를 그대로 구현한것이다. generator와 마찬가지로 모델학습값을 반환한다.

```
discriminator = Discriminator()
tf.keras.utils.plot_model(discriminator, show_shapes=True, dpi=64)
```
![dd](https://user-images.githubusercontent.com/65720894/89907350-8582d280-dc27-11ea-9123-43f060169a83.PNG)

discriminator 모델의 학습과정을 볼수 있다.

```
disc_out = discriminator([inp[tf.newaxis,...], gen_output], training=False)
plt.imshow(disc_out[0,...,-1], vmin=-20, vmax=20, cmap='RdBu_r')
plt.colorbar()
```
구분자(discriminator)의 값을 색으로 구분한것이다 잘모르지만 빨간색일수록 real_data와 비슷하다는거 아닐까?

```
 loss_object = tf.keras.losses.BinaryCrossentropy(from_logits=True)#다음에 다시 알아보자
 
 def discriminator_loss(disc_real_output, disc_generated_output):
  real_loss = loss_object(tf.ones_like(disc_real_output), disc_real_output) 

  generated_loss = loss_object(tf.zeros_like(disc_generated_output), disc_generated_output)

  #아까와 같이 생각하자 위와가팅 real_loss 의 label자리에 1을 넣는다면 disc_real_output 즉 
  #real 데이터 쌍은 TRUE(1)으로 학습시켜라 generated_loss는 label자리에 0을 넣었으므로 
  #disc_generated_output 즉 생성자의 데이터는 false(0)이라 학습해라 이말이야~

  total_disc_loss = real_loss + generated_loss #discriminator는 2번 학습한다.

  return total_disc_loss
  ```
  loss_object를 설정해주며 generator와 마찬가지로 loss를 구현한다. 
  
  ```
generator_optimizer = tf.keras.optimizers.Adam(2e-4, beta_1=0.5)
discriminator_optimizer = tf.keras.optimizers.Adam(2e-4, beta_1=0.5) 
#최적화 함수 선택

checkpoint_dir = './training_checkpoints'
checkpoint_prefix = os.path.join(checkpoint_dir, "ckpt")
checkpoint = tf.train.Checkpoint(generator_optimizer=generator_optimizer,
                                 discriminator_optimizer=discriminator_optimizer,
                                 generator=generator,
                                 discriminator=discriminator)
#단순한 세이브

```

optimizer(최적화)로 Adam을 선택하였고 학습의 결과를 저장하기 위해 디렉토리를 만들어 직접 저장해준다..지만 나는 사지방에서 공부하는 군인이기 떄문에 나에게는 아무런 의미가없다...따라서 학습하기위해서는 컴퓨터를 계속 켜두는 수밖에ㅜㅜ

```
def generate_images(model, test_input, tar):#이미지 생성함수 
  prediction = model(test_input, training=True)#모델 사용준비 완료
  plt.figure(figsize=(15,15))#15pix 

  display_list = [test_input[0], tar[0], prediction[0]]
  title = ['Input Image', 'Ground Truth', 'Predicted Image']

  for i in range(3):
    plt.subplot(1, 3, i+1)
    plt.title(title[i])
    # getting the pixel values between [0, 1] to plot it.
    plt.imshow(display_list[i] * 0.5 + 0.5)
    plt.axis('off')
  plt.show()
  #generate_images(generator, example_input, example_target)
  ```
  
  자 이제 본격적으로 생성자(generator)로 이미지를 생성해보자 model과 input_data, target_data를 인자로 받아 generator모델로 학습한뒤 이를 pli모듈로 가시화 시킨다 
  
  ```
  for example_input, example_target in test_dataset.take(1):
    generate_images(generator, example_input, example_target)
 ```
 ![epoch0](https://user-images.githubusercontent.com/65720894/89908301-adbf0100-dc28-11ea-9290-d32779486a91.PNG)
 
 좋은 결과를 얻어낼 수 있다.
 
 ```
 EPOCHS = 150

import datetime
log_dir="logs/"

summary_writer = tf.summary.create_file_writer(
  log_dir + "fit/" + datetime.datetime.now().strftime("%Y%m%d-%H%M%S"))
  ```
  반복 횟수와 저장된 학습결과를 불러온다.
  
  ```
  @tf.function#이전에도 나왔지만 함수계의 매크로라고 할수있다.
def train_step(input_image, target, epoch):
  with tf.GradientTape() as gen_tape, tf.GradientTape() as disc_tape:
    #매개변수의 손실을 미분하는데 있어  with tf.GradientTape() as tape: 안에서 계산을 하면 tape에 
    #계산 과정을 기록해두었다가 tape.gradient를 이용해서 미분을 자동으로 구할 수 있다.
    gen_output = generator(input_image, training=True)#생성자의 fake_Data

    disc_real_output = discriminator([input_image, target], training=True)#postive examples
    disc_generated_output = discriminator([input_image, gen_output], training=True)#negative examples

    gen_total_loss, gen_gan_loss, gen_l1_loss = generator_loss(disc_generated_output, gen_output, target)
    disc_loss = discriminator_loss(disc_real_output, disc_generated_output)

  generator_gradients = gen_tape.gradient(gen_total_loss,
                                          generator.trainable_variables)
  discriminator_gradients = disc_tape.gradient(disc_loss,
                                               discriminator.trainable_variables)
  #tape에 다 미분값이 저장이됩니다

  generator_optimizer.apply_gradients(zip(generator_gradients,
                                          generator.trainable_variables))
  discriminator_optimizer.apply_gradients(zip(discriminator_gradients,
                                              discriminator.trainable_variables))

  with summary_writer.as_default():
    tf.summary.scalar('gen_total_loss', gen_total_loss, step=epoch)
    tf.summary.scalar('gen_gan_loss', gen_gan_loss, step=epoch)
    tf.summary.scalar('gen_l1_loss', gen_l1_loss, step=epoch)
    tf.summary.scalar('disc_loss', disc_loss, step=epoch)
    
 ```
 
 지금까지 작성해온 C-GAN의 핵심이라고 볼 수 있는 부분이다 생성자(generator)모델과 구분자(discriminaotr) 모델이 `train_step` 에서 정의된 손실함수를 통해 학습을 한다. 정의된 손실함수는 `tape.gradient`로 tape에 미분값이 저장되면서 기울기를 계산한다. 그리고 `apply_gradient()`를 사용하여 처리된 기울기 값을 모델에 적용한다.
 
 ```
def fit(train_ds, epochs, test_ds):
  for epoch in range(epochs):
    start = time.time()

    display.clear_output(wait=True)

    for example_input, example_target in test_ds.take(1):
      generate_images(generator, example_input, example_target)
    print("Epoch: ", epoch)#미숙한 생성자로 미숙한 fakedata생성

    # Train
    for n, (input_image, target) in train_ds.enumerate():
      print('.', end='')
      if (n+1) % 100 == 0:
        print()
      train_step(input_image, target, epoch)
    print()
    

    # saving (checkpoint) the model every 20 epochs
    if epoch % 20 == 0:
      checkpoint.save(file_prefix = checkpoint_prefix)

    print ('Time taken for epoch {} is {} sec\n'.format(epoch + 1,
                                                        time.time()-start))
  checkpoint.save(file_prefix = checkpoint_prefix)
  ```
  이제 fir함수를 정의하여 학습을 시킬 시간이다 여기서 중요한점은 `generator_images` 으로 fake_data를 생성한후에 real_pair와 fake_pair를 만들어 구분자에게 학습시킨다 EPOCHs 가 20이 될때마다 학습값이 저장된다
  
  ```
   fit(train_dataset, EPOCHS, test_dataset)
   ```
   
   ![epoch0](https://user-images.githubusercontent.com/65720894/90312855-d0139000-df42-11ea-99d4-84537b2d6e34.PNG)
   
   ![epoch8](https://user-images.githubusercontent.com/65720894/90312851-cdb13600-df42-11ea-83fe-9399e2673332.PNG)
   
   
   
  
  
  



 
 
  

  



  
  
  
  
 
 



  
  
  

