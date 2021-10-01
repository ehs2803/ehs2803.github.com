---
title:  "[코딩테스트 스터디/C++] google 인터뷰 못생긴수"
excerpt: "google 인터뷰 못생긴수 풀이"
date: 2021-08-20 13:23:00
categories:
  - 스터디
  - 코딩테스트

tags:
  - 스터디
  - 코딩테스트
---

## 1.문제 

못생긴수란 오직 2,3,5만을 소인수로 가지는 수를 의미한다. 다시 말해 2,3,5를 약수로 가지는 합성수를 의미한다. 1은 못생긴 수라고 가정한다. 따라서 못생긴 수들은 {1,2,3,4,5,6,8,9,10,12,15, ...} 순으로 이어지게 된다. 이때 n번째 못생긴 수를 찾는 프로그램을 작성하세요. 예를 들어 11번째 못생긴 수는 15이다.

<br>
<br>

## 2. 풀이 전 계획과 생각

- **다이나믹 프로그래밍**


<br>
<br>

## 3. 풀이

```cpp
#include<iostream>
#include<queue> 
using namespace std;

int n;

int d[1000];

queue<int> two, three, five;

int main(){
	// 입력  
	cin>>n;
	
	// 못생긴 수 첫번째는 1  
	d[0]=1;
	for(int i=1; i<n; i++){
		// 이전 못갱긴 수에 2,3,5 곱하기  
		two.push(d[i-1]*2);
		three.push(d[i-1]*3);
		five.push(d[i-1]*5);
		
		// 가장 작은 수를 못생긴 수 배열에 저장  
		int inputnum = min(min(two.front(), three.front()), five.front());
		d[i]=inputnum;
		
		// 중복 제거  
		if(inputnum==two.front()) two.pop();
		if(inputnum==three.front()) three.pop();
		if(inputnum==five.front()) five.pop(); 
	}	
	
	// 답 출력
	cout<<d[n-1];
}
```


<br>
<br>

## 4. 풀이하면서 고민했던 점

- **처음에 문제를 못 풀다가 못생긴수는 못생긴수에 2,3,5를 곱한 수라는 것을 알게됨**


<br>
<br>

## 5. 문제를 풀고 알게된 개념 및 소감
- **처음에 접근법을 몰라 생각보다 어려운 문제였다.**