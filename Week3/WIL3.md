# \[1] 스프링(Spring)

> Java 기반의 애플리케이션 객체지향 프레임워크

오늘날 IT(정보 기술)는 사회 곳곳에 전방위적으로 도입되고 있다.
스프링은 PSA 기법을 통해 ORM, OXM, JMS, AOP, CoC 등 엔터프라이즈 애플리케이션이 필요로 하는 다양한 기술들을 표준화된 방식으로 사용할 수 있게 하였다.

우리나라의 경우 국가 ICT 프로젝트에 사용하는 전자정부 표준프레임워크를 스프링 기반으로 만들어 배포하고, 공공프로젝트에서 이를 사용하도록 권장하고 있다.
스프링은 오픈소스임에도 한 국가의 ICT 프레임워크의 기반이 될 만큼 그 안정성과 편의성을 인정받고 있다.

***
# [2] 스프링 삼각형

스프링은 POJO를 중심으로 IoC/DI, AOP, PSA라는 세 가지 원칙들을 덧붙여 현재의 그 방대함을 쌓아올렸다.
거대한 규모 속에서도 스프링 삼각형이라는 단순하고도 확실한 철학을 가지고 있는 것이다.

## 1) POJO(Plain Old Java Object)

> 객체지향적인 원리에 충실하면서, 환경과 기술에 종속되지 않고 필요에 따라 재활용될 수 있는 방식으로 설계된 자바 오브젝트

과거 Java EE와 같은 중량 프레임워크들을 사용하게 되면서, 프로그래머들이 프레임워크에 종속된 무거운 자바 객체를 흔히 설계하곤 하였다.
특정 기술과 환경에 종속되어 의존하게 된 자바 코드는 가독성이 떨어져 유지보수 및 확장성 측면에서 객체지향의 장점들을 잃어갔고, 이에 반발해 등장한 것이 POJO이다.

POJO는 다른 클래스나 인터페이스를 상속받지 않고, getter/setter처럼 기본적인 기능들을 가지고 있다.

|조건|
|:---|
|- 특정 규약에 종속되지 않는다.|
|- 특정 환경에 종속되지 않는다.|
|- 자바에 담긴 기본적인 객체지향적 원리에 충실한다.|

|장점|
|:---|
|- 깔끔한 코드|
|- 자동화된 간편한 테스트 가능|
|- 복잡한 도메인에서도 효과적으로 프로그램을 설계|

## 2) IoC(Inversion of Control, 제어의 역전) / DI(Dependency Injection, 의존성 주입)

### 1. IoC

개발자가 작성한 프로그램이 외부 라이브러리의 코드를 호출하여 사용하는 전통적인 프로그래밍과 반대로, 필요에 따라 외부 라이브러리의 코드가 개발자의 코드를 호출하는 것을 제어의 역전이라고 한다.
즉, 제어권이 프로그래머가 아닌 프레임워크에 있다는 것이다.

> don’t call me, I’ll call you.

스프링 애플리케이션에서는 이러한 IoC 원리가 적용되어, `스프링 컨테이너`가 객체의 생성과 의존 관계 설정/사용/제거 등의 작업을 담당한다.
즉, 객체에 대한 제어권을 코드가 아닌 스프링 컨테이너가 갖고 있으며, 이때 스프링 컨테이너에 의해 조작되는 자바 객체를 `빈(Bean)`이라고 한다.

> In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and managed by a Spring IoC container.

객체의 생명주기를 프레임워크에 맡김으로써 프로그래머는 로직 자체에 더 집중할 수 있게 된다.

### 2. DI

DI는 IoC 프로그래밍 모델을 구현하는 방식 중 하나이다.

자바에서 운전자가 자동차를 생산하는 것은 new Car(), 자동차가 내부적으로 타이어를 생산하는 것은 Car 객체 생성자에서 new Tire()로 표현할 수 있다.
이때 Car 클래스가 Tire 클래스에 의존한다고 하며, 한 클래스가 다른 클래스의 기능을 필요로 한다는 의미이다.

- 즉, 의존성은 new이다.

예시를 통해 의존성 주입에 대해 더 살펴보자.

```java
public interface Tire {}
public class KoreaTire implements Tire {}
public class AmericaTire implements Tire {}
```

```java
public class Car {
	Tire tire;
	
	public Car() {
		tire = new KoreaTire();
	}
}
```

위 코드의 문제점은 클래스 간 결합도가 높다는 점이다.
Car에 KoreaTire가 아닌 AmericaTire를 사용하고 싶다면, Car 생성자에 직접적인 변경이 필요하다.
또한 만약 Tire 인터페이스를 구현하는 클래스가 더 다양하고, Car 클래스를 상속받는 SportCar, Truck 등 다양한 자동차들이 각기 서로 다른 타이어를 사용한다면 굉장히 복잡하고 비효율적일 것이다.

**이러한 문제점을 해결하기 위해 제안된 것이 바로 의존성 주입으로, 객체의 의존 관계를 클래스 내부가 아닌 외부에서 런타임 시점에 결정하는 방식이다.
그중에서도 생성자 주입은, 객체 생성자에서 인터페이스 타입 매개변수를 통해 구현 클래스를 다이나믹하게 제공받아 클래스 간 결합도를 낮춘다.**

> 의존성 주입으로 생성자 주입, 수정자 주입, 필드 주입이 있는데, 그중 생성자 주입이 오늘날 권장된다.<br>`Spring Team recommends: “Always use constructor based dependency injection in your beans. Always use assertions for mandatory dependencies”`

```java
public class Car {
	Tire tire;
	
	public Car(Tire tire) {
		this.tire = tire;
	}
}
```

기존 Car 클래스에서는 KoreaTire, AmericaTire에 대해 정확히 알고 있어야만 그에 해당하는 객체를 생성할 수 있었다.
반면 의존성 주입을 적용하면, 추후에 ChinaTire, JapanTire, EnglandTire처럼 어떤 새로운 타이어 클래스가 설계되든 Tire 인터페이스를 구현하기만 하면 Car 클래스는 직접적인 내부 코드 변경 없이도 정상적으로 작동하므로 그 확장성이 좋다.
또한 Car 클래스와 타이어 클래스들의 테스트를 분리해서 진행할 수 있다는 장점도 있다.

오늘날 스프링에서는 ApplicationContext를 통해 간단히 의존성을 주입해 제어의 역전을 실현한다.

### 3. ApplicationContext

`BeanFactory`는 빈을 관리하고 조회하는 기능을 담당하는 인터페이스로, 스프링에서 최상위 IoC 컨테이너이다.
하지만 `BeanFactory`를 직접적으로 잘 사용하지는 않고, 주로 `ApplicationContext`라는 인터페이스를 스프링 컨테이너로 사용하게 된다.
`AppicationContext`는 `EnvironmentCapable`, `ListableBeanFactory`, `HierarchicalBeanFactory`, `MessageSource`, `ApplicationEventPublisher`, `ResourcePatternResolver`를 상속받는 인터페이스로, 빈을 관리하고 검색할 수 있을 뿐만 아니라 기타 다양한 부가 기능들을 지원한다.

> The `BeanFactory`interface provides an advanced configuration mechanism capable of managing any type of object. `ApplicationContext` is a sub-interface of `BeanFactory.`<br>(...)<br>In short, the `BeanFactory` provides the configuration framework and basic functionality, and the `ApplicationContext` adds more enterprise-specific functionality. The `ApplicationContext` is a complete superset of the `BeanFactory`, and is used exclusively in this chapter in descriptions of Spring's IoC container.<br>(...)<br>The interface `org.springframework.context.ApplicationContext` represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the aforementioned beans

### 4. Bean Scopes

@Scope 어노테이션을 통해 빈마다 스프링 컨테이너가 관리할 생명 주기 범위를 지정할 수 있다.
프로그래머가 별도로 지정하지 않으면 singleton으로 지정된다.

```java
@Scope("prototype")
@Component
public class Prototype { ... }
```

#### 4-1. singleton

싱글톤(Singleton)이란, 생성자가 최초로 호출될 때에만 컨테이너가 인스턴스를 생성 및 관리하는 디자인 패턴이다.
즉, 서버 컨테이너가 관리하는 하나의 인스턴스를 모든 클라이언트 요청이 공유하도록 함으로써 서버 컴퓨터 자원을 효율적으로 사용할 수 있게 하는 것이다.
스프링은 엔터프라이즈급 애플리케이션을 개발하기 위해 탄생한 프레임워크인 만큼, 수많은 클라이언트 요청에 대비해야 하므로 싱글톤 패턴을 적용하였다.

그런데 싱글톤 패턴에는 몇 가지 문제점이 있다.

1. private 생성자를 사용해 부모/자식 클래스 설계에 제약이 생긴다.
2. 객체 간 결합도가 높아진다.
3. 공유 객체의 전역성(static)으로 인해 테스트가 어렵고 동시성 문제의 우려가 있다.
4. 내부 설계를 변경하기 어렵다.
5. 클라이언트가 구체 클래스에 의존한다.

결국 싱글톤 패턴은 유연성이 떨어져 객체지향의 장점을 퇴색시키는데, 스프링에서는 싱글톤 레지스트리를 통해 많은 문제점들을 해결해 평범한 자바 클래스도 싱글톤으로 활용할 수 있게 하였다.

#### 4-2. prototype

스프링 컨테이너는 prototype에 대해 빈의 생성과 의존관계 주입에만 관여한 후 빈을 클라이언트에 반환해버린다.
즉, 컨테이너가 종료되어도 prototype 빈은 클라이언트에 존재하여 소멸되지 않는다.
따라서 스프링 컨테이너와 생명 주기가 같아 서버 환경에서 잘 관리되는 singleton과는 달리, prototype은 클라이언트에서 자체적으로 관리해주어야 한다.

#### 4-3. 웹 관련 스코프

웹 환경에서만 동작하는 스코프로, 생명 주기의 처음부터 끝까지 스프링 컨테이너가 잘 관리해준다.

|종류|특징|
|:---:|:---:|
|request|HTTP 요청이 들어오고 나갈 때까지 유지됨|
|session|HTTP Session과 동일한 생명 주기를 가짐|
|application|ServletContext와 동일한 생명 주기를 가짐|
|websocket|웹소켓과 동일한 동일한 생명 주기를 가짐|

### 5. 빈 설정

#### 5-1. @Bean

`@Bean`은 기본적으로 자바 객체를 반환하는 메소드에 붙게 된다.
`ApplicationContext`는 `@Bean`이 붙은 메소드를 찾으면 해당 메소드가 반환하는 객체를 빈으로 등록한다.

#### 5-2. @Configuration

> Indicates that a class declares one or more `Bean` methods and may be processed by the Spring container to generate bean definitions and service requests for those beans at runtime.

`@Configuration`이 붙은 클래스는 빈 설정을 담당하게 된다. `ApplicationContext`는 `@Configuration` 클래스를 빈으로 등록하고, 해당 클래스 내에 있는 `@Bean` 메소드들을 찾아 또 빈으로 등록한다.

스프링 컨테이너가 `@Configuration` 클래스를 읽으면 그 클래스를 그대로 빈으로 등록하지 않는다.
CGLIB 바이트 조작 라이브러리를 사용해 해당 클래스를 기반으로 새로운 클래스를 만들고 비로소 그것을 빈으로 등록하는 것이다.
이로써 싱글톤 패턴이 보장된다.

BeanA 클래스, BeanB 클래스, 그리고 빈을 관리하는 AppConfig 설정 클래스를 설계했다고 하자.

```java
class BeanA {
}

class BeanB {
	private BeanA beanA
	
	public BeanB(BeanA beanA) {
		this.beanA = beanA;
	}
}

@Configuration
public class AppConfig {
	@Bean
	public BeanA beanA() {
		return new BeanA();
	}

	@Bean
	public BeanB beanB() {
		return new BeanB(beanA());  // 의존성 주입
	}
}
```

결론적으로 말하면 아래 코드와 같이 `ApplicationContext`에 AppConfig 클래스 정보를 넘겨주기만 하면 AppConfig 클래스에 대해 싱글톤을 보장할 수 있게 된다.

```java
public class ConfigurationSingletonTest {
	@Test
	void test() {
		ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
		(...)
	}
}
```

#### 5-3. @ComponentScan, @Component, @AutoWired

앞서 살펴본 바와 같이 `@Configuration`과 `@Bean`을 통해 빈을 등록하고 의존성을 주입할 수 있다.
필요에 따라 위처럼 빈을 설정할 수도 있겠지만, 사실 그 과정이 번거롭고 빈 개수가 많아지면 인적 오류를 발생시킬 우려가 있어컴포넌트 스캔 방식을 사용하는 경우가 많다.

1. 우선 AppConfig 클래스에 `@Configuration`을 붙였듯이, 이번에는 설정 정보 클래스에 `@ComponentScan` 어노테이션을 붙여준다.
2. 스프링 컨테이너는 `@ComponentScan`이 선언된 위치를 시작으로 그 하위 패키지들까지 모두 탐색하며 `@Component`가 붙은 클래스를 찾아 모두 빈으로 등록한다.
3. 빈으로 등록된 클래스의 생성자에 `@Autowired`를 붙이면 스프링 컨테이너가 자동으로 의존성을 주입해준다.

```java
@ComponentScan
public class AppConfig {
}
```

```java
@Component
class BeanA {
}

@Component
class BeanB {
	private BeanA beanA;
	
	@Autowired
	public BeanB(BeanA beanA) {
		this.beanA = beanA;
	}
}
```

이로써 빈을 등록함은 물론이고, 직접 new 연산자로 객체를 생성하지 않고도 의존성을 쉽게 주입할 수 있다.
스프링 컨테이너가 관리하는 영역이므로 빈의 싱글톤 패턴이 보장됨은 물론이다.

참고로 스프링 부트는 `@SpringBootApplication` 어노테이션이 붙은 클래스를 기준으로 동작하게 되어 있는데, `@SpringBootApplication`은 `@ComponentScan`을 포함하고 있다.

#### 5-4. 주의점

스프링에서는 싱글톤 레지스트리가 싱글톤 패턴으로 설계되지 않은 클래스들도 싱글톤으로 활용할 수 있도록 돕기는 하지만, 프로그래머는 기본적으로 빈을 무상태(Stateless)로 설계해야 한다.
특히 멀티 쓰레드 환경을 주의해야 하는데, 특정 클라이언트에 의존적인 값이 있어서는 안 되며, 이를 위해 가급적 값들을 읽기 전용으로 지정해야 한다.

그리고 의존성을 자동 주입해야 할 인터페이스 구현체가 여러 개인 경우 스프링 컨테이너가 혼란을 겪을 수 있다.

```java
@Repository
public class StationInMemoryStationDao implements StationDao {}
```

```java
@Repository
public class StationJdbcStationDao implements StationDao { }
```

```java
@Service
public class StationService 
	private final StationDao stationDao;
	public StationService(final StationDao stationDao) {
		this.stationDao = stationDao;
	}
}
```

위와 같은 상황에서 컨테이너는 StationService 클래스의 생성자에 StationInMemoryStationDao의 인스턴스와 StationJdbcStationDao의 인스턴스 중 어떤 것을 주입할지 충돌을 겪게 된다.
이는 `@Primary`, `@Qualifier`와 같은 어노테이션을 통해 우선순위를 지정해주어 해결할 수 있다.

## 3) AOP(Aspect-Oriented Programming, 관점지향 프로그래밍)

## 4) PSA(Portable Service Abstraction, 일관성 있는 서비스 추상화)

***
# \[3] 📖 프로그램 구현 과제

## AppConfig.class

```java
package GDSC.backend.GDSC_3rd_HW;

import org.springframework.context.annotation.ComponentScan;

@ComponentScan
public class AppConfig {
}
```

## ClassA.class

```java
package GDSC.backend.GDSC_3rd_HW;

import org.springframework.stereotype.Component;

@Component
public class ClassA {
}
```

## ClassB.class

```java
package GDSC.backend.GDSC_3rd_HW;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class ClassB {
	private ClassA classA;
	
	@Autowired
	public ClassB(ClassA classA) {
		this.classA = classA;
	}
}
```

## ClassC.class

```java
package GDSC.backend.GDSC_3rd_HW;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class ClassC {
	private ClassA classA;
	
	@Autowired
	public ClassC(ClassA classA) {
		this.classA = classA;
	}
}
```

## Gdsc3rdHwApplication.class

```java
package GDSC.backend.GDSC_3rd_HW;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@Slf4j
@SpringBootApplication
public class Gdsc3rdHwApplication {
	public static void main(String[] args) {
		ApplicationContext applicationContext = SpringApplication.run(Gdsc3rdHwApplication.class, args);
		
		log.info("---------- 모든 Bean 출력 ----------");
		String[] beanDefinitionNames = applicationContext.getBeanDefinitionNames();
		for (String beanDefinitionName : beanDefinitionNames) {
			Object bean = applicationContext.getBean(beanDefinitionName);
			log.info("name=" + beanDefinitionName + " object=" + bean);
		}
		
		log.info("---------- ClassA Bean 값 출력----------");
		ClassA classA1 = (ClassA) applicationContext.getBean("classA", ClassA.class);
		ClassA classA2 = (ClassA) applicationContext.getBean("classA", ClassA.class);
		log.info("classA1 = " + classA1);
		log.info("classA2 = " + classA2);
	}
}
```

## 실행결과

```
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=classA object=GDSC.backend.GDSC_3rd_HW.ClassA@7f34a967
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=classB object=GDSC.backend.GDSC_3rd_HW.ClassB@77e80a5e
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=classC object=GDSC.backend.GDSC_3rd_HW.ClassC@1d8e2eea
```

Gdsc3rdHwApplication.class 파일을 실행한 결과의 일부로, 위와 같이 ClassA, ClassB, ClassC가 빈으로 등록되었음을 알 수 있다.
아래는 실행결과의 전체이다.

```
2023-10-03T20:04:51.679+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : ---------- 모든 Bean 출력 ----------
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.context.annotation.internalConfigurationAnnotationProcessor object=org.springframework.context.annotation.ConfigurationClassPostProcessor@6bfdb014
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.context.annotation.internalAutowiredAnnotationProcessor object=org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor@72889280
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.context.annotation.internalCommonAnnotationProcessor object=org.springframework.context.annotation.CommonAnnotationBeanPostProcessor@606fc505
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.context.event.internalEventListenerProcessor object=org.springframework.context.event.EventListenerMethodProcessor@4aa3d36
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.context.event.internalEventListenerFactory object=org.springframework.context.event.DefaultEventListenerFactory@2d140a7
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=gdsc3rdHwApplication object=GDSC.backend.GDSC_3rd_HW.Gdsc3rdHwApplication$$SpringCGLIB$$0@347bdeef
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.internalCachingMetadataReaderFactory object=org.springframework.boot.type.classreading.ConcurrentReferenceCachingMetadataReaderFactory@2aa27288
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=classA object=GDSC.backend.GDSC_3rd_HW.ClassA@7f34a967
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=classB object=GDSC.backend.GDSC_3rd_HW.ClassB@77e80a5e
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=classC object=GDSC.backend.GDSC_3rd_HW.ClassC@1d8e2eea
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.AutoConfigurationPackages object=org.springframework.boot.autoconfigure.AutoConfigurationPackages$BasePackages@240139e1
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration object=org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration@7ea4d397
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=propertySourcesPlaceholderConfigurer object=org.springframework.context.support.PropertySourcesPlaceholderConfigurer@49298ce7
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.aop.AopAutoConfiguration$ClassProxyingConfiguration object=org.springframework.boot.autoconfigure.aop.AopAutoConfiguration$ClassProxyingConfiguration@253c1256
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=forceAutoProxyCreatorToUseClassProxying object=org.springframework.boot.autoconfigure.aop.AopAutoConfiguration$ClassProxyingConfiguration$$Lambda$316/0x0000000800295770@8dfe921
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.aop.AopAutoConfiguration object=org.springframework.boot.autoconfigure.aop.AopAutoConfiguration@503fbbc6
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.availability.ApplicationAvailabilityAutoConfiguration object=org.springframework.boot.autoconfigure.availability.ApplicationAvailabilityAutoConfiguration@55f45b92
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=applicationAvailability object=org.springframework.boot.availability.ApplicationAvailabilityBean@54336c81
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration object=org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration@109f5dd8
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.context.properties.ConfigurationPropertiesBindingPostProcessor object=org.springframework.boot.context.properties.ConfigurationPropertiesBindingPostProcessor@67fe380b
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.context.internalConfigurationPropertiesBinder object=org.springframework.boot.context.properties.ConfigurationPropertiesBinder@4a325eb9
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.context.properties.BoundConfigurationProperties object=org.springframework.boot.context.properties.BoundConfigurationProperties@3dedb4a6
2023-10-03T20:04:51.680+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.context.properties.EnableConfigurationPropertiesRegistrar.methodValidationExcludeFilter object=org.springframework.boot.validation.beanvalidation.MethodValidationExcludeFilter$$Lambda$331/0x00000008002a26b8@57f64f5e
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration object=org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration@415e0bcb
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=lifecycleProcessor object=org.springframework.context.support.DefaultLifecycleProcessor@194152cf
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=spring.lifecycle-org.springframework.boot.autoconfigure.context.LifecycleProperties object=org.springframework.boot.autoconfigure.context.LifecycleProperties@49d98dc5
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration object=org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration@2c30b71f
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=spring.info-org.springframework.boot.autoconfigure.info.ProjectInfoProperties object=org.springframework.boot.autoconfigure.info.ProjectInfoProperties@1d81e101
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.sql.init.SqlInitializationAutoConfiguration object=org.springframework.boot.autoconfigure.sql.init.SqlInitializationAutoConfiguration@ec50f54
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=spring.sql.init-org.springframework.boot.autoconfigure.sql.init.SqlInitializationProperties object=org.springframework.boot.autoconfigure.sql.init.SqlInitializationProperties@bf71cec
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.sql.init.dependency.DatabaseInitializationDependencyConfigurer$DependsOnDatabaseInitializationPostProcessor object=org.springframework.boot.sql.init.dependency.DatabaseInitializationDependencyConfigurer$DependsOnDatabaseInitializationPostProcessor@22d6cac2
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.ssl.SslAutoConfiguration object=org.springframework.boot.autoconfigure.ssl.SslAutoConfiguration@30cdae70
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=sslPropertiesSslBundleRegistrar object=org.springframework.boot.autoconfigure.ssl.SslPropertiesBundleRegistrar@1654a892
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=sslBundleRegistry object=org.springframework.boot.ssl.DefaultSslBundleRegistry@2577d6c8
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=spring.ssl-org.springframework.boot.autoconfigure.ssl.SslProperties object=org.springframework.boot.autoconfigure.ssl.SslProperties@3163987e
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration object=org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration@6c000e0c
2023-10-03T20:04:51.681+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=taskExecutorBuilder object=org.springframework.boot.task.TaskExecutorBuilder@5f233b26
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=applicationTaskExecutor object=org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor@654c1a54
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=spring.task.execution-org.springframework.boot.autoconfigure.task.TaskExecutionProperties object=org.springframework.boot.autoconfigure.task.TaskExecutionProperties@5bdaf2ce
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration object=org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration@42d236fb
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=taskSchedulerBuilder object=org.springframework.boot.task.TaskSchedulerBuilder@1ce93c18
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=spring.task.scheduling-org.springframework.boot.autoconfigure.task.TaskSchedulingProperties object=org.springframework.boot.autoconfigure.task.TaskSchedulingProperties@19f21b6b
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : name=org.springframework.aop.config.internalAutoProxyCreator object=proxyTargetClass=true; optimize=false; opaque=false; exposeProxy=false; frozen=false
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : ---------- ClassA Bean 값 출력----------
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : classA1 = GDSC.backend.GDSC_3rd_HW.ClassA@7f34a967
2023-10-03T20:04:51.689+09:00  INFO 38540 --- [           main] G.b.GDSC_3rd_HW.Gdsc3rdHwApplication     : classA2 = GDSC.backend.GDSC_3rd_HW.ClassA@7f34a967
```

