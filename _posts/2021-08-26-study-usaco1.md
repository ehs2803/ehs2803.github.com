---
title:  "[코딩테스트 스터디/C++] USACO 숨바꼭질"
excerpt: "USACO 숨바꼭질 풀이"
date: 2021-08-26 14:34:00
categories:
  - 스터디
  - 코딩테스트

tags:
  - 스터디
  - 코딩테스트
---

## 1.문제 

동빈이는 1번 헛간으로부터 최단 거리가 가장 먼 헛간에 숨는다. 동빈이가 숨을 헛간의 번호를 출력하는 프로그램을 잣성하세요.

<br>
<br>

## 2. 풀이 전 계획과 생각

- **최단거리 알고리즘으로 풀기**


<br>
<br>

## 3. 풀이

```cpp
#include<iostream>
#include<vector>
#define INF 1e9 // 무한을 의미하는 값으로 10억을 설정
using namespace std;

int n, m;

// 각 노드에 연결되어 있는 노드를 담는 배열
vector<int> graph[20001];
// 방문한 적이 있는지 체크하는 목적의 배열 만들기
bool visited[20001];
// 최단 거리 테이블 만들기
int d[20001];

// 방문하지 않은 노드 중에서, 가장 최단 거리가 짧은 노드의 번호를 반환
int getSmallestNode() {
    int min_value = INF;
    int index = 0; // 가장 최단 거리가 짧은 노드
    for (int i = 1; i <= n; i++) {
        if (d[i] < min_value && !visited[i]) {
            min_value = d[i];
            index = i;
        }
    }
    return index;
}

// 다익스트라 알고리즘  
void dijkstra(int start) {
    d[start] = 0;
    visited[start] = true;
    for (int j = 0; j < graph[start].size(); j++) {
        d[graph[start][j]] = 1;
    }
	// 시작 노드를 제외한 전체 n - 1개의 노드에 대해 반복
    for (int i = 0; i < n - 1; i++) {
        // 현재 최단 거리가 가장 짧은 노드를 꺼내서, 방문 처리
        int now = getSmallestNode();
        visited[now] = true;
        // 현재 노드와 연결된 다른 노드를 확인
        for (int j = 0; j < graph[now].size(); j++) {
            int cost = d[now] + 1;
            // 현재 노드를 거쳐서 다른 노드로 이동하는 거리가 더 짧은 경우
            if (cost < d[graph[now][j]]) {
                d[graph[now][j]] = cost;
            }
        }
    }
}

int main(){
	// 입력  
	cin>>n>>m;
	
	// 입력  
	for(int i=0;i<m;i++){
		int x, y;
		cin>>x>>y;
		graph[x].push_back(y);
		graph[y].push_back(x);
	}	
	
	// 최단 거리 테이블을 모두 무한으로 초기화
    fill_n(d, 20001, INF);
	
	// 다익스트라 알고리즘 실행  
	int start = 1;
	dijkstra(start);

	// 답 구하기  
	int ans_n=1, ans_v;
	for(int i=2; i<=n ;i++){
		if(d[ans_n]<d[i]) {
			ans_n=i;
			ans_v=d[i];	
		}
	}
	
	int ans_c=0;
	for(int i=1;i<=n;i++){
		if(d[ans_n]==d[i]) ans_c++;
	}
	
	// 답 출력  
	cout<<ans_n<<' '<<ans_v<<' '<<ans_c;
}
```

다익스트라 알고리즘을 이용해 해결한다. 노드와 노드 사이의 거리는 무조건 1로 가정한다.

<br>
<br>

## 4. 풀이하면서 고민했던 점

- **연결되는 두 헛간을 저장하는 방법**
헛간 1,2가 연결되 있다고 입력으로 들어왔을 때 graph[1].push_back(2); 만 해줬는데 아애 실행이 못되는 노드가 있어서, graph[2].push_back(1); 이런식으로 한 입력에 두개의 입력을 넣어줬다.


<br>
<br>

## 5. 문제를 풀고 알게된 개념 및 소감
- **다익스트라 알고리즘에 대해 깊게 이해할 수 있었다.**