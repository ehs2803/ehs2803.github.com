---
title:  "[JAVA] 4. 배열, 예외, 단정"
date: 2021-06-22 11:45:00
categories:
  - JAVA 
tags:
  - JAVA 
  - 배열
  - 예외
  - 단정
  - exception
  - assertion
---

# 1. 배열
배열은 인덱스와 인덱스에 대응하는 데이터들로 이루어진 연속적인 자료구조.
같은 종류의 데이터들이 순차적으로 저장.

## 배열 선언 및 생성
```java
int intArray [];         // 배열에 대한 레퍼런스 변수 선언
intArray = new int[5];   // 배열의 저장 공간 할당. 배열 생성
```
intArray는 배열 공간에 대한 주소값(레퍼런스값)을 가지며 그 자체가 배열이 아니다. 아직 정수를 저장할 배열 공간이 생성되지 않았으므로 intArray값은 null이다.

배열의 레퍼런스 변수는 두가지 방법으로 선언할 수 있다.
1. int intArray[];
2. int [] intArray;
```java
int intArray[10]; // 배열 선언 시 []안에 크기 지정 않됨. 오류남.
```
```java
int intArray[] = new int[5]; // 배열의 선언과 동시에 생성
```
```java
int intArray[] = {1,2,3,4,5,0}; // 크기 6인 정수형 배열 생성.
```
배열 선언문에서 {}에 원소를 나열하면 초기화된 배열을 만들 수 있다.
<br>

## 레퍼런스 치환과 배열 공유
```java
int intArray[] = new int[5];
int myArray[] = intArray;
```
치환으로 intArray 배열이 복사되는 것이 아니라, 레퍼런스 즉 배열에 대한 주소만 복사된다. 그 결과 myArray는 intArray와 동일한 레퍼런스 값을 가지게 되어 배열을 공유하게 되고 myArray로 intArray의 배열 원소를 마음대로 접근, 수정 할 수 있다.
<br>

## 배열의 크기
```java
int intArray[] = new int[5];
int size = intArray.length; // size = 5
```
자바는 배열을 객체로 다룬다. 배열이 생성되면 객체가 생성되는데 이 객체에는 배열의 저장공간과 함깨 배열의 크기 값을 가진 length 필드가 존재한다.
<br>

## 배열과 for-each 문

기존의 for문을 변형해 배열이나 나열(enumeration)의 크기만큼 루프를 돌면서 각 원소를 순차적으로 접근하는데 유용하게 만든 for문을 for-each문(오라클에서는 advanced for라고 부름)이라고 한다.

for(변수 : 배열레퍼런스){
    .. 반복 작업문 ..
}
```java
int [] n ={1,2,3,4,5}
int sum = 0;
for(int k: n){
    sum+=k;
}

String names[] = {"a","b","c"};
for(String s : names){
    System.out.println(s);
}

enum Week {월,화,수,목,금,토,일}
for(Week day : Week.values()){
    System.out.println(day+"요일");
}
```
<br>

## 다차원 배열
자바에서는 여러 차원으 배열을 만들 수 있다. 1차원 배열과 선언, 생성 방법이 동일하다.
1. 2차원 배열
```java
int intArray1 [][];
int [][] intArray2;

intArray1 = new int[2][5];
int size1 = intArray1.length; //2
int size2 = intArray1[0].length; //5
int size3 = intArray1[1].length; //5
```

2. 비정방형 배열
지금까지의 배열은 모두 정방형 배열이다. 정방형 배열은 각 행의 열 개수가 모두 동일한 배열이다. 비정방형 배열은 행마다 열의 개수가 서로 다른 배열을 의미한다.
```java
int i[][] = new int[4][];

i[0] = new int[1]; // 첫째 행에 1개 크기의 배열 생성
i[1] = new int[2]; // 둘째 행에 2개 크기의 배열 생성
i[2] = new int[3]; // 셋째 행에 3개 크기의 배열 생성
i[3] = new int[4]; // 넷째 행에 4개 크기의 배열 생성
```
<br>

## 메소드에서 배열 리턴
메소드에서 어떤 배열이든지 리턴하면 배열공간 전체가 아니라 배열에 대한 레퍼런스만 리턴된다.
```java
int[] makeArray(){
    int temp[] = new int[4];
    return temp;
}

int [] intArray = makeArray();

int [][] make2DArray(){
    return new int [3][4];
}

int [][] int2Array = make2DArray();
```
메소드는 레퍼런스만 리턴하기 때문에 리턴타입을 선언할 때 []안에 배열 크기를 지정하지 않는다. 또한 리턴하는 배열의 타입이 리턴받는 레퍼런스 변수의 타입과 일치해야 한다.
```java
public class ReturnArray{
    static int[] makeArray(){
        int temp[] = new int[4];
        for(int i=0;i<temp.length;i++)
            temp[i]=i;
        return temp;
    }

    public static void main(String[] args){
        int intArray [] = makeArray();
        for(int i=0;i<intArray.length;i++){
            System.out.print(intArray[i]+" ");
        }
    }
}
```
위 코드의 결과 값은
0 1 2 3
이다.
<br>
<br>


# 2. 예외
자바에서 오동작이나 결과에 악영향을 미칠 수 있는 실행 중 발생한 오류를 예외(Exception)이라고 한다. 문법에 맞지 않게 작성된 코드는 사전에 컴파일러에 의해 컴파일 오류로 걸러지지만, 예외는 사용자의 잘못된 입력이나 배열의 인덱스가 배열의 크기를 넘어가는 등 예기치 못한 상황에 의해 프로그램 실행 중에 발생한다.
* 정수를 0으로 나누는 경우
* 배열의 크기보다 큰 인덱스로 배열을 접근하는 경우
* 존재하지 않는 파일을 읽으려고 하는 경우
* 정수 입력을 기다리는 코드가 실행되고 있을 때, 사용자가 문자를 입력한 경우

실행 중 예외 발생 시 자바 플랫폼이 가장 먼저 알게 되어 현재 실행 중인 응용프로그램에게 예외를 전달한다. 만약 응용프로그램이 예외에 대처하는 코드를 가지고 있지 않다면 자바 플랫폼은 응용프로그램을 바로 종료시킨다.

```java
try{
    예외가 발생할 가능성이 있는 실행문
}
catch(처리할 예외 타입 선언){
    예외처리문
}
finally{
    예외 발생 여부와 상관없이 무조건 실행
}
```

**자바의 예외 클래스**
응용프로그램에서는 자바 플랫폼에서 제공하는 예외클래스를 이용해 예외 발생을 탐지할 수 있다.

|예외타입(예외클래스)|예외 발생 경우|패키지|
|----|------|----|
|ArithmeticException|정수를 0으로 나눌 때 발생|java.lang|
|NullPointerException|null 레퍼런스를 참조할 때 발생|java.lang|
|ClassCastException|변환할 수 없는 타입으로 객체를 변환할 때 발생|java.lang|
|OutOfMemoryError|메모리가 부족한 경우 발생|java.lang|
|ArrayIndexOutOfBoundsException|배열의 범위를 벗어난 접근 시 발생|java.lang|
|IllegalArgumenntException|잘못된 인자 전달 시 발생|java.lang|
|IOException|입출력 동작 실패 또는 인터럽트 시 발생|java.io|
|NumberFormatException|문자열이 나타내는 숫자와 일치하지 않는 타입의 숫자로 변환 시 발생|java.lang|
|InputMismatchException|Scanner 클래스의 nextInt()를 호출해 정수로 입력받고자 했지만 사용자가 문자를 입력한 경우 발생|java.util|

```java
int intArray[] = new int[5];
try{
    intArray[3] = 2;
    intArray[6] = 5;
}
catch(ArrayIndexOutOfBoundException e){
    System.out.println("배열 범위를 벗어남!");
}
```
<br>
<br>

# 3. 단정
단정(assertion)은 프로그램이 올바르게 실행되는 데 필요한 조건을 선언하는 프로그래밍 언어의 기능이다. 단정 선언에 기술하는 조건은 참이나 거짓의 결과가 되는 조건식이다. 식의 결과가 참인 경우에 실행이 계속되고 거짓인 경우 AssertionError 예외가 발생한다.

**단정의 선언** : assert <조건식> [: <문자열 정보>];

```java
public class AssertWithStringExample{
    static void drawBox(int x, int y, int w, int h){
        assert (x>=0) : "x must be 0 or more.";
        assert (y>=0) : "y must be 0 or more.";
        assert (w>=0) : "w must be 0 or more.";
        assert (h>=0) : "h must be 0 or more.";
    }

    public static void main(String[] args){
        drawBox(100,200,10,20);
        drawBox(0,-10,5,30);
    }
}
```

프로그램 실행 중에 단정에 명시된 조건 검사는 실행조건을 느리게 하므로 기본 상태에서는 조건 검사를 하지 않는다.

```java
C:\> java -ea <class name>
C:\> java -enableassertions <class name>
```
단정에 있는 조건 검사를 하고자 하면 위와 같이 옵션을 주어야 한다.
```java
C:\> java -da <class name>
C:\> java -disableassertions <class name>
```
자바 가상기계는 기본적으로 단정 검사를 하지 않지만 명시적으로 단정 검사를 하지 않도록 하는 방법도 있다.

일반적으로 단정을 사용해 실행에 필요한 조건을 기술하고 단정 조건이 거짓인 경우에 예외로 처리하는 것이 바람직하다. 그리고 단정은 자바 가상기계의 실행 옵션에 따라 검사를 생략할 수 있기 때문에 항상 검사를 해야 하는 부분에서는 예외를 사용해야 한다.
