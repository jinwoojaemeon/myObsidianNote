#spring #application-context #ioc #container #bean #면접 #개념정리

**관련 개념:** [[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]] [[스프링 빈 (Spring Bean)|스프링 빈]] [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]

> [!note] 이어서 읽기
> Application Context를 이해하려면 **[[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]**의 핵심 개념과 **[[스프링 빈 (Spring Bean)|스프링 빈]]**을 함께 확인하세요.

---

## Topic (오늘의 주제)

**Application Context**는 스프링 IoC 컨테이너의 핵심 인터페이스로, 빈(Bean)의 생성, 의존성 주입, 생명주기 관리를 담당한다. 애플리케이션의 모든 빈을 중앙에서 관리하여 개발자가 비즈니스 로직에만 집중할 수 있게 한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- Application Context 없이 객체를 직접 생성하면 의존 관계가 복잡한 애플리케이션에서 구현체 변경 시 코드를 수정해야 하고, 객체의 생성 순서와 주입 관계를 일일이 파악해야 하며, 테스트 시 Mock 객체를 주입하기 어렵습니다.

- Application Context를 사용하면 스프링이 빈의 생성, 의존성 주입, 생명주기를 자동으로 관리하여 개발자는 비즈니스 로직에만 집중할 수 있습니다. 빈을 중앙에서 관리하여 유지보수성과 테스트 용이성이 크게 향상됩니다.

- Application Context의 정의, 역할, BeanFactory와의 차이, 그리고 빈 조회 방법을 이해해야 합니다.

---

## 1. Application Context란 무엇인가?

### **Application Context의 정의**

**Application Context**는 스프링 IoC 컨테이너의 핵심 인터페이스로, 빈(Bean)의 생성, 의존성 주입, 생명주기 관리를 담당하는 컨테이너입니다.

**핵심 특징:**
- 스프링 IoC 컨테이너의 구현체
- 빈 팩토리 기능 + 추가 기능 제공
- 애플리케이션의 모든 빈을 중앙에서 관리

### **Application Context의 역할**

1. **빈 생성 및 관리**: `@Component`, `@Service`, `@Repository` 등으로 등록된 빈을 생성하고 관리
2. **의존성 주입**: 빈 간의 의존 관계를 자동으로 주입
3. **생명주기 관리**: 빈의 생성부터 소멸까지 생명주기 관리
4. **빈 조회**: 필요할 때 빈을 조회하여 제공

---

## 2. BeanFactory vs Application Context

### **BeanFactory**

**BeanFactory**는 스프링 IoC 컨테이너의 최상위 인터페이스로, 빈의 생성과 조회 기능만 제공합니다.

**특징:**
- 빈 생성과 조회 기능만 제공
- 지연 로딩(Lazy Loading): 빈을 조회할 때 생성
- 경량 컨테이너

### **Application Context**

**Application Context**는 BeanFactory를 상속받아 추가 기능을 제공합니다.

**추가 기능:**
- 메시지 소스 처리 (국제화)
- 이벤트 발행 기능
- 리소스 로딩 기능
- 환경 변수 처리
- 즉시 로딩(Eager Loading): 애플리케이션 시작 시 빈 생성

### **비교**

| 구분 | BeanFactory | Application Context |
|------|------------|---------------------|
| **빈 생성 시점** | 지연 로딩 (Lazy) | 즉시 로딩 (Eager) |
| **추가 기능** | 없음 | 메시지 소스, 이벤트, 리소스 등 |
| **사용 빈도** | 거의 사용 안 함 | 실무에서 주로 사용 |

### **실무 사용**

```java
// 실무에서는 Application Context를 주로 사용
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
```

---

## 3. Application Context의 구현체

### **주요 구현체**

#### 1. AnnotationConfigApplicationContext

**어노테이션 기반 설정**을 사용하는 Application Context입니다.

```java
// 설정 클래스 기반
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
}

// Application Context 생성
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
```

**특징:**
- `@Configuration`, `@ComponentScan` 사용
- Java 설정 파일 기반
- 실무에서 가장 많이 사용

#### 2. GenericXmlApplicationContext

**XML 기반 설정**을 사용하는 Application Context입니다.

```java
// XML 파일 기반
ApplicationContext context = new GenericXmlApplicationContext("appConfig.xml");
```

**특징:**
- XML 설정 파일 사용
- 레거시 프로젝트에서 사용

#### 3. GenericGroovyApplicationContext

**Groovy 스크립트 기반** 설정을 사용하는 Application Context입니다.

```java
ApplicationContext context = new GenericGroovyApplicationContext("appConfig.groovy");
```

---

## 4. Application Context 동작 과정

### **애플리케이션 시작 시**

```
1️⃣ 애플리케이션 시작
   ↓
2️⃣ Application Context 생성
   ↓
3️⃣ 설정 정보 기반으로 빈 등록
   - @Component, @Service, @Repository 등 스캔
   - @Configuration의 @Bean 메서드 등록
   ↓
4️⃣ 빈 생성 (싱글톤)
   - 의존성이 없는 빈부터 생성
   - 의존성이 있는 빈은 의존성 주입 후 생성
   ↓
5️⃣ 의존관계 주입 (DI)
   - 생성자, 필드, 세터 주입
   ↓
6️⃣ 초기화 콜백 실행
   - @PostConstruct 메서드 실행
   ↓
7️⃣ 애플리케이션 실행 준비 완료
```

### **구체적인 예시**

```java
// 설정 클래스
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
}

// Repository
@Repository
public class UserRepository {
    public User findById(String id) {
        // ...
    }
}

// Service
@Service
public class UserService {
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
    
    public User findUser(String id) {
        return repository.findById(id);
    }
}
```

**Application Context의 처리 과정:**

1. **Application Context 생성**
   ```java
   ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
   ```

2. **컴포넌트 스캔**
   - `@ComponentScan`으로 `com.example` 패키지 스캔
   - `UserRepository`, `UserService` 발견

3. **빈 등록**
   - 빈 이름: `userRepository`, `userService`
   - 빈 타입: `UserRepository.class`, `UserService.class`

4. **빈 생성 순서**
   ```
   UserRepository 생성 (의존성 없음)
   ↓
   UserService 생성 (UserRepository 주입)
   ```

5. **의존성 주입**
   - `UserService` 생성자에 `UserRepository` 주입
   - 스프링이 자동으로 연결

6. **빈 사용**
   ```java
   // 개발자는 이렇게 사용
   UserService userService = context.getBean(UserService.class);
   User user = userService.findUser("123");
   ```

---

## 5. 빈 조회 방법

### **타입으로 조회**

```java
ApplicationContext context = ...;

// 타입으로 조회
UserService userService = context.getBean(UserService.class);
```

### **이름으로 조회**

```java
ApplicationContext context = ...;

// 이름으로 조회
UserService userService = (UserService) context.getBean("userService");
```

### **타입과 이름으로 조회**

```java
ApplicationContext context = ...;

// 타입과 이름으로 조회
UserService userService = context.getBean("userService", UserService.class);
```

### **모든 빈 조회**

```java
ApplicationContext context = ...;

// 특정 타입의 모든 빈 조회
Map<String, UserRepository> beans = context.getBeansOfType(UserRepository.class);

// 모든 빈 조회
String[] beanNames = context.getBeanDefinitionNames();
for (String beanName : beanNames) {
    Object bean = context.getBean(beanName);
    System.out.println("name = " + beanName + ", object = " + bean);
}
```

### **빈 존재 여부 확인**

```java
ApplicationContext context = ...;

// 빈 존재 여부 확인
boolean hasBean = context.containsBean("userService");

// 빈 타입 확인
boolean isType = context.isTypeMatch("userService", UserService.class);
```

---

## 6. Application Context의 생명주기

### **생명주기 단계**

```
1. Application Context 생성
   ↓
2. 빈 등록 정보 로드
   ↓
3. 빈 생성 (싱글톤)
   ↓
4. 의존성 주입 (DI)
   ↓
5. 초기화 콜백 (@PostConstruct)
   ↓
6. 사용
   ↓
7. 소멸 전 콜백 (@PreDestroy)
   ↓
8. Application Context 종료
```

### **초기화와 소멸 콜백**

```java
@Component
public class UserService {
    
    @PostConstruct
    public void init() {
        // 빈 초기화 후 실행
        System.out.println("UserService 초기화");
    }
    
    @PreDestroy
    public void destroy() {
        // 빈 소멸 전 실행
        System.out.println("UserService 소멸");
    }
}
```

---

## 7. Spring Boot에서의 Application Context

### **Spring Boot의 Application Context**

Spring Boot는 내부적으로 `AnnotationConfigApplicationContext`를 사용합니다.

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        // 내부적으로 ApplicationContext 생성
        SpringApplication.run(Application.class, args);
    }
}
```

### **Application Context 조회**

```java
@RestController
public class UserController {
    
    @Autowired
    private ApplicationContext applicationContext;
    
    @GetMapping("/beans")
    public List<String> getBeans() {
        return Arrays.asList(applicationContext.getBeanDefinitionNames());
    }
}
```

---

## 요약

- **Application Context**는 스프링 IoC 컨테이너의 핵심 인터페이스로, 빈의 생성, 의존성 주입, 생명주기 관리를 담당합니다.
- **BeanFactory vs Application Context**: BeanFactory는 빈 생성과 조회만 제공하지만, Application Context는 추가 기능(메시지 소스, 이벤트 등)을 제공합니다.
- **주요 구현체**: AnnotationConfigApplicationContext(어노테이션 기반), GenericXmlApplicationContext(XML 기반)
- **동작 과정**: 애플리케이션 시작 → Application Context 생성 → 빈 등록 → 빈 생성 → 의존성 주입 → 초기화 콜백 → 사용
- **빈 조회 방법**: 타입으로 조회, 이름으로 조회, 모든 빈 조회 등
- **생명주기**: 생성 → 등록 → 생성 → 주입 → 초기화 → 사용 → 소멸

---

### 설정 시 반드시 고려해야 할 파라미터

- **컴포넌트 스캔 범위**:
  ```java
  @ComponentScan(basePackages = "com.example")
  ```
  - 스캔할 패키지 범위를 명확히 지정하여 불필요한 스캔을 방지합니다.

- **빈 등록 방식**:
  - 컴포넌트 스캔: `@Component`, `@Service`, `@Repository`, `@Controller`
  - 자바 설정: `@Configuration` + `@Bean`
  - XML 설정: `<bean>` 태그

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "Application Context와 BeanFactory는 완전히 다른 개념이다"는 생각은 잘못되었습니다. Application Context는 BeanFactory를 상속받아 추가 기능을 제공하는 인터페이스입니다. 또한 "Application Context는 항상 모든 빈을 즉시 생성한다"는 것도 정확하지 않습니다. 싱글톤 빈은 즉시 생성되지만, 프로토타입 빈은 조회할 때마다 생성됩니다.

#### Application Context 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **빈 조회 실패**: Application Context 없이 빈을 조회하려고 하면 `NoSuchBeanDefinitionException` 발생
> - **의존성 주입 실패**: Application Context가 없으면 자동 의존성 주입이 작동하지 않음
> - **생명주기 관리 불가**: 빈의 초기화와 소멸 콜백이 실행되지 않음
> - **싱글톤 보장 실패**: Application Context 없이는 싱글톤 패턴을 보장할 수 없음
> - **테스트 어려움**: Application Context 없이는 통합 테스트 작성이 어려움

---

### 출처

- [Spring Framework - IoC Container](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans)
- [Spring Framework - ApplicationContext](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#context-introduction)

---

### 예상 꼬리질문정리

#### 1. Application Context란 무엇인가요?

- Application Context는 스프링 IoC 컨테이너의 핵심 인터페이스로, 빈의 생성, 의존성 주입, 생명주기 관리를 담당하는 컨테이너입니다. 애플리케이션의 모든 빈을 중앙에서 관리하여 개발자가 비즈니스 로직에만 집중할 수 있게 합니다.

#### 2. BeanFactory와 Application Context의 차이는 무엇인가요?

- **BeanFactory**: 스프링 IoC 컨테이너의 최상위 인터페이스로, 빈의 생성과 조회 기능만 제공합니다. 지연 로딩(Lazy Loading) 방식으로 빈을 조회할 때 생성합니다.
- **Application Context**: BeanFactory를 상속받아 추가 기능을 제공합니다. 메시지 소스 처리, 이벤트 발행, 리소스 로딩 등의 기능을 제공하며, 즉시 로딩(Eager Loading) 방식으로 애플리케이션 시작 시 빈을 생성합니다.

#### 3. Application Context의 주요 구현체는 무엇인가요?

- **AnnotationConfigApplicationContext**: 어노테이션 기반 설정을 사용하는 Application Context입니다. `@Configuration`, `@ComponentScan`을 사용하며, 실무에서 가장 많이 사용됩니다.
- **GenericXmlApplicationContext**: XML 기반 설정을 사용하는 Application Context입니다. 레거시 프로젝트에서 사용됩니다.
- **GenericGroovyApplicationContext**: Groovy 스크립트 기반 설정을 사용하는 Application Context입니다.

#### 4. Application Context의 동작 과정을 설명해주세요.

1. 애플리케이션 시작
2. Application Context 생성
3. 설정 정보 기반으로 빈 등록 (`@Component`, `@Service` 등 스캔)
4. 빈 생성 (싱글톤, 의존성이 없는 빈부터)
5. 의존관계 주입 (DI)
6. 초기화 콜백 실행 (`@PostConstruct`)
7. 애플리케이션 실행 준비 완료

#### 5. 빈을 조회하는 방법은 무엇인가요?

- **타입으로 조회**: `context.getBean(UserService.class)`
- **이름으로 조회**: `context.getBean("userService")`
- **타입과 이름으로 조회**: `context.getBean("userService", UserService.class)`
- **모든 빈 조회**: `context.getBeansOfType(UserRepository.class)`

#### 6. Application Context의 생명주기는 어떻게 되나요?

1. Application Context 생성
2. 빈 등록 정보 로드
3. 빈 생성 (싱글톤)
4. 의존성 주입 (DI)
5. 초기화 콜백 (`@PostConstruct`)
6. 사용
7. 소멸 전 콜백 (`@PreDestroy`)
8. Application Context 종료

#### 7. Spring Boot에서 Application Context는 어떻게 사용되나요?

- Spring Boot는 내부적으로 `AnnotationConfigApplicationContext`를 사용합니다.
- `@SpringBootApplication` 어노테이션이 있는 클래스를 기준으로 컴포넌트 스캔을 수행합니다.
- 개발자는 `@Autowired`나 생성자 주입을 통해 빈을 사용할 수 있습니다.

---

## 관련 노트

- **[[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]** - Application Context가 구현하는 IoC 원리
- **[[스프링 빈 (Spring Bean)|스프링 빈]]** - Application Context가 관리하는 빈의 개념
- **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]** - Application Context가 속한 스프링 프레임워크

---

**태그:** #spring #application-context #ioc #container #bean #면접 #개념정리
