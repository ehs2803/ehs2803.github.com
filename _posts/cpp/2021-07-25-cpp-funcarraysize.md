---
title:  "[C++] C++ 배열 사이즈 오류, 구하기 (함수에서)"
date: 2021-07-25 16:27:00
categories:
  - C++
tags:
  - c++
  - 배열
---

# c++ 배열 크기 구하기

```cpp
int a[5] = {1,2,3,4,5}

int size1 = sizeof(a)/sizeof(a[0]); // 5
int size2 = sizeof(a)/sizeof(int);  // 5
```
c++에서 배열의 크기를 구하기 위해서는 위 코드처럼 sizeof(a)/sizeof(a[0])으로 전체 배열 크기를 첫번째 원소로 나누는 방식으로 구할 수 있다.

혹은 전체배열 크기를 배열의 자료형으로 나누면 배열 크기를 구할 수 있다.

하지만 이 방법으로 배열의 크기를 구하는건 main함수에서만 유효하다.
<br>
<br>

# c++ 함수 매개변수에서 배열 크기 오류

```cpp
#include<iostream>
using namespace std;

int arraysizeTest(int array [])
{
	return sizeof(array)/sizeof(array[0]);
}

int main(){
	int array1[]={1};
	cout<<"array1 size in main : "<<sizeof(array1)/sizeof(array1[0])<<endl;
	cout<<"array1 size in func : "<<arraysizeTest(array1)<<endl;
	cout<<"--------------------------------"<<endl;
	int array2[]={1,2};
	cout<<"array2 size in main : "<<sizeof(array2)/sizeof(array2[0])<<endl;
	cout<<"array2 size in func : "<<arraysizeTest(array2)<<endl;
	cout<<"--------------------------------"<<endl;
	int array3[]={1,2,3,4,5,6,7,8,9,10};
	cout<<"array3 size in main : "<<sizeof(array3)/sizeof(array3[0])<<endl;
	cout<<"array3 size in func : "<<arraysizeTest(array3)<<endl;
	cout<<"--------------------------------"<<endl;
}
```
```cpp
array1 size in main : 1
array1 size in func : 2
--------------------------------
array2 size in main : 2
array2 size in func : 2
--------------------------------
array3 size in main : 10
array3 size in func : 2
--------------------------------
```
함수를 작성해 매개변수로 배열을 넘겨받아 위 방식으로 크기를 구하게 되면 무조건 2가 출력된다.

**그 이유는 배열이 함수로 넘어갈 때 포인터로 넘어가 배열의 size값이 안 넘어가기 때문이다.**
<br>
<br>

# c++ 함수 매개변수로 넘어온 배열 크기 구하기
```cpp
void myfunction(int array [], int size)
{
    ...
}

int main(){
    int a[]={1,2,3,4,5};
`   myfunction(a, sizeof(a)/sizeof(a[0]));
}
```
여러 방식이 있을 수 있겠지만 위 코드처럼 그냥 함수 매개변수로 배열이 사이즈를 함께 넘겨주면 된다.