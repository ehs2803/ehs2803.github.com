---
title:  "[코딩테스트 스터디/C++] 프로그래머스 실패율"
excerpt: "프로그래머스 실패율 풀이"
date: 2021-08-11 20:00:00
categories:
  - 스터디
  - programmers
  - 코딩테스트

tags:
  - 스터디
  - programmers
  - 코딩테스트
---

## 1.문제 링크

https://programmers.co.kr/learn/courses/30/lessons/42889

<br>
<br>

## 2. 풀이 전 계획과 생각

- **정렬 알고리즘 사용**


<br>
<br>

## 3. 풀이

```cpp
#include<vector>
#include<algorithm>
using namespace std;

bool cmp(pair<int, double> a, pair<int ,double> b){
    if(a.second>b.second) return true;
    else if(a.second==b.second){
        if(a.first<b.first) return true;
    }
    return false;
}

vector<int> solution(int N, vector<int> stages) {
    vector<int> answer;
    
    // 각 스테이지에 도달한 유저수 구하기  
    vector<int> depart(N, 0);
    for(int i=0;i<stages.size();i++){
        if(stages[i]>N){
            for(int j=0;j<N;j++){
                depart[j]+=1;
            }
        }
        else{
            for(int j=0; j<stages[i]; j++){
                depart[j]+=1;
            }
        }
    }
    
    // 각 스테이지를 클리어하지 못하는 유저수 구하기  
    vector<int> curStage(N,0);
    for(int i=0;i<stages.size();i++){
        if(stages[i]>N) continue;
        curStage[stages[i]-1]+=1;
    }
    
    // 실패율 구하기  
    vector<pair<int, double> > failRate;
    for(int i=0;i<N;i++){
        if(depart[i]==0){
            failRate.push_back({i+1,0});
        }
        else{
            double temp= (double)curStage[i]/depart[i]; 
           failRate.push_back({i+1,temp }); 
        }
        
    }
    
    // 정렬  
    sort(failRate.begin(), failRate.end(), cmp);
    
    // 정답 입력  
    for(int i=0;i<N;i++){
        answer.push_back(failRate[i].first);
    }
    
    // 답 반환  
    return answer;
}
```

1. 각 스테이지에 도달한 플레이어 수 depart 벡터에 구하기
2. 각 스테이지 별 스테이지에 도달했으나 클리어하지 못하는 플레이어수 curStage 벡터에 구하기
3. failRate벡터에 각 스테이지 실패율 구하기
4. 실패율은 내림차순으로, 같으면 스테이지 번호를 오름차순으로 정렬

<br>
<br>

## 4. 풀이하면서 고민했던 점

- **실패율을 구할 때 스테이지에 도달한 플레이어수가 0인경우(0으로 나누는 경우)에 대해서 예외 처리를 않해 오류가 계속났었음.**

- **제한사항에서 stages 벡터에 N+1은 마지막 스테이지까지 클리어한 사용자를 의미하는데 이 경우에 대해서 depart벡터와 curStage벡터를 만들 때 고려해서 구현함.**

<br>
<br>

## 5. 문제를 풀고 알게된 개념 및 소감
- **제한사항을 처음에 읽지않고 문제를 풀어 고생을함. 앞으로 제한사항을 꼼곰히 읽어야 겠다고 느낌.**