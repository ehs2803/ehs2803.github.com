---
title:  "[머신러닝] 결정 트리"
excerpt: "머신러닝 - 결정 트리"
date: 2021-09-07 20:25:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - sklearn
  - 결정트리
---

# 로지스틱 회귀로 와인 분류


```python
import pandas as pd

wine = pd.read_csv('https://bit.ly/wine_csv_data')

wine.info()
```
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 6497 entries, 0 to 6496
Data columns (total 4 columns):
 #   Column   Non-Null Count  Dtype  
---  ------   --------------  -----  
 0   alcohol  6497 non-null   float64
 1   sugar    6497 non-null   float64
 2   pH       6497 non-null   float64
 3   class    6497 non-null   float64
dtypes: float64(4)
memory usage: 203.2 KB
```
판다스를 이용해 인터넷에 있는 와인 데이터셋을 불러온다. wind.head()를 해보면 처음 3개의 열은 알코올 도수, 당도, ph값을 나타낸다. 네번째 열은 타깃값으로 0이면 레드와인, 1이면 화이트 와인이다.

화이트와인이 양성 클래스인 이진 분류 문제이다.

info메소드를 통해 데이터프레임의 각 열의 데이터 타입과 누락된 데이터가 있는지 확인한다.

decribe 메소드는 열에 대한 간략한 통계를 볼 수 있다. 최소, 최대, 평균값 등을 볼 수 있다.

이것을 통해 알 수 있는 사실은 알코올 도수와 당도, ph 값의 스케일이 다르다는 것이다. 

```python
data = wine[['alcohol', 'sugar', 'pH']].to_numpy()
target = wine['class'].to_numpy()

from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(
    data, target, test_size=0.2, random_state=42)

print(train_input.shape, test_input.shape)
```
```
(5197, 3) (1300, 3)
```
판다스 데이터프레임을 넘파이 배열로 바꾸고 훈련세트와 테스트세트로 나눈다. train_test_split함수는 설정값을 지정하지 않으면 25%를 테스트 세트로 지정한다. 여기서는 20%를 테스트 세트로 지정했다. 


```python
from sklearn.preprocessing import StandardScaler

ss = StandardScaler()
ss.fit(train_input)

train_scaled = ss.transform(train_input)
test_scaled = ss.transform(test_input)
```
```python
from sklearn.linear_model import LogisticRegression

lr = LogisticRegression()
lr.fit(train_scaled, train_target)

print(lr.score(train_scaled, train_target))
print(lr.score(test_scaled, test_target))
```
```
0.7808350971714451
0.7776923076923077
```
표준점수로 변환 후 로지스틱 회귀 모델을 통해 훈련했다.

결과가 나쁘다. 과소적합이 되었다. 하지만 왜 이렇게 훈련이 되었는지 설명하기가 어렵다.

<br>
<br>

# 결정 트리

결정트리(decision tree) 모델을 사용하면 이유를 설명하기 쉽다.


```python
from sklearn.tree import DecisionTreeClassifier

dt = DecisionTreeClassifier(random_state=42)
dt.fit(train_scaled, train_target)

print(dt.score(train_scaled, train_target))
print(dt.score(test_scaled, test_target))
```
```
0.996921300750433
0.8592307692307692
```
훈련 세트에 대한 점수가 높아졌다. 테스트 세트 점수를 보면 과대적합이 되었다.

여기서 plot_tree 함수를 이용하면 모델을 그림으로 표현해준다. 맨 위의 노드는 루트노드, 맨 아래끝에 달린 노드를 리프노드라고 한다.

결정트리에서 예측 방법은 리프 노드에서 가장 많은 클래스가 예측 클래스가 된다. k-최근접 이웃과 비슷하다.

# 불순도

gini는 지니 불순도(gini impurity)를 의미한다. DecisionTreeClassifier 클래스의 criterion 매개변수의 기본값이 'gini'이다. criterion 매개변수의 용도는 노드에서 데이터를 분할할 기준을 정하는 것이다.

지니 불순도 = 1 - (음성 클래스 비율 ^ 2 + 양성 클래스 비율 ^ 2)이다.

결정 트리 모델은 부모노드와 자식노드의 불순도 차이가 가능한 크도록 트리를 성장시킨다. 부모 노드와 자식 노드의 불순도 차이를 계산하는 법은 자식 노드의 불순도를 샘플 개수에 비례하여 모두 더하고 그 다음 부모 노드의 불순도에서 빼면 된다.

이런 부모와 자식 노드의 불순도 차이를 정보 이득(information gain)이라고 한다.

criterion='entropy'를 지정해 엔트로피 불순도를 사용할 수 있다. 엔트로피 불순도도 노드의 클래스 비율을 사용하지만 지니 불순도처럼 제곱이 아니라 밑이 2인 로그를 사용해 곱한다.

정리하면 결정 트리 알고리즘은 불순도 기준을 사용해 정보 이득이 최대가 되도록 노드를 분할한다. 노드를 순수하게 나눌수록 정보 이득이 커진다. 새로운 샘플에 대해 예측할 때에는 노드의 질문에 따라 트리를 이동한다. 그리고 마지막에 도달한 노드의 클래스 비율을 보고 예측을 만든다.

# 가지치기

결정 트리를 가지치기 할 수 있다. 그렇지 않으면 무작정 끝까지 자라는 트리가 만들어진다.


```python
dt = DecisionTreeClassifier(max_depth=3, random_state=42)
dt.fit(train_scaled, train_target)

print(dt.score(train_scaled, train_target))
print(dt.score(test_scaled, test_target))
```
```python
0.8454877814123533
0.8415384615384616
```
max_depth 매개변수를 3으로 지정해 모델을 만들면 루트 노드 아래로 최대 3개의 노드까지만 성장할 수 있다.

훈련세트의 성능은 낮아졌지만 테스트 세트의 성능은 거의 그대로이다. 


```python
print(dt.feature_importances_)
```

```
[0.12345626 0.86862934 0.0079144 ]
```
결정 트리는 어떤 특성이 가장 유용한지 나타내는 특성 중요도를 계산해 준다. 특성 중요도는 결정트리모델의 feature_importances_속성에 저장되어 있다.

특성 중요도는 각 노드의 정보 이득과 전체 샘플에 대한 비율을 곱한 후 특성별로 더하여 계산한다. 특성 중요도를 활용하면 결정 트리 모델을 특성 선택에 활용할 수 있다. 이것이 결정 트리 알고리즘의 또 다른 장점 중 하나이다.