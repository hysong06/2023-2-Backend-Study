# \[1] Spring Data JPA

```java
public static void main(String[] args) {
	EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
	EntityManager em = emf.createEntityManager();
	EntityTransaction tx = em.getTransaction();
	tx.begin();
	try {
		Team team = new Team();
		team.setName("팀A");
		em.persist(team);
		
		em.flush();
		em.clear();
		tx.commit();
	} catch (Exception e){
		tx.rollback();
	} finally {
		em.close();
	}
	emf.close();
}
```

```java
public interface UserRepository extends JpaRepository<User, Long> {
	List<User> findByUsername(String username);
    
    @Query("SELECT u FROM User u WHERE u.email = email")
    User findByEmail(String email);
}
```

위 코드들은 각각 JPA와 Spring Data JPA를 사용한 것이다.
프로그래머는 Spring Data JPA을 사용하면 `EntityManager`를 구현하지 않고도 데이터베이스에 쉽게 접근할 수 있다.

## 1) JpaRepository

`JpaRepository`는 `CrudRepository`, `ListCrudRepository`, `QueryByExampleExecutor` 등의 인터페이스를 상속받고 있어 CRUD 관련 메소드들을 쉽게 활용할 수 있게 해준다.
대표적으로 find, save, delete, count, exist와 같은 기능들을 제공한다.

각 인터페이스들에 대한 정보는 아래 페이지에서 확인할 수 있다.

- [`JpaRepository`](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html)
- [`CrudRepository`](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html)
- [`ListCrudRepository`](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html)
- [`QueryByExampleExecutor`](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/query/QueryByExampleExecutor.html)
<br>다음은 `JpaRepository`를 활용해 Hospital 엔티티에 대한 저장 및 조회 기능을 구현한 예이다.

```java
@RestController
@RequestMapping("/hospitals")
@RequiredArgsConstructor
public class HospitalController {
    private final HospitalService hospitalService;
	
    @GetMapping()
    public List<Hospital> getAllHospitals() {
        return hospitalService.getAllHospitals();
    }
	
    @PostMapping()
    public void createHospital(@RequestBody CreateHospitalDto createHospitalDto) {
        hospitalService.createHospital(createHospitalDto);
    }
}
```

```java
@Service
@RequiredArgsConstructor
public class HospitalService {
    private final HospitalRepository hospitalRepository;
    
    public List<Hospital> getAllHospitals() {
        return hospitalRepository.findAll();
    }
	
    public void createHospital(CreateHospitalDto createHospitalDto) {
        hospitalRepository.save(new Hospital(createHospitalDto.getName()));
    }
}
```

```java
public interface HospitalRepository extends JpaRepository<Hospital, Long> {
}
```

```java
@Getter
public class CreateHospitalDto {
    private String name;
}
```

<br>또한 서버가 클라이언트로부터 Hospital의 id를 전달받으면, 해당 Hospital을 데이터베이스에서 찾아 연관관계를 맺은 MedicalDepartment를 생성할 수도 있다.

```java
@RestController
@RequestMapping("/medicalDepartments")
@RequiredArgsConstructor
public class MedicalDepartmentController {
	final MedicalDepartmentService medicalDepartmentService;
	
	@GetMapping()
	public List<MedicalDepartment> getAllMedicalDepartments() {
		return medicalDepartmentService.getAllMedicalDepartments();
	}
	
	@PostMapping()
	public void createMedicalDepartment(@RequestBody CreateMedicalDepartmentDto createMedicalDepartment) {
		medicalDepartmentService.createMedicalDepartment(createMedicalDepartment);
	}
}
```

```java
@Service
@RequiredArgsConstructor
public class MedicalDepartmentService {
	private final MedicalDepartmentRepository medicalDepartmentRepository;
	private final HospitalRepository hospitalRepository;
	
	public List<MedicalDepartment> getAllMedicalDepartments() {
		return medicalDepartmentRepository.findAll();
	}
	
	public void createMedicalDepartment(CreateMedicalDepartmentDto createMedicalDepartmentDto) {
		Hospital hospital = hospitalRepository.findById(createMedicalDepartmentDto.getHospitalId())
			.orElseThrow(RuntimeException::new);
		medicalDepartmentRepository.save(new MedicalDepartment(hospital, createMedicalDepartmentDto.getName()));
	}
}
```

```java
public interface MedicalDepartmentRepository extends JpaRepository<MedicalDepartment, Long> {
}
```

## 2) 쿼리 메소드

Spring Data JPA는 기본적인 쿼리 이외에도, Repository의 메소드 이름을 분석해 새롭게 쿼리를 만들어준다.
find, update, delete, count, exist 등의 기능 뒤에 [쿼리 메소드 생성 규칙](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation)을 덧붙이면 된다.

```java
public interface HospitalRepository extends JpaRepository<Hospital, Long> {
    public Hospital findByName(String name);
}
```

## 3) 커스텀 쿼리

그밖의 다른 쿼리들을 구현하고 싶다면 `@Query`를 사용해 JPQL이나 SQL을 통해 작성할 수 있다.

```java
public interface MedicalDepartmentRepository extends JpaRepository<MedicalDepartment, Long> {
    @Query("select md from MedicalDepartment md " +
            "join fetch md.hospital h " +
            "where md.id = :id ")
    public MedicalDepartment findByIdWithHospital(Long id);
}
```