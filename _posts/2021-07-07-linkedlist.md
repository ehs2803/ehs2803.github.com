---
title:  "[자료구조/C++ STL] 연결리스트(LinkedList), STL list"
date: 2021-07-07 22:29:00
categories:
  - 자료구조
  - C++
  - 코딩테스트
tags:
  - 자료구조
  - C++
  - 코딩테스트
  - stl
  - 연결리스트
  - list
---

#  연결리스트

연결 리스트가 무엇인가 하면, 원소들을 저장할 때 그 다음 원소가 있는 위치를 포함시키는 방식으로 저장하는 자료구조

**연결리스트 성질**
1. k번째 원소를 확인/변경하기 위해서 O(K)가 필요함
2. 임의의 위치에 원소를 추가/임의 위치의 원소 제거는 O(1)
3. 원소들이 메모리 상에 연속해있지 않아 cache hit rate가 낮지만 할당이 다소 쉬움
<br>

**연결리스트 종류**
1. 단일 연결 리스트(Singly Linked List) : 각 원소가 자신의 다음 원소 주소를 들고 있는 연결리스트
2. 이중 연결 리스트(Double Linked List) : 각 원소가 자신의 이전 원소와 다음 원소의 주소를 둘다 들고 있다. 단일 연결 리스트는 주어진 원소의 이전 원소가 무엇인지 알 수 없지만 이중 연결 리스트는 알 수 있다. 다만 원소가 가지고 있어야 하는 정보가 1개 더 추가되어 메모리를 더 쓴다는 단점이 있다.
3. 원형 연결 리스트(Circular Linked List) : 끝이 처음과 연결되어 있다. 각 원소가 이전과 다음 원소의 주소를 모두 들고 있어도 들고있지 않아도 상관없다.
<br>


**시간복잡도**
* 임의의 위치에 있는 원소를 확인/변경 : O(N)
* 임의의 위치에 원소를 추가 : O(1)
* 임의의 위치에 원소를 제거 : O(1)

<br>

**배열vs연결 리스트**

||배열|연결 리스트|
|---|--|--|
|k번째 원소에 접근|O(1)|O(K)|
|임의 위치에 원소 추가/제거|O(N)|O(1)|
|메모리 상의 배치|연속|불연속|
|추가적으로 필요한 공간(Overhead)|-|O(N)|

<br>
<br>


# STL list

stl 연결리스트 list는 이중 연결 리스트이다.

```cpp
#include<iostream>
#include<list>

int main(){
    list<int> L = {1,2}; // 1 2
    list<int>::iterator t = L.begin();
    L.push_front(10); // 10 1 2
    cout<< *t; // 1
    L.push_back(5); // 10 1 2 5
    L.insert(t, 6); // 10 6 1 2 5
    t++;
    t = L.erase(t); // 10 6 1 5, t가 가리키는 값은 5

    cout<< *t; // 5
    for(auto i : L) cout<< i << ' ';
    for(list<int>::iterator it = L.begin(); it!=L.end(); it++)
        cout<< *it << ' ';
}
```

STL list에서 push_back, pop_back, push_front, pop_front는 모두 O(1)이고, iterator가 주소 역할을 한다. list::iterator라는 type을 치기가 귀찮으면 C++11 이상일 때 auto t = L.begin()으로 대체 가능하다.

그리고 erase는 제거한 다음 원소의 위치를 반환한다. 

순회를 할 때에는 C++11 이상이라면 for(auto i : L) 처럼 편하게 할 수 있지만,  C++11 미만이라면 위 코드 처럼 iterator등을 사용해 아주 불편하게 할 수 밖에 없다.