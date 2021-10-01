---
title:  "[JAVA] 5. 클래스 (1)"
date: 2021-06-23 16:25:00
categories:
  - JAVA 
tags:
  - JAVA 
  - 클래스
  - 오버로딩
  - 가비지
---

# 객체 지향 언어
객체 지향 언어는 실세계의 객체를 프로그램 내에 표현하기 위해 클래스와 객체 개념을 도입했다.
* 캡슐화(Encapsulation) : 캡슐화란 객체를 캡슐로 싸서 내부를 보호하고 불수 없게 하는 것으로 객체의 가장 본질적인 특징이다. 자바에서는 클래스라는 캡슐을 사용하며, 필드(멤버 변수)와 메소드(멤버 함수)로 구성된다.
* 상속(Inheritance) : 자바의 상속은 자식 클래스가 부모 클래스의 속성을 물려받고 기능을 추가하여 확장하는 개념이다. 부모 클래스를 슈퍼 클래스라고 하고 자식 클래스를 서브 클래스라고 부른다. 상속은 슈퍼 클래스의 필드와 메소드를 물려받아 코드를 재사용해 코드 작성에 드는 시간과 비용을 줄인다.
* 다형성(Polymorphism) : 다형성은 같은 이름의 메소드가 클래스 혹은 객체에 따라 다르게 동작하도록 구현되는 것을 말한다. 슈퍼 클래스에 구현된 메소드를 서브 클래스에서 동일한 이름으로 자신의 특징에 맞게 다시 구현하는 **오버라이딩**과 클래스 내에서 이름이 같지만 서로 다른 동작하는 메소드를 여러개 만드는 **메소드 오버로딩**이 있다.

<br>
<br>

# 클래스
클래스는 객체를 만들어 내기 위한 설계도 혹은 틀이다.
클래스 모양 그대로 생성된 실체가 객체이다. 객체를 클래스의 인스턴스(instance)라고도 부른다.

```java
public class Circle{
    public int radius;
    public String name;

    public Circle(){
        this(1,"n");
    }
    public Circle(int radius){
        this.radius = radius;
    }
    public Circle(int radius, String name){
        this.radius = radius;
        this.name = name;
    }
    public double getArea(){
        return 3.14*radius*radius;
    }
    public Circle getMe(){
        return this;
    }
}

public class ex{
    public static void main(String[] args){
        Circle pizza; //레퍼런스 변수 선언
        pizza = new Circle(); // new 연산자로 객체 생성
        
        Circle donut = new Circle();
        donut.radius = 2;
        double area = donut.getArea();
    }
}
```
### 생성자
생성자는 객체가 생성될 때 객체의 초기화를 위해 실행되는 메소드이다. 생성자를 구현하지 않으면 기본생성자, 디폴트 생성자가 생성되어 실행된다. 다만 생성자가 하나라도 존재하면 기본 생성자는 자동으로 생성되지 않는다. 생성자는 객체를 만들면 자동으로 호출된다. 생성자는 다음과 같은 규칙이 있다.

1. 생성자의 이름은 클래스 이름과 동일하다
2. 생성자는 여러개 작성(오버로딩)할 수 있다.
3. 생성자는 new를 통해 객체를 생성할 때 한번만 호출된다.
4. 생성자에 리턴타입을 지정할 수 없다.

### this 레퍼런스
this는 객체 자신에 대한 레퍼런스이다.

this는 매개변수 있는 생성자에서 사용된다. (위 코드 참조)

또 메소드가 객체 자신의 레퍼런스를 리턴해야 하는 경우에도 사용된다.

this()로 다른 생성자를 호출할 수도 있다. 이때 주의 사항이 있다.
1. this()는 반드시 생성자 코드에서만 호출할 수 있다.
2. this()는 반드시 같은 클래스 내 다른 생성자를 호출할 때 사용된다.
3. this()는 반드시 생성자의 첫번째 문장이 되어야 한다.

<br>
<br>

# 객체 배열

```java
Circle [] c = new Circle[5];

for(int i=0; i<c.length; i++){
    c[i] = new Circle(i);
}

for(int i=0; i<c.length; i++){
    System.out.println((int)(c[i].getArea()));
}
```
<br>
<br>

# 메소드 활용

## 1. 인자전달

메소드의 인자에 기본타입(byte, char, short, int, long, float, double, boolean)인 경우 값이 매개변수에 복사되어 전달된다.

메소드 매겨변수에 객체나 배열이 전달될 때 복사본이 전달되는 것이 아닌 객체의 레퍼런스가 전달되어 메모리의 오버헤드가 없지만, 값을 수정하면 실제 그 객체나 배열의 값이 변경된다.

## 2. 메소드 오버로딩
한 클래스 내 이름이 같지만 매개변수의 타입이나 개수가 서로 다른 여러개의 메소드를 중복 작성 가능하다. 메소드 오버로딩 또는 메소드 중복이라고 부른다.
* 메소드 이름이 동일해야 한다.
* 매개변수의 개수나 타입이 서로 달라야 한다.

```java
class Overloading{
    public int getSum(int i, int j){
        return i+j;
    }
    public int getSum(int i, int j, int k){
        return i+j+k;
    }
}

class OverloadingFail{
    public int getSum(int i, int j){
        return i+j;
    }
    public double getSum(int i, int j){
        return (double)(i+j);
    }
}
```
<br><br>

# 객체의 소멸과 가비지 컬렉션

자바에서 객체를 생성하는 new연산자가 있지만 객체를 소멸시키는 연산자는 없다.

객체소멸 : new에 의해 생성된 객체 공간을 자바 가상 기계에서 돌려주어 가용 메모리에 포함시키는 것.

자바에서는 new로 할당받은 후 사용하지 않게 된 객체 메모리는 가비지(garbage)라고 부르며, 자바 가상 기계의 가바지 컬렉터(garbage collector)가 적절한 시점에 자동으로 수집해 가용 메모리에 반환시킨다.

여기서 가비지는 자바 응용프로그램 내에서 더이상 사용되지 않게된 객체나 배열 메모리이다. 객체를 아무도 참조하지 않게 되어 더이상 접근할 수 없게되면 그 객체를 가비지라고 한다. 

자바플랫폼은 가용 메모리가 일정 크기 이하로 줄어들면 자동으로 가비지를 회수해 가용 메모리를 늘린다. 이것을 가비지 컬렉션이라고 부르며, 가비지 컬렉션은 자바 플랫폼에 의해 준비된 가비지 켈렉션 스레드에 의해 처리된다.

가비지 컬렉터가 실행되면 응용프로그램은 실행을 멈추고 가비지 컬렉션이 끝나기를 기다리게 되어 사용자의 눈에 프로그램이 중단된 것처럼 보이기 때문에 자바는 실시간 처리 응용에는 부적합하다.

가비지 컬렉션 강제요청 명령어도 있다. System 또는 Runtime 객체의 gc() 메소드를 호출하면 가비지 컬렉션을 요청할 수 있다.

```java
System.gc();
```
위 코드로 즉시 가비지 컬렉터가 작동하지 않는다. 단지 가비지 컬렉션이 필요하다는 요청에 불과해 가비지 컬렉션은 자바 플랫폼이 전적으로 판단해 적절한 시점에 동작한다.


