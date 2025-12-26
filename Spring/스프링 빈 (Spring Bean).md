#spring #bean #ioc #singleton #lifecycle #면접 #개념정리

**관련 개념:** [[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]] [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]

> [!note] 이어서 읽기
> 스프링 빈을 이해하려면 **[[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**스프링 빈(Spring Bean)** 은 스프링 IoC(제어의 역전) 컨테이너가 생성, 의존성 주입, 설정, 소멸까지 모든 생명주기를 관리하는 자바 객체이다. 개발자는 객체를 직접 생성하지 않고 스프링 컨테이너에 맡겨 비즈니스 로직에 집중할 수 있다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 객체를 직접 `new`로 생성하면 의존 관계가 복잡한 애플리케이션에서 구현체가 바뀔 때마다 코드를 수정해야 하고, 수많은 객체의 생성 순서와 주입 관계를 일일이 파악해야 하며, 테스트 시 Mock 객체를 주입하기 어렵습니다. 또한 매 요청마다 객체를 새로 생성하여 메모리 효율이 떨어집니다.

- 스프링 빈으로 관리하면 의존 관계 변경 시 설정만 변경하여 대응할 수 있고, 복잡한 의존성 관리를 스프링이 자동으로 처리하며, 테스트 시 Mock 객체 주입이 용이합니다. 또한 싱글톤으로 관리하여 메모리 효율을 극대화할 수 있습니다.

- 스프링 빈의 정의, 등록 방법, 싱글톤 레지스트리, 생명주기, 그리고 주의사항을 이해해야 합니다.

---

## 1. 스프링 빈(Spring Bean)의 본질 정의

### **스프링 빈이란?**

**스프링 빈(Spring Bean)** 은 스프링 IoC(제어의 역전) 컨테이너가 생성, 의존성 주입, 설정, 소멸까지 모든 생명주기를 관리하는 자바 객체입니다.

### **핵심 원칙 (IoC)**

**제어의 역전(Inversion of Control)**: 객체의 제어권이 개발자가 아닌 프레임워크(외부)에 있습니다.

- 개발자는 **"무엇이 필요한지"** 선언만 함
- 실제 **"언제, 어떻게 만들지"** 는 스프링이 결정

**예시:**

```java
// 개발자가 직접 생성 (제어권: 개발자)
UserService userService = new UserService();

// 스프링 빈으로 관리 (제어권: 스프링)
@Autowired
private UserService userService;  // 스프링이 생성하고 주입
```

> [!tip] 핵심 포인트
> 스프링 빈을 사용한다는 것은 객체 관리라는 무거운 짐을 프레임워크에 넘기고, 개발자는 사용자의 요구사항을 해결하는 비즈니스 로직 구현에만 온전히 집중하겠다는 의미입니다.

---

## 2. 왜 스프링 빈으로 관리해야 하는가?

### **객체를 직접 new로 생성하지 않는 이유**

객체를 직접 `new`로 생성하지 않고 스프링에게 맡기는 이유는 명확합니다.

### **2.1 유지보수와 유연성**

의존 관계가 복잡한 애플리케이션에서 특정 구현체가 바뀌어도 비즈니스 로직 수정 없이 설정만 변경하여 대응할 수 있습니다.

**직접 생성 시:**

```java
public class UserService {
    // 구현체에 직접 의존
    private UserRepository userRepository = new JpaUserRepository();
    
    public User findUser(String id) {
        return userRepository.findById(id);
    }
}

// 문제: 다른 구현체로 변경하려면 코드 수정 필요
// private UserRepository userRepository = new MyBatisUserRepository();
```

**스프링 빈 사용 시:**

```java
@Service
public class UserService {
    // 인터페이스에 의존
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;  // 스프링이 주입
    }
}

// 설정만 변경하면 구현체 교체 가능
@Repository
public class JpaUserRepository implements UserRepository { }

@Repository
public class MyBatisUserRepository implements UserRepository { }
```

**장점:**
- ✅ 구현체 변경 시 코드 수정 불필요
- ✅ 설정만 변경하여 대응 가능

### **2.2 복잡한 의존성 관리**

수많은 객체가 얽혀 있는 구조에서 개발자가 일일이 생성 순서와 주입 관계를 파악할 필요가 없습니다.

**직접 생성 시:**

```java
// 복잡한 의존성 관계를 직접 관리
DataSource dataSource = new HikariDataSource();
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
UserRepository userRepository = new UserRepository(jdbcTemplate);
UserService userService = new UserService(userRepository);
OrderService orderService = new OrderService(userService, orderRepository);
// ... 계속 복잡해짐
```

**스프링 빈 사용 시:**

```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

// 스프링이 자동으로 의존성 주입
// 개발자는 생성 순서를 신경 쓸 필요 없음
```

**장점:**
- ✅ 생성 순서 자동 관리
- ✅ 의존성 주입 자동 처리
- ✅ 복잡한 의존성 관계를 스프링이 해결

### **2.3 테스트 용이성**

실제 객체 대신 가짜 객체(Mock)를 주입하기 쉬워져 단위 테스트가 간편해집니다.

**직접 생성 시:**

```java
public class UserService {
    private UserRepository userRepository = new UserRepository();
    // 실제 DB에 접근해야 함 → 테스트 어려움
}
```

**스프링 빈 사용 시:**

```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}

// 테스트 코드
@Test
void testFindUser() {
    // Mock 객체 주입 가능
    UserRepository mockRepository = mock(UserRepository.class);
    UserService userService = new UserService(mockRepository);
    
    // 테스트 진행
    when(mockRepository.findById("123")).thenReturn(new User("123", "홍길동"));
    User user = userService.findUser("123");
    
    assertThat(user.getName()).isEqualTo("홍길동");
}
```

**장점:**
- ✅ Mock 객체 주입 용이
- ✅ 실제 DB 없이 테스트 가능
- ✅ 단위 테스트 간편

### **2.4 성능 최적화**

매 요청마다 객체를 새로 만들지 않고 하나를 공유(싱글톤)함으로써 메모리 효율을 극대화합니다.

**직접 생성 시:**

```java
// 매 요청마다 새로운 객체 생성
public void handleRequest() {
    UserService userService = new UserService();  // 매번 새로 생성
    // 메모리 낭비
}
```

**스프링 빈 사용 시:**

```java
// 싱글톤으로 하나의 인스턴스만 생성하여 공유
@Service  // 기본적으로 싱글톤
public class UserService {
    // 애플리케이션 전체에서 하나의 인스턴스만 사용
}
```

**장점:**
- ✅ 메모리 효율 극대화
- ✅ 객체 생성 비용 절감
- ✅ 애플리케이션 성능 향상

---

## 3. 스프링 빈의 등록 및 관리 메커니즘

### **3.1 빈 등록 방법**

#### **1. 컴포넌트 스캔 (권장)**

`@Component`, `@Service`, `@Controller`, `@Repository` 등을 붙여 자동 등록합니다.

```java
@Service
public class UserService {
    // 자동으로 스프링 빈으로 등록됨
}

@Repository
public class UserRepository {
    // 자동으로 스프링 빈으로 등록됨
}

@Controller
public class UserController {
    // 자동으로 스프링 빈으로 등록됨
}
```

**특징:**
- ✅ 가장 간편한 방법
- ✅ `@ComponentScan`으로 자동 스캔
- ✅ 실무에서 가장 많이 사용

#### **2. 자바 설정 파일**

`@Configuration` 클래스 내에서 `@Bean` 메서드로 수동 등록합니다.

```java
@Configuration
public class AppConfig {
    
    @Bean
    public UserService userService() {
        return new UserService(userRepository());
    }
    
    @Bean
    public UserRepository userRepository() {
        return new UserRepository();
    }
}
```

**특징:**
- ✅ 외부 라이브러리를 빈으로 등록할 때 유용
- ✅ 설정을 명시적으로 관리 가능

#### **3. XML 설정**

과거 방식이나 외부 라이브러리 적용 시 드물게 사용합니다.

```xml
<beans>
    <bean id="userService" class="com.example.UserService">
        <constructor-arg ref="userRepository"/>
    </bean>
    
    <bean id="userRepository" class="com.example.UserRepository"/>
</beans>
```

**특징:**
- ⚠️ 현재는 거의 사용하지 않음
- ⚠️ 레거시 시스템에서만 사용

### **3.2 싱글톤 레지스트리 (Singleton Registry)**

스프링은 일반적인 자바의 싱글톤 패턴이 가진 단점을 해결하기 위해 **'싱글톤 레지스트리'** 방식을 사용합니다.

#### **일반 싱글톤 패턴의 문제점**

```java
public class Singleton {
    private static final Singleton instance = new Singleton();
    
    private Singleton() {}  // private 생성자
    
    public static Singleton getInstance() {
        return instance;
    }
}
```

**문제점:**
- ❌ 상속 불가 (private 생성자)
- ❌ 테스트 어려움 (인스턴스 고정)
- ❌ 객체 지향적 설계 어려움

#### **스프링의 싱글톤 레지스트리**

스프링은 **애플리케이션 수준의 싱글톤**이 아니라, **컨테이너 내에서 ID당 하나의 인스턴스를 보장**합니다.

**특징:**
- ✅ 평범한 자바 클래스를 싱글톤으로 활용 가능
- ✅ 객체 지향적 설계(다형성)를 유지
- ✅ 테스트 용이 (Mock 객체 주입 가능)

**예시:**

```java
@Service  // 평범한 클래스를 싱글톤으로 관리
public class UserService {
    // 스프링이 하나의 인스턴스만 생성하여 공유
}

// 컨테이너 내에서 ID당 하나의 인스턴스
ApplicationContext context = ...;
UserService bean1 = context.getBean(UserService.class);
UserService bean2 = context.getBean(UserService.class);

System.out.println(bean1 == bean2);  // true (같은 인스턴스)
```

---

## 4. 빈의 생명주기 (Life Cycle)

빈은 컨테이너와 함께 다음과 같은 단계를 거칩니다.

### **생명주기 단계**

| 단계 | 설명 |
|------|------|
| **1. 스프링 컨테이너 생성** | 설정 정보(AppConfig 등)를 읽어 컨테이너 준비 |
| **2. 빈 생성** | 빈 데피니션(설계도)을 바탕으로 객체 인스턴스화 |
| **3. 의존 관계 주입** | `@Autowired` 등을 통해 필요한 의존 객체 연결 |
| **4. 초기화 콜백** | 객체 생성이 완료된 후 필요한 설정 작업 수행 |
| **5. 사용** | 애플리케이션에서 실제 비즈니스 로직 수행 |
| **6. 소멸 전 콜백** | 컨테이너 종료 직전 리소스 반납 등 정리 작업 |
| **7. 스프링 종료** | 빈 및 컨테이너 소멸 |

### **생명주기 예시**

```java
@Component
public class NetworkClient implements InitializingBean, DisposableBean {
    
    private String url;
    
    public NetworkClient() {
        System.out.println("1. 생성자 호출");
    }
    
    public void setUrl(String url) {
        this.url = url;
        System.out.println("2. setUrl 호출: " + url);
    }
    
    // 초기화 콜백
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("3. 초기화 콜백: connect()");
        connect();
    }
    
    public void connect() {
        System.out.println("connect: " + url);
    }
    
    public void call(String message) {
        System.out.println("4. 사용: call: " + message);
    }
    
    // 소멸 전 콜백
    @Override
    public void destroy() throws Exception {
        System.out.println("5. 소멸 전 콜백: disconnect()");
        disconnect();
    }
    
    public void disconnect() {
        System.out.println("disconnect: " + url);
    }
}
```

**실행 순서:**
```
1. 생성자 호출
2. setUrl 호출: http://hello-spring.dev
3. 초기화 콜백: connect()
   connect: http://hello-spring.dev
4. 사용: call: 초기화 연결 메시지
5. 소멸 전 콜백: disconnect()
   disconnect: http://hello-spring.dev
```

### **초기화 콜백 방법**

#### **1. InitializingBean 인터페이스**

```java
@Component
public class NetworkClient implements InitializingBean {
    @Override
    public void afterPropertiesSet() throws Exception {
        // 초기화 작업
    }
}
```

#### **2. @PostConstruct 어노테이션 (권장)**

```java
@Component
public class NetworkClient {
    @PostConstruct
    public void init() {
        // 초기화 작업
    }
}
```

#### **3. @Bean(initMethod) 설정**

```java
@Configuration
public class AppConfig {
    @Bean(initMethod = "init")
    public NetworkClient networkClient() {
        return new NetworkClient();
    }
}
```

### **소멸 전 콜백 방법**

#### **1. DisposableBean 인터페이스**

```java
@Component
public class NetworkClient implements DisposableBean {
    @Override
    public void destroy() throws Exception {
        // 소멸 전 작업
    }
}
```

#### **2. @PreDestroy 어노테이션 (권장)**

```java
@Component
public class NetworkClient {
    @PreDestroy
    public void close() {
        // 소멸 전 작업
    }
}
```

#### **3. @Bean(destroyMethod) 설정**

```java
@Configuration
public class AppConfig {
    @Bean(destroyMethod = "close")
    public NetworkClient networkClient() {
        return new NetworkClient();
    }
}
```

---

## 5. 주의사항 및 심화 개념

### **5.1 무상태(Stateless) 설계**

싱글톤 빈은 여러 스레드가 공유하므로, 특정 사용자에게 종속적인 상태값을 필드에 가지면 안 됩니다.

**❌ 잘못된 예시:**

```java
@Service
public class UserService {
    private String userName;  // ❌ 위험! 여러 스레드가 공유
    
    public void setUserName(String name) {
        this.userName = name;  // 스레드 간 충돌 발생 가능
    }
}
```

**✅ 올바른 예시:**

```java
@Service
public class UserService {
    // 상태를 필드에 저장하지 않음
    public void processUser(String userName) {
        // 파라미터로 받아서 사용
        // ...
    }
}
```

### **5.2 빈 스코프(Scope)**

기본은 싱글톤이지만, 매번 새로운 객체가 필요한 경우 프로토타입(Prototype) 스코프를 사용합니다.

#### **싱글톤 스코프 (기본)**

```java
@Service  // 기본적으로 싱글톤
public class UserService {
    // 애플리케이션 전체에서 하나의 인스턴스만 사용
}
```

#### **프로토타입 스코프**

```java
@Component
@Scope("prototype")
public class PrototypeBean {
    // 매번 새로운 인스턴스 생성
}
```

**특징:**
- 프로토타입 스코프는 소멸 단계를 컨테이너가 책임지지 않음
- 개발자가 직접 관리해야 함

### **5.3 충돌 해결**

동일 타입의 빈이 여러 개일 때 `@Primary`로 우선순위를 주거나 `@Qualifier`로 이름을 특정해야 합니다.

#### **@Primary 사용**

```java
@Repository
@Primary  // 우선순위 부여
public class JpaUserRepository implements UserRepository {
}

@Repository
public class MyBatisUserRepository implements UserRepository {
}

@Service
public class UserService {
    // @Primary가 붙은 JpaUserRepository가 주입됨
    private final UserRepository userRepository;
}
```

#### **@Qualifier 사용**

```java
@Repository("jpa")
public class JpaUserRepository implements UserRepository {
}

@Repository("mybatis")
public class MyBatisUserRepository implements UserRepository {
}

@Service
public class UserService {
    @Autowired
    @Qualifier("jpa")  // 특정 빈 이름 지정
    private UserRepository userRepository;
}
```

### **5.4 빈 조회 방법**

#### **타입으로 조회**

```java
ApplicationContext ac = ...;
UserService userService = ac.getBean(UserService.class);
```

#### **이름으로 조회**

```java
ApplicationContext ac = ...;
UserService userService = (UserService) ac.getBean("userService");
```

#### **타입으로 조회 시 같은 타입이 둘 이상**

```java
// 예외 발생: NoUniqueBeanDefinitionException
UserRepository repository = ac.getBean(UserRepository.class);

// 해결: @Primary 또는 @Qualifier 사용
```

---

## 요약

- **스프링 빈**은 스프링 IoC 컨테이너가 생성, 의존성 주입, 설정, 소멸까지 모든 생명주기를 관리하는 자바 객체입니다.
- **왜 사용하는가**: 유지보수와 유연성, 복잡한 의존성 관리, 테스트 용이성, 성능 최적화
- **빈 등록 방법**: 컴포넌트 스캔(권장), 자바 설정 파일, XML 설정
- **싱글톤 레지스트리**: 컨테이너 내에서 ID당 하나의 인스턴스를 보장하여 객체 지향적 설계를 유지
- **생명주기**: 컨테이너 생성 → 빈 생성 → 의존 관계 주입 → 초기화 콜백 → 사용 → 소멸 전 콜백 → 종료
- **주의사항**: 무상태 설계, 빈 스코프, 충돌 해결(@Primary, @Qualifier)

---

## 참고 자료

- [Spring Framework - Bean Definition](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-definition)
- [Spring Framework - Bean Scopes](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes)
- [Spring Framework - Bean Lifecycle](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-lifecycle)
