---
title:  "[코딩테스트 스터디/C++] Goldman Sachs 인터뷰 편집거리"
excerpt: "Goldman Sachs 인터뷰 편집거리 풀이"
date: 2021-08-21 11:39:00
categories:
  - 스터디
  - 코딩테스트

tags:
  - 스터디
  - 코딩테스트
---

## 1.문제 

두 개의 문자열 A와 B가 주어졌을 때, 문자열 A를 편집하여 문자열 B로 만들고자 한다. 문자열 A를 편집할 때는 다음의 세 연산 중에서 한 번에 하나씩 선택하여 이용할 수 있다.
1. 삽입(insert) : 특정한 위치에 하나의 문자를 삽입한다.
2. 삭제(remove) : 특정한 위치에 있는 하나의 문자를 삭제한다.
3. 교체(replace) : 특정한 위치에 있는 하나의 문자를 다른 문자로 교체한다.

이때 편집거리란 문자열 A를 편집하여 문자열 B로 만들기 위해 사용한 연산의 수를 의미한다. 문자열 A를 문자열 B로 만드는 최소 편집 거리를 계산하는 프로그램을 작성하시오. 

문자열의 길이는 1보다 크거나 같고, 5000보다 작거나 같다.

<br>
<br>

## 2. 풀이 전 계획과 생각

- **다이나믹 프로그래밍**


<br>
<br>

## 3. 풀이

```cpp
#include<iostream>
#include<cstring>
using namespace std;

string a, b;

int d[5001][5001];

int main(){
	// 입력  
	cin>>a>>b;
	
	int size_a = a.size(), size_b = b.size();
	
	// 배열 초기화  
	for(int i=0;i<=size_a;i++) d[i][0]=i;
	for(int i=0;i<=size_b;i++) d[0][i]=i;
	
	// 다이나믹 프로그래밍  
	for(int i=1;i<=size_a;i++){
		for(int j=1;j<=size_b;j++){
			int add;
			// 해당 인덱스 문자열이 같은 경우 add=0 
			if(a[i-1]==b[j-1]) add=0;
			else add=1;
			//최솟값으로 저장  
			d[i][j]=min(min(d[i][j-1]+1, d[i-1][j]+1), d[i-1][j-1]+add);	
		}
	}	
	
	/*
	for(int i=0;i<=size_a;i++){
		for(int j=0;j<=size_b;j++) cout<<d[i][j]<<' ';
		cout<<endl;
	}
	*/
	// 답 출력 
	cout<<d[size_a][size_b]; 
}
```


<br>
<br>

## 4. 풀이하면서 고민했던 점

- **없음**


<br>
<br>

## 5. 문제를 풀고 알게된 개념 및 소감
- **다이나믹 프로그래밍에 대한 이해가 높아졌다.**