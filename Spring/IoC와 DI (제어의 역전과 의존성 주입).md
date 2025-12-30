#spring #ioc #di #dependency-injection #inversion-of-control #면접 #개념정리

**관련 개념:** [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]] [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[SOLID 원칙 (객체지향 설계 원칙)|SOLID 원칙]]

> [!note] 이어서 읽기
> IoC와 DI를 이해하려면 **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**IoC(Inversion of Control, 제어의 역전)** 와 **DI(Dependency Injection, 의존성 주입)** 는 스프링 프레임워크의 핵심 원리이다. 객체의 생성과 의존성 관리를 개발자가 아닌 프레임워크가 담당하여 결합도를 낮추고 유연한 설계를 가능하게 한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)** 

- IoC와 DI를 통해 객체 생성과 의존성 관리를 프레임워크에 맡기면, 개발자는 비즈니스 로직에만 집중할 수 있습니다. 결합도가 낮아져 유연한 설계가 가능하고, 테스트 용이성이 크게 향상됩니다.

- 의존성과 결합도의 차이, IoC의 개념과 동작 원리, DI의 3가지 방식, 그리고 스프링 컨테이너가 어떻게 객체를 관리하는지 이해해야 합니다.

---

## 1. 의존 관계(Dependency)란?

### **의존 관계의 정의**

**의존 관계(Dependency)** 는 어떤 클래스가 다른 클래스의 존재를 알고 있고, 그 클래스의 타입, 생성자, 메서드, 필드 등을 사용하고 있는 관계를 의미합니다.

### **의존 관계가 발생하는 경우**

다음 중 하나라도 해당하면 두 클래스 사이에는 의존 관계가 있습니다:

1. **`new`로 직접 생성한다**
2. **메서드 파라미터로 받는다**
3. **필드로 가진다**
4. **반환 타입이나 변수 타입으로 쓴다**

**예시:**

```java
// 1. new로 직접 생성
public class UserService {
    private UserRepository repository = new UserRepository();  // 의존 관계
}

// 2. 메서드 파라미터로 받는다
public class UserService {
    public void save(User user) {  // User에 의존
        // ...
    }
}

// 3. 필드로 가진다
public class UserService {
    private UserRepository repository;  // 의존 관계
}

// 4. 반환 타입이나 변수 타입으로 쓴다
public class UserService {
    public User findById(String id) {  // User를 반환 타입으로 사용
        UserRepository repository = new UserRepository();  // 변수 타입으로 사용
        return repository.findById(id);
    }
}
```

---

## 2. 의존(Dependency) vs 결합(Coupling)

### **의존과 결합은 다른 개념**

**의존(Dependency)** 과 **결합(Coupling)** 은 다른 개념입니다:

- **의존**: '사용'의 문제 → 어떤 것을 사용하고 있음
- **결합**: '변경에 얼마나 취약한가'의 문제 → 그 대상이 바뀌면 내 코드가 얼마나 깨지는가

**의존성**은 어떤 객체의 기능을 사용하고 있다는 사실이고,  
**결합도**는 그 객체의 변경이 나에게 미치는 영향의 크기입니다.

### **높은 결합도 예시**

```java
// 높은 결합도: 구현체에 직접 의존
public class UserService {
    // UserRepositoryImpl에 직접 의존 (구현체에 의존)
    private UserRepositoryImpl repository = new UserRepositoryImpl();
    
    public User findUser(String id) {
        return repository.findById(id);
    }
}

// 문제점:
// 1. UserRepositoryImpl이 변경되면 UserService도 수정 필요
// 2. 테스트 시 실제 DB에 접근해야 함
// 3. 다른 구현체로 교체하기 어려움
```

### **낮은 결합도 예시**

```java
// 낮은 결합도: 인터페이스에 의존
public class UserService {
    // 인터페이스에 의존 (구현체가 아닌 추상화에 의존)
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;  // 외부에서 주입
    }
    
    public User findUser(String id) {
        return repository.findById(id);
    }
}

// 장점:
// 1. 구현체가 변경되어도 UserService는 수정 불필요
// 2. 테스트 시 Mock 객체 주입 가능
// 3. 다른 구현체로 쉽게 교체 가능
```

### **핵심 원리**

> [!important] 핵심 원리
> 좋은 설계는 **의존성을 제거하는 것이 아니라, 결합도를 낮추는 것**입니다.
> 
> - 의존은 필요합니다 (기능을 사용해야 하므로)
> - 결합도는 낮춰야 합니다 (변경에 유연하게 대응하기 위해)

---

## 3. 이게 DI / IoC로 어떻게 이어지나?

### **의존과 결합의 문제를 해결하는 방법**

의존은 필요하지만 결합도는 낮춰야 합니다. 이를 해결하는 방법이 바로 **DI (Dependency Injection)** 와 **IoC (Inversion of Control)** 입니다.

**핵심 연결:**
- 의존은 하되 → **DI**: 구현체 선택과 생성 책임을 외부로 이동
- 결합도를 낮추기 위해 → **IoC**: 내가 `new` 하지 않는다, 스프링 컨테이너가 넣어준다

### **DI로 해결**

**DI (Dependency Injection)**
- 의존은 하되
- 구현체 선택과 생성 책임을 외부로 이동

```java
// 직접 생성 (높은 결합도)
private UserRepository repository = new UserRepositoryImpl();

// DI 적용 (낮은 결합도)
private final UserRepository repository;
public UserService(UserRepository repository) {
    this.repository = repository;  // 외부에서 주입
}
```

### **IoC로 해결**

**IoC (Inversion of Control)**
- 내가 `new` 하지 않는다
- 이것들을 스프링 컨테이너가 넣어준다

```java
// IoC 없이
private UserRepository repository = new UserRepository();  // 직접 생성

// IoC 적용
@Autowired
private UserRepository repository;  // 스프링이 주입
```

### **DI와 IoC의 관계**

- **DI**: 의존성을 외부에서 주입받는 방법
- **IoC**: 제어권을 프레임워크에 넘기는 원칙
- **DI가 IoC를 실현하는 방법**: DI를 통해 IoC를 구현

---

## 4. DI (Dependency Injection, 의존성 주입)

### **DI의 정의**

**DI(Dependency Injection, 의존성 주입)** 는 의존은 하되, **구현체 선택과 생성 책임을 외부로 이동**시키는 기법입니다.

### **DI 없이 (직접 생성)**

```java
public class UserService {
    // 직접 생성 → 높은 결합도
    private UserRepository repository = new UserRepositoryImpl();
    
    public User findUser(String id) {
        return repository.findById(id);
    }
}
```

**문제점:**
- `UserRepositoryImpl`에 직접 의존
- 구현체 변경 시 코드 수정 필요
- 테스트 시 실제 객체만 사용 가능

### **DI 적용 (외부에서 주입)**

```java
public class UserService {
    // 외부에서 주입 → 낮은 결합도
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;  // 외부에서 주입받음
    }
    
    public User findUser(String id) {
        return repository.findById(id);
    }
}
```

**장점:**
- 인터페이스에 의존 (구현체가 아닌 추상화에 의존)
- 구현체 변경 시 코드 수정 불필요
- 테스트 시 Mock 객체 주입 가능

---

## 5. IoC (Inversion of Control, 제어의 역전)

### **IoC의 정의**

**제어의 역전(IoC: Inversion of Control)** 이란 프로그램의 흐름과 객체 생성·연결에 대한 제어권을 개발자가 직접 가지지 않고, 프레임워크(스프링)가 대신 관리하도록 넘기는 설계 원칙**을 의미합니다.

즉, **"언제 객체를 만들고, 어떻게 연결하고, 언제 사용할지"** 를 개발자가 아닌 스프링이 결정합니다.

### **IoC가 없는 경우**

```java
class UserService {
    // 개발자가 객체를 직접 생성
    private UserRepository repository = new UserRepository();
    
    public User findUser(String id) {
        return repository.findById(id);
    }
}
```

**특징:**
- ❌ 개발자가 객체를 직접 생성
- ❌ 의존 객체 선택과 생성 시점을 직접 제어
- ❌ 코드 흐름을 개발자가 끝까지 통제

### **IoC가 적용된 경우**

```java
class UserService {
    // Service는 Repository를 요구만 함
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;  // 외부에서 주입받음
    }
    
    public User findUser(String id) {
        return repository.findById(id);
    }
}
```

**특징:**
- ✅ Service는 Repository를 요구만 함
- ✅ 생성과 연결은 외부(스프링 컨테이너)가 담당
- ✅ 제어권이 개발자 → 프레임워크로 이동

### **IoC 컨테이너의 역할**

스프링 IoC 컨테이너는 객체를 직접 사용하는 주체 .가 아니라, **객체를 대신 관리하고 조립해주는 관리자 역할**을 합니다.

**IoC 컨테이너의 책임:**

1. **객체 생성 (Bean 생성)**
2. **객체 간 의존관계 설정 (DI)**
3. **객체 생명주기 관리**
4. **필요한 시점에 객체 제공**

개발자는: **"이 객체가 필요하다"** 만 선언하면 됩니다.

---

## 6. DI 방식 3가지

스프링에서 의존성을 주입하는 방법은 크게 3가지가 있습니다.

### **1. 생성자 주입 (Constructor Injection) - 권장**

```java
@Component
public class UserService {
    private final UserRepository repository;
    
    // 생성자를 통한 주입
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

**장점:**
- ✅ 필수 의존성을 보장 (final 키워드 사용 가능)
- ✅ 불변 객체 생성 가능
- ✅ 순환 참조 방지
- ✅ 테스트 용이

**특징:**
- 스프링에서 가장 권장하는 방식
- 의존성이 필수일 때 사용

### **2. 필드 주입 (Field Injection)**

```java
@Component
public class UserService {
    @Autowired
    private UserRepository repository;  // 필드에 직접 주입
}
```

**장점:**
- ✅ 코드가 간결함

**단점:**
- ❌ final 키워드 사용 불가
- ❌ 테스트 시 Mock 객체 주입이 어려움
- ❌ 순환 참조 감지가 어려움

**특징:**
- 간단한 프로젝트나 테스트 코드에서만 사용 권장

### **3. Setter 주입 (Setter Injection)**

```java
@Component
public class UserService {
    private UserRepository repository;
    
    @Autowired
    public void setRepository(UserRepository repository) {
        this.repository = repository;
    }
}
```

**장점:**
- ✅ 선택적 의존성 주입 가능
- ✅ 주입된 객체를 변경 가능

**단점:**
- ❌ final 키워드 사용 불가
- ❌ 불변 객체 생성 불가
- ❌ 필수 의존성을 보장하기 어려움

**특징:**
- 선택적 의존성이 있을 때 사용

### **DI 방식 비교**

| 방식            | 장점                       | 단점                | 권장도   |
| ------------- | ------------------------ | ----------------- | ----- |
| **생성자 주입**    | 필수 의존성 보장, 불변 객체, 테스트 용이 | 코드가 약간 길어짐        | ⭐⭐⭐⭐⭐ |
| **필드 주입**     | 코드 간결                    | 테스트 어려움, final 불가 | ⭐⭐    |
| **Setter 주입** | 선택적 의존성 가능               | 필수 의존성 보장 어려움     | ⭐⭐⭐   |

---

## 7. 스프링에서 DI가 IoC를 구현하는 전체 흐름

### **전체 흐름**

```
1️⃣ ApplicationContext 생성
   ↓
2️⃣ Bean 등록 정보 확인
   ↓
3️⃣ 객체 생성
   ↓
4️⃣ 의존관계 주입 (DI)
   ↓
5️⃣ 객체 제공
```

### **구체적인 예시**

```java
@Component
class UserService {
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}

@Component
class UserRepository {
    // ...
}
```

**Spring의 처리 과정:**

1. **ApplicationContext 생성**
   ```java
   ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
   ```

2. **Bean 등록 정보 확인**
   - `@Component` 어노테이션을 스캔
   - `UserService`, `UserRepository` 클래스 발견

3. **객체 생성**
   - `UserRepository`를 먼저 생성 (의존성이 없는 객체부터)
   - `UserService` 생성 준비

4. **의존관계 주입 (DI)**
   - `UserService` 생성 시 `UserRepository`를 주입
   - 생성자를 통해 의존성 주입

5. **객체 제공**
   - 개발자가 `context.getBean(UserService.class)`로 요청하면
   - 이미 생성되고 주입된 객체를 반환

**핵심:**
- `UserService`는 `UserRepository`가 어떻게 만들어졌는지 모름
- `UserService`는 단지 `UserRepository`를 요구만 함
- 생성과 연결은 스프링 컨테이너가 담당

### **IoC와 DI의 관계**

**IoC**는 "제어권을 넘긴다"는 설계 원칙이고,  
**DI(Dependency Injection)** 는 그 원칙을 코드로 실현하는 방법입니다.

**IoC가 말하는 것:**
- 객체가 스스로 의존 객체를 생성하지 않는다
- 제어권을 외부로 넘긴다

**DI란:**
- 객체가 사용할 의존 객체를 외부에서 생성하여 주입(inject)해주는 방식

**DI로 IoC를 완성한 코드는:**
- 생성에 관여 ❌
- 제어권은 외부로 이동 ✅
- IoC 실현 ✅

---

## 8. 스프링 IoC 컨테이너 동작 과정

### **애플리케이션 시작 시**

```
1️⃣ 애플리케이션 시작
   ↓
2️⃣ Spring이 ApplicationContext 생성
   ↓
3️⃣ 설정 정보 기반으로 객체 생성
   - @Component, @Configuration, @Bean 등을 스캔
   - 필요한 객체들을 미리 생성
   ↓
4️⃣ 의존관계 연결
   - 생성자/필드/세터 주입을 통해 객체 연결
   ↓
5️⃣ 애플리케이션 실행 중
   - 개발자는 객체를 직접 생성하지 않음
   - 컨테이너가 필요할 때 객체를 호출하여 사용
```

### **구체적인 예시**

```java
// 설정 클래스
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
}

// Repository
@Component
public class UserRepository {
    public User findById(String id) {
        // ...
    }
}

// Service
@Component
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

**스프링 컨테이너의 처리:**

1. **ApplicationContext 생성**
   ```java
   ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
   ```

2. **컴포넌트 스캔**
   - `@ComponentScan`으로 `com.example` 패키지 스캔
   - `UserRepository`, `UserService` 발견

3. **Bean 생성 순서**
   ```
   UserRepository 생성 (의존성 없음)
   ↓
   UserService 생성 (UserRepository 주입)
   ```

4. **의존성 주입**
   - `UserService` 생성자에 `UserRepository` 주입
   - 스프링이 자동으로 연결

5. **객체 사용**
   ```java
   // 개발자는 이렇게 사용
   UserService userService = context.getBean(UserService.class);
   User user = userService.findUser("123");
   ```

---

## 9. DI로 IoC를 실현하는 방법

### **IoC와 DI의 관계 정리**

**IoC**는 목표(제어권을 넘긴다),  
**DI**는 방법(의존성을 주입한다)

```
IoC (제어의 역전)
  ↓
DI (의존성 주입)로 실현
  ↓
결합도 감소, 유연한 설계
```

### **IoC의 핵심**

- **제어권을 넘긴다**: 객체 생성과 생명주기를 개발자가 아닌 프레임워크가 관리
- **역전(Inversion)**: 기존에는 개발자가 제어했지만, 이제는 프레임워크가 제어

### **DI의 핵심**

- **의존은 하되**: 객체는 필요한 의존성을 가짐
- **생성은 외부에서**: 의존 객체의 생성과 주입은 외부(스프링 컨테이너)가 담당

---
## 요약

- **의존 관계**는 클래스가 다른 클래스를 사용하는 관계이며, `new`, 파라미터, 필드, 반환 타입 등으로 발생합니다.
- **의존**은 '사용'의 문제이고, **결합**은 '변경에 얼마나 취약한가'의 문제입니다.
- **좋은 설계**는 의존성을 제거하는 것이 아니라 결합도를 낮추는 것입니다.
- **DI**는 의존은 하되, 구현체 선택과 생성 책임을 외부로 이동시키는 기법입니다.
- **IoC**는 객체 생성과 생명주기에 대한 제어권을 개발자가 아닌 프레임워크가 가지는 설계 원칙입니다.
- **DI 방식 3가지**: 생성자 주입(권장), 필드 주입, Setter 주입
- **스프링 컨테이너**는 Bean 생성, 의존관계 주입, 생명주기 관리를 담당합니다.
- **IoC**는 목표이고, **DI**는 그 목표를 실현하는 방법입니다.

---

## 참고 자료

- [Spring Framework - IoC Container](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans)
- [Dependency Injection - Martin Fowler](https://martinfowler.com/articles/injection.html)
- [Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html)
