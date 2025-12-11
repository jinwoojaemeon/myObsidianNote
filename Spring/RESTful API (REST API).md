# RESTful API (REST API)

#spring #rest #restful #api #http #웹개발 #면접 #개념정리

**관련 개념:** [[HTTP]] [[웹 서비스]] [[API 설계]]

> [!note] 이어서 읽기
> RESTful API를 이해하려면 **[[HTTP]]**의 메서드와 상태 코드에 대한 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

RESTful API의 정의, REST의 6가지 아키텍처 원칙, RESTful API 설계 규칙, 그리고 실무에서의 활용 방법을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- RESTful API가 없으면 각 개발자나 팀마다 다른 API 설계 방식을 사용하여 일관성이 없고, 클라이언트가 API를 이해하고 사용하기 어렵습니다. 비표준적인 API는 학습 비용이 높고, 문서화가 어려우며, 유지보수가 복잡해집니다. 또한 HTTP의 장점을 활용하지 못하여 불필요한 복잡성이 추가됩니다.

- RESTful API는 표준화된 설계 원칙을 제공하여 개발자 간 소통을 원활하게 하고, HTTP의 장점을 최대한 활용합니다. 리소스를 URI로 명확하게 표현하고, HTTP 메서드로 행위를 표현하여 직관적이고 이해하기 쉬운 API를 만들 수 있습니다. 또한 무상태성과 캐시 처리로 확장성과 성능을 향상시킬 수 있으며, 계층화 구조로 보안과 트래픽 분산이 가능합니다.

- RESTful API 설계 원칙 이해, HTTP 메서드와 상태 코드 활용, 실무에서의 RESTful API 구현 능력, 그리고 REST 원칙의 실용적 적용 능력을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**RESTful API**는 REST(REpresentational State Transfer)의 원칙을 따라 설계된 API입니다. 리소스(자원)를 URI에 명확한 규칙으로 표현하고, 클라이언트가 HTTP 메서드를 통해 CRUD 작업을 수행할 수 있도록 설계된 API 아키텍처 스타일입니다.

**핵심 특징**:
- 리소스 중심 설계: 자원(Resource)을 URI로 표현
- HTTP 메서드 활용: GET, POST, PUT, DELETE 등으로 행위 표현
- 무상태성: 서버가 클라이언트 상태를 저장하지 않음
- 표준화된 인터페이스: 일관된 설계 규칙

---

#### 1. REST의 6가지 아키텍처 원칙

##### 1.1 클라이언트-서버 구조 (Client-Server)

**원칙**: UI와 데이터 저장을 명확히 분리하여 역할을 나누어 독립성을 유지합니다.

**이점**:
- 개발 생산성 증가: 클라이언트와 서버를 독립적으로 개발 가능
- 유지보수 편함: 각각의 변경이 다른 쪽에 영향을 주지 않음
- 독립적 확장 가능: 클라이언트와 서버를 각각 확장 가능

**예시**:
```
클라이언트 (웹/모바일 앱) ←→ 서버 (API)
- 클라이언트: UI 로직, 사용자 인터랙션
- 서버: 비즈니스 로직, 데이터 저장
```

##### 1.2 무상태성 (Stateless)

**원칙**: 요청 간에 클라이언트 상태를 서버에 저장하지 않습니다. 각 요청은 독립적이며 필요한 모든 정보를 포함해야 합니다.

**이점**:
- 서버 확장성 극대화: 서버가 상태를 저장하지 않아 여러 서버로 분산 가능
- 장애 상황에서 안정적: 서버 장애 시 다른 서버가 요청 처리 가능
- 단순성: 서버 로직이 단순해짐

**예시**:
```http
# ❌ 나쁜 예: 상태 저장 (세션 사용)
GET /users/1
Cookie: sessionId=abc123  # 서버가 세션 상태 저장

# ✅ 좋은 예: 무상태 (토큰 사용)
GET /users/1
Authorization: Bearer token123  # 모든 정보가 요청에 포함
```

##### 1.3 캐시 처리 가능 (Cacheable)

**원칙**: 응답에 캐시 여부를 명시하여 성능 향상이 가능합니다.

**이점**:
- 네트워크 비용 감소: 동일한 요청을 캐시에서 처리
- 성능 극대화: 응답 시간 단축
- 서버 부하 감소: 불필요한 요청 감소

**예시**:
```http
# 캐시 가능한 응답
GET /users/1
Cache-Control: max-age=3600  # 1시간 캐시

# 캐시 불가능한 응답
GET /users/1
Cache-Control: no-cache  # 캐시하지 않음
```

##### 1.4 계층화 구조 (Layered System)

**원칙**: 중간 서버를 둘 수 있습니다 (로드 밸런서, 프록시, 게이트웨이 등).

**이점**:
- 보안 향상: 방화벽, 인증 서버 등 중간 계층 추가 가능
- 확장성 향상: 로드 밸런서로 트래픽 분산
- 트래픽 분산: 여러 서버로 요청 분산

**예시**:
```
클라이언트
  ↓
로드 밸런서
  ↓
API 게이트웨이
  ↓
인증 서버
  ↓
애플리케이션 서버
  ↓
데이터베이스
```

##### 1.5 인터페이스 일관성 (Uniform Interface)

**원칙**: URI, 메서드 사용 등 설계 규칙이 명확하고 일관적입니다.

**이점**:
- 학습 비용 감소: 표준화된 규칙으로 쉽게 이해 가능
- API 품질 향상: 일관된 설계로 예측 가능
- 실수 감소: 명확한 규칙으로 실수 방지

**구성 요소**:
- 리소스 식별: URI로 리소스 명확히 표현
- 리소스 조작: HTTP 메서드로 행위 표현
- 자기 서술적 메시지: 요청/응답에 필요한 정보 포함
- 하이퍼미디어: 리소스 간 관계 표현 (HATEOAS)

##### 1.6 Code on Demand (선택적)

**원칙**: 서버가 클라이언트로 코드를 전송할 수 있습니다 (자바스크립트 등).

**이점**:
- 필요 시 클라이언트 기능 확장: 동적으로 기능 추가 가능

**참고**: 실제로는 거의 사용되지 않는 선택적 원칙입니다.

---

#### 2. RESTful API 설계 규칙

##### 2.1 URI 설계 규칙

**핵심 원칙**:
- 리소스는 명사로 표현
- 복수형 사용 권장
- 계층 구조 표현
- 소문자 사용
- 하이픈(-) 사용 (언더스코어 _ 사용 지양)

**예시**:
```http
# ✅ 좋은 예
GET /users                    # 사용자 목록
GET /users/1                  # 특정 사용자
GET /users/1/posts             # 사용자의 게시글 목록
GET /users/1/posts/123         # 특정 게시글
GET /users/1/posts/123/comments  # 게시글의 댓글 목록

# ❌ 나쁜 예
GET /getUsers                 # 동사 사용
GET /user                     # 단수형
GET /users/1/getPosts         # 동사 사용
GET /users_1_posts            # 언더스코어 사용
```

##### 2.2 HTTP 메서드와 의미

| 메서드 | 의미 | 멱등성 | 안전성 | 예시 URI |
|--------|------|--------|--------|----------|
| **GET** | 자원 조회 | ✅ | ✅ | `GET /users/1` |
| **POST** | 자원 생성 | ❌ | ❌ | `POST /users` |
| **PUT** | 자원 전체 수정 (없으면 생성) | ✅ | ❌ | `PUT /users/1` |
| **PATCH** | 자원 일부 수정 | ❌ | ❌ | `PATCH /users/1` |
| **DELETE** | 자원 삭제 | ✅ | ❌ | `DELETE /users/1` |

**멱등성(Idempotent)**: 동일한 요청을 여러 번 수행해도 결과가 동일함
**안전성(Safe)**: 서버의 상태를 변경하지 않음

**PUT vs PATCH**:
```http
# PUT: 전체 수정 (모든 필드 필요)
PUT /users/1
{
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 30
}

# PATCH: 일부 수정 (수정할 필드만)
PATCH /users/1
{
  "age": 31
}
```

##### 2.3 HTTP 상태 코드

**주요 상태 코드**:

| 상태 코드 | 의미 | 사용 시나리오 |
|-----------|------|--------------|
| **200 OK** | 성공 | 조회, 수정, 삭제 성공 |
| **201 Created** | 생성 성공 | POST로 리소스 생성 성공 |
| **204 No Content** | 성공 (응답 본문 없음) | 삭제 성공, 수정 성공 (응답 불필요) |
| **400 Bad Request** | 잘못된 요청 | 요청 파라미터 오류 |
| **401 Unauthorized** | 인증 필요 | 인증 토큰 없음 |
| **403 Forbidden** | 권한 없음 | 인증은 되었으나 권한 없음 |
| **404 Not Found** | 리소스 없음 | 존재하지 않는 리소스 |
| **409 Conflict** | 충돌 | 중복 생성, 동시 수정 등 |
| **500 Internal Server Error** | 서버 오류 | 서버 내부 오류 |

**예시**:
```http
# 성공
GET /users/1
200 OK
{
  "id": 1,
  "name": "홍길동"
}

# 생성 성공
POST /users
201 Created
Location: /users/2
{
  "id": 2,
  "name": "김철수"
}

# 리소스 없음
GET /users/999
404 Not Found
{
  "message": "User not found"
}
```

##### 2.4 표현 (Representation)

**요청/응답 포맷**: 일반적으로 JSON 사용

**예시**:
```json
// 요청
POST /users
Content-Type: application/json
{
  "name": "홍길동",
  "email": "hong@example.com"
}

// 응답
200 OK
Content-Type: application/json
{
  "id": 1,
  "name": "홍길동",
  "email": "hong@example.com",
  "createdAt": "2024-01-01T00:00:00Z"
}
```

---

#### 3. RESTful API 설계 예시

##### 3.1 사용자 관리 API

```http
# 사용자 목록 조회
GET /users
200 OK
[
  { "id": 1, "name": "홍길동" },
  { "id": 2, "name": "김철수" }
]

# 특정 사용자 조회
GET /users/1
200 OK
{
  "id": 1,
  "name": "홍길동",
  "email": "hong@example.com"
}

# 사용자 생성
POST /users
Content-Type: application/json
{
  "name": "이영희",
  "email": "lee@example.com"
}
201 Created
Location: /users/3
{
  "id": 3,
  "name": "이영희",
  "email": "lee@example.com"
}

# 사용자 수정 (전체)
PUT /users/1
Content-Type: application/json
{
  "name": "홍길동",
  "email": "hong.new@example.com",
  "age": 30
}
200 OK

# 사용자 수정 (일부)
PATCH /users/1
Content-Type: application/json
{
  "age": 31
}
200 OK

# 사용자 삭제
DELETE /users/1
204 No Content
```

##### 3.2 중첩 리소스 API

```http
# 사용자의 게시글 목록
GET /users/1/posts
200 OK
[
  { "id": 1, "title": "첫 번째 게시글" },
  { "id": 2, "title": "두 번째 게시글" }
]

# 특정 게시글 조회
GET /users/1/posts/1
200 OK
{
  "id": 1,
  "title": "첫 번째 게시글",
  "content": "내용...",
  "userId": 1
}

# 게시글 생성
POST /users/1/posts
Content-Type: application/json
{
  "title": "새 게시글",
  "content": "내용..."
}
201 Created
Location: /users/1/posts/3
```

##### 3.3 필터링, 정렬, 페이지네이션

```http
# 필터링
GET /users?role=admin
GET /users?age=30

# 정렬
GET /users?sort=name,asc
GET /users?sort=createdAt,desc

# 페이지네이션
GET /users?page=1&size=10
GET /users?offset=0&limit=10

# 조합
GET /users?role=admin&sort=name,asc&page=1&size=10
```

---

#### 4. REST 원칙의 실용적 적용

##### 4.1 REST 원칙을 반드시 지켜야 할까?

REST의 창시자 로이 필딩(Roy Fielding)은 다음과 같이 언급했습니다:

> "시스템 전체를 통제할 수 있다고 생각하거나, 진화에 관심이 없다면, REST에 대해 따지느라 시간을 낭비하지마라."

**핵심 메시지**:
- 모든 것에 동일한 설계 패턴을 적용하는 것이 효율적이고 완전하지는 않습니다
- 핵심은 다수의 개발자가 이해하기 쉬운 RESTful API를 만드는 것입니다
- 원활한 소통을 가능하게 하는 것이 목적입니다

##### 4.2 실무에서의 유연한 적용

**완벽한 REST를 지키지 않아도 되는 경우**:
- 특정 비즈니스 로직이 REST 원칙과 맞지 않는 경우
- 성능 최적화가 필요한 경우
- 레거시 시스템과의 호환성이 필요한 경우

**예시: GraphQL vs REST**
```http
# REST: 여러 요청 필요
GET /users/1
GET /users/1/posts
GET /users/1/posts/1/comments

# GraphQL: 한 번의 요청으로 필요한 데이터만
POST /graphql
{
  "query": "{
    user(id: 1) {
      name
      posts {
        title
        comments {
          content
        }
      }
    }
  }"
}
```

**실무 권장사항**:
- 기본적으로 REST 원칙을 따르되, 필요시 유연하게 적용
- 팀 내 일관성 유지가 가장 중요
- 클라이언트 개발자와의 소통을 우선시
- 문서화를 통해 명확한 가이드 제공

---

### **Interview Answer Version (면접 답변식 요약)**

RESTful API는 REST의 원칙을 따라 설계된 API로, 리소스를 URI에 명확한 규칙으로 표현하고 HTTP 메서드로 CRUD 작업을 수행할 수 있도록 설계된 아키텍처 스타일입니다.

REST의 6가지 원칙은 클라이언트-서버 구조, 무상태성, 캐시 처리 가능, 계층화 구조, 인터페이스 일관성, Code on Demand입니다. 이 중 무상태성과 인터페이스 일관성이 가장 중요하며, 무상태성은 서버 확장성을 높이고, 인터페이스 일관성은 학습 비용을 낮춥니다.

RESTful API 설계 시 URI는 명사로 표현하고 복수형을 사용하며, HTTP 메서드로 행위를 표현합니다. GET은 조회, POST는 생성, PUT은 전체 수정, PATCH는 일부 수정, DELETE는 삭제에 사용합니다.

상태 코드를 통해 요청 결과를 명확히 전달하며, 일반적으로 JSON 형식으로 데이터를 주고받습니다. REST 원칙을 완벽하게 지키지 않아도 되지만, 팀 내 일관성과 클라이언트 개발자와의 소통을 우선시하는 것이 중요합니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### RESTful API 설계 시 주의사항

**1. 동사 사용 지양**
```http
# ❌ 나쁜 예
GET /getUsers
POST /createUser
DELETE /removeUser

# ✅ 좋은 예
GET /users
POST /users
DELETE /users/1
```

**2. 리소스 계층 구조 명확히**
```http
# ✅ 좋은 예: 명확한 계층 구조
GET /users/1/posts/123/comments

# ❌ 나쁜 예: 불명확한 구조
GET /users-posts-comments?userId=1&postId=123
```

**3. 적절한 HTTP 메서드 사용**
```http
# ❌ 나쁜 예: GET으로 삭제
GET /users/1/delete

# ✅ 좋은 예: DELETE 메서드 사용
DELETE /users/1
```

**4. 상태 코드 적절히 사용**
```http
# ❌ 나쁜 예: 모든 응답이 200 OK
200 OK
{
  "success": false,
  "message": "User not found"
}

# ✅ 좋은 예: 적절한 상태 코드
404 Not Found
{
  "message": "User not found"
}
```

#### 실무 활용 예시

**1. Spring Boot에서 RESTful API 구현**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping
    public ResponseEntity<List<User>> getUsers() {
        return ResponseEntity.ok(userService.findAll());
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        return userService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User created = userService.save(user);
        return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(created);
    }
    
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(
            @PathVariable Long id, 
            @RequestBody User user) {
        return ResponseEntity.ok(userService.update(id, user));
    }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

**2. 필터링과 페이지네이션**
```java
@GetMapping
public ResponseEntity<Page<User>> getUsers(
        @RequestParam(required = false) String role,
        @RequestParam(required = false) Integer age,
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size) {
    Page<User> users = userService.findByFilters(role, age, page, size);
    return ResponseEntity.ok(users);
}
```

**3. 에러 처리**
```java
@ExceptionHandler(UserNotFoundException.class)
public ResponseEntity<ErrorResponse> handleUserNotFound(
        UserNotFoundException ex) {
    ErrorResponse error = new ErrorResponse(
        "USER_NOT_FOUND",
        ex.getMessage()
    );
    return ResponseEntity
        .status(HttpStatus.NOT_FOUND)
        .body(error);
}
```

#### RESTful API 문서화

**Swagger/OpenAPI 사용**:
```java
@RestController
@Api(tags = "사용자 관리 API")
public class UserController {
    
    @GetMapping
    @ApiOperation(value = "사용자 목록 조회", notes = "모든 사용자 목록을 조회합니다")
    public ResponseEntity<List<User>> getUsers() {
        // ...
    }
}
```

---

### 예상 꼬리질문정리

1. **RESTful API와 REST API의 차이는 무엇인가요?**
   - RESTful API는 REST 원칙을 잘 따르는 API를 의미하며, REST API는 REST 아키텍처 스타일을 따르는 API를 의미합니다. 일반적으로 두 용어를 혼용하여 사용하지만, RESTful은 "REST 원칙을 잘 따르는" 의미로 더 엄격한 의미입니다.

2. **무상태성(Stateless)이 왜 중요한가요?**
   - 서버가 클라이언트 상태를 저장하지 않아 여러 서버로 분산이 가능하고, 서버 장애 시 다른 서버가 요청을 처리할 수 있습니다. 또한 서버 로직이 단순해져 유지보수가 쉬워집니다.

3. **PUT과 PATCH의 차이는 무엇인가요?**
   - PUT은 리소스 전체를 수정하며, 모든 필드를 제공해야 합니다. 멱등성을 가지며, 리소스가 없으면 생성합니다. PATCH는 리소스 일부만 수정하며, 수정할 필드만 제공하면 됩니다. 멱등성을 보장하지 않습니다.

4. **멱등성(Idempotent)이란 무엇인가요?**
   - 동일한 요청을 여러 번 수행해도 결과가 동일한 성질입니다. GET, PUT, DELETE는 멱등성을 가지지만, POST, PATCH는 멱등성을 보장하지 않습니다. 멱등성은 재시도 로직 구현 시 중요합니다.

5. **RESTful API에서 HATEOAS란 무엇인가요?**
   - Hypermedia As The Engine Of Application State의 약자로, 응답에 관련 리소스의 링크를 포함하여 클라이언트가 다음 작업을 할 수 있는 정보를 제공하는 것입니다. 실제로는 많이 사용되지 않지만 REST의 완전한 구현을 위한 원칙입니다.

6. **RESTful API에서 인증은 어떻게 처리하나요?**
   - 일반적으로 JWT(JSON Web Token)를 사용하여 무상태 인증을 구현합니다. Authorization 헤더에 Bearer 토큰을 포함하여 전송하며, 서버는 토큰을 검증하여 인증을 처리합니다.

7. **RESTful API와 GraphQL의 차이는 무엇인가요?**
   - RESTful API는 여러 엔드포인트로 리소스를 관리하며, 고정된 응답 구조를 가집니다. GraphQL은 단일 엔드포인트로 쿼리를 통해 필요한 데이터만 요청할 수 있습니다. REST는 표준화된 인터페이스가 장점이고, GraphQL은 유연한 데이터 요청이 장점입니다.

8. **RESTful API에서 버전 관리는 어떻게 하나요?**
   - URI에 버전을 포함하는 방법(`/api/v1/users`)과 헤더에 버전을 포함하는 방법(`Accept: application/vnd.api+json;version=1`)이 있습니다. URI에 포함하는 방법이 더 일반적이고 명확합니다.

9. **RESTful API에서 파일 업로드는 어떻게 처리하나요?**
   - POST 또는 PUT 메서드를 사용하며, `multipart/form-data` 형식으로 전송합니다. 또는 파일을 업로드한 후 리소스 URI를 반환하는 방식도 사용합니다.

10. **RESTful API 설계 시 성능 최적화 방법은?**
    - 캐시 헤더를 활용하여 클라이언트 캐싱을 활성화하고, 페이지네이션을 통해 대량 데이터를 분할하여 전송하며, 필요한 필드만 선택적으로 조회할 수 있도록 필드 선택 기능을 제공합니다. 또한 CDN을 활용하여 정적 리소스를 캐싱할 수 있습니다.

---

## 관련 노트

- **[[HTTP]]** - RESTful API가 기반으로 하는 HTTP 프로토콜
- **[[웹 서비스]]** - RESTful API를 포함한 웹 서비스 아키텍처
- **[[API 설계]]** - RESTful API 설계 원칙과 모범 사례

---

**태그:** #spring #rest #restful #api #http #웹개발 #면접 #개념정리

