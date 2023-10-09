# \[1] DI(Dependency Injection, 의존성 주입)

## 1) 의존성 주입이란?

DI란, 한 객체 내부에서 다른 객체를 생성하는 대신 외부에서 그 관계를 결정해주는 디자인 패턴이다.
객체 사이에 인터페이스를 두어 의존 관계가 고정되지 않고 런타임 시 동적으로 형성될 수 있도록 하여, 유연성은 높이고 결합도는 낮출 수 있게 된다.

> **dependency injection** is a programming technique in which an object or function receives other objects or functions that it requires, as opposed to creating them internally.<br><br>Dependency injection aims to separate the concerns of constructing objects and using them, leading to loosely coupled programs. The pattern ensures that an object or function which wants to use a given service should not have to know how to construct those services. Instead, the receiving 'client' (object or function) is provided with its dependencies by external code (an 'injector'), which it is not aware of.

|요소|특징|
|:---:|:---|
|서비스와 클라이언트|서비스는 유용한 기능을 제공하는 클래스이며, 클라이언트는 어떤 서비스를 사용하는 클래스이다. 따라서 클라이언트는 서비스에 의존적이다.|
|인터페이스|클라이언트는 서비스 내부가 어떻게 구현되었는지 알아서는 안 된다. 그저 인터페이스를 통해 잘 사용하기만 하면 된다.|
|컨테이너|컨테이너는 클라이언트가 요구하는 서비스를 제공함으로써 클라이언트와 서비스 사이의 의존 관계를 형성해준다. 이로써 원하는 옷을 그때그때 바꿔입히듯 클라이언트에게 다양한 서비스를 쉽게 제공할 수 있게 된다.|

## 2) 의존성 주입 방법
### 1. 생성자 주입 (Constructor Injection)

```java
@Component
public class Car {
	private Tire tire;
	
	// @Autowired
	public Car(Tire tire) {
		this.tire = tire;
	}
}
```

- 오늘날 스프링에서 가장 권장되는 방식
- 객체가 최초로 생성되는 시점에 의존성이 주입된다.
- 클래스 내에 생성자가 1개인 경우 `@Autowired`를 생략할 수 있다.
- `@Autowired`가 붙은 생성자가 여러 개인 경우, 의존성을 가장 많이 주입할 수 있는 생성자를 사용한다.
- 파이널 필드를 만들 수 있다.

### 2. 수정자 주입 (Setter Injection)

```java
@Component
public class Car {
	private Tire tire;
	
	@Autowired
	public void setTire(Tire tire) {
		this.tire = tire;
	}
}
```

- 의존성을 선택하거나 변경할 수 있다.
- 객체가 생성된 후에 setter로 의존성이 주입된다.
- 따라서 파이널 필드를 만들 수 없어 객체의 불변성을 보장할 수 없다.

### 3. 필드 주입 (Field Injection)

```java
@Component
public class Car {
	@Autowired
	private Tire tire;
}
```

- 권장되지 않는 방식
- 파이널 필드를 만들 수 없어 객체의 불변성을 보장할 수 없다.
- 생성자나 수정자가 없으므로 프로그래머가 수동적으로 의존성을 주입할 수 없다.
- 테스트용 코드, 설정 클래스 등 특별한 용도로만 사용하도록 한다.

### 4. 메소드 주입 (Method Injection)

```java
@Component
public class Car {
	private Tire tire;
	
	@Autowired
	public void init(Tire tire) {
		this.tire = tire;
	}
}
```

- 권장되지 않는 방식
- 수정자 주입과 비슷하며, 여러 필드를 한 번에 주입할 수 있다.

## 3) Lombok

Lombok은 어노테이션을 기반으로 getter, setter, toString, hashCode, equals 등 다소 반복적인 자바 코드를 대신 작성해주는 라이브러리로, 더욱 생산적으로 자바 코드를 작성할 수 있게 해준다.

아래는 `@AllArgsConstructor`로 객체 생성자 코드를 대신한 것이다.

```java
@Component
public class Car {
	private Tire tire;
	
	public Car(Tire tire) {
		this.tire = tire;
	}
}
```

```java
@Component
@AllArgsConstructor
public class Car {
	private Tire tire;
}
```

`@AllArgsConstructor` 이외에도 `@Getter`, `@Setter`, `@NoArgsConstructor`, `@RequiredArgsConstructor`, `@EqualsAndHashCode`, `@ToString`, `@Data`, `@Builder`, `@Delegate`, `Slf4j` 등을 지원한다.

## 4) 의존성 주입 시 주의점

### 1. NullPointerException

생성자 주입에서는 Null을 의도적으로 넣지 않는 이상 NullPointerException이 발생하지 않는다.
스프링 컨테이너가 객체 생성 시점에 의존성을 주입하기 때문이다.<br>그러나 이러한 스프링 빈 관리 기능을 빌리지 않고, new 연산자로 수정자나 필드로 의존성을 주입할 경우 NullPointerException이 발생할 수 있어 주의가 필요하다.

### 2. 순환 참조 문제

순환 참조는 서로 다른 클래스가 서로 맞물려 의존하면서 생기는 현상이다.
가령 A 클래스가 B 클래스에 의존하고 B 클래스가 A 클래스에 의존하는 경우, 스프링 컨테이너가 어떤 클래스부터 빈으로 만들어야 할지 결정할 수 없다는 문제점이 있다.

```
***************************
APPLICATION FAILED TO START
***************************

Description:

The dependencies of some of the beans in the application context form a cycle:

┌─────┐
|  a defined in file [~/demo/bin/main/com/example/demo/A.class]
↑     ↓
|  b defined in file [~/demo/bin/main/com/example/demo/B.class]
└─────┘
```

```
Action:

Relying upon circular references is discouraged and they are prohibited by default. Update your application to remove the dependency cycle between beans. (...)
```

스프링 2.6 전에는 생성자 주입에서, 그 이후로는 생성자/수정자/필드 주입에서 순환 참조가 발생하는 경우 애플리케이션이 종료되고 에러 메시지가 출력된다.

### 3. 주입 대상들의 우선순위 문제

```java
public interface Tire {}
```

```java
@Component
public class KoreaTire implements Tire {}
```

```java
@Component
public class AmericaTire implements Tire {}
```

```java
@Component
public class Car {
	private Tire tire;
	
	// @Autowired
	public Car(Tire tire) {
		this.tire = tire;
	}
}
```

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in com.example.demo.Car required a single bean, but 2 were found:
        - americaTire: defined in file [~/demo/bin/main/com/example/demo/AmericaTire.class]
        - koreaTire: defined in file [~/demo/bin/main/com/example/demo/KoreaTire.class]
```

위와 같이 Car 클래스가 Tire 클래스에 의존할 때, 컨테이너는 KoreaTire와 AmericaTire 둘 중 어느 것을 선택해 주입해야 할지 결정하지 못 한다.
KoreaTire와 AmericaTire 모두 Tire의 구현체이므로 Tire 타입으로 검색되기 때문이다.

우선 Car 클래스 생성자 매개변수명을 변경해 이러한 문제를 해결할 수 있다.

```java
@Component
public class Car {
	private Tire tire;
	
	public Car(Tire koreaTire) {
		this.tire = koreaTire;
	}
}
```

하지만 이러한 방식은 굉장히 번거롭고 객체지향의 측면에서 바람직하지도 않다.
스프링 컨테이너도 빈을 변수명을 통해 구분하는 것은 가장 후순위로 두고 있다.
따라서 `@Qualifier`와 `@Primary` 어노테이션을 활용하는 것이 바람직하다.

```
Action:

Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed
```

#### 3-1. @Qualifier

```java
@Component
@Qualifier("defaultTire")
public class KoreaTire implements Tire {}
```

```java
@Component
public class Car {
	private Tire tire;
	
	public Car(@Qualifier("defaultTire") Tire tire) {
		this.tire = tire;
	}
}
```

`@Qualifier`가 `@Primary`보다 우선순위가 높다.

#### 3-2.  @Primary

```java
@Component
@Primary
public class KoreaTire implements Tire {}
```

```java
@Component
public class Car {
	private Tire tire;
	
	public Car(Tire tire) {
		this.tire = tire;
	}
}
```

***
# \[2] AOP(Aspect-Oriented Programming, 관점지향 프로그래밍)

AOP란 횡단 관심사(Cross-cutting concerns)의 분리를 통해 모듈성 증가에 중점을 두는 프로그래밍 기법이다.
즉, 흩어져 있는 관심사들을 모아 모듈화하하여 OOP를 보완하는 것이다.

스프링은 Spring AOP 기술을 통해 관점지향 프로그래밍을 돕는다.

***
# \[3] PSA(Portable Service Abstraction, 일관성 있는 서비스 추상화)

PSA란 환경의 변화와 관계없이 일관된 방식으로 기술에 접근할 수 있도록 해주는 추상화 구조를 말한다.
애플리케이션에 사용되는 기술의 변경이 용이하여 요구사항에 대한 대처가 유연해진다.

***
# \[4] Spring Boot

> Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run".

스프링 부트는 스프링을 쉽고 빠르게 활용할 수 있도록 해주는 도구로, 다음과 같은 장점이 있다.

1. 자주 사용되는 라이브러리들의 버전 관리 자동화
2. AutoConfig로 복잡한 설정 자동화
3. 내장 웹서버 제공 (ex: Tomcat)
4. JAR로 개발이 가능해 애플리케이션 배포 및 관리 용이
5. 모니터링 및 관리를 위한 메트릭 기능 제공

따라서 스프링은 설정 파일을 프로그래머가 직접 작성하여 스프링 프레임워크를 보다 세밀하게 제어하고자 하는 경우에, 스프링 부트는 빠르고 간단하게 스프링 애플리케이션을 개발하고자 하는 경우에 주로 사용된다.

***
# \[5] 📖 프로그램 구현 과제

## 실행결과

```
2023-10-08T16:34:45.411+09:00  INFO 83703 --- [           main] G.b.GDSC_4th_HW.Gdsc4thHwApplication     : Starting Gdsc4thHwApplication using Java 17.0.8.1 with PID 83703 (...)
2023-10-08T16:34:45.415+09:00  INFO 83703 --- [           main] G.b.GDSC_4th_HW.Gdsc4thHwApplication     : No active profile set, falling back to 1 default profile: "default"
2023-10-08T16:34:45.803+09:00  INFO 83703 --- [           main] G.b.GDSC_4th_HW.Gdsc4thHwApplication     : Started Gdsc4thHwApplication in 0.924 seconds (process running for 1.424)
2023-10-08T16:34:45.805+09:00  INFO 83703 --- [           main] GDSC.backend.GDSC_4th_HW.BImpl           : function B
2023-10-08T16:34:45.805+09:00  INFO 83703 --- [           main] GDSC.backend.GDSC_4th_HW.C               : function C
2023-10-08T16:34:45.805+09:00  INFO 83703 --- [           main] GDSC.backend.GDSC_4th_HW.C               : function C
```

## A.class

```java
@Component
public class A {
	private B b;
	private C c;
	
	// @Autowired
	public A(B b, C c) {
		this.b = b;
		this.c = c;
	}
	
	public B getB() {
		return b;
	}
	
	public C getC() {
		return c;
	}
}
```

## D.class

```java
@Component
@RequiredArgsConstructor
public class D {
	@NonNull
	private C c;
	
	public C getC() {
		return c;
	}
}
```
