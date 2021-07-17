---
title:  "[코딩테스트 / C++] 코딩테스트 코드작성 요령"
date: 2021-06-28 9:57:00
categories:
  - 코딩테스트
tags:
  - 코딩테스트
  - C++
---

# 함수에 인자 넘기기
```cpp
void fun1(vector<int> v1, vector<int> v2){
    ...
}

void fun2(vector<int>& v1, vector<int>& v2){
    ...
}
```
v1, v2의 크기가 N이라고 가정했을 때 N개의 원소들을 하나하나 복사하는 과정은 O(N). 그래서 fun1 함수는 의도하지 않게 시간복잡도가 O(N).

func에서는 매개변수를 참조에의한 호출 형태로 구현했다. 그러면 fun2 함수가 호출될 때 복사본을 따로 만들지 않고 참조 대상의 주소 정보만 넘어가기 때문에 시간복잡도는 O(1)이다.
<br>
<br>

# 입출력
코딩테스트에서 입력과 출력은 표준 입출력을 사용. 
C에서는 scanf/printf로 입력과 출력을 처리하고
C++에서는 cin/cout을 사용하는데, 
기능에 별 차이가 없어서 어느 것을 사용해도 상관이 없다. 
```cpp
int main(void){
    char a[10];
    printf("input :");
    scanf("%s", a);
    string s(a); // string s = a;
    printf("a is %s\n", a);
    printf("a is %s\n", s.c_str());
}
```
scanf/printf에서 단 한 가지 아쉬운 점이라고 한다면, C++ string을 처리할 수가 없다. C에서는 char*으로 문자열을 다루는데 사실 char*보다 C++ string이 월등하게 편해서 scanf/printf를 쓰면서도 C++ string을 활용하고 싶으면 일단 char*으로 입력을 받고 string으로 형 변환을 해서 원하는 작업을 다 끝낸 후에 c_str() 메소드를 이용해 출력하면 된다. 

```cpp
int main(void){
    char a1[10];
    scanf("%[^\n]", a1);

    char a2[10];
    gets(a2);
    puts(a2);

    string s;
    getline(cin, s);
}
```
scanf를 쓰든 cin로는 공백 있는 문자열을 입력받을 수 없다.
해결책으로는 3가지 방법이 있다. 

첫 번째는 scanf에서 줄바꿈(\n)이 나오기 전 까지 입력을 받는다는걸 명시하는 방식. 
두 번째는 gets 함수를 사용하는건데, 문제는 저 함수가 보안상의 이유로 C++14 이상에서는 제거. 
세 번째는 getline을 이용. 대신 이건 type이 C++ string이어야 한다.
 
두 번째는 환경에 따라 사용못할 수도 있으니
첫 번째나 세 번째를 사용.

```cpp
int main(void){
    ios::sync_with_stdio(0);
    cin.tie(0);
}
```
앞서 말했듯 scanf/printf를 쓰든 cin/cout을 쓰든 아무 상관이 없지만, cin/cout에서는 꼭 주의할게 있다.scanf/printf와 다르게 cin/cout은 입출력으로 인한 시간초과를 막기 위해서 ios::sync_with_stdio(0), cin.tie(0)이라는 두 명령을 실행시켜야 한다. 이걸 안해두면 입/출력의 양이 많을 때 시간초과가 날 수 있다. 
 
기본적으로 scanf/printf 등에서 쓰는 C stream과 cin/cout 등에서 쓰는 C++ stream은 분리가 되어있다. 그런데 printf와 cout을 번갈아하며 사용하는 상황을 생각해보면 코드의 흐름과 실제 출력이 동일하기 위해서 기본적으로 프로그램에서는 C++ stream과 C stream을 동기화하고 있다. 그런데 내가 C++ stream만 쓸거면 굳이 두 stream을 동기화하고 있을 필요가 없게 된다. 쓸데 없이 시간만 잡아먹기 때문이다.
그렇기 때문에 C++ stream만 쓸거면 동기화를 끊어버려서 프로그램 수행 시간에서 이득을 챙길 수 있고, 동기화를 끊는 명령이 sync_with_stdio(0)이다. 엄밀히 말해 인자가 bool type이라 sync_with_stdio(false)가 더 맞긴 한데 false보다 0이 더 짧으니 그냥 0으로 할 수 있다.
 대신 동기화를 끊었으면 절대 cout과 printf를 섞어쓰면 안된다. 
 
두 번째 명령인 cin.tie(0)을 이해하려면 버퍼라는 개념을 이해해야 한다.

sync_with_stdio를 쓴 이후로는 무조건 cin/cout만 쓰고 printf/scanf를 쓰면 안된다.
<br>
<br>

# 줄바꿈 - endl(X), '\n'(O)
endl은 개행문자("\n")를 출력하고 출력 버퍼를 비워라는 명령이다.
프로그램이 종료될 때 출력이 어떻게 생겼는지를 보고 채점을 진행하므로 중간 중간 버퍼를 비우라고 명령을 줄 필요가 전혀 없다.
 
줄바꿈이 필요하면 endl 말고 그냥 개행문자를 출력.

