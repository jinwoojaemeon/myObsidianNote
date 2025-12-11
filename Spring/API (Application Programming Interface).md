# API (Application Programming Interface)

#spring #api #application-programming-interface #웹개발 #면접 #개념정리

**관련 개념:** [[RESTful API (REST API)|RESTful API]] [[HTTP]] [[웹 서비스]]

> [!note] 이어서 읽기
> API를 이해하려면 **[[RESTful API (REST API)|RESTful API]]**와 **[[HTTP]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

API의 정의, 종류, 동작 원리, 그리고 실무에서의 활용 방법을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- API가 없으면 각 애플리케이션이 독립적으로 동작해야 하며, 데이터나 기능을 공유하기 어렵습니다. 서로 다른 시스템 간 통신이 불가능하여 데이터 중복이 발생하고, 새로운 기능을 추가할 때마다 전체 시스템을 수정해야 하며, 개발 생산성이 크게 저하됩니다.

- API는 애플리케이션 간 통신을 표준화된 방식으로 제공하여 개발 생산성을 크게 향상시킵니다. 기존 시스템의 기능을 재사용할 수 있고, 모듈화된 개발이 가능하며, 다양한 플랫폼과 통합이 용이합니다. 또한 마이크로서비스 아키텍처의 기반이 되어 확장성과 유지보수성을 높입니다.

- API의 기본 개념과 종류, RESTful API와 GraphQL 등 다양한 API 스타일, 그리고 실무에서의 API 설계와 활용 능력을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**API(Application Programming Interface)**는 애플리케이션 간 통신을 위한 인터페이스입니다. 소프트웨어 컴포넌트가 서로 상호작용할 수 있도록 정의된 규칙과 프로토콜의 집합입니다.

**핵심 특징**:
- 표준화된 통신 방식
- 구현 세부사항 숨김
- 재사용 가능한 기능 제공
- 다양한 플랫폼 지원

> [!tip] 핵심 포인트
> API는 "어떻게 구현했는지"가 아니라 "무엇을 할 수 있는지"를 정의합니다. 클라이언트는 내부 구현을 알 필요 없이 API를 통해 기능을 사용할 수 있습니다.

---

#### 1. API의 기본 개념

##### 1.1 API란?

**API**는 두 개 이상의 소프트웨어 컴포넌트가 서로 통신하기 위한 인터페이스입니다.

**비유**:
- 레스토랑의 메뉴판: 손님(클라이언트)은 요리 방법(구현)을 몰라도 메뉴(API)를 통해 주문(요청)할 수 있음
- 리모컨: TV 내부 구조(구현)를 몰라도 버튼(API)을 통해 기능을 사용할 수 있음

##### 1.2 API의 역할

**1. 추상화**
- 내부 구현을 숨기고 필요한 기능만 노출
- 클라이언트는 구현 세부사항을 알 필요 없음

**2. 표준화**
- 일관된 방식으로 통신
- 다양한 플랫폼 간 호환성

**3. 재사용성**
- 한 번 구현한 기능을 여러 곳에서 사용
- 코드 중복 방지

**4. 모듈화**
- 독립적인 컴포넌트 개발
- 유지보수 용이

---

#### 2. API의 종류

##### 2.1 웹 API (Web API)

**웹 API**는 HTTP 프로토콜을 사용하여 웹을 통해 제공되는 API입니다.

**특징**:
- HTTP/HTTPS 프로토콜 사용
- JSON, XML 등으로 데이터 교환
- REST, GraphQL 등 다양한 스타일

**예시**:
```http
GET https://api.example.com/users/1
Content-Type: application/json

Response:
{
  "id": 1,
  "name": "홍길동",
  "email": "hong@example.com"
}
```

##### 2.2 라이브러리 API

**라이브러리 API**는 프로그래밍 언어의 라이브러리에서 제공하는 함수나 클래스입니다.

**예시**:
```java
// Java Collections API
List<String> list = new ArrayList<>();
list.add("item");
list.get(0);

// Java String API
String str = "Hello";
str.length();
str.substring(0, 3);
```

##### 2.3 운영체제 API

**운영체제 API**는 운영체제가 제공하는 시스템 호출입니다.

**예시**:
- 파일 읽기/쓰기
- 네트워크 통신
- 프로세스 관리

##### 2.4 데이터베이스 API

**데이터베이스 API**는 데이터베이스와 상호작용하기 위한 인터페이스입니다.

**예시**:
- JDBC (Java Database Connectivity)
- JPA (Java Persistence API)
- Hibernate

---

#### 3. 웹 API의 주요 스타일

##### 3.1 RESTful API

**RESTful API**는 REST 원칙을 따르는 웹 API입니다.

**특징**:
- 리소스 중심 설계
- HTTP 메서드 활용 (GET, POST, PUT, DELETE)
- 무상태성
- 표준화된 인터페이스

**예시**:
```http
GET /api/users/1          # 사용자 조회
POST /api/users           # 사용자 생성
PUT /api/users/1          # 사용자 수정
DELETE /api/users/1       # 사용자 삭제
```

**자세한 내용**: [[RESTful API (REST API)|RESTful API]] 참고

##### 3.2 GraphQL API

**GraphQL**은 클라이언트가 필요한 데이터만 요청할 수 있는 쿼리 언어입니다.

**특징**:
- 단일 엔드포인트
- 클라이언트가 필요한 필드만 선택
- 타입 시스템 제공
- 강력한 도구 지원

**예시**:
```graphql
# 쿼리
query {
  user(id: 1) {
    name
    email
    posts {
      title
      comments {
        content
      }
    }
  }
}
```

**REST vs GraphQL**:

| 구분 | REST | GraphQL |
| :--- | :--- | :------ |
| **엔드포인트** | 여러 개 | 단일 |
| **데이터 요청** | 고정된 구조 | 필요한 필드만 |
| **오버페칭** | 발생 가능 | 없음 |
| **언더페칭** | 발생 가능 | 없음 |
| **학습 곡선** | 낮음 | 높음 |

##### 3.3 SOAP API

**SOAP(Simple Object Access Protocol)**은 XML 기반의 프로토콜입니다.

**특징**:
- XML 메시지 형식
- WSDL로 인터페이스 정의
- 강력한 보안 기능
- 복잡한 구조

**사용 시나리오**:
- 엔터프라이즈 환경
- 높은 보안이 필요한 경우
- 복잡한 비즈니스 로직

##### 3.4 gRPC API

**gRPC**는 Google이 개발한 고성능 RPC 프레임워크입니다.

**특징**:
- Protocol Buffers 사용
- HTTP/2 기반
- 높은 성능
- 스트리밍 지원

**사용 시나리오**:
- 마이크로서비스 간 통신
- 높은 성능이 필요한 경우
- 실시간 스트리밍

---

#### 4. API의 동작 원리

##### 4.1 클라이언트-서버 통신

```
클라이언트                    서버
   │                          │
   │  1. API 요청 (Request)    │
   ├─────────────────────────>│
   │                          │
   │                          │ 2. 요청 처리
   │                          │    (비즈니스 로직)
   │                          │
   │  3. API 응답 (Response)   │
   │<─────────────────────────┤
   │                          │
```

**요청(Request)**:
- HTTP 메서드 (GET, POST 등)
- URI (엔드포인트)
- 헤더 (인증 정보 등)
- 본문 (데이터)

**응답(Response)**:
- 상태 코드 (200, 404 등)
- 헤더
- 본문 (데이터)

##### 4.2 API 요청 예시

```http
# HTTP 요청
GET /api/users/1 HTTP/1.1
Host: api.example.com
Authorization: Bearer token123
Accept: application/json

# HTTP 응답
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 1,
  "name": "홍길동",
  "email": "hong@example.com"
}
```

##### 4.3 Spring Boot에서의 API 구현

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping("/{id}")
    public ResponseEntity<UserResponse> getUser(@PathVariable Long id) {
        User user = userService.findById(id);
        return ResponseEntity.ok(UserResponse.from(user));
    }
    
    @PostMapping
    public ResponseEntity<UserResponse> createUser(
            @RequestBody UserCreateRequest request) {
        User user = userService.create(request);
        return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(UserResponse.from(user));
    }
}
```

---

#### 5. API 설계 원칙

##### 5.1 명확성

- API의 목적과 사용법이 명확해야 합니다.
- 일관된 네이밍 규칙 사용
- 명확한 에러 메시지

##### 5.2 일관성

- 동일한 패턴과 규칙 적용
- 예측 가능한 동작
- 표준화된 응답 형식

##### 5.3 버전 관리

- API 버전을 명시하여 하위 호환성 유지
- URI에 버전 포함: `/api/v1/users`
- 또는 헤더에 버전 포함

##### 5.4 보안

- 인증/인가 메커니즘
- HTTPS 사용
- 입력 검증
- 민감한 정보 보호

##### 5.5 문서화

- API 문서 제공 (Swagger, OpenAPI 등)
- 사용 예시 포함
- 에러 코드 설명

---

#### 6. API 사용 시나리오

##### 6.1 마이크로서비스 아키텍처

```
[사용자 서비스]  ←→  [주문 서비스]  ←→  [결제 서비스]
      │                    │                    │
      └────────────────────┴────────────────────┘
                    API Gateway
```

**특징**:
- 각 서비스가 독립적으로 개발/배포
- API를 통한 서비스 간 통신
- 서비스별로 다른 기술 스택 사용 가능

##### 6.2 모바일 앱과 백엔드 통신

```
[모바일 앱]  ←→  [REST API]  ←→  [백엔드 서버]
```

**특징**:
- iOS, Android 앱이 동일한 API 사용
- 백엔드 변경 시 앱 수정 최소화
- 버전 관리로 하위 호환성 유지

##### 6.3 서드파티 통합

```
[내 애플리케이션]  ←→  [외부 API]
  - 결제 API (PG사)
  - 지도 API (카카오맵, 네이버맵)
  - 소셜 로그인 API (카카오, 네이버)
```

**특징**:
- 외부 서비스의 기능 활용
- 빠른 개발
- 전문 서비스 활용

---

### **Interview Answer Version (면접 답변식 요약)**

API(Application Programming Interface)는 애플리케이션 간 통신을 위한 인터페이스로, 소프트웨어 컴포넌트가 서로 상호작용할 수 있도록 정의된 규칙과 프로토콜의 집합입니다.

API는 내부 구현을 숨기고 필요한 기능만 노출하여 추상화를 제공하며, 표준화된 방식으로 통신하여 다양한 플랫폼 간 호환성을 보장합니다. 또한 한 번 구현한 기능을 여러 곳에서 재사용할 수 있어 개발 생산성을 높입니다.

웹 API는 HTTP 프로토콜을 사용하며, RESTful API는 리소스 중심 설계와 HTTP 메서드를 활용하고, GraphQL은 클라이언트가 필요한 데이터만 요청할 수 있는 쿼리 언어입니다.

API 설계 시 명확성, 일관성, 버전 관리, 보안, 문서화를 고려해야 하며, 마이크로서비스 아키텍처, 모바일 앱과 백엔드 통신, 서드파티 통합 등 다양한 시나리오에서 활용됩니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### API 사용 시 주의사항

**1. 에러 처리**
```java
// ✅ 좋은 예: 명확한 에러 응답
@ExceptionHandler(UserNotFoundException.class)
public ResponseEntity<ErrorResponse> handleUserNotFound(
        UserNotFoundException ex) {
    ErrorResponse error = new ErrorResponse(
        "USER_NOT_FOUND",
        "사용자를 찾을 수 없습니다.",
        ex.getMessage()
    );
    return ResponseEntity
        .status(HttpStatus.NOT_FOUND)
        .body(error);
}
```

**2. 버전 관리**
```java
// URI에 버전 포함
@RestController
@RequestMapping("/api/v1/users")
public class UserControllerV1 {
    // ...
}

@RestController
@RequestMapping("/api/v2/users")
public class UserControllerV2 {
    // ...
}
```

**3. 인증/인가**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping("/{id}")
    @PreAuthorize("hasRole('USER')")
    public ResponseEntity<UserResponse> getUser(
            @PathVariable Long id,
            @AuthenticationPrincipal UserDetails userDetails) {
        // 인증된 사용자만 접근 가능
        // ...
    }
}
```

#### 실무 활용 예시

**1. API 문서화 (Swagger)**
```java
@RestController
@Api(tags = "사용자 관리 API")
public class UserController {
    
    @GetMapping("/{id}")
    @ApiOperation(value = "사용자 조회", notes = "ID로 사용자를 조회합니다")
    @ApiResponses({
        @ApiResponse(code = 200, message = "성공"),
        @ApiResponse(code = 404, message = "사용자를 찾을 수 없음")
    })
    public ResponseEntity<UserResponse> getUser(
            @ApiParam(value = "사용자 ID", required = true) @PathVariable Long id) {
        // ...
    }
}
```

**2. API 클라이언트 구현**
```java
// RestTemplate 사용
RestTemplate restTemplate = new RestTemplate();
String url = "https://api.example.com/users/1";
UserResponse response = restTemplate.getForObject(url, UserResponse.class);

// WebClient 사용 (비동기)
WebClient webClient = WebClient.create("https://api.example.com");
Mono<UserResponse> response = webClient.get()
    .uri("/users/1")
    .retrieve()
    .bodyToMono(UserResponse.class);
```

**3. API 테스트**
```java
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Test
    void getUserTest() throws Exception {
        mockMvc.perform(get("/api/users/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("홍길동"));
    }
}
```

#### API 성능 최적화

**1. 캐싱**
```java
@GetMapping("/{id}")
@Cacheable(value = "users", key = "#id")
public ResponseEntity<UserResponse> getUser(@PathVariable Long id) {
    // 캐시에서 먼저 조회, 없으면 DB 조회
}
```

**2. 페이지네이션**
```java
@GetMapping
public ResponseEntity<Page<UserResponse>> getUsers(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size) {
    Page<User> users = userService.findAll(PageRequest.of(page, size));
    return ResponseEntity.ok(users.map(UserResponse::from));
}
```

**3. 필드 선택**
```java
@GetMapping("/{id}")
public ResponseEntity<UserResponse> getUser(
        @PathVariable Long id,
        @RequestParam(required = false) String fields) {
    // fields 파라미터로 필요한 필드만 반환
}
```

---

### 예상 꼬리질문정리

1. **API와 라이브러리의 차이는 무엇인가요?**
   - API는 인터페이스로, 두 컴포넌트 간 통신을 위한 규칙입니다. 라이브러리는 재사용 가능한 코드 모음으로, API를 포함할 수 있습니다. API는 "무엇을 할 수 있는지"를 정의하고, 라이브러리는 "어떻게 구현했는지"를 포함합니다.

2. **RESTful API와 GraphQL의 차이는 무엇인가요?**
   - RESTful API는 여러 엔드포인트로 리소스를 관리하며 고정된 응답 구조를 가집니다. GraphQL은 단일 엔드포인트로 쿼리를 통해 필요한 데이터만 요청할 수 있습니다. REST는 표준화된 인터페이스가 장점이고, GraphQL은 유연한 데이터 요청이 장점입니다.

3. **API 버전 관리는 왜 필요한가요?**
   - API를 변경할 때 기존 클라이언트와의 호환성을 유지하기 위해 필요합니다. 버전을 명시하지 않으면 기존 클라이언트가 동작하지 않을 수 있으며, 버전 관리로 점진적인 마이그레이션이 가능합니다.

4. **API 설계 시 가장 중요한 원칙은 무엇인가요?**
   - 명확성과 일관성이 가장 중요합니다. API의 목적과 사용법이 명확해야 하고, 일관된 패턴과 규칙을 적용해야 합니다. 또한 버전 관리, 보안, 문서화도 중요합니다.

5. **RESTful API에서 PUT과 PATCH의 차이는 무엇인가요?**
   - PUT은 리소스 전체를 수정하며 멱등성을 가지며, 리소스가 없으면 생성합니다. PATCH는 리소스 일부만 수정하며 멱등성을 보장하지 않습니다. PUT은 모든 필드를 제공해야 하고, PATCH는 수정할 필드만 제공하면 됩니다.

6. **API 인증 방식에는 어떤 것들이 있나요?**
   - Basic Authentication, Bearer Token (JWT), OAuth 2.0, API Key 등이 있습니다. JWT는 무상태 인증에 적합하고, OAuth 2.0은 서드파티 인증에 사용됩니다.

7. **API 응답 형식을 표준화하는 이유는 무엇인가요?**
   - 클라이언트가 일관된 방식으로 응답을 처리할 수 있게 하기 위함입니다. 성공/실패 여부, 에러 메시지, 데이터 구조를 표준화하면 클라이언트 개발이 용이해집니다.

8. **API Rate Limiting이란 무엇인가요?**
   - API 호출 횟수를 제한하는 것으로, 서버 부하를 방지하고 남용을 막기 위해 사용합니다. 예를 들어 분당 100회, 일일 10,000회 등으로 제한할 수 있습니다.

9. **마이크로서비스에서 API Gateway의 역할은 무엇인가요?**
   - 여러 마이크로서비스의 API를 단일 진입점으로 제공하고, 라우팅, 인증/인가, 로드 밸런싱, 모니터링 등의 기능을 제공합니다. 클라이언트는 각 서비스의 위치를 알 필요 없이 API Gateway를 통해 통신합니다.

10. **API 테스트는 어떻게 하나요?**
    - 단위 테스트, 통합 테스트, E2E 테스트를 수행합니다. Postman, RestAssured, MockMvc 등의 도구를 사용하며, 자동화된 테스트를 통해 API의 안정성을 보장합니다.

---

## 관련 노트

- **[[RESTful API (REST API)|RESTful API]]** - REST 원칙을 따르는 웹 API
- **[[HTTP]]** - API가 기반으로 하는 HTTP 프로토콜
- **[[웹 서비스]]** - API를 포함한 웹 서비스 아키텍처

---

**태그:** #spring #api #application-programming-interface #웹개발 #면접 #개념정리

