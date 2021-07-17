---
title:  "[자료구조/C++ STL] 스택(Stack), 큐(Queue), 덱(Deque)"
date: 2021-06-26 16:54:00
categories:
  - 자료구조
  - C++
  - 코딩테스트
tags:
  - 자료구조
  - C++
  - 코딩테스트
  - stl
  - stack
  - queue
  - deque
---

스택, 큐, 덱은 특정 위치에서만 원소를 넣거나 뺄 수 있는 제한이 걸려있다. 그래서 스택, 큐, 덱을 묶어서 **Restricted Structure**라고 부르기도 한다.

# 스택 (STACK)
선형 자료구조의 일종으로 Last In First Out (LIFO). 즉, 나중에 들어간 원소가 먼저 나온다. 이것은 Stack 의 가장 큰 특징이다. 차곡차곡 쌓이는 구조로 먼저 Stack 에 들어가게 된 원소는 맨 바닥에 깔리게 된다. 그렇기 때문에 늦게 들어간 녀석들은 그 위에 쌓이게 되고 호출 시 가장 위에 있는 녀석이 호출되는 구조이다.

**스택의 성질**
* 원소 추가 O(1)
* 원소 삭제 O(1)
* 상단 원소 확인 O(1)
* 제일 상단 원소 외에 나머지 원소들의 확인/변경이 원칙적으로 불가

스택에서는 제일 상단이 아닌 나머지 원소들의 확인/변경 기능이 제공되지 않는다. STL stack에서도 이 기능은 없다. 그리고 스택을 활용하는 예시들을 보면 애초에 제일 상단이 아닌 나머지 원소들의 확인/변경이 필요하지 않는다. 
하지만 배열을 이용해 스택을 구현하면 기본적인 스택의 기능 이외에도 제일 상단이 아닌 나머지 원소들의 확인/변경을 가능하도록 만들 수는 있다.

```cpp
#include<stack>
using namespace std;

int main(void){
    stack<int> s;
    s.push(10); // 10
    s.push(20); // 10 20
    s.push(30); // 10 20 30
    cout<< s.size() << '\n'; //3

    if(s.empty()){  }
    else{  //실행  }

    s.pop(); // 10 20
    cout<< s.top() <<endl; //20
    s.pop(); // 10
    s.pop(); //
    if(s.empty()){  //실행 }
    else{  }

    cout<< s.top() <<endl; // runtime error 발생
}
```
스택이 비어있는데 top을 호출하면 런타임 에러가 발생한다. 스택이 비어있는데 pop을 호출해도 마찬가지이다. 그렇기 때문에 스택이 비어있을 때에는 top이나 pop을 호출하지 않도록 해야한다. 
<br>
<br>

# 큐 (QUEUE)
선형 자료구조의 일종으로 First In First Out (FIFO). 즉, 먼저 들어간 놈이 먼저 나온다. Stack 과는 반대로 먼저 들어간 놈이 맨 앞에서 대기하고 있다가 먼저 나오게 되는 구조이다. 참고로 Java Collection 에서 Queue 는 인터페이스이다. 이를 구현하고 있는 Priority queue등을 사용할 수 있다.

**큐의 성질**
* 원소 추가 O(1)
* 원소 삭제 O(1)
* 제일 앞/뒤 원소 확인 O(1)
* 제일 앞/뒤 원소 외에 나머지 원소들의 확인/변경이 원칙적으로 불가

스택과 비슷한 맥락으로 큐라는 자료구조에서는 인덱스를 가지고 원소에 접근하는 기능이 없지만, 배열을 가지고 직접 만들 땐 해당 기능이 가능하도록 구현을 할 수 있다 정도로 요약이 가능하다. 

```cpp
#include<queue>
using namespace std;

int main(void){
    queue<int> q;
    q.push(10); // 10
    q.push(20); // 10 20
    q.push(30); // 10 20 30
    cout<< q.size() << endl; // 3

    if(q.empty()) { }
    else { //실행 }

    q.pop(); // 20 30
    cout<< q.front() << endl; // 20
    cout<< q.back() << endl;  // 30
}
```
보통 큐는 BFS랑 Flood Fill를 할 때 쓰게 되는데 둘 다 코딩테스트 단골 문제여서 문제를 풀 때 STL queue를 쓸 일이 아주 많을 것이다. 
 
그리고 큐가 비어있는데 front나 back이나 pop을 호출하면 런타임에러가 발생한다.

STL queue에는 인덱스로 내부 원소를 접근하는 기능이 없다.
<br>
<br>


# 덱(DEQUE)
덱은 Restricted Structure의 끝판왕과 같은 느낌의 자료구조인데, 양쪽 끝에서 삽입과 삭제가 전부 가능하다. 참고로 자료구조의 덱은 deque고 Double Ended Queue라는 뜻을 가지고 있다. 
 
덱은 양쪽 끝에서 삽입과 삭제가 전부 가능한 자료구조이니 스택과 큐를 덱의 특수한 예시이다.

**덱의 성질**
* 원소 추가 O(1)
* 원소 삭제 O(1)
* 제일 앞/뒤 원소 확인 O(1)
* 제일 앞/뒤 원소 외에 나머지 원소들의 확인/변경이 원칙적으로 불가

제일 앞/뒤가 아닌 나머지 원소들의 확인/변경이 원칙적으로 불가능한데 독특하게도 STL deque에서는 인덱스로 원소에 접근할 수 있습니다. STL stack, queue에서 불가능했던걸 생각하면 꽤 독특한 일이다.

```cpp
#include<deque>
using namespace std;

int main(void){
    deque<int> dq;
    dq.push_front(10); // 10
    dq.push_back(20);  // 10 20
    dq.push_front(30); // 30 10 20
    cout<< dq.size() <<endl; // 3

    cout<< dq.front() <<endl; // 30
    cout<< dq.back() <<endl; // 20

    dq[1] = 11; // 30 11 20

    if(dq.empty()) { }
    else { //실행 }

    dq.pop_front(); // 11 20
    dq.pop_back(); // 11
    
    dq.push_back(20); // 11 20
    dq.push_back(30); // 11 20 30

    dq.insert(dq.begin()+1, 15); // 11 15 20 30
    dq.insert(dq.begin()+4, 40); // 11 15 20 30 40

    for(auto x : dq) cout<< x << ' ';

    dq.erase(dq.begin()+3); // 11 15 20 40

    cout<< dq[3] << endl; // 40

    dq.clear() // 모든 원소 제거

}
```
STL에 덱이 있어서 그냥 가져다쓰면 되는데, STL deque은 매우 독특하게도 double ended queue라는 느낌보다는 vector랑 비슷한데 front에서도 O(1)에 추가와 제거가 가능한 느낌이 강하다. pop_front, push_front, pop_back, push_front 함수뿐문 아니라 이외에도 insert도 있고 erase도 있고 인덱스로 원소에 접근 가능하다.

stl vector에서 제공되는 기능을 STL deque에서도 다 제공해주고 심지어 deque은 front에서도 O(1)에 추가와 제거가 가능하니 deque이 vector보다 상위호환이 아닌가 하는 생각이 들 수 있지만, vector와 달리 deque은 모든 원소들이 메모리 상에 연속하게 배치되어 있지 않다. 

앞쪽과 뒷쪽에서의 추가와 제거가 모두 필요하면 당연히 STL deque을 사용하는게 효율적이지만 굳이 앞쪽에서의 추가와 제거가 필요하지 않고 배열과 같은 느낌으로 쓰고싶을 땐 STL deque말고 STL vector를 쓰면된다.
