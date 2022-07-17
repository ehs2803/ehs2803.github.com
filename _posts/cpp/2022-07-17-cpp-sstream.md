---
title:  "[C++] C++ 문자열 쪼개기(sstream)"
date: 2022-07-17 18:06:00
categories:
  - C++
  - 코딩테스트
tags:
  - c++
  - 문자열
  - 코딩테스트
---


# sstream을 이용한 문자 쪼개기

```cpp
#include <sstream>
#include <vector>
#include <iostream>
#include <string>
using namespace std;

int main(){
	string input1="a:b:c:d:e";
	string input2="f g h i j";
	
	stringstream ss1(input1);
	stringstream ss2(input2);
	
	vector<string> ret1, ret2;
	string tmp;
	
	while(getline(ss1, tmp, ':')){
		ret1.push_back(tmp);
	}
	
	while(getline(ss2, tmp, ' ')){
		ret2.push_back(tmp);
	}
	
	for(int i=0;i<ret1.size();i++){
		cout<<ret1[i]<<' ';
	}
	cout<<'\n';
	
	for(int i=0;i<ret2.size();i++){
		cout<<ret2[i]<<' ';
	}
	cout<<'\n';
}

```
```
a b c d e
f g h i j
```
c++에서는 파이썬이나 자바와 같이 특정 문자를 기준으로 split하는 메소드가 없다.

따라서 위 코드와 같이 문자열을 분리할 수 있다.