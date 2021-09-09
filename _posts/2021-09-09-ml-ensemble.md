---
title:  "[머신러닝] 트리의 앙상블"
excerpt: "머신러닝 - 랜덤포레스트, 엑스트라 트리, 그레이디언트 부스팅, 히스토그램 기반 그레이디언트 부스팅"
date: 2021-09-09 09:12:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - sklearn
  - 앙상블
---

<br>
<br>

# 정형데이터와 비정형데이터

정형데이터(structured data) : 어떤 구조로 되어있다. 이런 데이터는 csv나 데이터베이스 혹은 엑셀에 저장하기 쉽다.

비정형데이터(unstructured data) : 데이터베이스나 엑셀로 표현하기 어려운 것들. 텍스트 데이터, 사진, 음악 등이 있다.

정형 데이터를 다루는 데 가장 뛰어난 성과를 내는 알고리즘이 앙상블 학습(ensemble learning)이다. 

비정형 데이터에는 신경망 알고리즘을 사용한다. 비정형 데이터는 규칙성을 찾기 어려워 전통적인 머신러닝 방법으로는 모델을 만들기 까다롭다.

<br>
<br>

# 랜덤 포레스트

랜덤 포레스트(random forest)는 앙상블 학습의 대표 주자 중 하나로 안정적인 성능 덕분에 널리 사용되고 있다.

랜덤 포레스트는 각 트리를 훈련하기 위해 데이터를 랜덤하게 만든다. 이 데이터를 만드는 방법은 우리가 입력한 훈련 데이터에서 랜덤하게 샘플을 추출하여 훈련 데이터를 만든다. 이때 한 샘플이 중복되어 추출될 수도 있다.

이렇게 만들어진 샘플을 **부트스트랩 샘플**(bootstrap sample)이라고 부른다. 기본적으로 부트스트랩 샘플은 훈련 세트의 크기와 같게 만든다. 

각 노드를 분할할 때 전체 특성 중에서 일부 특성을 무작위로 고른 다음 이 중에서 최선의 분할을 찾는다. 분류 모델인 RandomForestClassifier는 기본적으로 전체 특성 개수의 제곱근만큼의 특성을 선택한다. 즉 4개의 특성이 있다면 노드마다 2개를 랜덤하게 선택해 사용한다. 다만 회귀 모델인 RandomRorestRegressor는 전체 특성을 사용한다.

사이킷런의 랜덤 포레스트는 기본적으로 100개의 결정 트리를 이런 방식으로 훈련한다. 그다음 분류일 때는 각 트리의 클래스별 확률을 평균하여 가장 높은 확률을 가진 클래스를 예측하고, 회귀일 때는 단순히 각 트리의 예측을 평균한다.

랜덤 포레스트는 랜덤하게 선택한 샘플과 특성을 사용하기 때문에 훈련세트에 과대적합되는 것을 막아주고 검증 세트와 테스트 세트에서 안정적인 성능을 얻는다.

<br>
<br>

# 랜덤포레스트 - 와인 분류
```python
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split

wine = pd.read_csv('https://bit.ly/wine_csv_data')

data = wine[['alcohol', 'sugar', 'pH']].to_numpy()
target = wine['class'].to_numpy()

train_input, test_input, train_target, test_target = train_test_split(data, target, test_size=0.2, random_state=42)
```

```python
from sklearn.model_selection import cross_validate
from sklearn.ensemble import RandomForestClassifier

rf = RandomForestClassifier(n_jobs=-1, random_state=42)
scores = cross_validate(rf, train_input, train_target, return_train_score=True, n_jobs=-1)

print(np.mean(scores['train_score']), np.mean(scores['test_score']))
```
```
0.9973541965122431 0.8905151032797809
```
cross_validate 함수를 사용해 교차 검증을 수행한다. RandomForestClassifier는 기본적으로 100개의 결정 트리를 사용하므로 n_jobs 매개변수를 -1로 지정해 모든 cpu 코어를 사용하는 것이 좋다. 또 retrun_train_score 매개변수를 True로 지정해 검증 점수뿐만 아니라 훈련 세트에 대한 점수도 같이 반환한다. 훈련세트와 검증 세트의 점수를 비교하면 과대적합을 파악하는데 용이하다.

결과를 보면 훈련세트에 과대적합 되었다.

<br>
<br>

# 엑스트라 트리

엑스트라 트리(extra trees)는 랜덤 포레스트와 매우 비슷하게 동작한다. 기본적으로 100개의 결정 트리를 훈련한다. 또 전체 특성 중 일부 특성을 랜덤하게 선택해 노드를 분할하는데 사용한다.

랜덤 포레스트와 엑스트라 트리의 차이점은 부투스트랩 샘플을 사용하지 않는다는 점이다. 즉 각 결정 트리를 만들 때 전체 훈련 세트를 사용한다. 대신 노드를 분할 할 때 가장 좋은 분할을 찾는 것이 아니라 무작위로 분할한다.

하나의 결정 트리에서 특성을 무작위로 분할한다면 성능이 낮아지겠지만 많은 트리를 앙상블 하기 때문에 과대적합을 막고 검증 세트의 점수를 높이는 효과가 있다.

```python
from sklearn.ensemble import ExtraTreesClassifier

et = ExtraTreesClassifier(n_jobs=-1, random_state=42)
scores = cross_validate(et, train_input, train_target, return_train_score=True, n_jobs=-1)

print(np.mean(scores['train_score']), np.mean(scores['test_score']))
```
```
0.9974503966084433 0.8887848893166506
```
랜덤 포레스트와 비슷한 결과가 나왔다. 보통 엑스트라 트리가 무작위성이 좀 더 커 랜덤 포레스트보다 더 많은 결정 트리를 훈련해야 한다. 하지만 랜덤하게 노드를 분할하기 때문에 빠른 계산 속도가 엑스트라 트리의 장점이다.

<br>
<br>

# 그레이디언트 부스팅

그레이디언트 부스팅(gradient boosting)은 깊이가 얕은 결정 트리를 사용하여 이전 트리의 오차를 보완하는 방식으로 앙상블 하는 방법이다.

사이킷런의 GradientBoostingClassifier는 기본적으로 깊이가 3인 결정 트리를 100개 사용한다. 깊이가 얕은 결정 트리를 사용하기 때문에 과대적합에 강하고 일반적으로 높은 일반화 성능을 기대할 수 있다.

그레이디언트 부스팅은 경사 하강법을 사용해 트리를 앙상블에 추가한다. 분류에서는 로지스틱 손실 함수를, 회귀에서는 평균 제곱 오차 함수를 사용한다.

```python
from sklearn.ensemble import GradientBoostingClassifier

gb = GradientBoostingClassifier(random_state=42)
scores = cross_validate(gb, train_input, train_target, return_train_score=True, n_jobs=-1)

print(np.mean(scores['train_score']), np.mean(scores['test_score']))
```
```
0.8881086892152563 0.8720430147331015
```
과대적합이 거의 되지 않았다. 그레이디언트 부스팅은 결정 트리의 개수를 늘려도 과대적합에 매우 강하다. 학습률을 증가시키고 트리의 개수를 늘리면 조금 더 성능이 향상될 수 있다.

```python
gb = GradientBoostingClassifier(n_estimators=500, learning_rate=0.2, random_state=42)
scores = cross_validate(gb, train_input, train_target, return_train_score=True, n_jobs=-1)

print(np.mean(scores['train_score']), np.mean(scores['test_score']))
```
```
0.9464595437171814 0.8780082549788999
```
결정 트리 개수를 500개로 5배 늘렸지만 과대적합을 억제하고 있다. 

<br>
<br>

# 히스토그램 기반 그레이디언트 부스팅

히스토그램 기반 그레이디언트 부스팅(histogram-based gradient boosting)은 정형 데이터를 다루는 머신러닝 알고리즘 중에 가장 인기가 높은 알고리즘이다. 히스토그램 기반 그레이디언트 부스팅은 먼저 입력 특성을 256개의 구간으로 나눈다. 따라서 노드를 분할할 때 최적의 분할을 매우 빠르게 찾을 수 있다.

히스토그램 기반 그레이디언트 부스팅은 256개의 구간 중에서 하나를 떼어 놓고 누락된 값을 위해서 사용한다. 따라서 입력에 누락된 특성이 있어도 이를 따라 전처리할 필요가 없다.

```python
from sklearn.experimental import enable_hist_gradient_boosting
from sklearn.ensemble import HistGradientBoostingClassifier

hgb = HistGradientBoostingClassifier(random_state=42)
scores = cross_validate(hgb, train_input, train_target, return_train_score=True, n_jobs=-1)

print(np.mean(scores['train_score']), np.mean(scores['test_score']))
```
```
0.9321723946453317 0.8801241948619236
```
과대적합을 억제하면서 그레이디언트 부스팅보다 조금 더 높은 성능을 제공한다.

## XGBoost

```python
from xgboost import XGBClassifier

xgb = XGBClassifier(tree_method='hist', random_state=42)
scores = cross_validate(xgb, train_input, train_target, return_train_score=True, n_jobs=-1)

print(np.mean(scores['train_score']), np.mean(scores['test_score']))
```
```
0.8824322471423747 0.8726214185237284
```
XGBoost 라이브러리는 코랩에서 사용할 수 있으며 사이킷런의 cross_validate 함수와도 같이 사용이 가능하다.

## LightGBM

```python
from lightgbm import LGBMClassifier

lgb = LGBMClassifier(random_state=42)
scores = cross_validate(lgb, train_input, train_target, return_train_score=True, n_jobs=-1)

print(np.mean(scores['train_score']), np.mean(scores['test_score']))
```
```python
0.9338079582727165 0.8789710890649293
```
마이크로소프트에서 만든 LightGBM이 있다. 이것은 빠르고 최신 기술이 많이 적용하고 있다.