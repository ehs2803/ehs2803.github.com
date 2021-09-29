---
title:  "[JAVA] 6. 클래스 (2)"
date: 2021-06-24 13:07:00
categories:
  - JAVA 
tags:
  - JAVA 
  - 접근지정자
  - static
  - final
---

# 1. 접근 지정자

**패키지** : 서로 관련 있는 클래스 파일들을 패키지에 저장하고 관리한다. 패키지는 디렉터리 혹은 폴더와 같은 개념이다.

자바의 4가지 접근 지정자 : 접근 지정자는 클래스나 멤버들을 다른 클래스에서 접근해도 되는지의 여부를 선언하는 지시어이다.

private, protected, public, 접근 지정자 생략(디폴트 접근 지정)


### 클래스 접근 지정
* public 클래스
패키지와 상관없이 다른 어떤 클래스에서도 사용이 허용된다.

* 디폴트 클래스(접근 지정자 생략)
디폴트 클래스는 같은 패키지 내의 클래스들에게만 사용이 허용된다.

### 멤버 접근 지정
private -> 디폴트 -> protected -> public 순으로 공개 범위가 넓어진다.

* public 멤버 
패키지를 막론하고 모든 클래스들이 접근 가능하다.

* private 멤버
private는 비공개를 지시하는 것으로 클래스 내의 멤버들에게만 접근이 허용된다.

* protected 멤버
보호된 공개를 지시하는 것으로 2가지 유형의 클래스에만 접근을 허용한다. 
첫째, 같은 패키지의 모든 클래스에 접근이 혀용된다.
둘째, 다른 패키지에 있더라도 자식 클래스의 경우 접근이 허용된다.

* 디폴트 멤버(default or package-private)
디폴트 멤버라고 한다. 동일한 패키지 내에 있는 클래스들만 디폴트 멤버를 자유롭게 접근할 수 있다.
<br>
<br>

# 2. static 멤버

|특성|non-static memeber|static member|
|--|------|------|
|공간적 특성|멤버는 객체마다 별도 존재로 **인스턴스 멤버**라고 부름|멤버는 클래스당 하나 생성. 멤버는 개체 내부가 아닌 별도의 공간에 생성되며, **클래스 멤버**라고 부른다.|
|시간적 특성|객체 생성 시에 멤버 생성됨. 객체가 사라지면 멤버도 사라진다.|클래스 로딩 시에 멤버가 생성되어 객체가 생기기 전에 이미 생성되어 객체 생성전에 사용이 가능하고 객체가 사라져도 멤버는 사라지지 않는다. 멤버는 프로그램 종료 시 사라짐.|
|공유의 특성|공유되지 않음. 멤버는 객체 내에 각각 공간 유지.|동일한 클래스의 모든 객체들에 의해 공유됨.|

static 멤버 접근은 두가지로 접근이 가능하다.
1. 클래스이름.static멤버
2. 객체이름.static멤버

static의 활용은 전역 변수와 전역 변수를 만들 때 활용하고 공유 멤버를 만들 때 사용된다.

static 메소드는 static 멤버만 접근이 가능하다. 또 this를 사용할 수 없다.

다만 non-static 메소드에서는 static 멤버 접근이 가능하다.
<br>
<br>

# 3. final
final 키워드는 3군데에 사용된다.

### final 클래스
final이 클래스 이름 앞에 사용되면 클래스를 상속받을 수 없다는 것을 지정한다.

```java
final class FinalClass{ // 이 클래스는 상속 불가

}

class SubClass extends FinalClass{ // 컴파일 오류

}
```

### final 메소드
final로 메소드를 선언하면 오버라이딩할 수 없는 메소드임을 선언한다.
자식 클래스가 부모 클래스의 특정 메소드를 오버라이딩하지 못하게 하고 무조건 상속받아 사용하도록 한다면 final로 지정하면 된다.

```java
class SuperClass{ 
    protected final int finalMethod(){ ... } //finalMethod()는 자식이 오버라이딩 불가
}

class SubClass extends FinalClass{ 
    protected int finalMethod() { ... } //컴파일 오류. finalMethod() 오버라이딩 안됨
}
```

### final 필드
final로 필드를 선언하면 필드는 상수가 된다.

```java
public class FinalFieldClass{
    final int ROWS = 10;  
}
```
```java
class SharedClass{
    public static final double PI = 3.14;
}
```
public static과 함께 선언하면 프로그램 전체에서 사용할 수 있는 상수가 된다.

```java
double area1 = PI*radius*radius; //SharedClass 내에서 다음과 같이 사용.

double area2 = SharedClass.PI*radius*radius; //다른 클래스에서 사용할 때.
```
