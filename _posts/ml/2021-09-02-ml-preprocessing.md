---
title:  "[머신러닝] 데이터 전처리"
excerpt: "머신러닝 - 데이터 전처리"
date: 2021-09-02 09:36:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - sklearn
  - 전처리
---

올바른 결과 도출을 위해서 데이터를 사용하기 전에 데이터 전처리 과정을 거친다.

## 데이터 불러오기 

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
import numpy as np

fish_data = np.column_stack((fish_length, fish_weight))
print(fish_data[:5])
```
```
[[ 25.4 242. ]
 [ 26.3 290. ]
 [ 26.5 340. ]
 [ 29.  363. ]
 [ 29.  430. ]]
```

넘파이의 np.column_stack 함수를 이용해 전달받은 리스트를 일렬로 세운 다음 차례대로 나란히 연결한다. 연결할 리스트는 튜플로 전달한다.

```python
fish_target = np.concatenate((np.ones(35), np.zeros(14)))

print(fish_target)
```

```
[1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1.
 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
 0.]
```

np.ones()와 np.zeros()함수와 첫번째 차원의 배열을 연결하는 np.concatenate()함수를 사용해 타깃 데이터를 만든다. 이때 튜플로 연결할 리스트나 배열을 전달한다.

<br>
<br>

## 사이킷런으로 훈련, 테스트 세트 나누기

```python
from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(
    fish_data, fish_target, random_state=42)
```

사이킷런은 머신러닝 모델을 위한 알고리즘뿐만 아니라 다양한 유틸리티 도구도 제공한다. 대표적인 도구가 train_test_split함수이다. 이 함수는 전달되는 리스트나 배열을 비율에 맞게 훈련 세트와 테스트 세트로 나누어 준다. 나누기 전에 알아서 섞는다.

train_test_split 함수에는 자체적으로 랜덤 시드를 지정할 수 있는 random_state 매개변수가 있다. 

이 함수는 기본적으로 25%를 테스트 세트로 나눈다.

```python
print(train_input.shape, test_input.shape)
```
```
(36, 2) (13, 2)
```

```python
print(train_target.shape, test_target.shape)
```
```
(36,) (13,)
```

```python
print(test_target)
```
```
[1. 0. 0. 0. 1. 1. 1. 1. 1. 1. 1. 1. 1.]
```
도미와 빙어의 개수는 35:14 즉 2.5:1 이다. 하지만 위 코드를 보면 테스트 세트의 도미와 빙어의 비율은 3.3:1이다. 

```python
train_input, test_input, train_target, test_target = train_test_split(
    fish_data, fish_target, stratify=fish_target, random_state=42)
```
이를 해결하기 위해서 train_test_split함수에서 stratify 매개변수에 타깃 데이터를 전달하면 클래스 비율에 맞게 데이터를 나눈다.

훈련데이터가 작거나 특정 클래스의 샘플 개수가 적을 때 유용하다.

```python
print(test_target)
```
```
[0. 0. 1. 0. 1. 0. 1. 1. 1. 1. 1. 1. 1.]
```
그 결과 테스트 세트의 비율이 2.25:1이 되었다.

<br>
<br>

# 데이터 전처리

산점도를 그려보면 x축과 y축, 즉 길이와 무게 특성의 비율이 다르다. x축은 범위가 좁고, y축은 범위가 넓다. 따라서 y축으로 조금만 멀어져도 거리가 아주 큰 값으로 계산되어, k-최근접 이웃 알고리즘으로는 정확한 결과를 내기 어렵다.

이러한 현상을 두 특성의 **스케일**(scale)이 다르다고도 말한다.

그래서 데이터 전처리(data preprocessing)을 해야 한다.

가장 널리 사용하는 전처리 방법 중 하나는 표준점수(standard score)이다.
표준점수는 각 특성값이 0에서 표준편차의 몇 배만큼 떨어져 있는지를 나타낸다.
이를 통해 실제 특성값의 크기와 상관없이 동일한 조건으로 비교할 수 있다.

```python
mean = np.mean(train_input, axis=0)
std = np.std(train_input, axis=0)

print(mean, std)
```
```
[ 27.29722222 454.09722222] [  9.98244253 323.29893931]
```
np.mean함수는 평균을 계산하고, np.std함수는 표준편차를 계산한다. train_input은 (36,2) 크기의 배열로 특성마다 값의 스케일이 다르므로 평균과 표준편차는 각 특성별로 계산해야 한다. 이를 위해 axis=0으로 지정했다. 이렇게 하면 행을 따라 각 열의 통계 값을 계산한다.


```python
train_scaled = (train_input - mean) / std
```
원본 데이터에서 평균을 빼고 표준편차로 나누어 표준점수로 변환한다.

넘파이를 통해 train_input의 모든 행에서 mean에 있는 두 평균값을 빼주고 std에 있는 두 표준편차를 다시 모든 행에 적용한다. 이런기능을 브로드캐스팅(broadcasting)이라고 부른다.

```python
kn.fit(train_scaled, train_target)

test_scaled = (test_input - mean) / std

kn.score(test_scaled, test_target)
```
```
1.0
```
훈련을 마치고 테스트세트도 훈련 세트의 평균과 표준편차로 변환해야 한다.

```python
new = ([25, 150] - mean) / std

print(kn.predict([new]))
```
```
[1.]
```
도미를 정확히 예측한다.