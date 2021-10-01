---
title:  "[머신러닝] 훈련세트, 테스트세트 - K-최근접 이웃 알고리즘"
excerpt: "머신러닝 - 훈련세트, 테스트세트 - K-최근접 이웃 알고리즘"
date: 2021-09-01 14:40:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - sklearn
---

# 지도학습과 비지도학습

머신러닝 알고리즘은 크게 지도학습(supervised learning)과 비지도학습(unsupervised learning)으로 나눌 수 있다.

**지도학습**에는 데이터와 정답을 입력과 타킷이라고 하고, 이 둘을 합쳐 훈련 데이터(training data)라고 부른다. K-최근접 이웃 알고리즘은 지도학습이다.

**머신러닝 알고리즘에는 지도학습, 비지도학습 외에 강화학습도 있다.**

<br>
<br>

# 훈련 세트와 테스트 세트

시험을 보기전에 전에 출제된 시험 문제와 정답을 미리 알려주고 시험을 본다면 의미가 없다. 그래서 머신러닝에서도 도미와 빙어의 데이터와 타깃을 주고 훈련한 다음, 같은 데이터로 테스트한다면 모두 맞히는 것이 당연하다.

이를 해결하기 위해 평가를 위해 또 다른 데이터를 준비하거나 이미 준비된 데이터 중에서 일부를 떼어 내어 활용하는 것이다. 일반적으로 후자의 경우가 많다. 평가에 사용하는 데이터를 테스트 세트, 훈련에 사용되는 데이터를 훈련세트라고 부른다.

```python
fish_length = [25.4, 26.3, 26.5, 29.0, 29.0, 29.7, 29.7, 30.0, 30.0, 30.7, 31.0, 31.0, 
                31.5, 32.0, 32.0, 32.0, 33.0, 33.0, 33.5, 33.5, 34.0, 34.0, 34.5, 35.0, 
                35.0, 35.0, 35.0, 36.0, 36.0, 37.0, 38.5, 38.5, 39.5, 41.0, 41.0, 9.8, 
                10.5, 10.6, 11.0, 11.2, 11.3, 11.8, 11.8, 12.0, 12.2, 12.4, 13.0, 14.3, 15.0]
fish_weight = [242.0, 290.0, 340.0, 363.0, 430.0, 450.0, 500.0, 390.0, 450.0, 500.0, 475.0, 500.0, 
                500.0, 340.0, 600.0, 600.0, 700.0, 700.0, 610.0, 650.0, 575.0, 685.0, 620.0, 680.0, 
                700.0, 725.0, 720.0, 714.0, 850.0, 1000.0, 920.0, 955.0, 925.0, 975.0, 950.0, 6.7, 
                7.5, 7.0, 9.7, 9.8, 8.7, 10.0, 9.9, 9.8, 12.2, 13.4, 12.2, 19.7, 19.9]
```

```python

fish_data = [[l, w] for l, w in zip(fish_length, fish_weight)]
fish_target = [1]*35 + [0]*14
```

```python
from sklearn.neighbors import KNeighborsClassifier

kn = KNeighborsClassifier()
```

```python
train_input = fish_data[:35]
train_target = fish_target[:35]

test_input = fish_data[35:]
test_target = fish_target[35:]
```

```python
kn = kn.fit(train_input, train_target)
kn.score(test_input, test_target)
```
```
0.0
```

슬라이싱 연산으로 인덱스 0~34까지 처음 35개 샘플을 훈련 세트로 선택했고, 인덱스 35~48까지 나머지 14개 샘플을 테스트 세트로 선택했다.

그결과 정확도가 0.0이다.

그 이유는 **샘플링 편향**이 일어났기 때문이다.
훈련데이터와 테스트데이터에 도미와 빙어가 골고루 섞여 있어야 한다.

```python
import numpy as np

input_arr = np.array(fish_data)
target_arr = np.array(fish_target)

print(input_arr.shape)
```

```
(49, 2)
```

numpy배열로 만들고 샘플수, 특성 수를 확인할 수 있다.


```python
np.random.seed(42)
index = np.arange(49)
np.random.shuffle(index)

print(index)
```
```
[13 45 47 44 17 27 26 25 31 19 12  4 34  8  3  6 40 41 46 15  9 16 24 33
 30  0 43 32  5 29 11 36  1 21  2 37 35 23 39 10 22 18 48 20  7 42 14 28
 38]
```
seed()는 넘파이에서 난수를 생성하기 위한 정수 초깃값을 지정한다. 초깃값이 같으면 동일한 난수를 뽑을 수 있다. 따라서 랜덤 함수의 결과를 동일하게 재현하고 싶을 때 사용한다.

넘파이에서 무작위 결과를 만드는 함수들은 실행할 때마다 다른 결과를 만든다. 일정한 결과를 얻으려면 초기에 랜덤시드(random seed)를 지정하면 된다.

넘파이 arange 함수에 정수N을 전달하면 0부터 N-1까지 1씩 증가하는 배열을 만든다.
넘파이 random 패키지 아래에 있는 shuffle함수는 주어진 배열을 무작위로 섞는다.

```python
train_input = input_arr[index[:35]]
train_target = target_arr[index[:35]]

test_input = input_arr[index[35:]]
test_target = target_arr[index[35:]]
```
훈련데이터와 테스트데이터를 만들었다.


```python
kn = kn.fit(train_input, train_target)

kn.score(test_input, test_target)
```
```
1.0
```
fit메소드를 실행할 때마다 KNeighborsClassfier 클래스의 객체는 이전에 학습한 모든 것을 잃어버린다.


```python
kn.predict(test_input)
```
```
array([0, 0, 1, 0, 1, 1, 1, 0, 1, 1, 0, 1, 1, 0])
```

```python
test_target
```
```
array([0, 0, 1, 0, 1, 1, 1, 0, 1, 1, 0, 1, 1, 0])
```

predict 메소드로 테스트 세트의 예측 결과와 실제 타깃이 일치함을 확인할 수 있다.