---
title:  "[코딩테스트 스터디/C++] University of UIm Local Contest 어두운길"
excerpt: "University of UIm Local Contest 어두운길 풀이"
date: 2021-08-30 16:42:00
categories:
  - 스터디
  - 코딩테스트

tags:
  - 스터디
  - 코딩테스트
---

## 1.문제 

특정한 도로의 가로등을 하루동안 켜기 위한 비용은 해당 도로의 길이와 동일. 정부에서는 일부 가로등을 비활성화해서 절약할 수 있는 최대 금액을 구하고자 한다.
한마을은 N개의 집과 M개의 도로로 구성. 각 집은 0번부터 N-1번까지의 번호로 구분.
(1<=N<=200000), (N-1<=M<=200000)

<br>
<br>

## 2. 풀이 전 계획과 생각

- **최소신장트리(MST) 개념 이용 - 크루스칼 알고리즘**


<br>
<br>

## 3. 풀이

```cpp
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;

int n, m;

vector<pair<int, pair<int, int> > > edges;

int parent[200001];

// 특정 원소가 속한 집합을 찾기  
int find_parent(int x){
	if(parent[x]!=x) return find_parent(parent[x]);
	return x;
}

// 두 원소가 속한 집합을 합치기  
void union_parent(int a, int b){
	a = find_parent(a);
	b = find_parent(b);
	if(a<b) parent[b]=a;
	else parent[a]=b;
}

int main(){
	// 입력  
	cin>>n>>m;
	
	// 입력  
	for(int i=0;i<m;i++){
		int v1, v2, cost;
		cin>>v1>>v2>>cost;
		edges.push_back({cost, {v1, v2}});
	}	
	
	// 부모테이블 초기화  
	for (int i = 1; i <= n; i++) {
        parent[i] = i;
    }
    
    // 비용 오름차순으로 정렬  
    sort(edges.begin(), edges.end());
	
	int on=0;
	int total=0; 
    // 간선을 하나씩 확인하며
    for (int i = 0; i < edges.size(); i++) {
        int cost = edges[i].first;
        int a = edges[i].second.first+1;
        int b = edges[i].second.second+1;
        total+=cost;
        // 사이클이 발생하지 않는 경우에만 집합에 포함
        if (find_parent(a) != find_parent(b)) {
            union_parent(a, b);
            on += cost;
        }
    }
	
	// 답 출력  
    cout << total- on;
}
```

1. 비용에 따라 오름차순으로 정렬

2. 간선을 하나씩 확인하면서 현재의 간선이 사이클을 발생시키는지 확인.
사이클이 발생하지 않는 경우 최소 신장 트리에 포함하고, 사이클이 발생하면 최소 신장 트리에 포함시키지 않는다.

3. 간선의 모든 비용의 합계에서 최소신장트리 간선 비용을 뺀다.

<br>
<br>

## 4. 풀이하면서 고민했던 점

- **사이클이 발생했는지 여부를 어떻게 판단할지 고민**
서로소 집합을 이용해 해결


<br>
<br>

## 5. 문제를 풀고 알게된 개념 및 소감

- **크루스칼 알고리즘에 대한 이해도가 올라감.**
