# Java

##  1) 자바 프로그램의 개발과 구동

현실 세계에서 컴퓨터를 구동하기 위해서는 하드웨어와 운영체제, 그리고 그 위에서 구동될 소프트웨어가 필요하며, 소프트웨어 개발을 위한 개발 도구가 필요하다.
자바는 이러한 현실을 모방하여 가상 세계에서 소프트웨어를 작동시킨다.

|자바 가상 세계|
|:---:|
|JDK(Java Development Kit) - 자바 개발 도구|
|JRE(Java Runtime Environment) - 자바 실행 환경|
|JVM(Java Virtual Machine) - 자바 가상 기계|

- `JDK`는 `JRE`를 포함하고, `JRE`는 `JVM`을 포함한다.
	자바 프로그램을 개발하기 위해서는 `JDK`가 필요하며, 단지 실행만을 위해서라면 `JRE`가 필요하다.

- `JDK`을 이용해 개발된 프로그램은 `JRE`에 의해 `JVM` 상에서 구동된다.
	1. JDK에 포함된 javac.exe가 자바 소스코드(.java)를 자바 바이트코드(.class)로 변환
	2. JRE에 포함된 java.exe가 JVM을 구동시킴.
	3. JVM이 플랫폼(하드웨어와 운영체제)에 관계없이 바이트코드를 기계어로 해석 및 실행 ➡️ WORA(Write Once Run Anywhere):



# 객체지향 프로그래밍 (OOP, Object Oriented Programming)

## 1) 객체지향의 4대 특성

### 1. 추상화(Abstraction)

> 구체적인 것(Object)을 분해해 관찰자(Programmer)가 관심 있는 특성(Application Boundary 또는 Context)만 갖고 재조합하는 것
> 즉, 실제 사물을 정확히 복제하는 것이 아닌, 목적에 맞는 특성들만을 갖고 단순하게 묘사하는 모델링 기법

### 2. 상속

> 상위 클래스의 특성을 하위 클래스에서 그대로 물려받은 데에 더해 그 외 더 필요한 특성을 추가
> 즉, 하위 클래스에서 상위 클래스를 재사용 및 확장하는 것으로, 'is a kind of' 관계를 만족

### 3. 다형성(Polymorphism)

> 오버라이딩(Overriding) : 같은 메서드 이름과 같은 인자 목록으로 상위 클래스의 메서드를 재정의
> 오버로딩(Overloading) : 같은 메서드 이름과 다른 인자 목록으로 다수의 메서드를 중복정의

상위 클래스 타입의 객체 참조 변수를 사용하더라도 하위 클래스에서 오버라이딩한 메서드가 호출된다.

### 4. 캡슐화(Encapsulation)

> 정보 은닉


## 2) 자바의 객체지향

자바는 기본적으로 클래스를 통해 객체지향을 실현한다.

객체는 유일무이(unique)한 실존체이며, 클래스는 특정 객체들의 공통점을 묶어놓은 분류집합이다.
프로그래머는 개발 목적에 맞는 객체 특성들을 적절히 추출 및 모델링함으로써 클래스를 설계해야 한다.
이때 멤버변수, 메서드, instanceof 연산자, 접근 제어자, 그리고 abstract/static/final/instanceof/package 등 여러 키워드나 연산자를 적절히 활용하는 것이 좋겠다.


## 3) 객체지향 설계 5원칙 (SOLID)

- 결합도 : 모듈(클래스) 간의 상호의존도
- 응집도 : 하나의 모듈 내부에 존재하는 구성 요소들의 기능적 관련성

좋은 소프트웨어 설계를 위해서는 결합도(coupling)는 낮추고 응집도(cohesion)는 높이는 것이 바람직하다.
결합도가 낮으면, 모듈 간의 상호 의존성이 줄어들어 객체의 재사용/수정/유지보수가 용이하다.
응집도가 높으면, 각 모듈은 하나의 책임에 집중하여 독립성이 높아져 재사용/수정/유지보수하 용이하다.

아래 객체지향 설계 5원칙은 이러한 `High Cohesion` & `Low Coupling`이라는 고전적인 원칙을 객체지향의 관점에서 재정립한 것이다.
이는 디자인 패턴의 뼈대이자 스프링 프레임워크의 근간이기도 하다.

### 1. SRP(Single Responsibility Principle) - 단일 책임 원칙

> "어떤 클래스를 변경해야 하는 이유는 오직 하나뿐이어야 한다."

하나의 클래스는 하나의 기능만을 담당해야 한다.
어떤 클래스를 수정하게 되었을 때 다른 클래스들을 되도록 건드리지 않도록 클래스를 잘 추상화해야 한다.
혹시 메서드에 분기 처리를 위한 if문이 지나치게 많다면 단일 책임 원칙을 잘 지키지 않은 것은 아닌지 살펴보자.

### 2. OCP(Open Closed Principle) - 개방 폐쇄 원칙

> "소프트웨어 엔티티(클래스, 모듈, 함수 등)는 자신의 확장에 대해서는 열려 있어야 하지만, 주변의 변화에 대해서는 닫혀 있어야 한다."

가령 적절한 추상화 및 다형성을 통해 어떤 클래스의 객체를 생성하고 메서드를 실행하는 코드를 직접적으로 수정하지 않으면서도 실행되는 내용을 쉽게 변경할 수 있다.

자바 프로그램의 개발 과정에서도 개방 폐쇄 원칙을 엿볼 수 있다.
JVM 덕분에 자바 개발자는 현재 작성하고 있는 코드가 어떤 플랫폼 상에서 구동될지 신경 쓰지 않아도 된다.
자바 소스코드는 운영체제의 변화에 닫혀 있고, JVM은 운영체제의 확장에 열려 있는 것이다.

### 3. LSP(Liscov Substitution Principle) - 리스코프 치환 원칙

> "서브 타입은 언제나 자신의 기반 타입(base type)으로 교체할 수 있어야 한다."

객체지향에서의 상속은 조직도/계층도가 아닌 분류도가 되어야 한다.
하위 클래스의 인스턴스는 상위 클래스의 인스턴스 역할을 하는 데 문제가 없어야 한다는 것이다.

- 하위 클래스 is a kind of 상위 클래스
- 구현 클래스 is able to 인터페이스
위의 두 문장이 잘 성립하도록 구현된 프로그램은 리스코프 치환 원칙을 잘 지키고 있는 것이다.

### 4. ISP(Interface Segregation Principle) - 인터페이스 분리 원칙

> "클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안 된다."

클라이언트(사용자)를 기준으로 인터페이스를 분리해야 한다는 원칙이다.
만약 한 클래스를 이용하는 클라이언트가 여럿이고 필요로 하는 파트가 서로 다를 때, 각자에게 요구되는 파트를 인터페이스로 따로 빼내어 인터페이스의 단일 책임을 강조함을 의미한다.
아예 처음부터 극히 최소한의 기능만을 수행하는 인터페이스로 쪼개놓는 것도 한 방법이다.
클라이언트가 사용하지 않는 부분에 대해 어떠한 변경점이 발생하든 그에 간섭을 받아선 안 된다는 것이 핵심이다.

참고로, 단일 책임 원칙은 클래스의 책임을, 인터페이스 분리 원칙은 인터페이스의 책임을 정의한다.
클래스는 풍부할수록 좋고, 인터페이스는 작을수록 좋다.

### 5. DIP(Dependency Inversion Principle) - 의존 역전 원칙

> "고차원 모듈은 저차원 모듈에 의존하면 안 된다. 이 두 모듈 모두 다른 추상화된 것에 의존해야 한다."
> "추상화된 것은 구체적인 것에 의존하면 안 된다. 구체적인 것이 추상화된 것에 의존해야 한다."
> "자주 변경되는 구체(Concrete) 클래스에 의존하지 마라." > 즉, 자신보다 변하기 쉬운 것에 의존하지 마라.

위의 세 문장 모두 구체화가 아니라 추상화에 의존해야 함을 의미한다.
결국 하위 클래스나 구체 클래스가 아니라 비교적 변화가 적은 상위 클래스, 인터페이스, 추상 클래스에 의존하라는 뜻이다.
가령 자동차는 '스노우타이어'라는 구체적인 것이 아니라, '타이어'라는 추상적인 것에 의존해야 한다.



# 프로그램 구현 과제

- ## Main

```java
public class Main {
    public static void main(String[] args) {
        Calculator calculator = new MyCalculator();
        System.out.println("1234 + 4321 = " + calculator.plus(1234, 4321));
        System.out.println("911 - 170 = " + calculator.minus(911, 170));
        System.out.println("2 * 3 = " + calculator.mul(2, 3));
        System.out.println("33 / 11 = " + calculator.div(33, 11));

        System.out.println("\n");
        
        Cat cat = new Cat();
        Dog dog = new Dog();
        Animal[] animals = { cat, dog };
        for (Animal animal : animals) {
            animal.speak();
        }
    }
}
```


- ## Calculator ⇠ MyCalculator

```java
public interface Calculator {
    int plus(int a, int b);
    int minus(int a, int b);
    int mul(int a, int b);
    int div(int a, int b);
}
```

```java
public class MyCalculator implements Calculator {
    @Override
    public int plus(int a, int b) {
        return a + b;
    }

    @Override
    public int minus(int a, int b) {
        return a - b;
    }

    @Override
    public int mul(int a, int b) {
        return a * b;
    }

    @Override
    public int div(int a, int b) {
        return a / b;
    }
}
```


- ## Animal ← Cat, Dog 

```java
public abstract class Animal {
    public abstract void speak();
}
```

```java
public class Cat extends Animal {
    @Override
    public void speak() {
        System.out.println("cat says Meow!");
    }
}
```

```java
public class Dog extends Animal {
    @Override
    public void speak() {
        System.out.println("dog says Woof!");
    }
}```
