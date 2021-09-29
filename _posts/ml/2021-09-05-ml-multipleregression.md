---
title:  "[머신러닝] 다중회귀, 릿지회귀, 라쏘회귀"
excerpt: "머신러닝 - 다중회귀, 릿지회귀, 라쏘회귀"
date: 2021-09-05 19:37:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - sklearn
  - 다중회귀
  - 특성공학
  - 릿지회귀
  - 라쏘회귀
---

# 다중 회귀

여러 개의 특성을 사용한 선형 회귀를 다중 회귀(multiple regressioin)라고 한다.

농어의 길이뿐만 아니라 농어의 높이, 두께도 함께 사용한다. 또한 3개의 특성을 각각 제곱해 추가한다. 거기다가 각 특성을 서로 곱해서 또 다른 특성을 만든다. 즉 '농어길이*농어높이'를 새로운 특성으로 만드는 것이다. 이렇게 기존의 특성을 사용해 새로운 특성을 뽑아내는 작업을 **특성 공학**(feature engineering)이라고 한다.

<br>
<br>

# 데이터 준비

```python
import pandas as pd

df = pd.read_csv('https://bit.ly/perch_csv_data')
perch_full = df.to_numpy()
print(perch_full)
```
```
[[ 8.4   2.11  1.41]
 [13.7   3.53  2.  ]
 [15.    3.82  2.43]
 [16.2   4.59  2.63]
 [17.4   4.59  2.94]
 [18.    5.22  3.32]
 [18.7   5.2   3.12]
 [19.    5.64  3.05]
 [19.6   5.14  3.04]
 [20.    5.08  2.77]
 [21.    5.69  3.56]
 [21.    5.92  3.31]
 [21.    5.69  3.67]
 [21.3   6.38  3.53]
 [22.    6.11  3.41]
 [22.    5.64  3.52]
 [22.    6.11  3.52]
 [22.    5.88  3.52]
 [22.    5.52  4.  ]
 [22.5   5.86  3.62]
 [22.5   6.79  3.62]
 [22.7   5.95  3.63]
 [23.    5.22  3.63]
 [23.5   6.28  3.72]
 [24.    7.29  3.72]
 [24.    6.38  3.82]
 [24.6   6.73  4.17]
 [25.    6.44  3.68]
 [25.6   6.56  4.24]
 [26.5   7.17  4.14]
 [27.3   8.32  5.14]
 [27.5   7.17  4.34]
 [27.5   7.05  4.34]
 [27.5   7.28  4.57]
 [28.    7.82  4.2 ]
 [28.7   7.59  4.64]
 [30.    7.62  4.77]
 [32.8  10.03  6.02]
 [34.5  10.26  6.39]
 [35.   11.49  7.8 ]
 [36.5  10.88  6.86]
 [36.   10.61  6.74]
 [37.   10.84  6.26]
 [37.   10.57  6.37]
 [39.   11.14  7.49]
 [39.   11.14  6.  ]
 [39.   12.43  7.35]
 [40.   11.93  7.11]
 [40.   11.73  7.22]
 [40.   12.38  7.46]
 [40.   11.14  6.63]
 [42.   12.8   6.87]
 [43.   11.93  7.28]
 [43.   12.51  7.42]
 [43.5  12.6   8.14]
 [44.   12.49  7.6 ]]
```
판다스는 데이터 분석 라이브러리로 데이터프레임은 판다스의 핵심 데이터 구조이다.
넘파이 배열과 비슷하게 다차원 배열을 다룰 수 있지만 훨씬 더 많은 기능을 제공한다. 또 데이터프레임은 넘파이 배열로 쉽게 바꿀 수 있다.

넘파이와 다르게 판다스를 사용해 농어 데이터를 인터넷에서 내려받을 수 있다. 이렇게 내려받은 데이터를 데이터프레임에 저장하고 넘파이 배열로 변환해 선형 회귀 모델을 훈련한다.

판다스 데이터프레임을 만들기 위해 많이 사용하는 파일은 CSV파일이다. CSV파일은 콤마로 나누어져 있는 텍스트 파일이다.

```python
import numpy as np

perch_weight = np.array(
    [5.9, 32.0, 40.0, 51.5, 70.0, 100.0, 78.0, 80.0, 85.0, 85.0, 
     110.0, 115.0, 125.0, 130.0, 120.0, 120.0, 130.0, 135.0, 110.0, 
     130.0, 150.0, 145.0, 150.0, 170.0, 225.0, 145.0, 188.0, 180.0, 
     197.0, 218.0, 300.0, 260.0, 265.0, 250.0, 250.0, 300.0, 320.0, 
     514.0, 556.0, 840.0, 685.0, 700.0, 700.0, 690.0, 900.0, 650.0, 
     820.0, 850.0, 900.0, 1015.0, 820.0, 1100.0, 1000.0, 1100.0, 
     1000.0, 1000.0]
     )
```
타깃 데이터는 이전과 동일한 방식이다.

```python
from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(perch_full, perch_weight, random_state=42)
```
그다음 perch_full과 perch_weight를 훈련 세트와 테스트 세트로 나눈다.

<br>
<br>

# 사이킷런의 변환기

```python
from sklearn.preprocessing import PolynomialFeatures

poly = PolynomialFeatures()
poly.fit([[2, 3]])
print(poly.transform([[2, 3]]))
```
```
[[1. 2. 3. 4. 6. 9.]]
```
사이킷런은 특성을 만들거나 전처리하기 위한 다양한 클래스를 제공한다. 사이킷런에서는 이런 클래스를 **변환기**(transformer)라고 부른다. 변환기 클래스는 fit(), transform() 메소드를 제공한다.

2개의 특성 2와 3으로 이루어진 샘플 하나를 적용했다. 

fit메소드는 새롭게 만들 특성 조합을 찾고 transform메소드는 실제로 데이터를 변환한다. 변환기는 입력 데이터를 변환하는 데 타깃 데이터가 필요하지 않다. 따라서 모델 클래스와 다르게 fit메소드에 입력 데이터만 전달한다. 즉 여기에서는 2개의 특성(원소)을 가진 샘플이 6개의 특성을 가진 샘플로 바뀌었다.

기본적으로 각 특성을 제곱한 항을 추가하고 특성끼리 곱한 항을 추가한다.
여기서 1이 추가된 이유는 선형 방정식의 절편을 항상 값이 1인 특성과 곱해지는 계수라고 볼 수 있다. 이렇게 보면 특성은 (길이, 높이, 두께, 1)이 된다. 하지만 사이킷런의 선형 모델은 자동으로 절편을 추가하므로 굳이 이렇게 특성을 만들 필요가 없다. 

```python
poly = PolynomialFeatures(include_bias=False)
poly.fit([[2, 3]])
print(poly.transform([[2, 3]]))
```

```
[[2. 3. 4. 6. 9.]]
```
include_bias=False로 지정하면 1이 사라진다.
절편을 위한 항이 제거되고 특성의 제곱과 특성끼리 곱한 항만 추가되었다.

```python
poly = PolynomialFeatures(include_bias=False)

poly.fit(train_input)
train_poly = poly.transform(train_input)

print(train_poly.shape)
```
```
(42, 9)
```
위 방식으로 train_input에 적용한다.


```python
poly.get_feature_names()
```
```
['x0', 'x1', 'x2', 'x0^2', 'x0 x1', 'x0 x2', 'x1^2', 'x1 x2', 'x2^2']
```
PolynomialFeatures 클래스는 9개의 특성이 어떻게 만들어졌는지 ㅣ확인하는 get_feature_names() 메소드를 지원한다.

```python
test_poly = poly.transform(test_input)
```
테스트 세트를 변환한다.
여기서 fit 메소드에서 만들 특성의 조합을 준비만 하고 별도의 틍계값을 구하지 않아 테스트 세트를 따로 변환해도 딘다. 하지만 항상 훈련 세트를 기준으로 테스트 세트를 변환하는 습관을 들이는 것이 좋아 훈련 세트에 적용한 변환기로 테스트 세트를 변환했다.

<br>
<br>

# 다중 회귀 모델 훈련

```python
from sklearn.linear_model import LinearRegression

lr = LinearRegression()
lr.fit(train_poly, train_target)
print(lr.score(train_poly, train_target))
```
```
0.9903183436982124
```
특성이 늘어나면서 선형 회귀의 능력이 강해진 것을 정확도를 통해 알 수 있다.

```python
print(lr.score(test_poly, test_target))
```
```
0.9714559911594132
```
테스트 세트에 대한 점수는 높아지지 않았지만 농어의 길이만 사용했을 때 있던 과소적합 문제는 더이상 나타나지 않는다.

```python
poly = PolynomialFeatures(degree=5, include_bias=False)

poly.fit(train_input)
train_poly = poly.transform(train_input)
test_poly = poly.transform(test_input)

print(train_poly.shape)
```
```
(42, 55)
```
5제곱까지 특성을 만들어 출력한다. 만들어진 특성은 55개이다.

```python
lr.fit(train_poly, train_target)
print(lr.score(train_poly, train_target))
```
```
0.9999999999991096
```
거의 완벽한 정확도이다.

```python
print(lr.score(test_poly, test_target))
```
```
-144.40579242335605
```
하지만 테스트 세트에서는 형편없는 점수가 나왔다. 특성의 개수를 크게 늘리면 선형 모델은 아주 경력해져 훈련세트에 대해 거의 완벽하게 학습이 가능하지만 이런 모델은 훈련 세트에 너무 과대적합되는 문제가 발생한다.

<br>
<br>

# 규제

규제(regularization)은 머신러닝 모델이 훈련 세트를 너무 과도하게 학습하지 못하도록 훼방하는 것을 말한다. 즉 모델이 훈련 세트에 과대적합되지 않도록 만드는 것이다. 선형 회귀 모델의 경우 특성에 곱해지는 계수(또는 기울기)의 크기를 작게 만드는 일이다.

```python
from sklearn.preprocessing import StandardScaler

ss = StandardScaler()
ss.fit(train_poly)

train_scaled = ss.transform(train_poly)
test_scaled = ss.transform(test_poly)
```
특성의 스케일이 정규화되지 않으면 여기에 곱해지는 계수 값도 차이 나게 된다. 일반적으로 선형 회귀 모델에 규제를 적용할 때 계수 값의 크기가 서로 많이 다르면 공정하게 제어되지 않는다. 규제를 적용하기 전에 정규화를 해야한다.

선형 회귀 모델에 규제를 추가한 모델을 릿지(ridge)와 라쏘(lasso)라고 한다.
두 모델은 규제를 가하는 방법이 다르다. 

릿지는 계수를 제곱한 값을 기준으로 규제를 적용하고, 라쏘는 계수의 절댓값을 기준으로 규제를 적용한다. 일반적으로 릿지를 조금 더 선호한다. 두 알고리즘 모두 계수의 크기를 줄이지만 라쏘는 아예 0으로 만들 수도 있다. 사이킷런은 이 두 알고리즘 모두 제공한다.

릿지와 라쏘 모델을 사용할 때 규제의 양을 임의로 조절할 수 있다. 모델 객체를 만들 때 alpha매개변수로 규제의 강도를 조절한다. 이 매개변수 값이 크면 규제 강도가 세지므로 계수 값을 더 줄이고 조금 더 과소적합되도록 유도한다. alpha 값이 작으면 계수를 줄이는 역할이 줄어들고 선형 회귀 모델과 유사해지므로 과대적합될 가능성이 크다.

이러한 사람이 직접 지정해야하는 파라미터를 **하이퍼파라미터**(hyperparameter)라고 부른다. 
<br>
<br>

# 릿지 회귀

```python
from sklearn.linear_model import Ridge

ridge = Ridge()
ridge.fit(train_scaled, train_target)
print(ridge.score(train_scaled, train_target))
```
```
0.9896101671037343
```
선형 회귀에서는 거의 완벽하게 가까웠던 점수가 조금 낮아졌다.

```python
print(ridge.score(test_scaled, test_target))
```
```
0.9790693977615398
```
테스트 세트의 점수가 정상으로 돌아왔다. 많은 특성을 사용했음에도 훈련 세트에 과대적합되지 않아 테스트 세트에서도 좋은 성능을 내고 있다.

```python
ridge = Ridge(alpha=0.1)
ridge.fit(train_scaled, train_target)

print(ridge.score(train_scaled, train_target))
print(ridge.score(test_scaled, test_target))
```
```
0.9903815817570366
0.9827976465386927
```
alpha 값을 조절해서 훈련 세트와 테스트 세트의 점수가 비슷하게 모두 높고 과대적합과 과소적합 사이에서 균형을 맞추고 있다.

<br>
<br>

# 라쏘 회귀

```python
from sklearn.linear_model import Lasso

lasso = Lasso()
lasso.fit(train_scaled, train_target)
print(lasso.score(train_scaled, train_target))
```
```
0.9897898972080961
```
```python
print(lasso.score(test_scaled, test_target))
```
```
0.9800593698421883
```
훈련세트, 테스트 세트 결과 모두 릿지만큼 좋다. 라쏘 모델도 alpha 매개변수로 규제의 강도를 조절할 수 있다.

```python
lasso = Lasso(alpha=10)
lasso.fit(train_scaled, train_target)

print(lasso.score(train_scaled, train_target))
print(lasso.score(test_scaled, test_target))
```
```
0.9888067471131867
0.9824470598706695
```
라쏘 모델의 최적의 alpha 값으로 훈련한 결과이다.

```python
print(np.sum(lasso.coef_ == 0))
```
```
40
```
라쏘 모델은 계수 값을 아예 0으로 만들 수 있다. 라쏘 모델의 계수는 coef_ 속성에 저장되어 있다. 그 개수를 출력해보면 55개의 특성을 모델에 주입했지만 라쏘 모델이 사용한 특성은 15개밖에 되지 않는다. 이런 특징 때문에 라쏘 모델을 유용한 특성을 골라내는 용도로도 사용할 수 있다.
