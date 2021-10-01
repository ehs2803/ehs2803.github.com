---
title:  "[머신러닝] 비지도학습 - 군집 알고리즘"
excerpt: "머신러닝 - 비지도학습 - 군집 알고리즘"
date: 2021-09-10 10:10:00
categories:
  - 머신러닝
tags:
  - 머신러닝
  - 비지도학습
  - 군집
---

# 비지도 학습

비지도 학습은 타깃이 없다. 사람이 가르쳐 주지 않아도 데이터 있는 무언가를 학습한다.

# 과일 사진 데이터 준비하기

```python
import numpy as np
import matplotlib.pyplot as plt

!wget https://bit.ly/fruits_300_data -O fruits_300.npy

print(fruits.shape)
print(fruits[0, 0, :])
```
```
(300, 100, 100)
[  1   1   1   1   1   1   1   1   1   1   1   1   1   1   1   1   2   1
   2   2   2   2   2   2   1   1   1   1   1   1   1   1   2   3   2   1
   2   1   1   1   1   2   1   3   2   1   3   1   4   1   2   5   5   5
  19 148 192 117  28   1   1   2   1   4   1   1   3   1   1   1   1   1
   2   2   1   1   1   1   1   1   1   1   1   1   1   1   1   1   1   1
   1   1   1   1   1   1   1   1   1   1]
```
과일 데이터는 사과, 바나나, 파인애플을 담고 있는 흑백사진이다. 이 데이터는 넘파이 배열의 기본 저장 포맷인 npy 파일로 저장되어 있다. load 메서드를 통해 npy파일을 로드한다. 

배열의 크기를 확인해보면 300, 100, 100이 나온다. 300은 샘플의 개수, 두번째 차원 100은 이미지 높이, 세번째는 이미지 너비이다.

첫번째 행에 있는 픽셀 100개 값을 출력해보면 이 넘파이 배열은 흑백 사진을 담고 있어 0~255까지의 정수값을 가진다.

<br>
<br>

# 픽셀값 분석

```python
apple = fruits[0:100].reshape(-1, 100*100)
pineapple = fruits[100:200].reshape(-1, 100*100)
banana = fruits[200:300].reshape(-1, 100*100)

print(apple.shape)

print(apple.mean(axis=1))
```
```
(100, 10000)
[ 88.3346  97.9249  87.3709  98.3703  92.8705  82.6439  94.4244  95.5999
  90.681   81.6226  87.0578  95.0745  93.8416  87.017   97.5078  87.2019
  88.9827 100.9158  92.7823 100.9184 104.9854  88.674   99.5643  97.2495
  94.1179  92.1935  95.1671  93.3322 102.8967  94.6695  90.5285  89.0744
  97.7641  97.2938 100.7564  90.5236 100.2542  85.8452  96.4615  97.1492
  90.711  102.3193  87.1629  89.8751  86.7327  86.3991  95.2865  89.1709
  96.8163  91.6604  96.1065  99.6829  94.9718  87.4812  89.2596  89.5268
  93.799   97.3983  87.151   97.825  103.22    94.4239  83.6657  83.5159
 102.8453  87.0379  91.2742 100.4848  93.8388  90.8568  97.4616  97.5022
  82.446   87.1789  96.9206  90.3135  90.565   97.6538  98.0919  93.6252
  87.3867  84.7073  89.1135  86.7646  88.7301  86.643   96.7323  97.2604
  81.9424  87.1687  97.2066  83.4712  95.9781  91.8096  98.4086 100.7823
 101.556  100.7027  91.6098  88.8976]
```
넘파이 배열을 나눌 때 100*100 이미지를 펼쳐서 길이가 10000인 1차원 배열로 만든다.

사과의 배열 크기는 100, 10000이다. 즉 사과가 100개 샘플이 있다는 의미이다.

각 샘플의 픽셀 평균값을 계산하기 위해 mean 메서드를 사용한다. 샘플마다 픽셀의 평균값을 계산해야 하므로 mean 메소드가 평균을 계산할 축을 지정해야 한다. axis=0으로 하면 첫번째 축인 행을 따라 계산한다. axis=1로 지정하면 두번째 축인 열을 따라 계산한다.
필요한 데이터가 샘플의 평균값이고 샘플은 모두 가로로 값을 나열했으므로 axis=1로 지정하여 평균을 계산했다. 그 결과 100개에 대한 사과 샘플 픽셀 평균값을 계산했다.

```python
plt.hist(np.mean(apple, axis=1), alpha=0.8)
plt.hist(np.mean(pineapple, axis=1), alpha=0.8)
plt.hist(np.mean(banana, axis=1), alpha=0.8)
plt.legend(['apple', 'pineapple', 'banana'])
plt.show()
```

![fruit_histogram](/img/fruit_histogram.PNG?raw=true)

히스토그램을 그려보면 바나나 사진의 평균값은 40 아래로 집중되어 있고, 사과와 파인애플은 90~100 사이에 많이 모여 있다.

```python
fig, axs = plt.subplots(1, 3, figsize=(20, 5))
axs[0].bar(range(10000), np.mean(apple, axis=0))
axs[1].bar(range(10000), np.mean(pineapple, axis=0))
axs[2].bar(range(10000), np.mean(banana, axis=0))
plt.show()
```
![fruit_axis=0](/img/fruit_axis=0.PNG?raw=true)

샘플의 평균값이 아니라 픽섹별 평균값을 비교해보면 과일마다 값이 높은 구간이 다르다.

```python
apple_mean = np.mean(apple, axis=0).reshape(100, 100)
pineapple_mean = np.mean(pineapple, axis=0).reshape(100, 100)
banana_mean = np.mean(banana, axis=0).reshape(100, 100)

fig, axs = plt.subplots(1, 3, figsize=(20, 5))
axs[0].imshow(apple_mean, cmap='gray_r')
axs[1].imshow(pineapple_mean, cmap='gray_r')
axs[2].imshow(banana_mean, cmap='gray_r')
plt.show()
```
![fruit_axis=0_image](/img/fruit_axis=0_image.PNG?raw=true)

픽셀 평균값을 100*100크기로 바꿔서 이미지처럼 출력한다. 픽셀을 평균 낸 이미지를 모든 사진을 합쳐 놓은 대표 이미지로 생각할 수 있다. 세과일은 픽셀 위치에 따라 값의 크기가 차이가 난다.

<br>
<br>

# 평균값과 가까운 사진 고르기

사과 사진의 평균값인 apple_maen과 가장 가까운 사진을 고른다. 절댓값 오차를 사용한다.
fruits 배열에 있는 모든 샘플에서 apple_mean을 뺀 절댓값의 평균을 계산한다.

```python
abs_diff = np.abs(fruits - apple_mean)
abs_mean = np.mean(abs_diff, axis=(1,2))
print(abs_mean.shape)
```
```
(300,)
```
abs_mean은 각 샘플의 오차 평균이므로 크기가 (300,)인 1차원 배열이다.



```python
apple_index = np.argsort(abs_mean)[:100]
fig, axs = plt.subplots(10, 10, figsize=(10,10))
for i in range(10):
    for j in range(10):
        axs[i, j].imshow(fruits[apple_index[i*10 + j]], cmap='gray_r')
        axs[i, j].axis('off')
plt.show()
```

그다음 이 값이 가장 작은 순서대로 100개를 골라 그래프를 그린다. 즉 apple_mean과 오차가 가장 작은 샘플 100개를 고른다. np.argsort 함수는 작은 것에서 큰 순서대로 나열한 abs_mean 배열의 인덱스를 반환한다.

![fruit_apple_grid](/img/fruit_apple_grid.PNG?raw=true)

결과 모두 사과 100개를 출력한다.

이렇게 비슷한 샘플끼리 그룹으로 모으는 작업을 군집(clustering)이라고 한다. 군집은 대표적인 비지도학습 중 하나이다. 군집 알고리즘에서 만든 그룹을 클러스터(cluster)라고 부른다.

하지만 이미 사과, 파인애플, 바나나가 있다는 것을 알고 있어서 이 사진들의 평균값을 계싼해 가장 가까운 과일알 찾을 수 있었다. 즉 타깃값을 알고 있었다. 실제 비지도 학습에서는 타깃값을 모르기 때문에 이처럼 샘플의 평균값을 미리 구할 수 없다.