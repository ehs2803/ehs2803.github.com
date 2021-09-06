---
title:  "[머신러닝] 다중분류, 로지스틱회귀"
excerpt: "머신러닝 - 다중분류, 로지스틱회귀"
date: 2021-09-06 09:10:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - sklearn
  - 다중분류
  - 로지스틱회귀
---


럭키백에 들어있는 생선 7개가 있을 때, 럭키백에 들어간 생선의 크기, 무게 등이 주어졌을 때 7개 생선에 대한 확률을 출력해야 한다.

이러한 문제를 다중분류(multi class classification)이라고 한다.

# 데이터 준비하기

```python
import pandas as pd

fish = pd.read_csv('https://bit.ly/fish_csv_data')
fish.head()
```
판다스의 read_csv 함수로 csv 파일으 데이터프레임으로 변환한 다음 head 메소드로 처음 5개 행을 출력한다.

데이터프레임은 판다스에서 제공하는 2차원 표 형식의 주요 데이터 구조이다. 데이터프레임은 넘파이 배열과 비슷하게 열과 행으로 이루어져 있다. 데이터프레임은 통계와 그래프를 위한 메소드를 풍부하게 제공한다. 또 넘파이로 상호변환이 쉽고 사이킷런과도 잘 호환된다.

```python
print(pd.unique(fish['Species']))
```
```
['Bream' 'Roach' 'Whitefish' 'Parkki' 'Perch' 'Pike' 'Smelt']
```
판다수의 unique 함수를 사용해 생선의 종 7개를 출력해본다.

```python
fish_input = fish[['Weight','Length','Diagonal','Height','Width']].to_numpy()

print(fish_input[:5])
```
```
[[242.      25.4     30.      11.52     4.02  ]
 [290.      26.3     31.2     12.48     4.3056]
 [340.      26.5     31.1     12.3778   4.6961]
 [363.      29.      33.5     12.73     4.4555]
 [430.      29.      34.      12.444    5.134 ]]
```
데이터프레임에서 여러 열을 선택하면 새로운 데이터프레임이 반환된다. 이를 to_numpy 메소드로 넘파이 배열로 변환한다.

```python
fish_target = fish['Species'].to_numpy()

from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(
    fish_input, fish_target, random_state=42)


from sklearn.preprocessing import StandardScaler

ss = StandardScaler()
ss.fit(train_input)
train_scaled = ss.transform(train_input)
test_scaled = ss.transform(test_input)
```
타깃 데이터를 만들고, 데이터를 훈련세트와 테스트세트로 나눈다. 그 후 StandardScaler 클래스를 사용해 훈련세트와 테스트세트를 표준화 전처리한다. 훈련세트의 통계 값으로 테스트 세트를 변환해야 한다.

<br>
<br>

# K-최근접 이웃 분류기의 확률 예측


```python
from sklearn.neighbors import KNeighborsClassifier

kn = KNeighborsClassifier(n_neighbors=3)
kn.fit(train_scaled, train_target)

print(kn.score(train_scaled, train_target))
print(kn.score(test_scaled, test_target))
```
```
0.8907563025210085
0.85
```
다중분류에서 타깃값을 이진분류처럼 1,0으로 바꿀 수 있지만 사이킷런에서는 문자열로 된 타깃값을 그대로 사용할 수 있다.

이때 주의할 점은 타깃값을 그대로 사이킷런 모델에 전달하면 순서가 자동으로 알파벳 순으로 매겨진다.

```python
print(kn.classes_)
```
```
['Bream' 'Parkki' 'Perch' 'Pike' 'Roach' 'Smelt' 'Whitefish']
```
classes_속성을 보면 알파벳순으로 정렬되어 있다.

```python
print(kn.predict(test_scaled[:5]))
```
```
['Perch' 'Smelt' 'Pike' 'Perch' 'Perch']
```
테스트세트에 있는 처음 5개 샘플의 타깃값을 예측해보면 다음과 같이 나온다.
사이킷런의 분류 모델은 predict_proba 메소드로 클래스별 확률값을 반환한다.

```python
import numpy as np

proba = kn.predict_proba(test_scaled[:5])
print(np.round(proba, decimals=4))
```
```
[[0.     0.     1.     0.     0.     0.     0.    ]
 [0.     0.     0.     0.     0.     1.     0.    ]
 [0.     0.     0.     1.     0.     0.     0.    ]
 [0.     0.     0.6667 0.     0.3333 0.     0.    ]
 [0.     0.     0.6667 0.     0.3333 0.     0.    ]]
```
predict_proba 메소드의 출력 순서는 classes_ 속성과 같다.

<br>
<br>

# 로지스틱 회귀

로지스틱 회귀(logistic regression)는 이름은 회귀이지만 분류 모델이다.
이 알고리즘은 선형 회귀와 동일하게 선형 방정식을 학습한다.


```
z = a * (weight) + b * (length) + c * (diagonal) + d * (height) + e * (width) + f
```
위 방정식에서 a,b,c,d,e는 가중치 혹은 계수이다. 특성은 늘어났지만 다중 회귀를 위한 선형 방정식과 같다. z는 어떠한 값도 가능하다. 확률은 0~1 사이값이므로 z가 아주 큰 음수일때 0이 되고, z가 아주 큰 양수일 때 1이 되도록 시그모이드함수(sigmoid function)(또는 로지스틱 함수)를 사용하면 가능하다.


이진 분류의 경우 시그모이드 함수의 출력이 0.5보다 크면 양성 클래스, 작으면 음성 클래스로 분류한다.

딱 0.5일 때는 라이브러리마다 다를 수 있지만, 사이킷런은 0.5일 때 음성 클래스로 분류한다.

### 1. 로지스틱 회귀로 이진 분류

```python
bream_smelt_indexes = (train_target == 'Bream') | (train_target == 'Smelt')
train_bream_smelt = train_scaled[bream_smelt_indexes]
target_bream_smelt = train_target[bream_smelt_indexes]
```
도미와 빙어로 2진 분류를 할 때 비교 연산자를 사용해 도미와 빙어의 행을 모두 True로 만들면 된다.

```python
from sklearn.linear_model import LogisticRegression

lr = LogisticRegression()
lr.fit(train_bream_smelt, target_bream_smelt)

print(lr.predict(train_bream_smelt[:5]))
```
```
['Bream' 'Smelt' 'Bream' 'Bream' 'Bream']
```
훈련한 모델을 사용해 train_bream_smelt에 있는 처음 5개 샘플을 예측해봤다.


```python
print(lr.predict_proba(train_bream_smelt[:5]))
```
```
[[0.99759855 0.00240145]
 [0.02735183 0.97264817]
 [0.99486072 0.00513928]
 [0.98584202 0.01415798]
 [0.99767269 0.00232731]]
```
predict_proba 메소드를 통해 확률을 출력해보면 정상적으로 결과가 출력된 것을 알 수 있다.

```python
print(lr.classes_)
```
```
['Bream' 'Smelt']
```
타깃값을 알파벳순으로 정렬한다. 그래서 빙어가 양성클래스이다.

```python
print(lr.coef_, lr.intercept_)
```
```
[[-0.4037798  -0.57620209 -0.66280298 -1.01290277 -0.73168947]] [-2.16155132]
```
로지스틱 회귀가 학습한 계수이다.

```python
decisions = lr.decision_function(train_bream_smelt[:5])
print(decisions)
```
```
[-6.02927744  3.57123907 -5.26568906 -4.24321775 -6.0607117 ]
```
LogisticRegression 모델로 z값을 예측해보면 다음과 같다. 이 z값을 시그모이드 함수에 통과시키면 확률을 얻을 수 있다.

```python
from scipy.special import expit

print(expit(decisions))
```
```
[0.00240145 0.97264817 0.00513928 0.01415798 0.00232731]
```
출력된 값을 보면 predict_proba 메소드 출력의 두번째 열의 값과 동일하다. 즉 decision_function 메소드는 양성 클래스에 대한 z값을 반환한다.

### 2. 로지스틱 회귀로 다중 분류


```python
lr = LogisticRegression(C=20, max_iter=1000)
lr.fit(train_scaled, train_target)

print(lr.score(train_scaled, train_target))
print(lr.score(test_scaled, test_target))
```
```
0.9327731092436975
0.925
```
LogisticRegression 클래스는 기본적으로 반복적인 알고리즘을 사용한다. max_iter 매개변수에서 반복 횟수를 지정하며 기본값은 100이다.

또 기본적으로 릿지 회귀와 같이 계수의 제곱을 규제한다. 이런 규제를 **L2규제**라고도 부른다. 규제를 제어하는 매개변수는 C이고 작을수록 규제가 커진다. C의 기본값은 1이다.

```python
print(lr.classes_)
```
```
['Bream' 'Parkki' 'Perch' 'Pike' 'Roach' 'Smelt' 'Whitefish']
```
타깃은 알파벳순으로 정렬되어 있다.

```python
print(lr.predict(test_scaled[:5]))
```
```
['Perch' 'Smelt' 'Pike' 'Roach' 'Perch']
```
테스트 세트의 처음 5개 샘플에 대한 예측이다.

```python
proba = lr.predict_proba(test_scaled[:5])
print(np.round(proba, decimals=3))
```
```
[[0.    0.014 0.841 0.    0.136 0.007 0.003]
 [0.    0.003 0.044 0.    0.007 0.946 0.   ]
 [0.    0.    0.034 0.935 0.015 0.016 0.   ]
 [0.011 0.034 0.306 0.007 0.567 0.    0.076]
 [0.    0.    0.904 0.002 0.089 0.002 0.001]]
```
예측 확률이다.

```python
print(lr.coef_.shape, lr.intercept_.shape)
```
```
(7, 5) (7,)
```
다중 분류의 선형 방정식을 확인할 수 있다. 이 데이터는 5개의 특성을 확인하므ㅏ로 coef_ 배열의 열은 5개이다. 그런데 행이 7개이다. intercept_도 7개이다. 이말은 이진 분류에서 보았던 z를 7개나 계산한다는 의미이다. 다중 분류는 클래스마다 z값을 하나씩 계산한다. 이중 가장 높은 z값을 출력하는 클래스가 예측 클래스가 된다. 다중 분류에서는 시그모이드함수가 아닌 **소프트맥스**(softmax)함수를 사용해 7개의 z값을 확률로 변환한다.

소프트맥스함수는 여러개의 선형 방정식의 출력값을 0~1사이로 압축하고 전체합이 1이 되도록 만든다. 이를 위해 지수 함수를 사용하기 때문에 **정규화된 지수 함수**라고도 부른다.


```python
decision = lr.decision_function(test_scaled[:5])
print(np.round(decision, decimals=2))
```
```
[[ -6.5    1.03   5.16  -2.73   3.34   0.33  -0.63]
 [-10.86   1.93   4.77  -2.4    2.98   7.84  -4.26]
 [ -4.34  -6.23   3.17   6.49   2.36   2.42  -3.87]
 [ -0.68   0.45   2.65  -1.19   3.26  -5.75   1.26]
 [ -6.4   -1.99   5.82  -0.11   3.5   -0.11  -0.71]]
```
테스트 세트의 처음 5개 샘플에 대한 z1~z7의 값을 구한다.

```python
from scipy.special import softmax

proba = softmax(decision, axis=1)
print(np.round(proba, decimals=3))
```
```
[[0.    0.014 0.841 0.    0.136 0.007 0.003]
 [0.    0.003 0.044 0.    0.007 0.946 0.   ]
 [0.    0.    0.034 0.935 0.015 0.016 0.   ]
 [0.011 0.034 0.306 0.007 0.567 0.    0.076]
 [0.    0.    0.904 0.002 0.089 0.002 0.001]]
```
앞서 구한 decision 배열을 softmax 함수에 전달한다. softmax의 axis 매개변수는 소프트맥스를 계산할 축을 지정한다. 여기에서는 1로 지정하여 각 행, 즉 각 샘플에 대한 소프트맥스를 계산한다. 만약 axis 매개변수를 지정하지 않으면 배열 전체에 대해 소프트맥스를 계산한다.