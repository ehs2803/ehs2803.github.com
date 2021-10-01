---
title:  "[머신러닝] 선형회귀, 다항회귀"
excerpt: "머신러닝 - 선형회귀, 다항회귀"
date: 2021-09-04 09:34:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - sklearn
  - 선형회귀
  - 다항회귀
 
---

# k-최근접 이웃의 한계

50cm 농어의 무게를 예측해보면, 1033g을 예측한다. 산점도를 보면 길이가 커질 수록 농어의 무게가 증가하므로 당여한 결과이다.

하지만 100cm 농어의 무게를 예측하면, 여전히 1033g을 예측한다.

이유는 k-최근접 이웃 알고리즘은 샘플들의 무게를 평균을 하기 때문에 이 문제를 해결하기 위해서는 가장 큰 농어가 포함되도록 훈련 세트를 다시 만들어야 한다.

그래서 머신러닝 모델은 주기적으로 훈련해야 한다. 머신러닝 모델은 한 번 만들고 끝나는 프로그램이 아니다. 시간과 환경이 변하면서 데이터도 바뀌기 때문에 주기적으로 새로운 훈련 데이터로 모델을 다시 훈련해야 한다.

<br>
<br>

# 선형 회귀

선형 회귀(linear regression)는 널리 사용되는 대표적인 회귀 알고리즘이다. 비교적 간단하고 성능이 뛰어나다.

```python
from sklearn.linear_model import LinearRegression

lr = LinearRegression()
# 선형 회귀 모델 훈련
lr.fit(train_input, train_target)

# 50cm 농어에 대한 예측
print(lr.predict([[50]]))
```
```
[1241.83860323]
```
사이킷런은 sklearn.linear_model 패키지 아래 LinearRegression 클래스로 선형 회귀 알고리즘을 구현해 놓았다.

하나의 직선을 그리려면 기울기와 절편이 있어야 한다. y = a * x + b 처럼 쓸 수 있다. 여기에서 x를 농어의 길이, y를 농어의 무게이다.
여기서 LinearRegression 클래스가 a, b를 찾는다.


```python
print(lr.coef_, lr.intercept_)
```
```
[39.01714496] -709.0186449535477
```
위 코드로 그 a, b를 확인할 수 있다. coef_와 intercept_를 머신러닝 알고리즘이 찾은 값이라는 의미로 모델 파리미터라고 부른다. 이를 **모델 기반 학습**이라고 부른다. 앞서 사용한 k-최근접 이웃에는 모델 파라미터가 없다. 훈련 세트를 저장하는 것이 훈련의 전부이다. 이를 **사례 기반 학습**이라고 한다.

이제 훈련 세트 범위에서 벗어난 농어의 무게도 예측할 수 있다.

```python
print(lr.score(train_input, train_target))
print(lr.score(test_input, test_target))
```
```
0.939846333997604
0.8247503123313558
```
훈련 세트의 점수가 높지 않다. 전체적으로 과소적합 되있다고 할 수 있다.

<br>
<br>

# 다항 회귀

이전의 y = a * x + b의 경우 직선의 형태이다. 이 직선 형태라면 농어의 무게가 0g이하로 내려가는 일이 발생하는데 현실에서는 불가능한 일이다.

농어의 길이와 무게에 대한 산점도를 보면 일직선이라기보다 왼쪽 위로 조금 구부러진 곡선에 가깝다.

그 그래프는 y = a*길이^2 + b*길이 + c 이다.


```python
train_poly = np.column_stack((train_input ** 2, train_input))
test_poly = np.column_stack((test_input ** 2, test_input))

print(train_poly.shape, test_poly.shape)
```
```
(42, 2) (14, 2)
```
제곱한 항이 훈련세트에 추가되어야 한다. column_stack 함수를 사용하면 간단하다. 원래 특성인 길이를 제곱하여 왼쪽 열에 추가했기 때문에 훈련, 테스트 세트 모두 열이 2개로 늘어났다.


```python
lr = LinearRegression()
lr.fit(train_poly, train_target)

print(lr.predict([[50**2, 50]]))
```
```
[1573.98423528]
```
이것을 기반으로 다시 훈련한다. 이 모델이 2차원 방정식의 a,b,c를 찾을 것이다. 타깃값은 그대로 사용한다.

```python
print(lr.coef_, lr.intercept_)
```
```
[  1.01433211 -21.55792498] 116.0502107827827
```
훈련개수와 절편을 출력하는 코드이다.

이런방정식을 다항식이라 부르며 다항식을 사용한 선형회귀를 다항회귀(polynomial regression)라고 부른다.

```python
print(lr.score(train_poly, train_target))
print(lr.score(test_poly, test_target))
```
```
0.9706807451768623
0.9775935108325122
```

훈련 세트와 테스트 세트에 대한 점수가 크게 높아졌다. 하지만 테스트 세트가 약간 높은걸로 봐서 과소적합이 아직 남아있다.