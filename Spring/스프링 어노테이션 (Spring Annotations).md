#spring #annotation #component #service #repository #controller #면접 #개념정리

**관련 개념:** [[스프링 빈 (Spring Bean)|스프링 빈]] [[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]] [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]

> [!note] 이어서 읽기
> 스프링 어노테이션을 이해하려면 **[[스프링 빈 (Spring Bean)|스프링 빈]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

스프링에서 사용하는 주요 어노테이션(`@Component`, `@Service`, `@Repository`, `@Controller`)의 역할과 차이점을 이해하고, 각 어노테이션을 언제 사용해야 하는지 학습한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 어노테이션 없이는 모든 클래스를 XML이나 Java Config로 직접 등록해야 하며, 설정 파일이 복잡해지고 유지보수가 어려워집니다. 또한 각 계층의 역할을 명확히 구분하기 어렵습니다.

- 스프링 어노테이션을 사용하면 클래스에 어노테이션만 붙이면 자동으로 스프링 빈으로 등록되어 설정이 간소화됩니다. 각 어노테이션은 계층별 역할을 명확히 구분하여 코드의 가독성과 유지보수성을 향상시킵니다.

- `@Component`, `@Service`, `@Repository`, `@Controller`의 차이점과 각각의 특별한 기능, 그리고 언제 어떤 어노테이션을 사용해야 하는지 이해해야 합니다.

---

### **Core Concept (핵심 개념 정리)**

**스프링 어노테이션**은 클래스나 메서드에 메타데이터를 추가하여 스프링 컨테이너가 자동으로 처리할 수 있게 해주는 표식입니다.

**컴포넌트 스캔의 기본:**
- `@Component`는 모든 스프링 빈 등록 어노테이션의 부모입니다
- `@Service`, `@Repository`, `@Controller`는 모두 `@Component`를 포함합니다
- 기능적으로는 동일하지만, **의미적 구분**을 위해 계층별로 다른 어노테이션을 사용합니다

> [!tip] 핵심 포인트
> 모든 어노테이션은 기능적으로 동일하게 스프링 빈으로 등록되지만, 각 어노테이션은 계층별 역할을 명확히 구분하여 코드의 가독성과 유지보수성을 향상시킵니다.

---

## 1. @Component

### **@Component란?**

`@Component`는 스프링이 자동으로 스프링 빈으로 등록하는 가장 기본적인 어노테이션입니다.

### **특징**

- **범용 어노테이션**: 특정 계층에 속하지 않는 일반적인 컴포넌트에 사용
- **컴포넌트 스캔 대상**: `@ComponentScan`으로 자동 스캔되어 빈으로 등록
- **기본 기능**: 스프링 빈 등록만 수행 (추가 기능 없음)

### **사용 예시**

```java
@Component
public class UserValidator {
    public boolean validate(User user) {
        // 검증 로직
        return user != null && user.getName() != null;
    }
}
```

### **언제 사용하는가?**

- 특정 계층에 속하지 않는 유틸리티 클래스
- 공통 기능을 제공하는 헬퍼 클래스
- 범용 컴포넌트

---

## 2. @Service

### **@Service란?**

`@Service`는 비즈니스 로직을 담당하는 서비스 계층에 사용하는 어노테이션입니다.

### **특징**

- **의미적 구분**: 비즈니스 로직 계층임을 명확히 표현
- **기능적으로는 @Component와 동일**: 내부적으로 `@Component`를 포함
- **트랜잭션 관리**: 일반적으로 서비스 계층에서 트랜잭션을 관리

### **내부 구조**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component  // @Component를 포함
public @interface Service {
    @AliasFor(annotation = Component.class)
    String value() default "";
}
```

### **사용 예시**

```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    @Transactional
    public User createUser(String name, String email) {
        // 비즈니스 로직
        User user = new User(name, email);
        return userRepository.save(user);
    }
}
```

### **언제 사용하는가?**

- 비즈니스 로직을 처리하는 서비스 클래스
- 트랜잭션을 관리하는 계층
- 여러 Repository를 조합하여 복잡한 비즈니스 로직을 처리하는 클래스

---

## 3. @Repository

### **@Repository란?**

`@Repository`는 데이터 접근 계층(DAO, Data Access Object)에 사용하는 어노테이션입니다.

### **특징**

- **의미적 구분**: 데이터 접근 계층임을 명확히 표현
- **기능적으로는 @Component와 동일**: 내부적으로 `@Component`를 포함
- **예외 변환**: JPA의 `PersistenceException`을 스프링의 `DataAccessException`으로 자동 변환

### **내부 구조**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component  // @Component를 포함
public @interface Repository {
    @AliasFor(annotation = Component.class)
    String value() default "";
}
```

### **예외 변환 기능**

`@Repository`가 붙은 클래스는 AOP 프록시가 생성되어 예외를 자동으로 변환합니다.

**예시:**

```java
@Repository
public class UserRepository {
    public User findById(Long id) {
        // JPA 예외 발생 시
        // PersistenceException → DataAccessException으로 자동 변환
        return entityManager.find(User.class, id);
    }
}
```

**예외 변환의 장점:**
- JPA, JDBC, MyBatis 등 다양한 데이터 접근 기술을 써도 모든 예외가 `DataAccessException`으로 통일됨
- 예외 처리를 일관되게 할 수 있음
- `@Transactional`과 함께 사용할 때 트랜잭션 롤백도 안전하게 작동

### **사용 예시**

```java
@Repository
public class UserRepository {
    @PersistenceContext
    private EntityManager em;
    
    public User save(User user) {
        em.persist(user);
        return user;
    }
    
    public User findById(Long id) {
        return em.find(User.class, id);
    }
}
```

### **언제 사용하는가?**

- 데이터베이스 접근을 담당하는 Repository 클래스
- JPA, MyBatis 등을 사용하는 데이터 접근 계층
- 예외 변환이 필요한 데이터 접근 클래스

---

## 4. @Controller

### **@Controller란?**

`@Controller`는 웹 계층(프레젠테이션 계층)의 컨트롤러에 사용하는 어노테이션입니다.

### **특징**

- **의미적 구분**: 웹 계층의 컨트롤러임을 명확히 표현
- **기능적으로는 @Component와 동일**: 내부적으로 `@Component`를 포함
- **핸들러 매핑**: 스프링 MVC에서 요청을 처리하는 핸들러로 인식

### **내부 구조**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component  // @Component를 포함
public @interface Controller {
    @AliasFor(annotation = Component.class)
    String value() default "";
}
```

### **사용 예시**

```java
@Controller
@RequestMapping("/users")
public class UserController {
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping("/{id}")
    public String getUser(@PathVariable Long id, Model model) {
        User user = userService.findById(id);
        model.addAttribute("user", user);
        return "user/detail";
    }
    
    @PostMapping
    public String createUser(@ModelAttribute User user) {
        userService.createUser(user);
        return "redirect:/users";
    }
}
```

### **@RestController와의 차이**

`@RestController`는 `@Controller` + `@ResponseBody`를 합친 어노테이션입니다.

```java
// @Controller: 뷰를 반환
@Controller
public class UserController {
    @GetMapping("/users")
    public String getUsers(Model model) {
        // 뷰 이름 반환
        return "users/list";
    }
}

// @RestController: JSON/XML 등 데이터를 반환
@RestController
public class UserRestController {
    @GetMapping("/api/users")
    public List<User> getUsers() {
        // JSON 데이터 반환
        return userService.findAll();
    }
}
```

### **언제 사용하는가?**

- 웹 요청을 처리하는 컨트롤러 클래스
- 뷰를 반환하는 MVC 컨트롤러
- RESTful API를 제공하는 경우 `@RestController` 사용

---

## 5. 어노테이션 간의 관계

### **상속 관계**

모든 어노테이션은 내부적으로 `@Component`를 포함합니다.

```
@Component (부모)
    ├─ @Service
    ├─ @Repository
    └─ @Controller
```

### **기능적 동일성**

모든 어노테이션은 기능적으로 동일하게 스프링 빈으로 등록됩니다.

```java
// 모두 동일하게 스프링 빈으로 등록됨
@Component
public class ComponentClass { }

@Service
public class ServiceClass { }

@Repository
public class RepositoryClass { }

@Controller
public class ControllerClass { }
```

### **의미적 차이**

기능은 동일하지만, 각 어노테이션은 계층별 역할을 명확히 구분합니다.

| 어노테이션 | 계층 | 역할 | 특별한 기능 |
|-----------|------|------|------------|
| `@Component` | 범용 | 일반 컴포넌트 | 없음 |
| `@Service` | 서비스 계층 | 비즈니스 로직 | 없음 (의미적 구분) |
| `@Repository` | 데이터 접근 계층 | 데이터 접근 | 예외 변환 |
| `@Controller` | 웹 계층 | 요청 처리 | 핸들러 매핑 |

---

## 6. 어노테이션 비교표

### **기능 비교**

| 항목 | @Component | @Service | @Repository | @Controller |
|------|-----------|----------|-------------|-------------|
| **스프링 빈 등록** | ✅ | ✅ | ✅ | ✅ |
| **컴포넌트 스캔 대상** | ✅ | ✅ | ✅ | ✅ |
| **예외 변환** | ❌ | ❌ | ✅ | ❌ |
| **핸들러 매핑** | ❌ | ❌ | ❌ | ✅ |
| **의미적 구분** | 범용 | 서비스 계층 | 데이터 접근 계층 | 웹 계층 |

### **사용 시나리오 비교**

| 어노테이션 | 사용 시나리오 | 예시 |
|-----------|--------------|------|
| `@Component` | 범용 컴포넌트, 유틸리티 클래스 | `UserValidator`, `EmailSender` |
| `@Service` | 비즈니스 로직 처리 | `UserService`, `OrderService` |
| `@Repository` | 데이터베이스 접근 | `UserRepository`, `OrderRepository` |
| `@Controller` | 웹 요청 처리 | `UserController`, `OrderController` |

---

## 7. 실제 사용 예시

### **전체 구조 예시**

```java
// 1. Repository 계층
@Repository
public class UserRepository {
    @PersistenceContext
    private EntityManager em;
    
    public User findById(Long id) {
        return em.find(User.class, id);
    }
}

// 2. Service 계층
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    @Transactional
    public User findUser(Long id) {
        return userRepository.findById(id);
    }
}

// 3. Controller 계층
@Controller
@RequestMapping("/users")
public class UserController {
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping("/{id}")
    public String getUser(@PathVariable Long id, Model model) {
        User user = userService.findUser(id);
        model.addAttribute("user", user);
        return "user/detail";
    }
}

// 4. 범용 컴포넌트
@Component
public class EmailValidator {
    public boolean isValid(String email) {
        return email != null && email.contains("@");
    }
}
```

### **잘못된 사용 예시**

```java
// ❌ 잘못된 사용: Repository 계층에 @Service 사용
@Service
public class UserRepository {
    // 의미적으로 혼란스러움
}

// ❌ 잘못된 사용: Service 계층에 @Repository 사용
@Repository
public class UserService {
    // 예외 변환이 불필요한데 @Repository 사용
}

// ✅ 올바른 사용: 각 계층에 맞는 어노테이션 사용
@Repository
public class UserRepository { }

@Service
public class UserService { }
```

---

## 8. 컴포넌트 스캔 동작 원리

### **컴포넌트 스캔이란?**

`@ComponentScan`은 지정한 패키지와 그 하위 패키지에서 `@Component`가 붙은 클래스를 찾아서 스프링 빈으로 자동 등록합니다.

### **스캔 대상**

- `@Component`를 포함한 모든 어노테이션
  - `@Component`
  - `@Service`
  - `@Repository`
  - `@Controller`
  - `@RestController`
  - `@Configuration`

### **스캔 설정**

```java
@Configuration
@ComponentScan(
    basePackages = "com.example",  // 스캔할 패키지
    excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AppConfig {
}
```

또는

```java
@SpringBootApplication  // @ComponentScan 포함
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

---

## 9. 주의사항

### **1. 어노테이션 선택의 중요성**

기능적으로는 동일하지만, 의미적 구분을 위해 올바른 어노테이션을 사용해야 합니다.

```java
// ✅ 좋은 예: 계층별로 명확히 구분
@Repository
public class UserRepository { }

@Service
public class UserService { }

@Controller
public class UserController { }

// ❌ 나쁜 예: 모든 곳에 @Component 사용
@Component
public class UserRepository { }  // 의미가 불명확
@Component
public class UserService { }     // 의미가 불명확
```

### **2. @Repository의 예외 변환**

`@Repository`는 예외 변환 기능이 있으므로, 데이터 접근 계층에만 사용해야 합니다.

```java
// ✅ 올바른 사용: 데이터 접근 계층
@Repository
public class UserRepository {
    // JPA 예외가 자동으로 DataAccessException으로 변환됨
}

// ❌ 잘못된 사용: 서비스 계층에 @Repository 사용
@Repository
public class UserService {
    // 예외 변환이 불필요한데 사용
}
```

### **3. 중복 등록 방지**

같은 클래스에 여러 어노테이션을 붙이면 안 됩니다.

```java
// ❌ 잘못된 사용: 중복 어노테이션
@Component
@Service
public class UserService { }  // 불필요한 중복

// ✅ 올바른 사용: 하나만 사용
@Service
public class UserService { }
```

---

## 요약

- **@Component**: 범용 컴포넌트에 사용하는 기본 어노테이션으로, 모든 스프링 빈 등록 어노테이션의 부모입니다.
- **@Service**: 비즈니스 로직을 담당하는 서비스 계층에 사용하며, 의미적 구분을 위한 어노테이션입니다.
- **@Repository**: 데이터 접근 계층에 사용하며, JPA 예외를 스프링 예외로 자동 변환하는 기능이 있습니다.
- **@Controller**: 웹 계층의 컨트롤러에 사용하며, 스프링 MVC에서 요청을 처리하는 핸들러로 인식됩니다.
- **기능적 동일성**: 모든 어노테이션은 기능적으로 동일하게 스프링 빈으로 등록되지만, 의미적 구분을 위해 계층별로 다른 어노테이션을 사용합니다.
- **컴포넌트 스캔**: `@ComponentScan`으로 지정한 패키지에서 자동으로 스프링 빈을 찾아 등록합니다.

---

## 참고 자료

- [Spring Framework - Component Scanning](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-annotation-config)
- [Spring Framework - Stereotype Annotations](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-stereotype-annotations)
- [Spring MVC - @Controller and @RestController](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-controller)
