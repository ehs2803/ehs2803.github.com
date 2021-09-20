---
title:  "[프로그래머스/C++] 섬 연결하기"
excerpt: "프로그래머스 섬 연결하기 풀이"
date: 2021-09-20 22:21:00
categories:
  - programmers
  - 코딩테스트

tags:
  - programmers
  - 코딩테스트
---

## 1.문제 링크

https://programmers.co.kr/learn/courses/30/lessons/42861

<br>
<br>

## 2. 풀이 전 계획과 생각

- **최소신장 트리 만들기**


<br>
<br>

## 3. 풀이

```cpp
#include <vector>
#include <algorithm>
using namespace std;

vector<pair<int, pair<int, int> > > edges;

int parent[101];

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

int solution(int n, vector<vector<int>> costs) {
    
    for(int i=1;i<=n;i++){
		parent[i]=i;
	}
    
    for(int i=0;i<costs.size();i++){
        edges.push_back({costs[i][2],{costs[i][0],costs[i][1]}});
    }
    
    sort(edges.begin(), edges.end());
    
    int answer = 0;
    for (int i = 0; i < edges.size(); i++) {
        int cost = edges[i].first;
        int a = edges[i].second.first;
        int b = edges[i].second.second;
        // 사이클이 발생하지 않는 경우에만 집합에 포함
        if (find_parent(a) != find_parent(b)) {
            union_parent(a, b);
            answer += cost;
        }
    }

    return answer;
}
```

1. 모든 입력을 edges 벡터에 삽입. {비용, {노드1, 노드2}} 형태로 삽입.
2. edges 벡터를 오름차순으로 정렬.
3. 크루스칼 알고리즘으로 최소신장 트리를 구함.

<br>
<br>

## 4. 풀이하면서 고민했던 점

- **없음**


<br>
<br>

## 5. 문제를 풀고 알게된 개념 및 소감

- **크루스칼 알고리즘 구현을 복습함.**