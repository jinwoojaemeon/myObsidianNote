#spring #mvc #spring-mvc #dispatcher-servlet #web #면접 #개념정리

**관련 개념:** [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]] [[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]] [[RESTful API (REST API)|RESTful API]]

> [!note] 이어서 읽기
> Spring MVC를 이해하려면 **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]**의 핵심 개념과 **[[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]**를 함께 확인하세요.

---

## Topic (오늘의 주제)

**Spring MVC(Model-View-Controller)**는 스프링 프레임워크의 웹 계층을 담당하는 모듈로, 웹 요청을 처리하고 응답을 생성하는 구조화된 아키텍처를 제공한다. Dispatcher Servlet을 중심으로 모든 요청을 중앙에서 관리하여 개발자가 비즈니스 로직에만 집중할 수 있게 한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- Spring MVC 없이 순수 서블릿을 사용하면 요청마다 각각의 서블릿을 만들어야 하며, 파라미터 파싱, 객체 생성, 응답 생성 등 반복적인 코드를 직접 작성해야 합니다. 이는 코드 중복을 증가시키고 유지보수를 어렵게 만듭니다.

- Spring MVC는 Dispatcher Servlet이라는 단일 진입점을 통해 모든 요청을 중앙에서 관리하고, Handler Mapping, Handler Adapter, View Resolver 등의 컴포넌트가 자동으로 요청을 처리하여 개발자는 비즈니스 로직에만 집중할 수 있습니다. 프론트 컨트롤러 패턴을 구현하여 코드 재사용성과 유지보수성을 크게 향상시킵니다.

- Dispatcher Servlet의 동작 원리, 요청 처리 흐름, 각 컴포넌트의 역할, 그리고 컨트롤러 작성 방법을 이해해야 합니다.

---

## 1. Spring MVC란 무엇인가?

### **Spring MVC의 정의**

**Spring MVC(Model-View-Controller)**는 스프링 프레임워크의 웹 계층을 담당하는 모듈로, 웹 요청을 처리하고 응답을 생성하는 구조화된 아키텍처를 제공합니다.

**MVC 패턴:**
- **Model**: 데이터와 비즈니스 로직
- **View**: 사용자 인터페이스(화면)
- **Controller**: 사용자 입력을 처리하고 Model과 View를 연결

### **Spring MVC의 핵심 특징**

1. **프론트 컨트롤러 패턴**: Dispatcher Servlet이 모든 요청의 단일 진입점
2. **자동 요청 매핑**: URL 패턴에 따라 적절한 컨트롤러로 자동 라우팅
3. **의존성 주입**: IoC/DI를 통한 느슨한 결합
4. **유연한 뷰 처리**: JSP, Thymeleaf, JSON 등 다양한 뷰 기술 지원

---

## 2. 서블릿과 서블릿 컨테이너

### **서블릿(Servlet)이란?**

**서블릿(Servlet)**은 HTTP 요청 정보를 객체로 변환하여 개발자가 로직에만 집중하게 돕는 프로그램입니다.

**서블릿의 역할:**
- HTTP 요청을 받아 처리
- 비즈니스 로직 실행
- HTTP 응답 생성

### **서블릿 컨테이너(Servlet Container)**

**서블릿 컨테이너**는 서블릿의 생명주기(생성, 실행, 소멸)를 관리하며, 멀티스레드 처리를 담당합니다.

**서블릿 컨테이너의 역할:**
1. 서블릿 인스턴스 생성 및 관리
2. 요청을 적절한 서블릿으로 라우팅
3. 멀티스레드 환경에서 요청 처리
4. 서블릿 생명주기 관리

### **순수 서블릿의 문제점**

**스프링 없이:**
```
요청 → UserServlet (각각의 서블릿)
요청 → OrderServlet
요청 → ProductServlet
```

**문제점:**
- 요청마다 각각의 서블릿을 만들어야 함
- 파라미터 파싱, 객체 생성, 응답 생성 등 반복적인 코드
- 코드 중복 증가
- 유지보수 어려움

---

## 3. 프론트 컨트롤러 패턴

### **프론트 컨트롤러 패턴이란?**

**프론트 컨트롤러 패턴(Front Controller Pattern)**은 모든 요청을 단일 진입점에서 받아 처리하는 디자인 패턴입니다.

**스프링 사용:**
```
모든 요청 → Dispatcher Servlet (단일 진입점)
         ↓
    적절한 컨트롤러로 위임
```

**장점:**
- 중앙 집중식 요청 처리
- 공통 로직(인증, 로깅 등)을 한 곳에서 처리
- 코드 재사용성 향상
- 유지보수 용이

### **Dispatcher Servlet**

**Dispatcher Servlet**은 Spring MVC의 핵심 컴포넌트로, 모든 HTTP 요청을 받아 적절한 핸들러(컨트롤러)로 라우팅하는 프론트 컨트롤러입니다.

**Dispatcher Servlet의 역할:**
1. 모든 요청을 받아 처리
2. Handler Mapping을 통해 적절한 컨트롤러 찾기
3. Handler Adapter를 통해 컨트롤러 메서드 호출
4. View Resolver를 통해 뷰 결정
5. 응답 생성 및 반환

---

## 4. Spring MVC 요청 처리 흐름

### **전체 요청 처리 흐름**

```
1️⃣ 클라이언트 요청
   ↓
2️⃣ Dispatcher Servlet (모든 요청의 중앙 컨트롤러)
   ↓
3️⃣ Handler Mapping (URL을 보고 어떤 컨트롤러가 처리할지 결정)
   ↓
4️⃣ Handler Adapter (컨트롤러 메서드를 실제로 호출)
   ↓
5️⃣ Controller (개발자가 작성한 비즈니스 로직)
   ↓
6️⃣ ModelAndView 반환 (또는 직접 데이터 반환)
   ↓
7️⃣ View Resolver (결과를 어떤 화면으로 보여줄지 결정)
   ↓
8️⃣ View 렌더링
   ↓
9️⃣ 클라이언트 응답
```

### **구체적인 예시**

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

**요청 처리 과정:**

1. **클라이언트 요청**: `GET /api/users/123`
2. **Dispatcher Servlet**: 요청을 받아 처리 시작
3. **Handler Mapping**: `/api/users/{id}` 패턴과 매칭되는 `UserController.getUser()` 메서드 찾기
4. **Handler Adapter**: `getUser()` 메서드를 호출할 수 있도록 준비
5. **Controller**: `getUser()` 메서드 실행, `userService.findById(id)` 호출
6. **View Resolver**: `@RestController`이므로 JSON으로 변환하여 반환
7. **응답**: `{"id": "123", "name": "홍길동"}` 반환

---

## 5. Spring MVC 주요 구성 요소

### **핵심 컴포넌트**

| 구성 요소 | 역할 |
|---------|------|
| **Dispatcher Servlet** | 모든 요청의 중앙 컨트롤러. 적절한 핸들러에게 작업을 위임 |
| **Handler Mapping** | URL 요청을 보고 어떤 컨트롤러(Handler)가 처리할지 결정 |
| **Handler Adapter** | 결정된 컨트롤러의 메서드를 실제로 호출할 수 있게 연결 |
| **Controller** | 개발자가 실제 비즈니스 로직을 작성하는 곳 |
| **View Resolver** | 실행 결과를 어떤 화면(Thymeleaf, JSP 등)으로 보여줄지 결정 |
| **Model** | 컨트롤러에서 뷰로 전달하는 데이터 |
| **View** | 사용자에게 보여줄 화면 |

### **Handler Mapping**

**Handler Mapping**은 요청 URL을 분석하여 어떤 컨트롤러 메서드가 처리할지 결정합니다.

**동작 방식:**
- `@RequestMapping`, `@GetMapping`, `@PostMapping` 등의 어노테이션을 스캔
- URL 패턴과 매칭되는 핸들러 찾기
- 적절한 핸들러 반환

### **Handler Adapter**

**Handler Adapter**는 Handler Mapping에서 찾은 핸들러를 실제로 실행할 수 있도록 연결합니다.

**동작 방식:**
- 컨트롤러 메서드의 파라미터를 분석
- HTTP 요청에서 필요한 데이터 추출 및 바인딩
- 컨트롤러 메서드 호출
- 반환값 처리

### **View Resolver**

**View Resolver**는 컨트롤러가 반환한 뷰 이름을 실제 뷰 객체로 변환합니다.

**지원하는 뷰 기술:**
- JSP
- Thymeleaf
- FreeMarker
- JSON (REST API)
- XML

---

## 6. Controller 작성 방법

### **@Controller vs @RestController**

**@Controller**: 뷰를 반환하는 전통적인 MVC 컨트롤러

```java
@Controller
@RequestMapping("/users")
public class UserController {
    
    @GetMapping
    public String getUsers(Model model) {
        List<User> users = userService.findAll();
        model.addAttribute("users", users);
        return "users/list";  // 뷰 이름 반환
    }
}
```

**@RestController**: JSON/XML 등 데이터를 반환하는 REST 컨트롤러

```java
@RestController
@RequestMapping("/api/users")
public class UserRestController {
    
    @GetMapping
    public List<User> getUsers() {
        return userService.findAll();  // JSON 데이터 반환
    }
}
```

**차이점:**
- `@RestController` = `@Controller` + `@ResponseBody`
- `@Controller`는 뷰 이름을 반환하고, `@RestController`는 데이터를 직접 반환

### **요청 매핑 어노테이션**

| 어노테이션 | HTTP 메서드 | 용도 |
|-----------|------------|------|
| `@GetMapping` | GET | 데이터 조회 |
| `@PostMapping` | POST | 데이터 생성 |
| `@PutMapping` | PUT | 데이터 수정 |
| `@DeleteMapping` | DELETE | 데이터 삭제 |
| `@PatchMapping` | PATCH | 데이터 부분 수정 |
| `@RequestMapping` | 모든 메서드 | 범용 매핑 |

### **파라미터 바인딩**

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    // 1. 경로 변수
    @GetMapping("/{id}")
    public User getUser(@PathVariable String id) {
        return userService.findById(id);
    }
    
    // 2. 쿼리 파라미터
    @GetMapping
    public List<User> getUsers(@RequestParam String name) {
        return userService.findByName(name);
    }
    
    // 3. 요청 본문
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.save(user);
    }
    
    // 4. HTTP 헤더
    @GetMapping("/header")
    public String getHeader(@RequestHeader("Authorization") String token) {
        return token;
    }
}
```

---

## 7. Spring MVC와 순수 서블릿 비교

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

**스프링 MVC:**
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
- 스프링 MVC: 비즈니스 로직만 작성하면 나머지는 프레임워크가 처리

---

## 8. Spring MVC 동작 원리 상세

### **애플리케이션 시작 시**

```
1️⃣ 애플리케이션 시작
   ↓
2️⃣ Dispatcher Servlet 초기화
   - Handler Mapping 등록
   - Handler Adapter 등록
   - View Resolver 등록
   ↓
3️⃣ 컨트롤러 스캔 및 등록
   - @Controller, @RestController 어노테이션 스캔
   - 요청 매핑 정보 수집
   ↓
4️⃣ 요청 대기 상태
```

### **요청 처리 시**

```
1️⃣ HTTP 요청 도착
   ↓
2️⃣ Dispatcher Servlet이 요청 받음
   ↓
3️⃣ Handler Mapping에서 적절한 핸들러 찾기
   - URL 패턴 매칭
   - HTTP 메서드 확인
   ↓
4️⃣ Handler Adapter 선택
   - 핸들러 타입에 맞는 어댑터 선택
   ↓
5️⃣ 파라미터 바인딩
   - @PathVariable, @RequestParam 등 처리
   - 요청 본문 파싱 (@RequestBody)
   ↓
6️⃣ 컨트롤러 메서드 실행
   - 비즈니스 로직 실행
   ↓
7️⃣ 반환값 처리
   - @ResponseBody: JSON 변환
   - 뷰 이름: View Resolver로 뷰 찾기
   ↓
8️⃣ 응답 생성 및 반환
```

### **구체적인 예시**

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        return ResponseEntity.ok(user);
    }
}
```

**처리 과정:**

1. **요청**: `GET /api/users/123`
2. **Handler Mapping**: `getUser()` 메서드 찾기
3. **Handler Adapter**: `@PathVariable` 파라미터 바인딩 (`id = 123`)
4. **Controller**: `getUser(123)` 실행
5. **Service**: `userService.findById(123)` 호출
6. **반환**: `ResponseEntity<User>` 반환
7. **응답**: JSON으로 변환하여 `{"id": 123, "name": "홍길동"}` 반환

---

## 9. View 처리 방식

### **뷰 기반 응답 (@Controller)**

```java
@Controller
@RequestMapping("/users")
public class UserController {
    
    @GetMapping
    public String getUsers(Model model) {
        List<User> users = userService.findAll();
        model.addAttribute("users", users);
        return "users/list";  // 뷰 이름 반환
    }
}
```

**동작:**
- View Resolver가 `users/list`를 실제 뷰 파일로 변환
- Thymeleaf, JSP 등으로 렌더링
- HTML 응답 반환

### **데이터 기반 응답 (@RestController)**

```java
@RestController
@RequestMapping("/api/users")
public class UserRestController {
    
    @GetMapping
    public List<User> getUsers() {
        return userService.findAll();  // 데이터 직접 반환
    }
}
```

**동작:**
- `@ResponseBody`가 자동 적용
- Jackson이 객체를 JSON으로 변환
- JSON 응답 반환

---

## 요약

- **Spring MVC**는 스프링 프레임워크의 웹 계층을 담당하는 모듈로, 웹 요청을 처리하고 응답을 생성하는 구조화된 아키텍처를 제공합니다.
- **Dispatcher Servlet**은 모든 요청의 단일 진입점으로, 프론트 컨트롤러 패턴을 구현합니다.
- **요청 처리 흐름**: Dispatcher Servlet → Handler Mapping → Handler Adapter → Controller → View Resolver → 응답
- **주요 구성 요소**: Dispatcher Servlet, Handler Mapping, Handler Adapter, Controller, View Resolver
- **@Controller**: 뷰를 반환하는 전통적인 MVC 컨트롤러
- **@RestController**: JSON/XML 등 데이터를 반환하는 REST 컨트롤러
- **파라미터 바인딩**: @PathVariable, @RequestParam, @RequestBody, @RequestHeader 등
- **Spring MVC의 장점**: 코드 중복 제거, 비즈니스 로직에 집중, 유지보수 용이

---

### 설정 시 반드시 고려해야 할 파라미터

- **Dispatcher Servlet 설정**:
  - `spring.mvc.servlet.path`: Dispatcher Servlet의 경로 (기본값: `/`)
  - `spring.mvc.view.prefix`: 뷰 파일 경로 접두사 (예: `/WEB-INF/views/`)
  - `spring.mvc.view.suffix`: 뷰 파일 확장자 (예: `.jsp`)

- **컨트롤러 스캔 범위**:
  - `@ComponentScan`: 컨트롤러를 찾을 패키지 지정
  - 기본적으로 `@SpringBootApplication`이 있는 패키지와 하위 패키지 스캔

- **JSON 변환 설정**:
  - Jackson 라이브러리 자동 포함 (Spring Boot)
  - `application.yml`에서 날짜 형식 등 커스터마이징 가능

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "Spring MVC는 서블릿을 대체한다"는 생각은 잘못되었습니다. Spring MVC는 서블릿 위에 구축된 프레임워크로, Dispatcher Servlet이라는 서블릿을 사용합니다. 또한 "모든 요청을 Dispatcher Servlet이 처리한다"는 것도 정확하지 않습니다. 정적 리소스(이미지, CSS, JS 등)는 Dispatcher Servlet을 거치지 않고 직접 처리됩니다.

#### Spring MVC 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **요청 매핑 실패**: URL 패턴과 컨트롤러 메서드 매핑이 제대로 되지 않아 404 에러 발생
> - **파라미터 바인딩 오류**: @PathVariable, @RequestParam 등을 잘못 사용하여 데이터를 받지 못함
> - **뷰를 찾을 수 없음**: View Resolver 설정이 잘못되어 뷰 파일을 찾지 못함
> - **JSON 변환 실패**: Jackson 설정이 잘못되어 객체를 JSON으로 변환하지 못함
> - **순환 참조**: 컨트롤러와 서비스 간 순환 참조로 인한 빈 생성 실패

---

### 출처

- [Spring Framework - Web MVC](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc)
- [Spring Boot - Web Applications](https://docs.spring.io/spring-boot/docs/current/reference/html/web.html)

---

### 예상 꼬리질문정리

#### 1. Spring MVC의 핵심 컴포넌트는 무엇인가요?

- **Dispatcher Servlet**: 모든 요청의 중앙 컨트롤러로, 프론트 컨트롤러 패턴을 구현합니다.
- **Handler Mapping**: URL 요청을 보고 어떤 컨트롤러가 처리할지 결정합니다.
- **Handler Adapter**: 결정된 컨트롤러의 메서드를 실제로 호출할 수 있게 연결합니다.
- **Controller**: 개발자가 실제 비즈니스 로직을 작성하는 곳입니다.
- **View Resolver**: 실행 결과를 어떤 화면으로 보여줄지 결정합니다.

#### 2. Dispatcher Servlet의 동작 원리를 설명해주세요.

- Dispatcher Servlet은 모든 HTTP 요청을 받아 처리하는 프론트 컨트롤러입니다.
- 요청이 오면 Handler Mapping을 통해 적절한 컨트롤러를 찾고, Handler Adapter를 통해 컨트롤러 메서드를 호출합니다.
- 컨트롤러가 반환한 결과를 View Resolver를 통해 뷰로 변환하거나, @ResponseBody가 있으면 JSON으로 변환하여 응답합니다.

#### 3. @Controller와 @RestController의 차이는 무엇인가요?

- **@Controller**: 뷰를 반환하는 전통적인 MVC 컨트롤러입니다. 메서드가 뷰 이름을 반환하면 View Resolver가 해당 뷰를 렌더링합니다.
- **@RestController**: JSON/XML 등 데이터를 직접 반환하는 REST 컨트롤러입니다. `@Controller` + `@ResponseBody`의 조합으로, 반환값이 자동으로 JSON으로 변환됩니다.

#### 4. 요청 처리 흐름을 설명해주세요.

1. 클라이언트 요청 도착
2. Dispatcher Servlet이 요청 받음
3. Handler Mapping에서 적절한 핸들러 찾기
4. Handler Adapter가 핸들러 메서드 호출
5. Controller에서 비즈니스 로직 실행
6. View Resolver가 뷰 결정 (또는 JSON 변환)
7. 응답 생성 및 반환

#### 5. 파라미터 바인딩 방법에는 어떤 것들이 있나요?

- **@PathVariable**: URL 경로에서 변수 추출 (예: `/users/{id}`)
- **@RequestParam**: 쿼리 파라미터 추출 (예: `?name=홍길동`)
- **@RequestBody**: 요청 본문을 객체로 변환 (JSON → 객체)
- **@RequestHeader**: HTTP 헤더 값 추출
- **@ModelAttribute**: 폼 데이터를 객체로 바인딩

#### 6. 프론트 컨트롤러 패턴이란 무엇인가요?

- 프론트 컨트롤러 패턴은 모든 요청을 단일 진입점에서 받아 처리하는 디자인 패턴입니다.
- Spring MVC에서는 Dispatcher Servlet이 프론트 컨트롤러 역할을 합니다.
- 장점: 중앙 집중식 요청 처리, 공통 로직을 한 곳에서 처리, 코드 재사용성 향상

#### 7. Handler Mapping과 Handler Adapter의 차이는 무엇인가요?

- **Handler Mapping**: 요청 URL을 분석하여 어떤 컨트롤러 메서드가 처리할지 결정합니다. "어떤 핸들러를 사용할까?"를 결정합니다.
- **Handler Adapter**: Handler Mapping에서 찾은 핸들러를 실제로 실행할 수 있도록 연결합니다. "핸들러를 어떻게 실행할까?"를 담당합니다.

#### 8. View Resolver는 어떤 역할을 하나요?

- View Resolver는 컨트롤러가 반환한 뷰 이름을 실제 뷰 객체로 변환합니다.
- 예를 들어, 컨트롤러가 `"users/list"`를 반환하면, View Resolver가 이를 `/WEB-INF/views/users/list.jsp`로 변환합니다.
- Thymeleaf, JSP, FreeMarker 등 다양한 뷰 기술을 지원합니다.

---

## 관련 노트

- **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]** - Spring MVC가 속한 스프링 프레임워크의 전체 구조
- **[[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]** - Spring MVC에서 사용하는 의존성 주입 원리
- **[[RESTful API (REST API)|RESTful API]]** - Spring MVC를 활용한 REST API 개발

---

**태그:** #spring #mvc #spring-mvc #dispatcher-servlet #web #면접 #개념정리
