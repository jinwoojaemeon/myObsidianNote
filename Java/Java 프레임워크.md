#java #framework #spring #ioc #di #면접 #개념정리

**관련 개념:** [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[SOLID 원칙 (객체지향 설계 원칙)|SOLID 원칙]] [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]

> [!note] 이어서 읽기
> Java 프레임워크를 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**과 **[[SOLID 원칙 (객체지향 설계 원칙)|SOLID 원칙]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

Java 프레임워크가 무엇인지, 왜 사용하는지, 그리고 주요 프레임워크들의 특징과 차이점을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 프레임워크 없이 애플리케이션을 개발하면 반복적인 보일러플레이트 코드를 매번 작성해야 하며, 인증, 보안, 데이터베이스 연결, 트랜잭션 관리 등 공통 기능을 직접 구현해야 합니다. 이는 개발 시간을 크게 늘리고 버그 발생 가능성을 높이며, 유지보수가 어려워집니다. 대규모 프로젝트에서 이러한 작업을 수동으로 관리하는 것은 거의 불가능하며, 코드 중복과 일관성 부족으로 인한 심각한 문제가 발생합니다.

- 프레임워크는 애플리케이션 개발의 공통 패턴과 아키텍처를 제공하여 개발 생산성을 극대화합니다. 검증된 설계 패턴과 모범 사례를 내장하고 있어, 개발자가 비즈니스 로직에 집중할 수 있게 합니다. 프레임워크는 보안, 성능 최적화, 확장성 등 엔터프라이즈급 기능을 제공하여 안정적이고 확장 가능한 애플리케이션을 빠르게 구축할 수 있게 합니다.

- 프레임워크와 라이브러리의 차이, 제어의 역전(IoC) 개념, 의존성 주입(DI) 원리, 그리고 주요 프레임워크별 특징과 선택 기준을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. 프레임워크란?

**프레임워크(Framework)**는 애플리케이션 개발을 위한 구조와 뼈대를 제공하는 재사용 가능한 소프트웨어 플랫폼입니다. 프레임워크는 개발자가 특정 규칙과 패턴에 따라 코드를 작성하도록 하며, 애플리케이션의 실행 흐름을 제어합니다.

##### 프레임워크 vs 라이브러리

| 구분 | 라이브러리 | 프레임워크 |
| --- | --- | --- |
| **제어 흐름** | 개발자 → 라이브러리 | 프레임워크 → 개발자 코드 |
| **역할** | 개발자가 필요할 때 호출 | 애플리케이션 실행 흐름 제어 |
| **예시** | `java.util.ArrayList`, `java.lang.String` | Spring Framework, Hibernate |
| **특징** | 개발자가 코드를 작성하고 필요할 때 라이브러리의 기능을 호출 | 프레임워크가 정의한 규칙에 따라 코드 작성 |

**라이브러리 예시:**
```java
// 개발자가 직접 호출
List<String> list = new ArrayList<>();
list.add("item");
```

**프레임워크 예시:**
```java
// 프레임워크가 자동으로 호출
@RestController
public class UserController {
    @GetMapping("/users")
    public List<User> getUsers() {
        // Spring이 이 메서드를 자동으로 호출
    }
}
```

> [!tip] 핵심 포인트
> 프레임워크는 "제어의 역전"을 통해 개발자가 프레임워크의 규칙에 따라 코드를 작성하도록 합니다. 이는 개발 생산성을 높이지만, 동시에 프레임워크의 학습 곡선과 제약사항을 이해해야 합니다.

---

#### 2. 프레임워크의 핵심 원리: 제어의 역전 (IoC)

**제어의 역전(Inversion of Control, IoC)**은 프레임워크의 핵심 개념입니다.

##### 전통적인 프로그래밍 방식

- 개발자가 객체 생성, 의존성 관리, 생명주기 제어를 직접 담당
- 코드가 실행 흐름을 완전히 제어

```java
// 전통적인 방식
public class UserService {
    private UserRepository userRepository;
    
    public UserService() {
        // 개발자가 직접 객체 생성
        this.userRepository = new UserRepository();
    }
    
    public User findUser(Long id) {
        // 개발자가 직접 메서드 호출
        return userRepository.findById(id);
    }
}
```

##### IoC 방식 (프레임워크)

- 프레임워크가 객체 생성, 의존성 관리, 생명주기 제어를 담당
- 개발자는 비즈니스 로직에만 집중
- 프레임워크가 실행 흐름을 제어

```java
// IoC 방식 (Spring Framework)
@Service
public class UserService {
    private UserRepository userRepository;
    
    // 프레임워크가 의존성을 주입
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public User findUser(Long id) {
        return userRepository.findById(id);
    }
}
```

**IoC 동작 흐름:**

```
전통적인 방식:
개발자 코드
  ↓
객체 생성 (new MyClass())
  ↓
의존성 주입 (직접 설정)
  ↓
메서드 호출
  ↓
생명주기 관리

IoC 방식 (프레임워크):
프레임워크
  ↓
객체 생성 및 관리 (컨테이너)
  ↓
의존성 주입 (자동)
  ↓
개발자 코드 호출 (콜백)
  ↓
생명주기 관리 (프레임워크)
```

**IoC의 장점:**
- ✅ 객체 간 결합도 감소
- ✅ 테스트 용이성 향상 (Mock 객체 주입 가능)
- ✅ 코드 재사용성 증가
- ✅ 유지보수성 향상

---

#### 3. 의존성 주입 (Dependency Injection, DI)

**의존성 주입(DI)**은 IoC의 구현 방식 중 하나로, 객체가 필요로 하는 의존성을 외부에서 주입받는 패턴입니다.

##### 의존성 주입 없이 (전통적인 방식)

```java
public class UserService {
    private UserRepository userRepository;

    public UserService() {
        // 직접 의존성 생성 (강한 결합)
        this.userRepository = new UserRepository();
    }
    
    public User findUser(Long id) {
        return userRepository.findById(id);
    }
}
```

**문제점:**
- 강한 결합도 (UserService가 UserRepository 구현에 의존)
- 테스트 어려움 (Mock 객체 주입 불가)
- 유연성 부족 (다른 구현체로 교체 어려움)

##### 의존성 주입 사용 (Spring Framework)

```java
@Service
public class UserService {
    private UserRepository userRepository;

    // 생성자를 통한 의존성 주입 (약한 결합)
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public User findUser(Long id) {
        return userRepository.findById(id);
    }
}

@Repository
public class UserRepository {
    // ...
}
```

**장점:**
- 약한 결합도 (인터페이스에 의존)
- 테스트 용이성 (Mock 객체 주입 가능)
- 유연성 (구현체 교체 용이)

##### 의존성 주입 방식

**1. 생성자 주입 (Constructor Injection) - 가장 권장되는 방식**

```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    // 생성자 주입
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

**장점:**
- 불변성 보장 (`final` 키워드 사용 가능)
- 필수 의존성 명확
- 순환 참조 방지

**2. 필드 주입 (Field Injection)**

```java
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository; // 필드 주입
}
```

**단점:**
- 테스트 어려움 (Mock 객체 주입 불가)
- 불변성 보장 불가
- 순환 참조 가능

**3. 세터 주입 (Setter Injection)**

```java
@Service
public class UserService {
    private UserRepository userRepository;
    
    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

**특징:**
- 선택적 의존성에 적합
- 생성자 주입보다 덜 권장

> [!important] 핵심 원리
> DI는 객체 간 결합도를 낮추고 테스트 용이성을 높이는 핵심 패턴입니다. 생성자 주입을 기본으로 사용하는 것이 좋습니다.

---

#### 4. 주요 자바 프레임워크 분류

자바 프레임워크는 용도에 따라 여러 카테고리로 분류됩니다.

##### 4.1 웹 애플리케이션 프레임워크

**Spring Framework**

- **역할**: 엔터프라이즈급 Java 애플리케이션 개발을 위한 종합 프레임워크
- **특징**:
  - IoC 컨테이너와 DI 제공
  - AOP (Aspect-Oriented Programming) 지원
  - 트랜잭션 관리
  - 다양한 모듈 (Spring MVC, Spring Data, Spring Security 등)
- **사용 시기**: 대규모 엔터프라이즈 애플리케이션, 마이크로서비스 아키텍처

**Spring Boot**

- **역할**: Spring Framework 기반의 빠른 애플리케이션 개발 도구
- **특징**:
  - 자동 설정 (Auto Configuration)
  - 내장 서버 (Tomcat, Jetty)
  - 프로덕션 준비 기능 (Actuator)
  - 의존성 관리 간소화
- **사용 시기**: 빠른 프로토타이핑, 마이크로서비스, RESTful API 개발

**Spring Boot vs Spring Framework:**

```java
// Spring Framework (수동 설정)
@Configuration
public class AppConfig {
    @Bean
    public DataSource dataSource() {
        // 수동으로 DataSource 설정
        return new HikariDataSource();
    }
}

// Spring Boot (자동 설정)
// application.properties만 설정하면 자동으로 DataSource 생성
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
```

**Struts**

- **역할**: 전통적인 MVC 패턴 기반 웹 프레임워크
- **특징**:
  - MVC 아키텍처 강제
  - XML 기반 설정
- **사용 시기**: 레거시 시스템, 전통적인 웹 애플리케이션

##### 4.2 ORM (Object-Relational Mapping) 프레임워크

**Hibernate**

- **역할**: Java 객체와 관계형 데이터베이스 간 매핑
- **특징**:
  - JPA (Java Persistence API) 구현체
  - 객체 중심 개발
  - 자동 SQL 생성
  - 캐싱 지원
- **사용 시기**: 복잡한 데이터베이스 관계, 객체 중심 개발

**MyBatis**

- **역할**: SQL 매퍼 프레임워크
- **특징**:
  - SQL 직접 제어 가능
  - XML 또는 어노테이션 기반 매핑
  - 가벼움
- **사용 시기**: 복잡한 SQL 쿼리, SQL 제어가 중요한 경우

**Hibernate vs MyBatis:**

```java
// Hibernate (JPA) - 객체 중심
@Entity
public class User {
    @Id
    private Long id;
    private String name;
}

// 자동으로 SQL 생성
List<User> users = entityManager.createQuery("SELECT u FROM User u", User.class).getResultList();

// MyBatis - SQL 직접 제어
@Select("SELECT * FROM users WHERE id = #{id}")
User findById(Long id);
```

##### 4.3 테스트 프레임워크

**JUnit**

- **역할**: Java 단위 테스트 프레임워크
- **특징**:
  - 어노테이션 기반 테스트
  - Assertion 메서드 제공
  - 테스트 러너 지원
- **사용 시기**: 모든 Java 프로젝트의 단위 테스트

**Mockito**

- **역할**: Mock 객체 생성 및 테스트 프레임워크
- **특징**:
  - Mock 객체 생성
  - 행위 검증 (Verification)
  - 스파이(Spy) 기능
- **사용 시기**: 의존성이 있는 객체의 단위 테스트

**테스트 예시:**

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository userRepository;
    
    @InjectMocks
    private UserService userService;
    
    @Test
    void testFindUser() {
        // Mock 객체 설정
        when(userRepository.findById(1L)).thenReturn(new User(1L, "John"));
        
        // 테스트 실행
        User user = userService.findUser(1L);
        
        // 검증
        assertEquals("John", user.getName());
        verify(userRepository).findById(1L);
    }
}
```

---

#### 5. 프레임워크 선택 기준

##### 프로젝트 규모

- **소규모**: Spring Boot (빠른 개발)
- **중규모**: Spring Framework (유연성)
- **대규모**: Spring Framework + Spring Cloud (마이크로서비스)

##### 팀 역량

- **초보자**: Spring Boot (자동 설정, 학습 곡선 낮음)
- **경험자**: Spring Framework (세밀한 제어 가능)

##### 성능 요구사항

- **일반적인 웹 애플리케이션**: Spring Framework
- **고성능 요구**: Vert.x, Netty (비동기 처리)

##### 데이터베이스 복잡도

- **단순한 쿼리**: Spring Data JPA
- **복잡한 SQL**: MyBatis
- **복잡한 관계**: Hibernate

---

#### 6. 프레임워크 사용 시 전체 플로우

```
프로젝트 시작
   Spring Boot 프로젝트 생성
      ↓
의존성 설정
   pom.xml 또는 build.gradle
      ↓
애플리케이션 구조 설정
   Controller, Service, Repository 계층
      ↓
IoC 컨테이너 초기화
   Spring이 빈(Bean) 생성 및 관리
      ↓
의존성 주입
   @Autowired, @Inject를 통한 자동 주입
      ↓
애플리케이션 실행
   프레임워크가 요청 처리 흐름 제어
      ↓
비즈니스 로직 실행
   개발자가 작성한 코드 실행
      ↓
응답 반환
   프레임워크가 응답 처리
```

> [!important] 핵심 정리
> 
> - **프레임워크**: 제어의 역전을 통해 애플리케이션 실행 흐름을 관리
> - **라이브러리**: 개발자가 필요할 때 호출하는 도구 모음
> - **IoC**: 프레임워크가 객체 생성과 생명주기를 관리
> - **DI**: 객체 간 의존성을 외부에서 주입하여 결합도 감소

---

#### 장점/단점

**장점:**
- 개발 생산성 향상 (보일러플레이트 코드 감소)
- 검증된 아키텍처와 패턴 제공
- 공통 기능 제공 (보안, 트랜잭션, 로깅 등)
- 유지보수성 향상 (표준화된 구조)
- 커뮤니티 지원과 문서화
- 테스트 용이성 (Mock 객체, 테스트 프레임워크)

**단점:**
- 학습 곡선 (프레임워크별 학습 필요)
- 제약사항 (프레임워크의 규칙에 따라야 함)
- 오버헤드 (프레임워크 자체의 메모리/성능 비용)
- 버전 의존성 (프레임워크 업그레이드 시 호환성 문제)
- 과도한 추상화 (복잡도 증가 가능)

---

#### 필요 조건

- **Java 개발 환경**: JDK 설치 및 IDE 설정
- **빌드 도구**: Maven 또는 Gradle
- **프레임워크 의존성**: 프로젝트에 프레임워크 라이브러리 추가
- **프레임워크 학습**: 선택한 프레임워크의 기본 개념과 사용법 이해
- **아키텍처 이해**: MVC, 레이어드 아키텍처 등 기본 설계 패턴

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 프레임워크 선택 시 고려사항

- [ ] 프로젝트 규모와 복잡도 파악
- [ ] 팀의 기술 스택과 경험 수준 확인
- [ ] 성능 요구사항 분석
- [ ] 유지보수성과 확장성 고려
- [ ] 커뮤니티 지원과 문서화 수준 확인
- [ ] 라이선스 정책 확인

#### 실무 활용 예시

**1. Spring Boot 프로젝트 생성**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

**2. 계층별 구조 설계**

```java
// Controller 계층
@RestController
@RequestMapping("/api/users")
public class UserController {
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findUser(id);
    }
}

// Service 계층
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public User findUser(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }
}

// Repository 계층
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // JPA 메서드 자동 제공
}
```

**3. 설정 파일 관리**

```yaml
# application.yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: password
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

# 프로파일별 설정 분리
---
spring:
  profiles: dev
  datasource:
    url: jdbc:mysql://localhost:3306/mydb_dev

---
spring:
  profiles: prod
  datasource:
    url: jdbc:mysql://prod-server:3306/mydb_prod
```

---

### 설정 시 반드시 고려해야 할 파라미터

- **프레임워크 버전 선택**:
  - 최신 버전 vs 안정 버전
  - LTS (Long Term Support) 버전 고려
  - 예: Spring Boot 2.7.x (안정), Spring Boot 3.x (최신)

- **의존성 관리**:
  - **Maven**: `pom.xml`에서 의존성 관리
    - Spring Boot Starter를 사용하면 관련 의존성 자동 관리
  - **Gradle**: `build.gradle`에서 의존성 관리
    - 버전 카탈로그를 통한 중앙화된 버전 관리

- **프로파일 설정**:
  - `application.properties` 또는 `application.yml`
  - 개발/테스트/프로덕션 환경별 설정 분리
  - 예: `spring.profiles.active=dev`

- **빌드 도구 선택**:
  - **Maven**: XML 기반, 널리 사용됨
  - **Gradle**: Groovy/Kotlin DSL, 더 유연함

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "프레임워크를 사용하면 모든 것이 자동으로 해결된다"는 생각은 위험합니다. 프레임워크는 도구일 뿐이며, 잘못된 사용은 오히려 복잡도만 증가시킬 수 있습니다. 또한 "프레임워크 없이도 개발할 수 있다"는 생각도 있지만, 대규모 프로젝트에서는 프레임워크 없이 개발하는 것이 비효율적입니다.
> 
> "모든 프레임워크를 다 배워야 한다"는 생각도 잘못되었습니다. 프로젝트에 필요한 프레임워크를 선택적으로 학습하고, 하나의 프레임워크를 깊이 있게 이해하는 것이 더 중요합니다. Spring Framework를 잘 이해하면 다른 프레임워크도 쉽게 학습할 수 있습니다.

#### 프레임워크 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **코드 중복**: 반복적인 보일러플레이트 코드 작성
> - **보안 취약점**: 인증/인가 로직을 직접 구현하면서 발생하는 보안 문제
> - **유지보수 어려움**: 일관성 없는 코드 구조로 인한 유지보수 비용 증가
> - **성능 문제**: 최적화되지 않은 코드로 인한 성능 저하
> - **테스트 어려움**: 의존성이 강하게 결합되어 단위 테스트 작성 어려움
> - **개발 지연**: 공통 기능을 직접 구현하면서 개발 시간 증가

---

## 요약

- **프레임워크의 정의와 역할**: 프레임워크는 애플리케이션 개발을 위한 구조와 뼈대를 제공하는 재사용 가능한 소프트웨어 플랫폼입니다. 라이브러리는 개발자가 호출하지만, 프레임워크는 애플리케이션의 실행 흐름을 제어합니다.

- **제어의 역전 (IoC)**: 프레임워크가 객체 생성, 의존성 관리, 생명주기 제어를 담당합니다. 개발자는 비즈니스 로직에만 집중할 수 있습니다. 이는 전통적인 프로그래밍 방식과 반대로 제어 흐름이 역전됩니다.

- **의존성 주입 (DI)**: 객체가 필요로 하는 의존성을 외부에서 주입받는 패턴입니다. 생성자 주입, 필드 주입, 세터 주입 방식이 있습니다. 객체 간 결합도를 감소시키고 테스트 용이성을 향상시킵니다.

- **주요 자바 프레임워크**: 웹 프레임워크(Spring Framework, Spring Boot, Struts), ORM 프레임워크(Hibernate, MyBatis), 테스트 프레임워크(JUnit, Mockito) 등이 있으며, 각 프레임워크는 특정 용도와 장점을 가지고 있어 프로젝트 특성에 맞게 선택해야 합니다.

- 프레임워크는 단순한 도구가 아니라 애플리케이션의 아키텍처와 개발 방식을 결정하는 핵심 요소입니다.
- IoC와 DI 원리를 이해하면 프레임워크의 동작 방식을 깊이 있게 이해할 수 있습니다.
- 적절한 프레임워크 선택과 활용은 개발 생산성과 코드 품질을 크게 향상시킵니다.

---

### 예상 꼬리질문정리

#### 1. 프레임워크와 라이브러리의 차이를 설명해주세요.

- **제어 흐름의 차이**: 라이브러리는 개발자가 코드를 작성하고 필요할 때 호출하지만, 프레임워크는 애플리케이션의 실행 흐름을 제어합니다.
- **제어의 역전**: 프레임워크는 IoC를 통해 개발자가 아닌 프레임워크가 제어 흐름을 관리합니다.
- **예시**: `ArrayList`는 라이브러리(개발자가 호출), Spring Framework는 프레임워크(Spring이 메서드를 자동 호출)

#### 2. 제어의 역전(IoC)이란 무엇인가요?

- **정의**: 전통적으로 개발자가 제어하던 객체 생성, 의존성 관리, 생명주기 제어를 프레임워크가 담당하는 것
- **장점**: 객체 간 결합도 감소, 테스트 용이성 향상, 코드 재사용성 증가, 유지보수성 향상
- **동작 방식**: 프레임워크가 컨테이너를 통해 객체를 생성하고 관리하며, 개발자 코드를 콜백으로 호출

#### 3. 의존성 주입(DI)의 세 가지 방식과 각각의 장단점은?

- **생성자 주입**: 가장 권장되는 방식, 불변성 보장, 필수 의존성 명확, 순환 참조 방지
- **필드 주입**: 간단하지만 테스트 어려움, 불변성 보장 불가, 순환 참조 가능
- **세터 주입**: 선택적 의존성에 적합, 생성자 주입보다 덜 권장

#### 4. Spring Framework와 Spring Boot의 차이는?

- **Spring Framework**: 엔터프라이즈급 애플리케이션을 위한 종합 프레임워크, 수동 설정 필요
- **Spring Boot**: Spring Framework 기반의 빠른 개발 도구, 자동 설정, 내장 서버, 의존성 관리 간소화
- **관계**: Spring Boot는 Spring Framework를 기반으로 하되, 설정을 자동화하고 개발 생산성을 높인 도구

#### 5. Hibernate와 MyBatis의 차이와 선택 기준은?

- **Hibernate**: JPA 구현체, 객체 중심 개발, 자동 SQL 생성, 복잡한 관계 매핑에 적합
- **MyBatis**: SQL 매퍼, SQL 직접 제어 가능, 복잡한 SQL 쿼리에 적합
- **선택 기준**: 객체 중심 개발이면 Hibernate, SQL 제어가 중요하면 MyBatis

#### 6. 프레임워크를 사용하지 않고 개발할 수 있나요?

- **가능하지만 비효율적**: 소규모 프로젝트는 가능하지만, 대규모 프로젝트에서는 비효율적
- **문제점**: 보일러플레이트 코드 증가, 보안 취약점, 유지보수 어려움, 개발 시간 증가
- **결론**: 프레임워크 사용이 현대적인 개발 방식이며, 생산성과 품질을 크게 향상시킴

#### 7. IoC 컨테이너란 무엇인가요?

- **정의**: 객체의 생성, 생명주기, 의존성 주입을 관리하는 컨테이너
- **역할**: 빈(Bean)을 생성하고 관리하며, 의존성을 자동으로 주입
- **예시**: Spring의 `ApplicationContext`가 IoC 컨테이너 역할

#### 8. 프레임워크 선택 시 고려사항은?

- **프로젝트 규모**: 소규모는 Spring Boot, 대규모는 Spring Framework + Spring Cloud
- **팀 역량**: 초보자는 Spring Boot, 경험자는 Spring Framework
- **성능 요구사항**: 일반적인 웹 애플리케이션은 Spring, 고성능은 Vert.x, Netty
- **데이터베이스 복잡도**: 단순한 쿼리는 JPA, 복잡한 SQL은 MyBatis

#### 9. 의존성 주입이 테스트에 도움이 되는 이유는?

- **Mock 객체 주입 가능**: 실제 구현체 대신 Mock 객체를 주입하여 단위 테스트 작성 가능
- **결합도 감소**: 인터페이스에 의존하여 구현체를 쉽게 교체 가능
- **격리된 테스트**: 각 컴포넌트를 독립적으로 테스트 가능

#### 10. Spring의 빈(Bean)이란 무엇인가요?

- **정의**: Spring IoC 컨테이너가 관리하는 객체
- **생명주기**: 컨테이너가 생성부터 소멸까지 관리
- **스코프**: Singleton(기본), Prototype, Request, Session 등
- **등록 방법**: `@Component`, `@Service`, `@Repository`, `@Controller` 등

#### 11. AOP(Aspect-Oriented Programming)란 무엇인가요?

- **정의**: 횡단 관심사(로깅, 트랜잭션, 보안 등)를 모듈화하는 프로그래밍 패러다임
- **목적**: 비즈니스 로직과 횡단 관심사를 분리하여 코드 중복 제거
- **예시**: `@Transactional` 어노테이션으로 트랜잭션 관리

#### 12. 프레임워크의 단점은 무엇인가요?

- **학습 곡선**: 프레임워크별 학습 필요
- **제약사항**: 프레임워크의 규칙에 따라야 함
- **오버헤드**: 프레임워크 자체의 메모리/성능 비용
- **버전 의존성**: 업그레이드 시 호환성 문제
- **과도한 추상화**: 복잡도 증가 가능

#### 13. Spring Boot의 자동 설정(Auto Configuration) 원리는?

- **조건부 빈 등록**: `@ConditionalOnClass`, `@ConditionalOnMissingBean` 등으로 조건에 따라 빈 등록
- **스타터 의존성**: `spring-boot-starter-*`를 통해 관련 의존성과 설정 자동 제공
- **설정 파일**: `application.properties` 또는 `application.yml`로 간단히 설정

#### 14. 프레임워크 없이 개발할 때 발생하는 문제는?

- **코드 중복**: 반복적인 보일러플레이트 코드 작성
- **보안 취약점**: 인증/인가 로직을 직접 구현하면서 발생하는 보안 문제
- **유지보수 어려움**: 일관성 없는 코드 구조
- **성능 문제**: 최적화되지 않은 코드
- **테스트 어려움**: 강한 결합도로 인한 단위 테스트 작성 어려움

#### 15. JUnit과 Mockito의 차이와 사용 시기는?

- **JUnit**: 단위 테스트 프레임워크, 테스트 메서드 작성 및 실행
- **Mockito**: Mock 객체 생성 및 행위 검증, 의존성이 있는 객체 테스트에 사용
- **관계**: JUnit과 함께 사용하여 격리된 단위 테스트 작성

---

## 참고 자료

- [Spring Framework 공식 문서](https://docs.spring.io/spring-framework/docs/current/reference/html/) - Spring Framework 공식 문서
- [Spring Boot 공식 사이트](https://spring.io/projects/spring-boot) - Spring Boot 공식 사이트
- [Hibernate 공식 사이트](https://hibernate.org/) - Hibernate 공식 사이트
- [MyBatis 공식 문서](https://mybatis.org/mybatis-3/) - MyBatis 공식 문서

---

## 관련 노트

- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - 프레임워크가 해결하는 객체지향 설계 원리
- **[[SOLID 원칙 (객체지향 설계 원칙)|SOLID 원칙]]** - 프레임워크가 적용하는 설계 원칙
- **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]** - ORM 프레임워크의 필요성과 동작 원리
- **[[JPA 엔티티 매핑|JPA 엔티티 매핑]]** - JPA 프레임워크를 활용한 엔티티 설계
- **[[API (Application Programming Interface)|API]]** - 프레임워크를 활용한 API 개발
- **[[RESTful API (REST API)|RESTful API]]** - Spring Framework를 활용한 REST API 개발

---

**태그:** #java #framework #spring #ioc #di #면접 #개념정리
