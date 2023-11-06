# \[1] 데이터베이스

데이터베이스(DB, DataBase)란 여러 사람에 의해 공유되어 사용될 목적으로 통합하여 관리되는 데이터의 집합을 말한다.
자료를 저장하는 방식에는 SQL(Structured Query Language)과 NoSQL이 있다.

## 1) SQL

SQL은 관계형 데이터베이스(RDB, Relational DataBase)를 다룬다.
관계형 데이터베이스는 행과 열을 통해 데이터를 저장하는데, 각 열은 하나의 속성에 대한 정보를 저장하고 행에는 각 열의 데이터 형식에 맞는 데이터가 저장된다.
미리 작성된 스키마를 기반으로 정해진 형식에 맞게 데이터를 저장해야 한다.

SQL은 데이터 수정이 빈번하고, 구조의 중요도가 높은 경우 주로 활용된다.

## 2) NoSQL

NoSQL은 non SQL, Non-relational Operation SQL, Not Only SQL 정도의 의미를 갖는다.

NoSQL은 기존 관계형 데이터베이스의 단점을 극복하기 위해 만들어진 것으로, 비관계형 데이터베이스(Non-Relational DataBase)를 다룬다.
비관계형 데이터베이스는 key-value, 문서, 그래프 등 다양한 데이터 저장 방식을 활용함으로써 관계형 데이터베이스에 비해 유연하고 확장성이 높다.

NoSQL은 데이터 수정이 적고, 정확한 데이터 구조를 알 수 없거나 변경 및 확장될 경우 주로 활용된다.

***
# \[2] 자바 클래스와 데이터베이스 간 매핑

## 1) ORM

데이터베이스에 넣을 객체를 엔티티(Entity)라고 하는데, ORM(Object-Relational Mapping)은 SQL이 아닌 애플리케이션 개발 언어로 엔티티를 조작하여 데이터베이스를 쉽게 다룰 수 있도록 하는 기술이다.

ORM은 개발 언어의 일관성과 가독성을 높여준다는 장점이 있다.
하지만 ORM은 SQL로 작성한 쿼리보다 비효율적일 수 있고, 복잡한 쿼리나 특정 데이터베이스 최적화 기술을 사용하기 어렵다는 한계 또한 존재한다.

## 2) JPA

JPA(Java Persistence API)는 자바 ORM 기술 표준이 되는 인터페이스들을 모아놓은 것이다.
JPA를 구현한 것으로 대표적으로 Hibernate가 있다.

JPA는 `EntityManager`를 통해 영속성 컨텍스트(PersistenceContext)를 관리한다.
영속성 컨텍스트는 애플리케이션과 데이터베이스 사이에서 객체를 보관하는 논리적인 개념이다.
이는 1차적인 캐싱, 엔티티의 생명 주기 관리, 데이터베이스 스키마의 자동 생성 및 업데이트 등의 역할을 한다.

JPA는 `@Entity`가 붙은 자바 클래스를 관리하며, 데이터베이스 테이블과 해당 클래스를 매핑해준다.

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToMany(fetch = FetchType.LAZY)
    private List<Feed> feeds;

    private String name;
}
```

## 3) ERD

ERD(Entity-Relation Diagram)는 수많은 엔티티들 사이의 관계를 설명해주는 다이어그램으로, 프로젝트에 사용되는 데이터베이스의 구조를 쉽게 파악하는 데 도움을 준다.
자바에서는 `@OneToOne`, `@OneToMany`, `@ManyToOne` 등의 어노테이션을 통해 ERD를 구현할 수 있다.

### 1. @OneToOne (1:1)

1:1 관계는 한 엔티티가 오직 하나의 다른 엔티티와 관계를 맺는 것을 뜻한다.
가령 대한민국은 일부일처제이므로 남편 개체와 아내 개체는 서로를 하나만 가질 수 있다.

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Husband {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToOne
    private Wife wife;
    
	private String name;
}
```

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Wife {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToOne
    private Husband husband;
    
	private String name;
}
```

### 2. @OneToMany (1:N)

한 부서에는 서로 다른 여러 사람들이 소속될 수 있다.
그러나 보통 한 개인은 한 부서에만 소속된다.
이러한 관계를 1:N 관계라고 칭한다.

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToMany(fetch = FetchType.LAZY)
    private List<Employee> employees;
    
    private String name;
}
```

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne
    private Department department;
    
    private String name;
}
```

만약 Department에서 Employee로의 접근이 불필요하다면 Department 클래스 코드에서 `@OneToMany(fetch = FetchType.LAZY) private List<Employee> employees` 부분을 생략할 수 있다.
개체 간 접근이 양방향으로 가능하도록 할지, 한 방향으로만 가능하도록 할지는 프로그래머의 설계에 달렸다.

### 3. @ManyToMany (M:N)

서점 고객들은 각자가 원하는 책을 마음껏 구매할 수 있다.
이러한 관계를 M:N 관계라고 칭한다.

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToMany
    private List<Client> clients;
    
    private String name;
}
```

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Client {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToMany
    private List<Books> books;
    
    private String name;
}
```

하지만 `@ManyToMany`는 사용하지 않는 것이 좋다.
`@ManyToMany`를 사용하면 내부적으로 새로운 연결 테이블을 만들게 되는데, 이는 추가적인 정보(가령 주문 시간, 주문 수량 등)의 삽입을 불가능하게 하고 예상치 못 한 쿼리들을 발생시킬 우려가 있다.
따라서 연결 테이블용 엔티티를 직접 추가하는 것이 바람직하다.

다음은 하나의 Order 엔티티를 매개로 삼아 Book 엔티티와 Client 엔티티의 M:N 관계를 표현한 예시이다.

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    private Client client;
    
    @ManyToOne(fetch = FetchType.LAZY)
    private Book book;
}
```

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToMany
    private List<Order> orders;
    
    private String name;
}
```

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Client {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToMany
    private List<Order> orders;
    
    private String name;
}
```

***
# \[3] Spring Data JPA

> Spring Data JPA, part of the larger Spring Data family, makes it easy to easily implement JPA based repositories. This module deals with enhanced support for JPA based data access layers. It makes it easier to build Spring-powered applications that use data access technologies.

Spring Data JPA는 데이터 접근 계층을 쉽게 구현할 수 있도록 하는 모듈로, CRUD 메소드를 쉽게 구현할 수 있도록 해준다.

***

# \[4] 📖 프로그램 구현 과제

## Hospital.java

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
class Hospital {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	@OneToMany
	private List<MedicalDepartment> medicalDepartments;
}
```

## MedicalDepartment.java

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
class MedicalDepartment {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	private String name;
	
	@ManyToOne
	private Hospital hospital;
	
	@OneToMany
	private List<Doctor> doctors;
}
```

## Doctor.java

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
class Doctor {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	private String name;
	
	@ManyToOne
	MedicalDepartment medicalDepartment;
	
	@ManyToOne
	Reservation reservation;
}
```

## Patient.java

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
class Patient {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	private String name;
	
	@ManyToOne
	Reservation reservation;
}
```

## Reservation.java

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
class Reservation {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	private String time;
	
	@OneToMany
	List<Doctor> doctors;
	
	@OneToMany
	List<Patient> patients;
}
```