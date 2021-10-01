---
title:  "[딥러닝스터디] 1. 딥러닝에 필요한 파이썬"
excerpt: "numpy, matplotlib"
date: 2021-08-06 22:53:00
categories:
  - 딥러닝스터디
  - python
  - 딥러닝

tags:
  - 딥러닝스터디
---


# 넘파이(numpy)

딥러닝을 구현하다 보면 배열이나 행렬 계산이 많이 등장한다. 넘파이의 배열 클래스인 numpy.array에는 편리한 메소드가 많다. 딥러닝을 구현할 때 이들 메소드를 이용한다.

파이썬과 같은 동적 언어는 C나C++같은 정적언어(컴파일 언어)보다 처리 속도가 늦어 파이썬에서 빠른 성능을 요구될 경우 해당 부분을 C/C++로 구현한다. 이때 파이썬은 C/C++로 쓰인 프로그램을 호출해주는 중개자 역할을 한다. **넘파이**도 주된처리는 C와 C++로 구현했다. 그래서 성능을 해치지 않으면서 파이썬의 편리한 문법을 사용할 수 있다.


```python
import numpy as np
```
numpy는 외부 라이브러리이다. numpy를 사용하기 위해서는 별도로 설치해 줘야한다.


```python
>>> x = np.array([1.0, 2.0, 3.0]) # 1차원 배열
>>> print(x)
[1. 2. 3.]
>>> type(x)
<class 'numpy.ndarray'>
```
넘파이 배열을 만들 때 np.array() 메소드를 이용. 이 메소드에 리스트를 인수로 받아 넘파이 라이브러리가 제공하는 특수한 형태의 배열을 반환한다.

```python
>>> x = np.array([1.0, 2.0, 3.0])
>>> y = np.array([2.0, 4.0, 6.0])
>>> x+y
array([3., 6., 9.])
>>> x-y
array([-1., -2., -3.])
>>> x*y
array([2., 8., 18.])
>>> x/y
array([0.5, 0.5, 0.5])
```
넘파이 배열로 산술연산을 할 수 있다. 단, 배열 x와 y의 원소수가 같아야 된다.
위 코드는 원소별 연산을 하는 예이다.

```python
>>> x = np.array([1.0, 2.0, 3.0])
>>> x / 2.0
array([0.5, 1., 1.5])
```
넘파이 배열과 수치 하나(스칼라값)의 조합으로 된 산술 연산의 예이다.
브로드캐스트라고 한다.

```python
>>> A = np.array([[1,2],[3,4]])
>>> print(A)
[[1 2]
 [3 4]]
 >>> A.shape
 (2,2)
 >>> A.type
 dtype('int64')
 >>> B = np.array([[3,0],[0,6]])
 >>> A+B
 array([[4, 2],
        [3, 10]])
>>> A*B
array([[3, 0],
       [0, 24]])
>>> A*10
array([[10, 20]
       [30, 40]])
```
넘파이에서는 n차열 배열을 만들 수 있다. 
형상이 같은 행렬끼리면 행렬의 산술 연산도 대응하는 원소별로 계산된다.
또 행렬과 스칼라값의 산술 연산도 가능하다. 배열과 마찬가지로 브로드캐스트 기능이 작동된다.

```python
>>> A = np.array([[1,2],[3,4]])
>>> B = np.array([10,20])
>>> A*B
array([[10, 40],
       [30, 80]])
```
넘파이는 형상이 다른 배열끼리도 계산할 수 있다. 브로드캐스트 기능을 제공하기 때문이다. 

```python
>>> X = np.array([[51,55],[14,19],[0,4]])
>>> X = X.flatten() # 1차원 배열로 변환(평탄화)
>>> print(X)
[51 55 14 19 0 4]
>>> X[np.array([0,2,4])] # 인덱스가 0,2,4인 원소 얻기
array([51,14,0])
>>> X>15
array([True, True, False, True, False, False], dtype=bool)
>>> X[X>15]
array([51,55,19])
```

# matplotlib

matplotlib는 그래프를 그려주는 라이브러리이다.
딥러닝 실험에서 그래프 그리기와 데이터 시각화는 중요하다. 
matplotlib를 사용해 그래프 그리기와 데이터 시각화가 쉬워진다.

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.arange(0, 6, 0.1) # 0~6까지 0.1 간격으로 생성
y = np.sin(x)

plt.plot(x,y)
plt.show()
```
위 코드에서 arange메소드를 통해 [0, 0.1, 0.2, ... 5.8, 5.9] 데이터를 생성한다.

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.arange(0, 6, 0.1) # 0~6까지 0.1 간격으로 생성
y1 = np.sin(x)
y2 = np.cos(x)

plt.plot(x, y1, label="sin")
plt.plot(x, y2, linestyle="--", label="cos") # cos함수 점선으로 그리기
plt.xlabel("x") # x축 이름
plt.ylabel("y") # y축 이름
plt.title('sin & cos') # 제목
plt.legend()
plt.show()
```

pyplot 기능을 이용하면 여러개의 그래프, 제목, 축 이름 표시등이 가능하다.

```python
import matplotlib.pyplot as plt
from matplotlib.image import imread

img = imread('lena.png')

plt.imshow(img)
plt.show()
```
pyplot에는 이미지를 표시해주는 imshow메소드도 있다.

