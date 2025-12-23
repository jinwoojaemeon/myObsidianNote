#spring #framework #java #mvc #ioc #di #면접 #개념정리

**관련 개념:** [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[SOLID 원칙 (객체지향 설계 원칙)|SOLID 원칙]] [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]

> [!note] 이어서 읽기
> 스프링 프레임워크를 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**스프링 프레임워크(Spring Framework)**는 엔터프라이즈급 자바 애플리케이션 개발을 위한 통합 프레임워크이다. 복잡한 인프라 설정과 공통 관심사를 프레임워크가 처리하여 개발자가 비즈니스 로직에만 집중할 수 있게 해준다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 스프링 없이 개발하면 각 개발자가 서블릿 설정, 객체 생성, 의존성 관리, 트랜잭션 처리 등을 직접 구현해야 하며, 코드가 중복되고 일관성이 떨어집니다. 대규모 프로젝트에서 각 개발자의 코딩 스타일이 달라 유지보수가 어렵고, 인프라 설정에 시간을 많이 소비하게 됩니다.

- 스프링은 인프라 설정과 공통 관심사를 프레임워크가 처리하여 모든 개발자가 동일한 수준의 코드를 작성할 수 있게 합니다. IoC/DI를 통해 객체 생명주기를 관리하고, AOP로 공통 로직을 분리하며, 통합된 생태계를 제공하여 라이브러리 간 충돌 없이 안정적으로 개발할 수 있습니다.

- 스프링의 핵심 개념(IoC, DI, AOP), MVC 패턴, 서블릿과의 관계, 그리고 엔터프라이즈 시스템에서 스프링이 해결하는 문제들을 이해해야 합니다.

---

## 1. 스프링 프레임워크 탄생 배경

### **스프링 없이 개발했던 방식**

스프링이 등장하기 전에는 각 개발자가 모든 것을 직접 구현해야 했습니다.

#### **1. 서블릿 기반 개발의 문제점**

```java
// 스프링 없이: 각 요청마다 서블릿을 직접 만들어야 함
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // 1. 요청 파라미터 파싱
        String userId = request.getParameter("userId");
        
        // 2. 객체 생성 (의존성 직접 관리)
        UserRepository userRepository = new UserRepository();
        UserService userService = new UserService(userRepository);
        
        // 3. 비즈니스 로직 실행
        User user = userService.findById(userId);
        
        // 4. 응답 생성
        response.setContentType("application/json");
        PrintWriter out = response.getWriter();
        out.print("{\"id\":\"" + user.getId() + "\",\"name\":\"" + user.getName() + "\"}");
    }
}

// 주문 서블릿도 똑같이 반복...
public class OrderServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // 또 다시 파라미터 파싱, 객체 생성, 응답 생성...
    }
}
```

**문제점:**
- 각 서블릿마다 중복 코드가 많음
- 객체 생성과 의존성 관리를 직접 해야 함
- 공통 로직(인증, 로깅 등)을 각 서블릿에 반복 작성
- 코드 일관성이 떨어짐

#### **2. 객체 생성과 의존성 관리의 문제**

```java
// 스프링 없이: 객체 생성을 직접 관리
public class UserService {
    private UserRepository userRepository;
    
    public UserService() {
        // 의존성을 직접 생성
        this.userRepository = new UserRepository();
        // 만약 UserRepository가 다른 의존성을 필요로 하면?
        // this.userRepository = new UserRepository(new DataSource());
        // 계속 중첩되어 복잡해짐
    }
}

// 다른 서비스에서도 똑같이 반복
public class OrderService {
    private OrderRepository orderRepository;
    
    public OrderService() {
        this.orderRepository = new OrderRepository();
        // 또 다시 직접 생성...
    }
}
```

**문제점:**
- 객체 생성을 각 클래스에서 직접 관리
- 의존성이 복잡해질수록 생성 코드가 중첩됨
- 테스트 시 Mock 객체 주입이 어려움
- 객체 간 결합도가 높아짐

#### **3. 공통 관심사의 중복**

```java
// 스프링 없이: 각 서블릿에 공통 로직 반복
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
        // 로깅
        System.out.println("요청: " + request.getRequestURI());
        
        // 인증 체크
        String token = request.getHeader("Authorization");
        if (token == null || !isValidToken(token)) {
            response.setStatus(401);
            return;
        }
        
        // 비즈니스 로직
        // ...
    }
}

public class OrderServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
        // 또 다시 로깅
        System.out.println("요청: " + request.getRequestURI());
        
        // 또 다시 인증 체크
        String token = request.getHeader("Authorization");
        if (token == null || !isValidToken(token)) {
            response.setStatus(401);
            return;
        }
        
        // 비즈니스 로직
        // ...
    }
}
```

**문제점:**
- 로깅, 인증, 트랜잭션 등 공통 로직이 모든 서블릿에 중복
- 공통 로직 변경 시 모든 서블릿 수정 필요
- 코드 가독성 저하

---

## 2. 스프링 프레임워크란?

### **스프링의 정의**

**스프링 프레임워크(Spring Framework)**는 엔터프라이즈급 자바 애플리케이션 개발을 위한 통합 프레임워크로, 다음과 같은 목표를 가집니다:

1. **관심사의 분리**: 인프라 설정과 비즈니스 로직을 분리
2. **코드 레벨의 동등화**: 모든 개발자가 동일한 수준의 코드 작성
3. **통합 생태계**: 데이터베이스, 보안, 배치 등 기업용 기능을 통합 제공

### **스프링의 핵심 철학**

> [!tip] 핵심 포인트
> 스프링은 개발자가 **"인프라 설정"이 아닌 "비즈니스 로직"**에만 집중할 수 있게 만드는 것이 목표입니다. 이를 통해 모든 개발자가 동일한 수준의 코드를 작성할 수 있습니다.

---

## 3. 스프링의 핵심 개념

### **3.1 IoC (Inversion of Control, 제어의 역전)**

**IoC**는 객체의 생성과 생명주기를 개발자가 아닌 **스프링 컨테이너**가 관리하는 것을 의미합니다.

**스프링 없이:**
```java
// 개발자가 직접 객체 생성
UserService userService = new UserService();
userService.doSomething();
```

**스프링 사용:**
```java
// 스프링 컨테이너가 객체 생성 및 관리
@Autowired
private UserService userService;  // 스프링이 자동으로 주입

// 또는
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
UserService userService = context.getBean(UserService.class);
```

**장점:**
- 객체 생성을 개발자가 신경 쓸 필요 없음
- 객체 간 결합도 감소
- 테스트 용이성 향상

### **3.2 DI (Dependency Injection, 의존성 주입)**

**DI**는 객체가 필요로 하는 의존성을 외부에서 주입받는 것을 의미합니다.

**스프링 없이:**
```java
public class UserService {
    private UserRepository userRepository;
    
    public UserService() {
        // 의존성을 직접 생성 (강한 결합)
        this.userRepository = new UserRepository();
    }
}
```

**스프링 사용:**
```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    // 생성자를 통한 의존성 주입 (약한 결합)
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;  // 스프링이 주입
    }
}
```

**장점:**
- 객체 간 결합도 감소
- 테스트 시 Mock 객체 주입 용이
- 유연한 설계 가능

### **3.3 AOP (Aspect-Oriented Programming, 관점 지향 프로그래밍)**

**AOP**는 공통 관심사(로깅, 인증, 트랜잭션 등)를 비즈니스 로직과 분리하는 기법입니다.

**스프링 없이:**
```java
public class UserService {
    public User findUser(String id) {
        // 로깅
        System.out.println("findUser 호출: " + id);
        
        // 트랜잭션 시작
        beginTransaction();
        
        try {
            // 비즈니스 로직
            User user = userRepository.findById(id);
            
            // 트랜잭션 커밋
            commitTransaction();
            return user;
        } catch (Exception e) {
            // 트랜잭션 롤백
            rollbackTransaction();
            throw e;
        } finally {
            // 로깅
            System.out.println("findUser 완료");
        }
    }
}
```

**스프링 사용:**
```java
@Service
public class UserService {
    @Transactional  // AOP로 트랜잭션 처리
    public User findUser(String id) {
        // 비즈니스 로직에만 집중
        return userRepository.findById(id);
    }
}

// 공통 관심사는 별도로 분리
@Aspect
@Component
public class LoggingAspect {
    @Around("execution(* com.example.service.*.*(..))")
    public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("메서드 호출: " + joinPoint.getSignature());
        return joinPoint.proceed();
    }
}
```

**장점:**
- 비즈니스 로직과 공통 관심사 분리
- 코드 중복 제거
- 유지보수 용이

---

## 4. 스프링 MVC의 동작 원리

### **4.1 서블릿과 서블릿 컨테이너**

**서블릿(Servlet)**은 HTTP 요청 정보를 객체로 변환하여 개발자가 로직에만 집중하게 돕는 프로그램입니다.

**서블릿 컨테이너**는 서블릿의 생명주기(생성, 실행, 소멸)를 관리하며, 멀티스레드 처리를 담당합니다.

### **4.2 프론트 컨트롤러 패턴**

과거에는 요청마다 각각의 서블릿을 만들어야 했으나, 스프링은 **디스패처 서블릿(Dispatcher Servlet)**이라는 단일 진입점을 두어 모든 요청을 중앙에서 관리합니다.

**스프링 없이:**
```
요청 → UserServlet (각각의 서블릿)
요청 → OrderServlet
요청 → ProductServlet
```

**스프링 사용:**
```
모든 요청 → Dispatcher Servlet (단일 진입점)
         ↓
    적절한 컨트롤러로 위임
```

### **4.3 스프링 MVC 요청 처리 흐름**

```
1. 클라이언트 요청
   ↓
2. Dispatcher Servlet (모든 요청의 중앙 컨트롤러)
   ↓
3. Handler Mapping (URL을 보고 어떤 컨트롤러가 처리할지 결정)
   ↓
4. Handler Adapter (컨트롤러 메서드를 실제로 호출)
   ↓
5. Controller (개발자가 작성한 비즈니스 로직)
   ↓
6. View Resolver (결과를 어떤 화면으로 보여줄지 결정)
   ↓
7. 클라이언트 응답
```

### **4.4 스프링 MVC 주요 구성 요소**

| 구성 요소 | 역할 |
|---------|------|
| **Dispatcher Servlet** | 모든 요청의 중앙 컨트롤러. 적절한 핸들러에게 작업을 위임 |
| **Handler Mapping** | URL 요청을 보고 어떤 컨트롤러(Handler)가 처리할지 결정 |
| **Handler Adapter** | 결정된 컨트롤러의 메서드를 실제로 호출할 수 있게 연결 |
| **Controller** | 개발자가 실제 비즈니스 로직을 작성하는 곳 |
| **View Resolver** | 실행 결과를 어떤 화면(Thymeleaf, JSP 등)으로 보여줄지 결정 |

**예시:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/{id}")
    public User getUser(@PathVariable String id) {
        // 비즈니스 로직에만 집중
        return userService.findById(id);
    }
}
```

---

## 5. 스프링을 사용하면 좋은 이유

### **5.1 코드 레벨의 동등화**

스프링을 사용하면 모든 개발자가 동일한 수준의 코드를 작성할 수 있습니다.

**스프링 없이:**
```java
// 개발자 A의 스타일
public class UserService {
    private UserRepository repo = new UserRepository();
    // ...
}

// 개발자 B의 스타일
public class OrderService {
    private OrderRepository repository;
    public OrderService() {
        this.repository = new OrderRepository();
    }
    // ...
}

// 개발자 C의 스타일
public class ProductService {
    private ProductRepository productRepository;
    // 생성자? 필드 초기화? 스타일이 모두 다름
}
```

**스프링 사용:**
```java
// 모든 개발자가 동일한 패턴 사용
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

@Service
public class OrderService {
    private final OrderRepository orderRepository;
    
    public OrderService(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }
}
```

**장점:**
- 코드 일관성 향상
- 유지보수 용이
- 신입 개발자도 빠르게 적응

### **5.2 통합 생태계 제공**

스프링은 데이터베이스, 보안, 배치 등 기업용 기능을 통합 제공합니다.

**스프링 없이:**
```java
// 각 라이브러리를 직접 통합해야 함
// Hibernate + Spring Security + Quartz + ...
// 버전 충돌, 설정 복잡, 통합 어려움
```

**스프링 사용:**
```java
// Spring Boot Starter로 간단하게 통합
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.springframework.boot:spring-boot-starter-batch'
    // 버전 관리, 설정 자동화 모두 스프링이 처리
}
```

**장점:**
- 라이브러리 간 충돌 없음
- 버전 관리 자동화
- 설정 간소화

### **5.3 검증된 아키텍처**

스프링은 오랜 기간 검증된 아키텍처와 패턴을 제공합니다.

- **MVC 패턴**: 관심사 분리
- **Repository 패턴**: 데이터 접근 추상화
- **Service 계층**: 비즈니스 로직 캡슐화

### **5.4 엔터프라이즈 시스템에 최적화**

**멀티스레드 기반의 동기 방식**을 사용하여 복잡한 비즈니스 연산이 필요한 시스템에 적합합니다.

| 구분 | Spring MVC (Java) | Node.js (JavaScript) |
|------|------------------|---------------------|
| **모델** | 멀티스레드 / 동기(Blocking) | 싱글스레드 / 비동기(Non-blocking) |
| **강점** | 복잡한 연산, 안정적인 통합 생태계 | 대규모 I/O 처리 (채팅, 스트리밍) |
| **적합한 시스템** | 금융, 결제, ERP 등 복잡한 비즈니스 로직 | 실시간 채팅, 스트리밍 등 I/O 집중 |

### **5.5 인력 및 인프라의 우위**

- 오랜 기간 시장을 주도해왔기에 유지보수를 위한 고급 엔지니어를 확보하기에 유리
- 풍부한 문서와 커뮤니티 지원
- 기업 환경에서 널리 사용되어 인력 확보 용이

---

## 6. 스프링의 지향점: 관심사의 분리

### **개발자가 집중해야 할 영역**

스프링을 사용하면 개발자는 **노란색 영역(비즈니스 로직)**에만 집중하면 됩니다:

```
┌─────────────────────────────────────┐
│  스프링 프레임워크 (인프라)              │
│  - 서블릿 관리                        │
│  - 객체 생성/주입                     │
│  - 트랜잭션 관리                      │
│  - 공통 관심사 처리                   │
└─────────────────────────────────────┘
           ↑
           │
┌─────────────────────────────────────┐
│  개발자가 작성하는 코드 (비즈니스 로직)   │
│  - Controller                        │
│  - Service                          │
│  - Repository                       │
└─────────────────────────────────────┘
```

### **추상화된 인프라**

복잡한 멀티스레드 관리나 HTTP 파싱은 스프링에 맡기고, 개발자는 비즈니스 로직 구현에만 집중할 수 있습니다.

---

## 7. 스프링 vs 순수 서블릿 비교

### **요청 처리 비교**

**순수 서블릿:**
```java
public class UserServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // 1. 파라미터 파싱
        String userId = request.getParameter("userId");
        
        // 2. 객체 생성
        UserRepository repository = new UserRepository();
        UserService service = new UserService(repository);
        
        // 3. 비즈니스 로직
        User user = service.findById(userId);
        
        // 4. 응답 생성
        response.setContentType("application/json");
        PrintWriter out = response.getWriter();
        out.print("{\"id\":\"" + user.getId() + "\"}");
    }
}
```

**스프링:**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/{id}")
    public User getUser(@PathVariable String id) {
        // 비즈니스 로직에만 집중
        return userService.findById(id);
    }
}
```

**차이점:**
- 순수 서블릿: 파라미터 파싱, 객체 생성, 응답 생성 모두 직접 처리
- 스프링: 비즈니스 로직만 작성하면 나머지는 프레임워크가 처리

---

## 요약

- **스프링 프레임워크**는 엔터프라이즈급 자바 애플리케이션 개발을 위한 통합 프레임워크입니다.
- **IoC/DI**: 객체의 생성과 의존성 관리를 스프링 컨테이너가 담당하여 결합도를 낮춥니다.
- **AOP**: 공통 관심사를 비즈니스 로직과 분리하여 코드 중복을 제거합니다.
- **MVC 패턴**: 디스패처 서블릿을 통한 단일 진입점으로 모든 요청을 중앙에서 관리합니다.
- **코드 레벨의 동등화**: 모든 개발자가 동일한 수준의 코드를 작성할 수 있게 합니다.
- **통합 생태계**: 데이터베이스, 보안, 배치 등 기업용 기능을 통합 제공하여 라이브러리 간 충돌을 방지합니다.
- **관심사의 분리**: 개발자는 비즈니스 로직에만 집중하고, 인프라 설정은 스프링에 맡깁니다.

---

## 참고 자료

- [Spring Framework 공식 문서](https://spring.io/projects/spring-framework)
- [Spring MVC Documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html)
- [Spring Boot 공식 문서](https://spring.io/projects/spring-boot)
