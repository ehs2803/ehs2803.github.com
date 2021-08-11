---
title:  "[딥러닝스터디] 3. 신경망"
excerpt: "신경망, 활성화함수"
date: 2021-08-07 16:55:00
categories:
  - 딥러닝스터디
  - 딥러닝

tags:
  - 딥러닝스터디
  - 퍼셉트론
---

<br>
<br>

퍼셉트론의 장점은 복잡한 함수를 표현할 수 있지만
**단점**은 가중치를 사람이 수동으로 해야 한다는 것이다. 
이를 해결하는 방법으로 **신경망**이 있다.

신경망은 가중치 매개변수의 적절한 값을 데이터로부터 자동으로 학습하는 능력이 있다.

<br>
<br>

# 신경망

신경망은 입력층, 은닉층, 출력층으로 구성된다.
은닉층은 입력층이나 출력층과 달리 사람 눈에 보이지 않는다.

신경망은 모두 3층으로 구성된다. 가중치를 갖는 층은 2개뿐이라 '2층 신경망'이라고도 한다. 문헌에 따라서는 신경망을 구성하는 층수를 기준으로 '3층 신경망'이라고 하는 경우가 있다.

<br>
<br>

# 활성화 함수

앞서 배운 퍼셉트론 식에서 **y = h(b + w1x1 + w2x2)** 다음과 같이 바꿔보았다.
달라진 점은 h(x)라는 함수가 등장했다. 이처럼 입력 신호의 총합을 출력신호로 변환하는 함수를 일반적으로 활성화함수(activation function)이라고 한다.

이러한 활성화함수가 퍼셉트론에서 신경망으로 가기위한 시작이다.

활성화함수는 임계값을 경계로 출력이 바뀌는데, 이런 함수를 계단 함수(step function)이라고 한다.
퍼셉트론에서는 활성화 함수로 계단함수를 이용한다.

신경망에서 사용하는 활성화함수는 sigmoid, relu 함수가 있다.

#### 1. 시그모이드 함수 (sigmoid function)

h(x) = 1 / (1 + exp(-x))

위 식이 시그모이드 함수이다.
신경망에서는 활성화 함수로 시그모이드 함수를 이용해 신호를 변환하고, 그 변화된 신호를 다음 뉴런에 전달한다.

```python
import numpy as np

def sigmoid(x):
    return 1 / (1 + np.exp(-x)) 
```


#### 2. ReLU 함수 (Rectified Linear Unit)

시그모이드 함수는 신경망 분야에서 오래전부터 이용해왔으나, 최근에는 ReLU 함수를 주로 사용한다.

relu 함수는 입력이 0을 넘으면 그 입력을 그대로 출력하고, 0 이하이면 0을 출력하는 함수이다.

```python
import numpy as np

def relu(x):
    return np.maximum(0, x) 
```

#### 3. 계단함수 vs sigmoid vs relu

![activationfunction](/img/activationfunction.PNG?raw=true)

계단함수와 시그모이드 함수를 비교해서 계단함수는 0 or 1을 반환하는 반면 시그모이드 함수는 실수를 돌려준다. 즉 연속적인 실수가 신경망에 흐를 수 있다. 시그모이드 함수는 그래프가 매끈하다. 이 매끈함이 신경망 학습에서 아주 중요한 역할을 한다.

두 함수의 공통점이라면 큰관점에서 같은 모양을 하고 있다. 계단함수, 시그모이드 함수 모두 입력이 작을 때의 출력은 0에 가깝고(혹은 0), 입력이 커지면 출력은 1에 가까워진다(혹은1). 즉 두개함수 모두 입력이 중요하면 큰 값을 출력하고 입력이 중요하지 않으면 작은 값을 출력한다. 
입력이 아무리 작거나 커도 출력은 0과 1 사이라는 것도 공통점이다.

계단함수와 시그모이드 함수의 공통점은 직선 한개로 표현 못하는 비선형 함수라는 것이다.

신경망에서는 활성화 함수로 **비선형 함수**를 사용해야 한다. 선형 함수를 사용하면 신경망의 층을 깊게 하는 의미가 없어진다. 그래서 층을 쌓는 해택을 얻고 싶다면 활성화 함수로 반드시 비선형 함수를 사용해야 한다.

<br>
<br>

# 3층 신경망 구현

넘파이 다차원 배열을 사용해 3층 신경망(입력층, 첫번째 은닉층, 두번째 은닉층, 출력층)을 구현할 수 있다.

```python
def init_networt():
    network = {}
    network['W1'] = np.array([[0.1, 0.3, 0.5], [0.2, 0.4, 0.6]])
    network['b1'] = np.array([0.1, 0.2, 0.3])
    network['W2'] = np.array([[0.1, 0.4], [0.2, 0.5], [0.3, 0.6]])
    network['b2'] = np.array([0.1, 0.2])
    network['W3'] = np.array([[0.1, 0.3], [0.2, 0.4]])
    network['b3'] = np.array([0.1, 0.2])

    return network

def forward(network, x):
    W1, W2, W3 = network['W1'], network['W2'], network['W3']
    b1, b2, b3 = network['b1'], network['b2'], network['b3']

    a1 = np.dot(x, w1) + b1
    z1 = sigmoid(a1)
    a2 = np.dot(z1, W2) + b2
    z2 = sigmoid(a2)
    a3 = np.dot(z2, W3) + b3
    y = identity_function(a3) # 항등함수

    return y

network = init_network()
x = np.array([1.0, 0.5])
y = forward(network, x)
print(y) # [0.31682708 0.69627909]
```

init_network() 함수는 가중치와 편향을 초기화하고 이들을 딕셔너리 변수인 network에 저장했다. forward() 함수는 입력 신호를 출력으로 변환하는 처리 과정을 모두 구현한다. forward라는 함수이름은 신호가 순방향으로 전달됨(순전파)을 알리기 위함이다.

np.dot은 내적 계산을 한다.

출력층의 활성화 함수는 풀고자 하는 문제의 성질에 맞게 정해진다.
회귀에는 항등함수, 2진 분류에는 시그모이드 함수, 다중 클래스 분류에는 소프트맥스 함수를 사용하는 것이 일반적이다. 

<br>
<br>

# 출력층 설계

신경망은 분류와 회귀 모두에 이용할 수 있다. 
분류는 데이터가 어느 클래스에 속하느냐는 문제이고, 회귀는 입력 데이터에서 연속적인 수치를 예측하는 문제이다. 사진 속 인물의 몸무게를 예측하는 문제가 회귀이다.
일반적으로 회귀에는 항등함수를, 분류에는 소프트맥스 함수를 사용한다.

#### 항등 함수

항등함수(identify function)는 입력을 그대로 출력한다. 출력층에서 항등함수를 사용하면 입력신호가 그대로 출력신호가 된다. 

#### 소프트맥스 함수

소프트맥스 함수(softmax function)

```python
def softmax(a):
    exp_a = np.exp(a)
    sum_exp_a = np.sum(exp_a)
    y = exp_a / sum_exp_a

    return y
```
소프트맥스 함수를 구현할 때 오버플로 문제가 발생한다. 지수 함수를 사용하기 때문에 큰 값끼리 나눗셈을 하면 결과 수치가 불안정해진다.

```python
def softmax(a):
    c = np.max(a)
    exp_a = np.exp(a-c) # 오버플로 대책
    sum_exp_a = np.sum(exp_a)
    y = exp_a / sum_exp_a

    return y
```

오버플로 문제를 해결한 소프트맥스함수 코드이다.

```python
>>> a = np.array([0.3, 2.9, 4.0])
>>> y = softmax(a)
>>> print(y)
[0.01821127 0.24519181 0.73659691]
>>> np.sum(y)
1
```
소프트맥스 함수의 출력은 0~1 사이의 실수이다. 또 출력의 총값은 항상 1이다.
이러한 성질때문에 소프트맥스 함수의 출력을 **확률**로 해석할 수 있다.

위 코드에서 y[0]은 1.8%, y[1]은 24.5%, y[2]는 73.7%로 해석이 가능하다.
이러한 확률로 y[2]클래스에 속한다라고 결론을 낼 수 있다.

여기서 주의할 점은 소프트맥스 함수를 적용해도 각 원소의 대소 관계는 변하지 않는다. 지수함수가 단조 증가함수이기 때문이다. 실제로 위 코드를 보면 a의 원소들 사이의 대소관계와 y의 원소들 사이의 대소관계가 일치한다.

신경망을 이용한 분류에서는 일반적으로 가장 큰 출력을 내는 뉴런에 해당하는 클래스로만 인식한다. 그리고 소프트맥스 함수를 적용해도 출력이 가장 큰 뉴런의 위치는 달라지지 않는다. 
**결과적으로 신경망으로 분류할 때 출력층의 소프트맥스 함수를 생략해도 된다. 
현업에서도 지수 함수 계산에 드는 자원 낭비를 줄이고자 출력층의 소프트맥스 함수는 생략하는 것이 일반적이다.**

<br>
<br>

# 손글씨 숫자 인식

손글씨 숫자 인식의 추론 과정을 신경망의 순전파(foward propagatioin)이라고도 한다.

신경망은 기계학습과 마찬가지로 두단계를 거쳐 문제를 해결한다. 먼저 훈련 데이터를 사용해 가중치 매개변수를 학습하고, 추론 단계에서는 앞서 학습한 매개변수를 사용해 입력데이터를 분류한다.

#### MNIST 데이터셋

데이터셋 mnist는 손글씨 숫자 이미지 집합으로 기계학습 분야에서 아주 유명하다.
mnist 데이터셋은 0부터 9까지의 숫자 이미지로 구성된다.
훈련이미지 60000장, 시험 이미지 10000장으로 구성된다.
일반적으로 훈련 이미지를 사용해 모델을 학습하고, 학습한 모델로 시험 이미지들을 얼마나 정확하게 분류하는지를 평가한다.

mnist 이미지 데이터는 28*28 크기의 회색조 이미지(1채널)이며, 각 픽셀은 0에서 255까지의 값을 취한다.

```python
import sys, os
sys.path.append(os.pardir)  # 부모 디렉터리의 파일을 가져올 수 있도록 설정
from dataset.mnist import load_mnist

(x_train, t_train), (x_test, t_test) = load_mnist(flatten=True, normalize=False)

print(x_train.shape) # (60000, 784)
print(t.train.shape) # (60000, )
print(x_test.shape)  # (10000, 784)
print(t_test.shape)  # (10000, )
```
load_mnist 함수는 인수로 normalize, flatten, one_hot_label 세가지를 설정할 수 있다. 세 인수 모두 bool 값이다. 
파이썬에서는 pickle이라는 편리한 기능이 있다. 이는 프로그램 실행 중에 특정 객체를 파일로 저장하는 기능이다. 저장해둔 pickle 파일을 로드하면 실행 당시의 객체를 즉시 복원할 수 있다. load_mnist함수에서도 pickle을 이용해 mnist 데이터를 순식간에 준비할 수 있다.

첫번째 인수 normalize는 입력 이미지의 픽셀 값을 0.0~1.0사이의 값으로 **정규화**할지를 정한다. False로 정하면 입력 이미지의 픽셀은 원래 값 0~255 사이의 값을 유지한다. 

두번째 인수 flatten은 입력 이미지를 평탄하게, 즉 1차원 배열로 만들지를 정한다. False로 설정하면 입력이미지를 1*28*28의 3차원 배열로, True로 설정하면 784개의 원소로 이루어진 1차원 배열로 저장한다.

세번째 인수 one_hot_label은 레이블을 원-핫 인코딩(one-hot-encoding) 형태로 저장할지를 정한다. 원-핫 인코딩이란 [0,0,1,0,0,0,0,0,0,0] 처럼 정답을 뜻하는 원소만 1이고 나머지는 모두 0인 배열이다. 만약 false로 설정하면 '7'이나 '3'과 같이 숫자 형태의 레이블을 저장한다.


```python
import sys, os
sys.path.append(os.pardir)  # 부모 디렉터리의 파일을 가져올 수 있도록 설정
import numpy as np
from dataset.mnist import load_mnist
from PIL import Image


def img_show(img):
    pil_img = Image.fromarray(np.uint8(img))
    pil_img.show()

(x_train, t_train), (x_test, t_test) = load_mnist(flatten=True, normalize=False)

img = x_train[0]
label = t_train[0]
print(label)  # 5

print(img.shape)  # (784,)
img = img.reshape(28, 28)  # 형상을 원래 이미지의 크기로 변형
print(img.shape)  # (28, 28)

img_show(img)
```
위 코드에서 flatten=True로 설정했기 때문에 reshape함수를 통해 원래 형상으로 변경해야 한다. 또 넘파이로 저장된 이미지 데이터를 PIL용 데이터 객체로 변환해야해서 이미지 변환은 Image.fromarray()가 수행하게 된다.

#### 신견망의 추론 처리

신경망은 입력층 뉴런을 784개, 출력층 뉴런은 10개로 구성된다.
입력층 뉴런이 784개인 이유는 이미지크기 28*28=754이고, 0~9 숫자를 분류하는 문제이므로 출력층 뉴런은 10개이다.

은닉층은 총 두개로, 첫번째 은닉층은 50개뉴런, 두번째 은닉층은 100개의 뉴런을 배치한다. 여기서 50개와 100개의 뉴런 숫자는 임의로 정한것으로 특별한 의미는 없다. 

```python
import sys, os
sys.path.append(os.pardir)  # 부모 디렉터리의 파일을 가져올 수 있도록 설정
import numpy as np
import pickle
from dataset.mnist import load_mnist
from common.functions import sigmoid, softmax


def get_data():
    (x_train, t_train), (x_test, t_test) = load_mnist(normalize=True, flatten=True, one_hot_label=False)
    return x_test, t_test


def init_network():
    with open("sample_weight.pkl", 'rb') as f:
        network = pickle.load(f)
    return network


def predict(network, x):
    W1, W2, W3 = network['W1'], network['W2'], network['W3']
    b1, b2, b3 = network['b1'], network['b2'], network['b3']

    a1 = np.dot(x, W1) + b1
    z1 = sigmoid(a1)
    a2 = np.dot(z1, W2) + b2
    z2 = sigmoid(a2)
    a3 = np.dot(z2, W3) + b3
    y = softmax(a3)

    return y


x, t = get_data()
network = init_network()
accuracy_cnt = 0
for i in range(len(x)):
    y = predict(network, x[i])
    p= np.argmax(y) # 확률이 가장 높은 원소의 인덱스를 얻는다.
    if p == t[i]:
        accuracy_cnt += 1

print("Accuracy:" + str(float(accuracy_cnt) / len(x)))
```
init_network함수는 pickle 파일인 sample_weight.pkl에 저장된 학습된 가중치 매개변수를 읽는다. 이 파일에는 가중치와 편향 매개변수가 딕셔너리 변수로 저장되어 있다.

get_data함수에서 normalize를 true로 설정했다. 이처럼 데이터를 특정 범위로 변환하는 처리를 **정규화(normalization)**이라고 하고, 신경망의 입력 데이터에 특정 변환을 가하는 것을 **전처리(pre-processing)**이라고 한다. 여기에서 입력 이미지 데이터에 대한 전처리 작업으로 정규화를 수행한 것이다. 
현업에서는 전처리로 데이터 전체 평균과 표준편차를 이용해 데이터들을 0을 중심으로 분포하도록 이동하거나 데이터의 확산범위를 제한하는 정규화를 수행한다. 그 외에도 전체 데이터를 균일하게 분포시키는 데이터 백색화 등도 있다.

for문을 통해 x에 저장된 이미지 데이터를 한장씩 꺼내 predict메소드를 통해 분류한다. predict 함수는 각 레이블의 확률을 넘파이 배열로 반환한다.
예를 들어 [0.1, 0.3, 0.2, ... 0.04] 이런식으로 반환한다. 그후 넘파이 모듈의 argmax를 통해 가장 큰원소의 인덱스를 구한다.
신경망이 예측한 값과 정답 레이블을 비교해 맞으면 accuracy_cnt를 증가시켜 전체 이미지 숫자로 나눠 정확도를 구한다.
