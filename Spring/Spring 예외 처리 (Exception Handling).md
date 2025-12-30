#spring #exception #예외처리 #exception-handling #controller-advice #exception-handler #면접 #개념정리

**관련 개념:** [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]] [[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]] [[스프링 어노테이션 (Spring Annotations)|스프링 어노테이션]]

> [!note] 이어서 읽기
> Spring 예외 처리를 이해하려면 **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**Spring 예외 처리(Exception Handling)** 는 애플리케이션 실행 중 발생하는 비정상적인 상황을 적절히 처리하여 안정성과 사용자 경험을 보장하는 핵심 기법이다. Spring MVC에서는 `@ControllerAdvice`와 `@ExceptionHandler`를 통해 예외를 중앙에서 관리하고 표준화된 응답을 제공한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 예외를 적절히 처리하지 않으면 사용자에게 스택 트레이스 같은 내부 에러 메시지가 노출되어 보안 위험이 증가하고, 어디서 실패했는지 찾기 어려워 디버깅 비용이 증가하며, 사용자 경험이 악화됩니다. 대규모 애플리케이션에서 예외 처리가 없으면 서비스 장애로 이어질 수 있습니다.

- Spring 예외 처리는 애플리케이션의 안정성과 신뢰성을 보장하는 핵심 요소입니다. 서버가 예외를 잡고 의미 있는 메시지로 변환하여 일관된 방식으로 응답하는 구조를 통해 개발 생산성을 높이고, 비즈니스 로직에 집중할 수 있게 합니다. 글로벌 예외 처리기를 통해 중복을 제거하고 유지보수성을 향상시킬 수 있습니다.

- 예외의 정의와 종류, Spring MVC의 예외 처리 흐름, 예외 처리 방법 3가지, 그리고 글로벌 예외 처리기 설계 원리를 이해해야 합니다.

---

## 1. 예외 처리가 중요한 이유

### **예외란?**

**예외(Exception)** 는 애플리케이션 실행 중 발생하는 비정상적인 상황을 의미합니다.

**예외의 예시:**
- 잘못된 로그인 시도
- 존재하지 않는 데이터 요청
- 서버 내부 오류
- 유효성 검증 실패
- 권한 부족

### **예외 처리 없이 발생하는 문제**

예외를 적절히 처리하지 않으면 다음과 같은 심각한 문제가 발생합니다:

1. **스택 트레이스 노출**
   - 사용자에게 내부 에러 메시지와 스택 트레이스가 그대로 노출됨
   - 보안 위험 증가 (시스템 구조, 라이브러리 정보 노출)

2. **디버깅 어려움**
   - 어디서 실패했는지 찾기 어려움
   - 로그 분석 비용 증가
   - 문제 원인 파악 시간 증가

3. **일관성 없는 응답**
   - 컨트롤러마다 다른 에러 포맷
   - 클라이언트가 에러 처리 로직을 일관되게 구현하기 어려움

4. **사용자 경험 악화**
   - 기술적인 에러 메시지로 인한 혼란
   - 사용자가 문제를 이해하고 해결하기 어려움

5. **유지보수 어려움**
   - 각 컨트롤러마다 중복된 예외 처리 코드
   - 새로운 예외 추가 시 여러 곳 수정 필요

**예시: 예외 처리 없이**

```java
@RestController
public class UserController {
    
    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        // 예외 처리 없이 → NullPointerException 발생 시 스택 트레이스 노출
        return user;
    }
}
```

**문제점:**
- `user`가 `null`이면 `NullPointerException` 발생
- 스택 트레이스가 그대로 클라이언트에 전달됨
- 일관된 에러 응답 형식 없음

### **예외 처리의 목적**

그래서 서버가 예외를 잡고 → 의미 있는 메시지로 변환 → 일관된 방식으로 응답하는 구조가 필요합니다.

---

## 2. Spring MVC 기본 예외 처리 흐름

### **예외 처리 흐름**

컨트롤러에서 예외가 발생하면 Spring MVC는 다음과 같은 흐름으로 처리합니다:

```
1️⃣ Controller에서 예외 발생
   ↓
2️⃣ DispatcherServlet이 예외 감지
   ↓
3️⃣ HandlerExceptionResolver가 처리 가능한 핸들러 탐색
   ↓
4️⃣ 적절한 @ExceptionHandler, @ControllerAdvice 실행
   (없다면 BasicErrorController가 기본 JSON 에러 반환)
   ↓
5️⃣ 표준화된 에러 응답 반환
```

### **DispatcherServlet의 역할**

**DispatcherServlet**은 Spring MVC의 핵심 컴포넌트로, 모든 요청을 받아 적절한 핸들러로 라우팅하고, 예외가 발생하면 `HandlerExceptionResolver`를 통해 예외를 처리합니다.

### **HandlerExceptionResolver의 동작**

**HandlerExceptionResolver**는 예외를 처리하는 인터페이스로, 다음과 같은 순서로 처리합니다:

1. **ExceptionHandlerExceptionResolver**: `@ExceptionHandler` 메서드 찾기
2. **ResponseStatusExceptionResolver**: `@ResponseStatus` 어노테이션 처리
3. **DefaultHandlerExceptionResolver**: Spring 기본 예외 처리

### **Spring 기본 응답 예시**

예외 처리기가 없을 때 Spring이 반환하는 기본 응답:

```json
{
  "timestamp": "2024-01-15T10:30:00.000+00:00",
  "status": 400,
  "error": "Bad Request",
  "message": "이름은 필수입니다.",
  "path": "/users"
}
```

**문제점:**
- 실무에서 이 포맷이 일관되지 않음
- 확장성이 부족함 (커스텀 에러 코드 추가 어려움)
- 비즈니스 로직과 예외 처리 로직이 분리되지 않음

**해결책:**
- 글로벌 예외 처리기 설계 필요

---

## 3. 예외 처리 방법 3가지

### **예외 처리 방법 비교**

| 방법 | 특징 | 단점 | 권장도 |
|------|------|------|--------|
| **메서드 내부 try-catch** | 단순 | 중복 많음, 유지보수 어려움 | ⭐ |
| **@ExceptionHandler (컨트롤러 단위)** | 컨트롤러 범위 처리 | 컨트롤러마다 중복 발생 | ⭐⭐⭐ |
| **@ControllerAdvice (글로벌 처리)** | 전역 통합 처리 | 없음 | ⭐⭐⭐⭐⭐ |

### **1. 메서드 내부 try-catch**

```java
@RestController
public class UserController {
    
    @GetMapping("/users/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        try {
            User user = userService.findById(id);
            return ResponseEntity.ok(user);
        } catch (UserNotFoundException e) {
            return ResponseEntity.status(404)
                    .body(null); // 일관성 없는 응답
        } catch (Exception e) {
            return ResponseEntity.status(500)
                    .body(null); // 중복 코드
        }
    }
}
```

**문제점:**
- ❌ 모든 컨트롤러 메서드마다 try-catch 중복
- ❌ 일관된 에러 응답 형식 유지 어려움
- ❌ 비즈니스 로직과 예외 처리 로직 혼재

### **2. @ExceptionHandler (컨트롤러 단위)**

```java
@RestController
public class UserController {
    
    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id); // 예외 던지기만
    }
    
    // 컨트롤러 내부 예외 처리
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(404)
                .body("사용자를 찾을 수 없습니다: " + ex.getMessage());
    }
}
```

**장점:**
- ✅ 비즈니스 로직과 예외 처리 로직 분리
- ✅ 컨트롤러 내에서 일관된 처리

**단점:**
- ❌ 컨트롤러마다 중복된 예외 처리 코드
- ❌ 전역적으로 일관된 응답 형식 유지 어려움

### **3. @ControllerAdvice (글로벌 처리) - 권장**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("서버 오류가 발생했습니다: " + ex.getMessage());
    }
}
```

**장점:**
- ✅ 전역 통합 처리
- ✅ 일관된 에러 응답 형식
- ✅ 중복 제거
- ✅ 유지보수 용이

**특징:**
- 규모 있는 서비스라면 글로벌 처리 방식이 표준

---

## 4. @ControllerAdvice와 @ExceptionHandler

### **@ControllerAdvice란?**

**@ControllerAdvice**는 모든 컨트롤러의 예외를 전역적으로 처리하는 어노테이션입니다.

**특징:**
- 모든 `@Controller` 또는 `@RestController`에서 발생하는 예외를 처리
- `@Component`를 포함하여 스프링 빈으로 등록됨
- 여러 컨트롤러에 공통으로 적용되는 로직을 한 곳에서 관리

### **@RestControllerAdvice**

**@RestControllerAdvice**는 `@ControllerAdvice` + `@ResponseBody`를 합친 어노테이션입니다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@ControllerAdvice
@ResponseBody  // 자동으로 JSON 응답
public @interface RestControllerAdvice {
    // ...
}
```

**차이점:**
- `@ControllerAdvice`: 일반 컨트롤러용 (뷰 반환 가능)
- `@RestControllerAdvice`: REST API용 (자동으로 JSON 응답)

### **@ExceptionHandler란?**

**@ExceptionHandler**는 특정 예외 타입을 처리하는 메서드에 붙이는 어노테이션입니다.

**특징:**
- `@ControllerAdvice` 클래스 내부에 정의하여 전역 처리
- 컨트롤러 내부에 정의하여 해당 컨트롤러만 처리
- 여러 예외 타입을 배열로 지정 가능

### **기본 글로벌 예외 처리기**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("서버 오류가 발생했습니다: " + ex.getMessage());
    }
}
```

**동작 원리:**
- `@RestControllerAdvice`: 모든 컨트롤러의 예외를 전역적으로 처리
- `@ExceptionHandler`: 특정 예외 타입을 처리하는 메서드 지정
- 모든 예외를 잡아서 일관된 형식으로 응답

**한계:**
- 예외 타입별 세밀한 처리 불가
- 에러 응답 형식이 단순함 (문자열만 반환)
- 확장성 부족

---

## 5. 예외 타입별 세밀한 처리

### **예외 타입별 처리**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    // IllegalArgumentException 처리
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException ex) {
        return ResponseEntity.badRequest()
                .body("잘못된 요청입니다: " + ex.getMessage());
    }
    
    // 유효성 검증 예외 처리
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationExceptions(
            MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors()
                .forEach(err -> errors.put(err.getField(), err.getDefaultMessage()));
        return ResponseEntity.badRequest().body(errors);
    }
    
    // 일반 예외 처리
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("서버 오류가 발생했습니다: " + ex.getMessage());
    }
}
```

**장점:**
- ✅ 예외 타입별로 다른 처리 가능
- ✅ 유효성 검증 에러를 구조화된 형식으로 반환
- ✅ HTTP 상태 코드를 예외 타입에 맞게 설정

**한계:**
- 여전히 응답 형식이 일관되지 않음
- 커스텀 예외와 기본 예외의 처리 방식이 다름

---

## 6. 비즈니스 예외는 "던지고", 글로벌 핸들러가 "처리"

### **관심사 분리 원칙**

**비즈니스 로직의 역할:**
- 예외를 던지기만 함
- 예외 처리 로직은 포함하지 않음

**글로벌 핸들러의 역할:**
- 예외를 잡아서 처리
- 표준화된 응답으로 변환

### **커스텀 예외 정의**

```java
// 커스텀 예외 정의
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}
```

### **비즈니스 로직에서 예외 던지기**

```java
@Service
public class UserService {
    
    public User findById(Long id) {
        User user = userRepository.findById(id);
        if (user == null) {
            throw new UserNotFoundException("해당 사용자가 존재하지 않습니다.");
        }
        return user;
    }
}
```

### **글로벌 핸들러에서 예외 처리**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```

**핵심 원리:**
- 비즈니스 로직: 예외를 던지기만 함
- 글로벌 핸들러: 예외를 잡아서 처리
- 관심사 분리로 코드 가독성과 유지보수성 향상

---

## 7. 예외 응답 구조 표준화하기

### **표준화의 필요성**

실무에서는 일관된 에러 응답 형식이 필요합니다:

```json
{
  "status": 404,
  "message": "사용자를 찾을 수 없습니다.",
  "path": "/users/999",
  "timestamp": "2024-01-15T10:30:00"
}
```

### **ErrorCode 정의**

```java
@Getter
@RequiredArgsConstructor
public enum ErrorCode {
    USER_NOT_FOUND(HttpStatus.NOT_FOUND, "사용자를 찾을 수 없습니다."),
    INVALID_INPUT(HttpStatus.BAD_REQUEST, "입력값이 올바르지 않습니다."),
    INTERNAL_ERROR(HttpStatus.INTERNAL_SERVER_ERROR, "서버 오류 발생");

    private final HttpStatus status;
    private final String message;
}
```

**장점:**
- ✅ 에러 코드와 메시지를 중앙에서 관리
- ✅ HTTP 상태 코드와 메시지가 일관되게 매핑
- ✅ 새로운 에러 추가 시 확장 용이

### **BaseException (모든 커스텀 예외의 부모)**

```java
@Getter
public abstract class BaseException extends RuntimeException {
    private final ErrorCode errorCode;

    protected BaseException(ErrorCode errorCode) {
        super(errorCode.getMessage());
        this.errorCode = errorCode;
    }
}
```

**장점:**
- ✅ 모든 커스텀 예외가 ErrorCode를 가짐
- ✅ 일관된 예외 처리 가능
- ✅ 예외 계층 구조 명확화

### **개선된 커스텀 예외**

```java
public class UserNotFoundException extends BaseException {
    public UserNotFoundException() {
        super(ErrorCode.USER_NOT_FOUND);
    }
}
```

**장점:**
- ✅ ErrorCode를 통해 일관된 에러 정보 제공
- ✅ 생성자가 간단해짐
- ✅ 메시지 중복 제거

### **ErrorResponse DTO**

```java
@Getter
@AllArgsConstructor
public class ErrorResponse {
    private final int status;
    private final String message;
    private final String path;
    private final LocalDateTime timestamp;

    public static ErrorResponse of(ErrorCode errorCode, String path) {
        return new ErrorResponse(
            errorCode.getStatus().value(),
            errorCode.getMessage(),
            path,
            LocalDateTime.now()
        );
    }
}
```

**장점:**
- ✅ 일관된 에러 응답 형식
- ✅ 정적 팩토리 메서드로 생성 간편
- ✅ 타임스탬프 자동 추가

### **최종 글로벌 핸들러**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(BaseException.class)
    public ResponseEntity<ErrorResponse> handleBaseException(
            BaseException ex, HttpServletRequest request) {
        ErrorResponse error = ErrorResponse.of(
            ex.getErrorCode(), 
            request.getRequestURI()
        );
        return ResponseEntity
                .status(ex.getErrorCode().getStatus())
                .body(error);
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(
            MethodArgumentNotValidException ex, HttpServletRequest request) {
        ErrorResponse error = ErrorResponse.of(
            ErrorCode.INVALID_INPUT,
            request.getRequestURI()
        );
        return ResponseEntity
                .status(HttpStatus.BAD_REQUEST)
                .body(error);
    }
}
```

**최종 응답 예시:**

```json
{
  "status": 404,
  "message": "사용자를 찾을 수 없습니다.",
  "path": "/users/999",
  "timestamp": "2024-01-15T10:30:00"
}
```

---

## 8. 전체 흐름 요약 (DispatcherServlet 흐름과 연결)

### **예외 처리 전체 흐름**

```
1️⃣ Service/Repository에서 예외 발생
   UserService.findById() → UserNotFoundException 던짐
      ↓
2️⃣ 예외가 Controller → DispatcherServlet으로 전파
   Controller 메서드에서 예외가 발생하면 DispatcherServlet이 감지
      ↓
3️⃣ ExceptionHandlerExceptionResolver가 글로벌 핸들러 확인
   HandlerExceptionResolver가 처리 가능한 핸들러 탐색
      ↓
4️⃣ 일치하는 @ExceptionHandler 실행
   GlobalExceptionHandler.handleBaseException() 실행
      ↓
5️⃣ 표준화된 JSON 응답 반환
   ErrorResponse 객체를 JSON으로 변환하여 클라이언트에 반환
```

### **구체적인 예시**

```java
// 1. Service에서 예외 발생
@Service
public class UserService {
    public User findById(Long id) {
        User user = userRepository.findById(id);
        if (user == null) {
            throw new UserNotFoundException(); // 예외 던짐
        }
        return user;
    }
}

// 2. Controller에서 예외 전파
@RestController
public class UserController {
    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id); // 예외가 여기서 발생
    }
}

// 3. DispatcherServlet이 예외 감지
// Spring MVC가 자동으로 예외를 감지하고 HandlerExceptionResolver 호출

// 4. GlobalExceptionHandler가 예외 처리
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(BaseException.class)
    public ResponseEntity<ErrorResponse> handleBaseException(
            BaseException ex, HttpServletRequest request) {
        // 예외를 표준화된 응답으로 변환
        return ResponseEntity.status(ex.getErrorCode().getStatus())
                .body(ErrorResponse.of(ex.getErrorCode(), request.getRequestURI()));
    }
}

// 5. 클라이언트에 JSON 응답 반환
// {
//   "status": 404,
//   "message": "사용자를 찾을 수 없습니다.",
//   "path": "/users/999",
//   "timestamp": "2024-01-15T10:30:00"
// }
```

**핵심:**
- 비즈니스 로직은 예외를 던지기만 함
- DispatcherServlet이 예외를 감지하고 HandlerExceptionResolver 호출
- 글로벌 핸들러가 예외를 잡아서 표준화된 응답으로 변환
- 관심사 분리로 코드 가독성과 유지보수성 향상

---

## 9. 이 구조의 장점

### **주요 장점**

**✔ 관심사 분리**
- 비즈니스 로직은 "로직만", 예외 변환은 "글로벌 핸들러가"
- 각 계층의 책임이 명확해짐

**✔ 중복 제거**
- 컨트롤러마다 try-catch 제거
- 예외 처리 로직을 한 곳에서 관리

**✔ 일관된 응답**
- 모든 API가 동일한 에러 포맷 반환
- 클라이언트가 일관되게 에러 처리 가능

**✔ 유지보수 용이**
- 새로운 에러 추가 시, ErrorCode/Exception만 확장
- 예외 처리 로직 변경 시 한 곳만 수정

**✔ 확장성**
- ErrorCode enum으로 에러 코드 중앙 관리
- BaseException으로 예외 계층 구조 명확화

**✔ 테스트 용이**
- 비즈니스 로직과 예외 처리 로직 분리로 단위 테스트 용이
- Mock 객체로 예외 상황 시뮬레이션 가능

---

## 요약

- **예외**는 애플리케이션 실행 중 발생하는 비정상적인 상황이며, 적절히 처리하지 않으면 보안 위험, 디버깅 어려움, 사용자 경험 악화가 발생합니다.
- **Spring MVC 예외 처리 흐름**은 Controller → DispatcherServlet → HandlerExceptionResolver → @ExceptionHandler 순으로 진행됩니다.
- **예외 처리 방법 3가지**: 메서드 내부 try-catch, @ExceptionHandler (컨트롤러 단위), @ControllerAdvice (글로벌 처리) - 글로벌 처리가 가장 권장됩니다.
- **@ControllerAdvice와 @ExceptionHandler**: @ControllerAdvice는 전역 예외 처리, @ExceptionHandler는 특정 예외 타입을 처리하는 메서드입니다.
- **관심사 분리 원칙**: 비즈니스 로직은 예외를 던지기만 하고, 글로벌 핸들러가 예외를 잡아서 처리합니다.
- **예외 응답 구조 표준화**: ErrorCode enum, BaseException, ErrorResponse DTO를 통해 일관된 에러 응답 형식을 제공합니다.
- **전체 흐름**: Service/Repository에서 예외 발생 → Controller로 전파 → DispatcherServlet 감지 → HandlerExceptionResolver 처리 → 글로벌 핸들러 실행 → 표준화된 JSON 응답 반환
- **핵심 원리**: "예외를 던지고, 중앙에서 표준화해 응답한다" 구조를 만드는 것이 Spring 예외 처리의 핵심입니다.

---

## 참고 자료

- [Spring Framework - Exception Handling](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-exceptionhandlers)
- [Spring Boot - Error Handling](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.developing-web-applications.error-handling)
- [Baeldung - Spring Exception Handling](https://www.baeldung.com/exception-handling-for-rest-with-spring)

---

### 예상 꼬리질문 정리

#### 1. @ControllerAdvice와 @ExceptionHandler의 차이는?

- **@ControllerAdvice**: 모든 컨트롤러의 예외를 전역적으로 처리하는 어노테이션
  - `@RestControllerAdvice`: REST API용 (자동으로 `@ResponseBody` 적용)
  - `@ControllerAdvice`: 일반 컨트롤러용
- **@ExceptionHandler**: 특정 예외 타입을 처리하는 메서드에 붙이는 어노테이션
  - `@ControllerAdvice` 클래스 내부에 정의하여 전역 처리
  - 컨트롤러 내부에 정의하여 해당 컨트롤러만 처리

**사용 예시:**
```java
@RestControllerAdvice  // 전역 예외 처리
public class GlobalExceptionHandler {
    
    @ExceptionHandler(UserNotFoundException.class)  // 특정 예외 처리
    public ResponseEntity<ErrorResponse> handleUserNotFound(...) {
        // ...
    }
}
```

#### 2. HandlerExceptionResolver의 동작 원리는?

- **HandlerExceptionResolver**: Spring MVC에서 예외를 처리하는 인터페이스
- **동작 순서**:
  1. `ExceptionHandlerExceptionResolver`: `@ExceptionHandler` 메서드 찾기
  2. `ResponseStatusExceptionResolver`: `@ResponseStatus` 어노테이션 처리
  3. `DefaultHandlerExceptionResolver`: Spring 기본 예외 처리
- **우선순위**: 위 순서대로 처리 가능한 Resolver를 찾아 실행

#### 3. 커스텀 예외를 만들 때 BaseException을 상속받는 이유는?

- **일관성**: 모든 커스텀 예외가 ErrorCode를 가지도록 보장
- **중복 제거**: 예외 처리 로직을 BaseException 하나로 통합 가능
- **확장성**: 새로운 예외 추가 시 ErrorCode만 추가하면 됨
- **타입 안정성**: `@ExceptionHandler(BaseException.class)` 하나로 모든 커스텀 예외 처리

#### 4. ErrorCode를 enum으로 만드는 이유는?

- **중앙 관리**: 에러 코드와 메시지를 한 곳에서 관리
- **타입 안정성**: 컴파일 타임에 에러 코드 검증 가능
- **일관성**: HTTP 상태 코드와 메시지가 일관되게 매핑
- **확장성**: 새로운 에러 추가 시 enum만 수정

#### 5. 예외 처리와 로깅의 관계는?

- **로깅 위치**: 글로벌 핸들러에서 예외를 잡을 때 로깅
- **로깅 내용**: 예외 메시지, 스택 트레이스, 요청 정보
- **목적**: 디버깅과 모니터링을 위한 정보 수집

**예시:**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);
    
    @ExceptionHandler(BaseException.class)
    public ResponseEntity<ErrorResponse> handleBaseException(
            BaseException ex, HttpServletRequest request) {
        log.error("예외 발생: {}", ex.getMessage(), ex);  // 로깅
        return ResponseEntity.status(ex.getErrorCode().getStatus())
                .body(ErrorResponse.of(ex.getErrorCode(), request.getRequestURI()));
    }
}
```

#### 6. 검증 예외(MethodArgumentNotValidException)를 어떻게 처리하나?

- **@Valid 어노테이션**: 요청 데이터 유효성 검증
- **BindingResult**: 검증 결과를 담는 객체
- **글로벌 핸들러에서 처리**: 검증 실패 시 구조화된 에러 응답 반환

**예시:**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationException(
            MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors()
                .forEach(err -> errors.put(err.getField(), err.getDefaultMessage()));
        return ResponseEntity.badRequest().body(errors);
    }
}
```

#### 7. 예외 처리 성능에 미치는 영향은?

- **오버헤드**: 예외 처리 자체는 미미한 오버헤드
- **주의사항**: 예외를 남용하면 성능 저하 가능
  - 예외는 "예외적인 상황"에만 사용
  - 정상적인 흐름에서는 예외를 던지지 않음
- **최적화**: 예외 처리 로직은 간결하게 유지

---

## 관련 노트

- **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]** - 예외 처리가 동작하는 Spring 환경
- **[[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]** - 관심사 분리 원칙과 예외 처리의 관계
- **[[스프링 어노테이션 (Spring Annotations)|스프링 어노테이션]]** - @ControllerAdvice와 @ExceptionHandler의 어노테이션 구조

---

**태그:** #spring #exception #예외처리 #exception-handling #controller-advice #exception-handler #면접 #개념정리

