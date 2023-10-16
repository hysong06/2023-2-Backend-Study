# \[1] Spring MVC

## 1) MVC란?

MVC(Model-View-Controller)란 애플리케이션을 모델/뷰/컨트롤러 세 개의 영역으로 나누고 각 요소에 고유한 역할을 부여하는 디자인 패턴이다.

1. 컨트롤러가 사용자로부터 입력(Action)을 받음
2. 컨트롤러가 해당 입력에 따라 모델을 업데이트
3. 컨트롤러가 적절한 뷰를 선택
4. 뷰가 화면을 통해 모델을 보여줌

|요소|특징|
|:---:|:---|
|모델|- 데이터와 관련된 로직 처리<br>- 변경점 발생 시 뷰와 컨트롤러에 통보<br>- 다른 컴포넌트에 대해 알지 못 함|
|뷰|- 애플리케이션 화면에 표시되는 UI를 의미<br>- 다른 컴포넌트에 대해 알지 못 함|
|컨트롤러|- 모델과 뷰 사이의 중개자<br>- 모델의 상태를 변경하거나, 뷰의 표시 방법을 변경할 수 있음|

즉, MVC를 통해 사용자가 컨트롤러를 조작하여 모델을 통해 데이터를 가져오고, 그 데이터를 적절한 뷰를 통해 사용자에게 시각적으로 보여주게 된다.
컴포넌트를 분리함으로써 수정이나 유지 보수 등에 유리하며, 확장성이 좋아 새로운 기능이나 로직을 추가하기 용이하다.

## 2) Spring MVC

스프링에서는 HTTP 요청/응답을 다루는 `DispatcherServelet` 클래스를 통해 MVC 패턴을 쉽게 다룰 수 있다.
`DispatcherServlet`은 클라이언트의 요청이 있으면 `HandlerMapping`, 컨트롤러, `ViewResolver`, 뷰의 도움을 받아 클라이언트에게 적절히 응답한다.

1. `HandlerMapping` : `DispatcherServlet`에게 클라이언트 요청을 전달 받으면 그에 매핑되는 컨트롤러를 반환
2. 컨트롤러 : 요청에 맞게 모델을 구성하여 `DispatcherServlet`에 반환
3. `ViewResolver` : 처리 결과를 보여줄 적절한 뷰를 선택
4. 뷰 : 비즈니스 로직이 처리된 모델을 최종적으로 클라이언트 화면에 나타내어 응답을 마침

이러한 일련의 과정들은 Spring이 내부적으로 처리해준다.

***
# \[2] API 서버 계층 구조

계층 구조는 한 서비스를 여러 개의 논리 계층으로 나누어 설계하는 디자인 패턴이다.
각 계층은 관심사를 분리해 특정 역할과 책임을 가지며, 자신 아래 계층에 의존하여 원하는 기능을 가져다 쓴다.
이로써 애플리케이션의 유지 보수를 용이하게 한다.
스프링 프로젝트에서는 계층이 높은 순서대로 Controller - Service - Repository의 구조로 서버를 구성한다.

## 1) Controller 계층

: 클라이언트 요청을 받아 수행

```java
@RestController
@RequiredArgsConstructor
@RequestMapping(value = "/demo")
public class DemoController {
    private final DemoService demoService;

    @GetMapping("/test/1/{id}")
    public DemoDto getAllStudents(@PathVariable Long id) {
        return demoService.getDemoEntity(id);
    }
    
    @PostMapping("/test/2")
    public void addStudent(@RequestBody DemoDto demoDto) {
        demoService.addDemoEntity(demoDto);
    }
}
```

|어노테이션|특징|
|:---:|:---|
|`@RequestBody`|HTTP 요청 body의 내용, 즉 사용자의 입력값을 받아온다.|
|`@ResponseBody`|메소드의 반환값을 HTTP 응답 body로 전송한다. (주로 JSON이나 XML 형태)|
|`@Controller`|해당 클래스가 Spring MVC의 컨트롤러임을 나타낸다.|
|`@RestController`|`@ResponseBody` + `@Controller`|
|`@PathVariable`|URI의 일부를 메소드 매개변수에 바인딩하여 변수값으로 다룰 수 있게 한다.|
|`@RequestParam`|HTTP 요청 파라미터를 메소드 매개변수에 바인딩한다.|
|`@RequestMapping`|URI와 컨트롤러 메소드를 매핑해준다.|
|`@GetMapping`|'@RequestMapping(method = RequestMethod.GET)'과 동일|
|`@PostMapping`|'@RequestMapping(method = RequestMethod.POST)'와 동일|

## 2) Service 계층

: 실질적인 로직을 실행

```java
@Service
@RequiredArgsConstructor
public class DemoService {
    private final DemoRepository demoRepository;

    @Transactional
    public DemoDto getDemoEntity(Long id) {
        DemoEntity demoEntity = demoRepository.findById(id).orElseThrow(RuntimeException::new);
        return new DemoDto(demoEntity.getId(), demoEntity.getStrA(), demoEntity.getStrB());
    }

    @Transactional
    public void addDemoEntity(DemoDto demoDto) {
        DemoEntity demoEntity = new DemoEntity(demoDto.getId(), demoDto.getStrA(), demoDto.getStrB());
        demoRepository.save(demoEntity);
    }
}
```

`@Service`를 통해 해당 클래스가 Service 계층에 속함을 나타낸다.

## 3) Repository 계층

: 데이터베이스와 통신

```java
public class DemoRepository extends JpaRepository<DemoEntity, Long> {
	(...)
}
```

특정 도메인 모델에 대한 CRUD 연산을 캡슐화하는 계층이다.

***
# \[3] 📖 프로그램 구현 과제

## DemoController.class

```java
@RequiredArgsConstructor
@RestController
@RequestMapping("/demo")
public class DemoController {
	private final DemoService demoService;
	
	@GetMapping("/demoEntities")
	public ResponseEntity<List<DemoDto>> getDemoEntities() {
		return ResponseEntity.ok().body(demoService.getDemoEntities());
	}
	
	@GetMapping("/demoEntities/{id}")
	public ResponseEntity getDemoEntity(@PathVariable Long id) {
		return ResponseEntity.ok().body(demoService.getDemoEntity(id));
	}
	
	@PostMapping("/demoEntities")
	public void addDemoEntity(@RequestBody DemoDto demoDto) {
		demoService.addDemoEntity(demoDto);
	}
}
```

## DemoService.class

```java
@Service
public class DemoService {
	private final List<DemoEntity> demoEntities = new ArrayList<>(Arrays.asList(
			new DemoEntity(1L, "1번"),
			new DemoEntity(2L, "2번"),
			new DemoEntity(3L, "3번")));
	
	public List<DemoDto> getDemoEntities() {
		List<DemoDto> demoDtos = new ArrayList<>();
		for (DemoEntity demoEntity : demoEntities) {
			demoDtos.add(new DemoDto(demoEntity.getId(), demoEntity.getName()));
		}
		return demoDtos;
	}
	
	public DemoDto getDemoEntity(Long id) {
		for (DemoEntity demoEntity : demoEntities) {
			if (demoEntity.getId().equals(id)) {
				return new DemoDto(demoEntity.getId(), demoEntity.getName());
			}
		}
		throw new RuntimeException(); // NoSuchDataException
	}
	
	public void addDemoEntity(DemoDto demoDto) {
		demoEntities.add(new DemoEntity(demoDto.getId(), demoDto.getName()));
	}
}
```

## DemoDta.java

```java
@RequiredArgsConstructor
@NoArgsConstructor
@Getter
public class DemoDto {
	private Long id;
	private String name;
}
```

## DemoEntity.java

```java
@RequiredArgsConstructor
@NoArgsConstructor
@Getter
public class DemoEntity {
	private Long id;
	private String name;
}
```