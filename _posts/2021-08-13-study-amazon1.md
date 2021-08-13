---
title:  "[코딩테스트 스터디/C++] Amazon인터뷰 고정점 찾기"
excerpt: "Amazon인터뷰 고정점 찾기 풀이"
date: 2021-08-13 11:06:00
categories:
  - 스터디
  - 코딩테스트

tags:
  - 스터디
  - 코딩테스트
---

## 1.문제 

고정점이란, 수열의 원소 중에서 그 값이 인덱스와 동일한 원소를 의미한다. 예를 들어 수열 a = {-15,-4,2,8,13}이 있을 때 a[2]=2이므로, 고정점은 2가 된다.
하나의 수열이 N개의 서로 다른 원소를 포함하고 있으며, 모든 원소가 오름차순으로 정렬되어 있다. 이때 이 수열에서 고정점이 있다면 고정점을 출력하고 없다면 -1을 출력한다. 고정점은 최대 1개만 존재한다.

<br>
<br>

## 2. 풀이 전 계획과 생각

- **이진 탐색 사용**


<br>
<br>

## 3. 풀이

```cpp
#include<iostream>
#include<vector>
using namespace std;

int binarySearch(vector<int>& arr, int start, int end) {
    if (start > end) return -1;
    int mid = (start + end) / 2;
    // 고정점을 찾은 경우 중간점 인덱스 반환  
    if (arr[mid] == mid) return mid;
    // 중간점의 값보다 중간점이 작은 경우 왼쪽 확인
    else if (arr[mid] > mid) return binarySearch(arr, start, mid - 1);
    // 중간점의 값보다 중간점이 큰 경우 오른쪽 확인
    else return binarySearch(arr, mid + 1, end);
}

int main(){
	int N;	
	cin>>N;
	
	vector<int> v(N);
	
	for(int i=0;i<N;i++) cin>>v[i];
	
	// 이진탐색
    int ans = binarySearch(v , 0, N - 1);

    // 답 출력 
    cout << ans << '\n';
}
```


<br>
<br>

## 4. 풀이하면서 고민했던 점

- **없음**


<br>
<br>

## 5. 문제를 풀고 알게된 개념 및 소감
- **이진탐색 구현을 복습할 수 있었음**