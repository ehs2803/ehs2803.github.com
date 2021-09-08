---
title:  "[머신러닝] 검증 세트, 교차 검증"
excerpt: "머신러닝 - 검증 세트, 교차 검증"
date: 2021-09-08 09:27:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - sklearn
  - 검증세트
  - 교차검증
---

# 검증 세트

테스트 세트를 사용하지 않으면 모델이 과대적합인지 과소적합인지 판단하기 어렵다. 테스트 세트를 사용하지 않으면서 이를 측정하는 방법은 훈련 세트를 나눈다. 이 데이터를 검증 세트(validation set)라고 한다.

보통 20~30%를 테스트 세트와 검증 세트로 떼어 놓는다. 하지만 문제에 따라 다르다. 훈련 데이터가 아주 많다면 단 몇 %만 떼어 놓아도 전체 데이터를 대표하는 데 문제가 없다.

훈련 세트에서 모델을 훈련하고 검증 세트로 모델을 평가한다. 이런 식으로 테스트하고 싶은 매개변수를 바꿔가며 가장 좋은 모델을 고른다. 그다음 이 매개변수를 사용해 훈련 세트와 검증 세트를 합쳐 전체 훈련 데이터에서 모델을 다시 훈련한다. 그리고 마지막으로 테스트 세트에서 최종 점수를 평가한다.

```python
import pandas as pd

wine = pd.read_csv('https://bit.ly/wine_csv_data')

data = wine[['alcohol', 'sugar', 'pH']].to_numpy()
target = wine['class'].to_numpy()

from sklearn.model_selection import train_test_split

train_input, test_input, train_target, test_target = train_test_split(
    data, target, test_size=0.2, random_state=42)

sub_input, val_input, sub_target, val_target = train_test_split(
    train_input, train_target, test_size=0.2, random_state=42)

print(sub_input.shape, val_input.shape)
```
```
(4157, 3) (1040, 3)
```
train_input과 train_target을 다시 train_test_split함수에 넣어 훈련 세트 sub_input, sub_target과 검증 세트 val_input, val_target을 만든다. 훈련 세트의 약 20%를 검증 세트로 만든다.

```python
from sklearn.tree import DecisionTreeClassifier

dt = DecisionTreeClassifier(random_state=42)
dt.fit(sub_input, sub_target)

print(dt.score(sub_input, sub_target))
print(dt.score(val_input, val_target))
```
```
0.9971133028626413
0.864423076923077
```
이 모델은 확실히 훈련세트에 과대적합되어 있다.,

<br>
<br>

# 교차 검증

검증 세트를 만드느라 훈련 세트가 줄었다. 보통 많은 데이터를 훈련에 사용할수록 좋은 모델을 만들 수 있다. 그렇다고 검증세트를 너무 조금 떼어 놓으면 검증 점수가 들쭉날쭉하고 불안정할 것이다.

이럴 때 교차검증(cross validation)을 이용하면 안정적인 검증 점수를 얻고 훈련에 더 많은 데이터를 사용할 수 있다.

교차 검증은 검증 세트를 떼어 내어 평가하는 과정을 여러 번 반복한다. 그다음 이 점수를 평균하여 최종 검증 점수를 얻는다. 

**3-폴드 교차 검증** : 훈련 세트를 세부분으로 나눠서 교차 검증을 수행하는 것을 3-폴드 교차 검증이라고 한다. 통칭 k-폴드 교차 검증(k-fold cross validation)이라고 하며, 훈련 세트를 몇 부분으로 나누냐에 따라 다르게 부른다. k-겹 교차 검증이라고도 부른다.

보통 5-폴드 교차 검증이나 10-폴드 교차 검증을 많이 사용한다. 이렇게 하면 데이터의 80~90%까지 훈련에 사용할 수 있다. 검증 세트가 줄어들지만 각 폴드에서 계산한 검증 점수를 평균하기 때문에 안정된 점수를 생각할 수 있다.

```python
from sklearn.model_selection import cross_validate

scores = cross_validate(dt, train_input, train_target)
print(scores)
```
```
{'fit_time': array([0.00725031, 0.00697041, 0.00710249, 0.00712824, 0.00681305]), 'score_time': array([0.00077963, 0.00055647, 0.0005784 , 0.00052595, 0.00059152]), 'test_score': array([0.86923077, 0.84615385, 0.87680462, 0.84889317, 0.83541867])}
```
사이킷런에 cross_validate()라는 교차 검증 함수가 있다. 평가할 모델 객체를 첫 번째 매개변수로 전달하고 훈련세트 전체를 함수에 전달한다.

이 함수는 fit_time, score_time, test_score 키를 가진 딕셔너리를 반환한다. 처음 2개의 키는 각각 모델을 훈련하는 시간과 검증하는 시간을 의미한다. 각 키마다 5개의 숫자가 담겨 있다. cross_validate함수는 기본적으로 5-폴드 교차 검증을 수행한다. cv 매개변수에서 폴드 수를 바꿀 수도 있다.

교차 검증의 최종 점수는 test_score 키에 담긴 5개의 점수를 평균해 얻을 수 있다. 이름은 test_score이지만 검증 폴드의 점수이다.

```python
import numpy as np

print(np.mean(scores['test_score']))
```
```
0.855300214703487
```
최종 검증 점수이다.

```python
from sklearn.model_selection import StratifiedKFold

scores = cross_validate(dt, train_input, train_target, cv=StratifiedKFold())
print(np.mean(scores['test_score']))
```
```
0.855300214703487
```
만약 교차 검증을 할 때 훈련세트를 섞으려면 분할기(splitter)를 지정해야 한다.

사이킷런의 분할기는 교차 검증에서 폴드를 어떻게 나눌지 결정해 준다. cross_validate 함수는 기본적으로 회귀모델일 경우 KFold 분할기를 사용하고 분류 모델일 경우 타깃 클래스를 골고루 나누기 위해 StratifiedKFold를 사용한다.

```python
splitter = StratifiedKFold(n_splits=10, shuffle=True, random_state=42)
scores = cross_validate(dt, train_input, train_target, cv=splitter)
print(np.mean(scores['test_score']))
```
```
0.8574181117533719
```
만약 훈련 세트를 섞은 후 10-폴드 교차 검증을 수행하려면 다음과 같이 작성한다.

<br>
<br>

# 하이퍼파라미터 튜닝

머신러닝 모델이 학습하는 파라미터를 모델 파라미터라고 부른다.

모델이 학습할 수 없어서 사용자가 지정해야 하는 파라미터를 하이퍼파라미터라고 한다.

사이킷런과 같은 머신러닝 라이브러리를 사용할 때 이런 하이퍼파라미터는 모두 클래스나 메소드의 매개변수로 표현된다.

이러한 하이퍼파라미터를 튜닝하는 작업은 먼저 라이브러리가 제공하는 기본값을 그대로 사용해 모델을 훈련한다. 그 다음 검증 세트의 점수나 교차 검증을 통해서 매개변수를 조금씩 바꾼다. 이 매개변수를 바꿔가면서 모델을 훈련하고 교차 검증을 수행한다.

한 매개변수의 최적값을 찾고 다른 매개변수의 최적값을 찾지 못한다. 한 매개변수가 바뀌면 그에대한 최적 매개변수는 달라진다. 그래서 두 매개변수를 동시에 바꿔가며 최적의 값을 찾아야 한다. 사이킷런에서는 이러한 일을 도와주는 **그리드 서치**(grid search)를 제공한다.

```python
from sklearn.model_selection import GridSearchCV

params = {'min_impurity_decrease': [0.0001, 0.0002, 0.0003, 0.0004, 0.0005]}

gs = GridSearchCV(DecisionTreeClassifier(random_state=42), params, n_jobs=-1)

gs.fit(train_input, train_target)
```
```
GridSearchCV(cv=None, error_score=nan,
             estimator=DecisionTreeClassifier(ccp_alpha=0.0, class_weight=None,
                                              criterion='gini', max_depth=None,
                                              max_features=None,
                                              max_leaf_nodes=None,
                                              min_impurity_decrease=0.0,
                                              min_impurity_split=None,
                                              min_samples_leaf=1,
                                              min_samples_split=2,
                                              min_weight_fraction_leaf=0.0,
                                              presort='deprecated',
                                              random_state=42,
                                              splitter='best'),
             iid='deprecated', n_jobs=-1,
             param_grid={'min_impurity_decrease': [0.0001, 0.0002, 0.0003,
                                                   0.0004, 0.0005]},
             pre_dispatch='2*n_jobs', refit=True, return_train_score=False,
             scoring=None, verbose=0)
```
사이킷런의 GridSearchCV 클래스는 하이퍼파라미터 탐색과 교차검증을 한 번에 수행한다. 별로돌 cross_validate 함수를 호출할 필요가 없다.

```python
dt = gs.best_estimator_
print(dt.score(train_input, train_target))
```
```
0.9615162593804117
```
교차 검증에서 최적의 하이퍼파라미터를 찾으면 전체 훈련세트로 모델을 다시 만들어야 한다고 했지만 사이킷런의 그리드 서치는 훈련이 끝나면 25개 모델 중 검증 점수가 가장 높은 모델의 매개변수 조합으로 전체 훈련 세트에서 자동으로 다시 모델을 훈련한다. 이 모델은 gs 객체의 best_estimator_ 속성에 저장되있다.

```python
print(gs.best_params_)
```
```
{'min_impurity_decrease': 0.0001}
```
그리드 서치로 찾은 최적의 매개변수는 best_params_ 속성에 저장된다.

```python
print(gs.cv_results_['mean_test_score'])
```
```
[0.86819297 0.86453617 0.86492226 0.86780891 0.86761605]
```
각 매개변수에서 수행한 교차 검증의 평균 점수는 cv_results_ 속성의 'mean_test_score' 키에 저장되어 있다.

```python
best_index = np.argmax(gs.cv_results_['mean_test_score'])
print(gs.cv_results_['params'][best_index])
```
```
{'min_impurity_decrease': 0.0001}
```
넘파이 argmax 함수를 사용하면 가장 큰 값의 인덱스를 추출할 수 있다. 그 결과 최적의 매개변수를 출력할 수 있다.

이 과정을 정리하면

1. 탐색할 매개변수 지정
2. 그다음 훈련세트에서 그리드 서치를 수행해 최상의 평균 검증 점수가 나오는 매개변수 조합을 찾고 이 조합은 그리드 서치 객체에 저장
3. 그리드 서치는 최상의 매개변수에서 (교차 검증에 사용한 훈련세트가 아니라) 전체 훈련 세트를 사용해 최종 모델을 훈련. 이 모델도 그리드 서치 객체에 저장.

<br>
<br>

# 랜덤 서치

매개변수의 값이 수치일 때 값의 범위나 간격을 미리 정하기 어려울 수 있다. 또 너무 많은 매개변수 조건이 있어 그리드 서치 수행 시간인 오래걸릴 수 있다. 이럴 때 랜덤 서치(random search)를 사용한다.

랜덤 서치는 매개변수 값의 목록을 전달하는 것이 아니라 매개벼수를 샘플링할 수 있는 확률 분포 객체를 전달한다. 

```python
from scipy.stats import uniform, randint

rgen = randint(0, 10)
rgen.rvs(10)
```
```
array([9, 2, 1, 8, 6, 4, 5, 6, 2, 6])
```
싸이파이는 파이썬의 핵심 과학 라이브러리 중 하나이다. 적분, 보간, 선형 대수, 확률 등을 포함한 수치 계산 전용 라이브러리이다. 사이킷런은 넘파이와 싸이파이 기능을 많이 사용한다.

싸이파이의 stats 서브 패키지에 있는 uniform과 randint클래스는 모두 주어진 범위에서 고르게 값을 뽑는다. 이를 균등 분포에서 샘플링한다고 말한다. randint는 정수값을 뽑고, uniform은 실수값을 뽑는다.

```python
params = {'min_impurity_decrease': uniform(0.0001, 0.001),
          'max_depth': randint(20, 50),
          'min_samples_split': randint(2, 25),
          'min_samples_leaf': randint(1, 25),
          }

from sklearn.model_selection import RandomizedSearchCV

gs = RandomizedSearchCV(DecisionTreeClassifier(random_state=42), params, 
                        n_iter=100, n_jobs=-1, random_state=42)
gs.fit(train_input, train_target)
```
```
RandomizedSearchCV(cv=None, error_score=nan,
                   estimator=DecisionTreeClassifier(ccp_alpha=0.0,
                                                    class_weight=None,
                                                    criterion='gini',
                                                    max_depth=None,
                                                    max_features=None,
                                                    max_leaf_nodes=None,
                                                    min_impurity_decrease=0.0,
                                                    min_impurity_split=None,
                                                    min_samples_leaf=1,
                                                    min_samples_split=2,
                                                    min_weight_fraction_leaf=0.0,
                                                    presort='deprecated',
                                                    random_state=42,
                                                    splitter='best'),...
                                        'min_impurity_decrease': <scipy.stats._distn_infrastructure.rv_frozen object at 0x7fb7bdd74a20>,
                                        'min_samples_leaf': <scipy.stats._distn_infrastructure.rv_frozen object at 0x7fb7bdd742b0>,
                                        'min_samples_split': <scipy.stats._distn_infrastructure.rv_frozen object at 0x7fb7bdd74cc0>},
                   pre_dispatch='2*n_jobs', random_state=42, refit=True,
                   return_train_score=False, scoring=None, verbose=0)
```
탐색할 매개변수의 딕셔너리를 만든다. 여기에 min_samples_leaf 매개변수를 탐색 대상에 추가한다. 이 매개변수는 리프 노드가 되기 위한 최소 샘플의 개수이다. 어떤 노드가 분할하여 만들어질 자식 노드의 샘플 수가 이 값보다 작을 경우 분할하지 않는다.

위 params에 정의된 매개변수 범위에서 총 100번(n_iter)을 샘플링하여 교차검증을 수행하고 최적의 매개변수 조합을 찾는다. 앞서 그리드 서치보다 훨씬 교차 검증 수를 줄이면서 넓은 영역을 효과적으로 탐색할 수 있다.

```python
print(gs.best_params_)
```
```
{'max_depth': 39, 'min_impurity_decrease': 0.00034102546602601173, 'min_samples_leaf': 7, 'min_samples_split': 13}
```
최적의 매개변수 조합이다.


```python
print(np.max(gs.cv_results_['mean_test_score']))
```
```
0.8695428296438884
```
최고의 교차 검증 점수이다.

```python
dt = gs.best_estimator_

print(dt.score(test_input, test_target))
```
```
0.86
```
최적의 모델은 이미 전체 훈련 세트로 훈련되어 best_estimator_ 속성에 저장되어 있다. 이 모델을 최정 모델로 결정하고 테스트 세트의 성능을 확인한다.