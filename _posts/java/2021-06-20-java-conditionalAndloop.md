---
title:  "[JAVA] 3. 조건문, 반복문"
date: 2021-06-20 12:58:00
categories:
  - JAVA 
tags:
  - JAVA 
  - 조건문
  - 반복문
---

# 조건문

조건문에는 if, if-else, switch문이 있다.

```java
int score, year;
boolean isStudent;

if(score>=60 || isStudent){
    if(year!=4){

    }
    else if(score>=70){

    }
    else{

    }
}
else{

}
```
if문의 조건식은 비교 연산이나 논리 연산이 혼합된 식으로 구성되며, 조건식의 결과는 boolean 값이다. 조건식이 참이면 fi 내부의 코드가 실행된다.

if-else문은 if 조건식이 참인 경우와 거짓인 경우에 각가 실행할 문장을 지시할 수 있다.

다중 if-else 문은 조건이 여러개 있을 수 있다. 이때 else if문을 통해 조건을 추가할 수 있다.

중첩 if-else문은 if문이나 if-else문, else문의 실행 문장에 if문이나 if-else문을 내포할 수 있다.
<br>

```java
switch(식){
    case 값1:
        실행문장;
        break;
    case 값2:
        실행문장;
        break;
    default:
        실행문장;
}
```
**case문의 값**: 정수 리터럴, 문자 리터럴, 문자열 리터럴만 허용.

switch문은 식을 계산하고 그 결과 값과 일치하는 case문으로 분기해 실행문장을 실행 후 break문을 만나 switch블록에서 빠져나간다.
만약 case문으로 분기못하면 default문으로 분기한다. default문은 생략 가능하다.

switch문에서 break 문은 중요하다. 만약 break문이 없으면 break문을 만날 때까지 해당 아래에 있는 모든 case문과 혹은 default문이 실행된다.
<br>

```java
int b;
switch(b){
    case a: ...; break;    //오류. 변수 a 사용 안 됨
    case b>3: ...; break;  //오류. 식 사용 안 됨
    case b==3: ...; break; //오류. 식 사용 안 됨
}
```

<br><br>

# 반복문

프로그램에서 동일한 연산이나 코드 블록을 반복적으로 실행하는 경우에 자바는 3가지 종류의 반복문을 제공한다.

for문, while문, do-while문

세가지 반복문은 기능적인 면에서 동일하다. 각 반복문을 다른 반복문으로 변환이 가능하다. 처음부터 반복 횟수나 범위가 명확한 경우에 for문을 많이 사용하고, 반복 횟수를 처음부터 알 수 없고, 반복이 진행되면서 평가되어야 하는 경우에는 while문, do-while문을 사용한다. 

## 1. for문
```java
for(초기문; 조건식; 반복 후 작업){
    작업문
}

//무한 반복
for(초기문; true; 반복 후 작업){
    작업문
}
for(초기문; ; 반복 후 작업){
    작업문
}

for(int i=0; i<10; i++, System.out.println(i)){
    작업문
}
```
* 초기문 : 주로 조건식에서 사용하는 변수를 초기화한다. 초기문은 시작할 때 한번만 사용되고 콤마로 분리해 여러 문장이 나열 가능하고 초기문은 빈 상태로 두어도 되지만 끝에 세미콜론은 있어야 한다.
* 조건식 : 논리형 변수나 논리 연산을 사용한다. 조건식의 결과가 true이면 반복이 계속되고, false면 for 문을 빠져나온다.
* 반복 후 작업 : 콤마로 구분해 여러문장을 나열 가능.
<br>

## 2. while문
```java
while(조건식){
    작업문
}

int i=0;
while(i<10){
    ...
    i++;
}

//무한 반복
while(ture){
    ...
}
```
while 반복문은 반복 횟수를 알 수 없는 경우에 적합. while문의 조건식은 for문의 경우와 동일하다. true인 동안 작업문이 반복 실행된다.
조건식에서 사용되는 변수를 whlie문 실행 전에 초기화해야 한다.
<br>

## 3. do-while문
```java
do{
    작업문
}while(조건식);

int i=0;
do{
    i++;
}while(i<10);
```
do-while 문의 조건식은 while문과 동일하며 조건식이 없으면 컴파일 오류가 발생한다. 다른 반복문과 달리 do-while문은 작업문 실행 후 조건식을 검사해 작업문이 최초 한번은 반드시 실행된다.
<br>

## 4. 중첩 반복
반복문 안에 다른 반복문을 만들 수 있다. 이것을 중첩 반복(nested loop)라고 한다.
반복은 몇 번이고 중첩 가능하지만, 2중 또는 3중 중첩 이상하지 않는 것이 바람직하다.
```java
for(int i=0; i<100; i++){
    for(int j=0; j<10000; j++){
        ...
        ...
    }
    ...
}
```
<br>
<br>

# Continue문과 break문
반복문 내에 continue문이나 break 문을 이용해 반복의 흐름을 제어할 수 있다.
break문은 현재 반복문을 벗어나게 해주고, continue문은 즉시 다음 반복으로 넘어가게 해준다.

* continue
for문에서는 반복 후 작업으로 분기하고, while, do-while문에서는 조건식을 검사하는 과정으로 분기한다.

* break
현재 반복문을 벗어나 실행이 계속된다. break문은 하나의 반복문만 벗어나기 때문에 중첩 반복의 경우 안쪽 반복문에서 break문이 실행되면 안쪽 반복문만 벗어나고 바로 바깥 쪽 반복문 내에서 실행이 유지된다.

```java
while((n%5==0) || (n<0)){
    ...
}

while(true){
    if(n%5 == 0) break;
    if(n<0) break;
    ...
}
```
while문을 벗어나는 방법은 위 두가지가 있다. while문에 조건식을 이용하는 방법과 우선 while문의 조건식에 true을 적어 무한 반복문을 작성하고 if문으로 벗어나는 조건을 만드는 경우가 있다.
