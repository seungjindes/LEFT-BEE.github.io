---
layout: post
title: "데이터 전처리 방법"
subtitle: "how to dataprocess"
categories: deeplearning
tags: tech
comments: true
---


### 데이터 전처리의 필요성

인공신경망은 설계가 구현 이상으로 중요하다. 설게와 구현의 최적화가 performance에 미치는 비중으로 치자면 이론적 설계 단계가 훨씬 더 높은 가중치를 
지니고 있다. 그 설계의 부분에서도 비중이 큰것이 바로 데이터의 전처리이다.

디지털화 가능한 모든 종류의 데이터를 분석할 수 있는 인공신경망 분석모델을 구축하는데 있어서, 중요한 것은 어떤 데이터를 어떤 방식으로 어떻게 가공하여 어떤식으로
출력할지에 대해 설계하는 것이다.

'데이터 주도 학습'이니 어떤 데이터가 주입되는지가 가장 중요한 것은 당연한일.. 정확도를 일정 부분 이상(실용성의 임계점)으로 끌어올리는
방법으로, 이러한 데이터 전처리를 행하거나, 혹은 데이터 전처리 이후에만 나오는 정보 역시 존재하기에 이는 무척 중요한 일이다. 따라서 데이터를 적적한 형태로 가공해줘야한다.

텐서플로의 keras모듈 역시 이러한 데이터 전처리를 위한 기법을 제공해준다.

ex)자연어 처리 데이터 전처리 예시

### 1.라이브러리 import

`import os`
`import tensorflow as tf`
`import numpy as np`
`form tensorflow,keras import preprocessing`

:주로 tf의 data모듈과 keras모듈의 preprocessing을 사용할 것이다,

### 2.언어 데이터 및 정답 레이블 준비

`sample = ['너 정말 예쁘다' , '나는 오늘 화났다' , '길동이가 오늘 기분이 좋은가봐', '정말 끝내주는 날이야', '재정말 화났어','굉장한데? 진짜 좋다']`

`label = [[1],[0],[1],[1],[0],[1]]`

여기서 규칙성은 사람이 판단하기에 긍정적인 문장은 1 , 부정적인 문장은 0으로 나눈 것이다. 여기서 데이터와 라벨의 의미는, 머신러닝 모델을 학습시킬 때의 기준을 의마한다.
이러한 문장은 1로 긍정 저러한 문장은 0으로 부정..이런식이다.

즉 데이터와 라벨을 잘 준비하는 것은 무척 중요한 일이고, 모델 자체는 추후 이에 맞게 시도를 해나가며 변경하면 된다. 이제 자연어 데이터와 정답이 준비됬다.
아래부터는 데이털르 사용하기 쉽게 가공하는 작업, 즉 데이터 전처리를 실행해 보자.

### 3. 데이터 토큰화 

`tokenizer = preprocessing.text.Tokenizer()`
`tokenizer.fit_on_texts(samples) 단어단위로 변환`
`sequences = tokenizer.texts_to_sequences(samples) 벡터로 변환`
`word_index = tokenizer.word_index`

keras 모듈의 preprocessing 에는 Tokenizer라는 객체가 있다. 이는 주로 자연어 처리에서 문자열을 말 그대로 토큰화 하는 객체인데, 
이것으로 객체를 만들고 fit_on_texts 메소드를 사용하면, 문자열 배열 내의 문장이 '단어'단위로 나뉘어져서 토큰화 된다. 
이렇게 되면 단어가 문자열 타입이 아니라, 일련의 숫자 인덱스로 치환이 가능한 단어 인덱스 사전이 생기는 것이다.  

text_to_sequences() 메소드를 사용하면, 위의 과정에서 tokenizer의 멤버변수로 저장된 인덱스 사전을 통해 단어 형태소가 숫자로 변환된 sequences 데이터, 즉 숫자의 벡터로써 samples가 치환되는 것이다. 이후 프로그래머가 변환된 데이터를 해석하기 위해서 word_index를 tokenizer객체에서 가져올 수도 있다.

### 4. 정답과 학습 데이터 묶기(중요★)

위에서 토크나이징한 배열들은 , 각 배열별로 레이블 하나에 대응된다. tensorflow에서 입력층에 넣어줄 학습 데이터 타입으로써, 데이터와 레이블을 묶은 타입으로 만듦으로써 쉽게 데이터를 사용할 수 있는데, `tf.data`를 사용하면 된다.  

`dataset = tf.data.Dataset.from_tensor_slices((sequences, label))
iterator = dataset.make_one_shot_iterator()
next_data=iterator.get_next()`
위의 코드처럼 tf.data의 메소드를 사용함으로써, 데이터 배열과 정답 레이블을 하나로 묶은 dataset객체를 가져올수 있다.(sequences는 벡터화된 sample) 순서대로 정답과 데이터가 서로 대응되도록 묶어서 저장하고 있는 객체 -dataset

dataset을 사용하려면 객체 안에 포함된 `iterator`를 불러와서 사용하면 된다. 해당 dataset객체에서 가져온 iterator 객체의 `get_next()` 메소드를 사용함으로써 처음부터 바로 뒤 값들을 차례로 반환해준다. 반환값은 `(array([1,2,3,4]), array([1]))`로 데이터와 정답이 묶여서 제공된다. iterator는 next()함수로 데이터를 순차적으로 호출 가능한 object이다 이때 조심해야 할것은 list와 list_iterartor의 형태는 다르다는 것이다. list는 iterator가 아니지만 list_iterator는 맞다. 따라서 dataset을 iterator의 형태로 바꿔주어 next()함수를 사용가능하게 하였다. 위에서 설명하였듯이 next()함수로 불러온 것은 sample과 label이 묶여있는 데이터이다.  

### 5.mini batch

똑같은 데이터를 똑같은 방식으로 학습시키면 overfitting이 일어나기 쉽다. 그렇기에 전체 학습 데이터를 batch단위로 랜덤하게 쪼개어 사용하는 방식을 사용한다. Dataset객체에는 이러한 배치를 자동으로 해주는 메소드를 제공해준다

~~~
BATCH_SIZE = 2

dataset = tf.data.Dataset.from_tensor_slices((sequences, label))
dataset = dataset.batch(BATCH_SIZE) - 데이터 2개단위 배치로 미니배치를 생성
iterator = dataset.make_one_shot_iterator()
next_data = iterator.get_next()

with tf.Session() as sess:
    while True:
        try:
            print(sess.run(next_data))
        except tf.errors.OutOfRangeError:
            break
~~~

위와 같이 Batch_size를 두면 (array([[4,1,5,6],[7,1,8,9]]), array([1],[0])) 이런식으로 학습 데이터와 정답 레이블의 쌍이 2개씩 묶인 
단위로 나뉘어진다. 전체 학습 데이터에서 적당히 나뉘도록 배치 사이즈를 설정하면 된다.

### 6.Shuffle

이제 minibatch에 따라서 batch단위로 훈련데이터를 나눴는데, 다음과정으로는 학습할 때마다 훈련데이터가 나오는 순서를 바꿔줘야한다. 기껏 배치단위로 나눴어도 그 순서가 매번 동일하다면 인공신경망의 가중치 학습 결과도 동일하다. 인공신경망 학습은 변화에 대한 학습으로 보편성을 띄고, overfitting을 방지할 수 있고 동일한 데이터양으로 훈련량을 올릴 수 있다.

`dataset = dataset.shuffle(len(sequences))`

### 7. Epochs 설정

에폿이란 인공신경망 학습은 한번에 최적값을 찾아내는 것이 아니다, 같은 데이터를 여러번 시도하면서 오차율을 최대한 줄인 최적의 값을 찾아낸다. 반복학습이 바로 epoch인데 얼마나 반복을 실행할지에 대한 것으로 tensorflow Dataset에는 epoch을 적용할 수 있는 메소드가 제공된다.

`dataset = dataset.repeat(EPOCH)`

dataset.repeat() 안에 원하는 학습 반복 횟수인 epoch을 넣어주면 dataset 안에 존재하던 원소들이 설정횟수 만큼 증가한다. 예를들어 
(1,2,3,4)였다면 EPOCH를 2로 두고 repeat을 실행하면 (1,2,3,4,1,2,3,4) 이런식으로 데이터가 늘어난다

### 8.Mapping 

데이터를 준비했으면, 인공 신경망 입력층에 맞게 형태를 가공하는 것이다. 때론 인공신경망 입력이 두개 이상일 수 있는데, 이 경우에는 그냥 Dataset을 사용하여 하나씩 데이터를 사용할 수는 없다. 데이터를 입력층에 맞도록 mapping해줘야 한다.

~~~
def mapping_fn(X, Y=None):
    input = {'x':X}
    label = Y
    return input, label
~~~

`dataset = dataset.map(mapping_fn)`

`dataset.map` 함수를 사용하면 Mapping 이 가능하다. 중요한 것은 그 안에 넣어주는 callback function 로 callback function interface는 인공신경망 interface와 맞춰주면 된다. 위에서는 가장 기본적인 방식으로 변경한 것인데, 훈련데이터 X와 정답레이블 Y를 전해주는 것이 기본이고 Y의 경우 그냥 그대로 넣어주면 되는데 X는 dictionary type으로 감싸줘야 한다.

만일 X데이터를 2개 이상으로 하고 싶다면 다른걸 변경할 필요없이, callback function의 X인자 값을 변경해주면 된다  
example)

~~~
def mapping_fn(x1, x2, Y=None):
    input={'X1':X1, 'X2':X2}
    label = Y
    return input, label
 ~~~
 
 결국에 완성되는는 것은
 
 ~~~
 
 BATCH_SIZE = 2
EPOCH = 2

def mapping_fn(X,Y=None):
    input = {'x' : X}
    label = Y
    return input, label
    
dataset = tf.data.Dataset.from_tensor_slices((sequences, label))
dataset = dataset.map(mapping_fn)
dataset = dataset.shuffle(len(sequences))
dataset = dataset.batch(BATCH_SIZE)
dataset = dataset.repeat(EPOCH)
iterator = dataset.make_one_shot_iterator()
next_data = iterator.get_next()

with tf.Session() as sess:
    while True:
        try:
            print(sess.run(next_data))
        except tf.errors.OutOfRangeError:
            break        
  ~~~
            
위와 같이 만들어진다 
 









