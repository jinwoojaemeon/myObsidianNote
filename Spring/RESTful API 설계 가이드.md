#spring #rest #restful #api #api-design #웹개발 #면접 #개념정리

**관련 개념:** [[RESTful API (REST API)|RESTful API]] [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]] [[스프링 부트 (Spring Boot)|스프링 부트]]

> [!note] 이어서 읽기
> RESTful API 설계를 이해하려면 **[[RESTful API (REST API)|RESTful API]]**의 기본 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**RESTful API 설계**는 리소스를 URI로 명확하게 표현하고, HTTP 메서드로 행위를 표현하는 표준화된 설계 방식이다. 하지만 실무에서는 REST 원칙을 그대로 적용하기 어려운 애매한 경우가 많으며, 이럴 때는 실용성과 일관성을 우선시하여 유연하게 설계해야 한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- RESTful 설계 원칙을 모르면 일관성 없는 API가 만들어져 클라이언트 개발자가 API를 이해하고 사용하기 어렵습니다. 표준화된 설계 규칙이 없으면 각 개발자마다 다른 방식으로 API를 설계하여 학습 비용이 증가하고, 유지보수가 어려워집니다.

- RESTful 설계 방식을 이해하면 직관적이고 예측 가능한 API를 만들 수 있습니다. 리소스 중심 설계로 URI가 명확해지고, HTTP 메서드로 행위를 표현하여 코드만 봐도 API의 의미를 파악할 수 있습니다. 또한 실무에서 자주 발생하는 애매한 경우에 대한 해결 방법을 알고 있으면 실용적인 API를 설계할 수 있습니다.

- RESTful 설계의 기본 원칙, URI 설계 규칙, HTTP 메서드 선택 기준, 그리고 실무에서 발생하는 애매한 경우와 해결 방법을 이해해야 합니다.

---

## 1. RESTful 설계의 기본 원칙

### **핵심 원칙**

1. **리소스 중심 설계**: URI는 명사로 표현
2. **HTTP 메서드로 행위 표현**: GET, POST, PUT, PATCH, DELETE
3. **계층 구조 표현**: 리소스 간 관계를 URI로 표현
4. **일관성 유지**: 팀 내 일관된 설계 규칙

### **기본 설계 규칙**

```http
# ✅ 좋은 예
GET    /users              # 사용자 목록 조회
GET    /users/1            # 특정 사용자 조회
POST   /users              # 사용자 생성
PUT    /users/1            # 사용자 전체 수정
PATCH  /users/1            # 사용자 일부 수정
DELETE /users/1            # 사용자 삭제

# ❌ 나쁜 예
GET    /getUsers           # 동사 사용
POST   /createUser         # 동사 사용
GET    /users/1/delete     # GET으로 삭제
POST   /users/1/update     # POST로 수정
```

---

## 2. 실무에서 발생하는 애매한 경우와 해결 방법

### **케이스 1: 리소스가 명확하지 않은 작업**

#### **문제 상황**

**예시: 로그인, 로그아웃, 비밀번호 재설정**

```http
# 어떻게 설계해야 할까?
POST /login?              # 리소스가 명확하지 않음
POST /users/login?        # 사용자 리소스의 하위?
POST /auth/login?         # auth는 리소스인가?
```

#### **해결 방법**

**1. 컬렉션 리소스로 표현**

```http
# 로그인: 세션(인증 토큰)을 생성하는 작업
POST /auth/sessions
{
  "username": "user123",
  "password": "password123"
}

# 로그아웃: 세션을 삭제하는 작업
DELETE /auth/sessions/{sessionId}
또는
DELETE /auth/sessions/me  # 현재 세션
```

**2. 동사 사용 허용 (실용적 접근)**

```http
# 로그인/로그아웃은 일반적으로 동사 허용
POST /auth/login
POST /auth/logout

# 비밀번호 재설정
POST /auth/password-reset
POST /auth/password-reset/confirm
```

**3. 하위 리소스로 표현**

```http
# 사용자 리소스의 하위 액션으로 표현
POST /users/{userId}/login      # 일반적이지 않음
POST /users/{userId}/logout     # 일반적이지 않음

# 대신 세션 리소스 사용
POST /sessions                  # 로그인
DELETE /sessions/{id}           # 로그아웃
```

**권장 방법:**
- **인증/인가 관련**: `/auth/login`, `/auth/logout` (동사 허용)
- **리소스 생성/삭제**: `/sessions`, `/tokens` (리소스로 표현)

---

### **케이스 2: 복잡한 검색/필터링**

#### **문제 상황**

**예시: 다양한 조건으로 사용자 검색**

```http
# 쿼리 파라미터가 너무 많아짐
GET /users?name=홍길동&age=30&role=ADMIN&status=ACTIVE&createdFrom=2024-01-01&createdTo=2024-12-31&sort=name,asc&page=1&size=10
```

#### **해결 방법**

**1. 쿼리 파라미터 사용 (일반적)**

```http
# 필터링
GET /users?name=홍길동&age=30&role=ADMIN

# 정렬
GET /users?sort=name,asc&sort=age,desc

# 페이지네이션
GET /users?page=1&size=10

# 조합
GET /users?name=홍길동&role=ADMIN&sort=name,asc&page=1&size=10
```

**2. POST로 검색 (복잡한 조건일 때)**

```http
# 복잡한 검색 조건은 POST 사용 허용
POST /users/search
{
  "filters": {
    "name": "홍길동",
    "age": { "min": 20, "max": 40 },
    "roles": ["ADMIN", "USER"],
    "status": "ACTIVE",
    "createdDate": {
      "from": "2024-01-01",
      "to": "2024-12-31"
    }
  },
  "sort": [
    { "field": "name", "direction": "asc" },
    { "field": "age", "direction": "desc" }
  ],
  "pagination": {
    "page": 1,
    "size": 10
  }
}
```

**권장 방법:**
- **간단한 필터링**: GET + 쿼리 파라미터
- **복잡한 검색**: POST + Request Body

---

### **케이스 3: 리소스가 아닌 액션 (상태 변경)**

#### **문제 상황**

**예시: 주문 취소, 결제 승인, 게시글 공개/비공개**

```http
# 어떻게 설계해야 할까?
POST /orders/1/cancel?        # 취소는 리소스인가?
POST /orders/1/approve?       # 승인은 리소스인가?
PUT  /posts/1?status=public?  # 상태만 변경?
```

#### **해결 방법**

**1. 하위 리소스로 표현 (권장)**

```http
# 주문 취소: 취소 리소스를 생성
POST /orders/1/cancellations
{
  "reason": "고객 요청"
}

# 결제 승인: 승인 리소스를 생성
POST /payments/1/approvals

# 게시글 공개/비공개: 상태 리소스
PUT /posts/1/status
{
  "status": "PUBLIC"
}
또는
PATCH /posts/1
{
  "status": "PUBLIC"
}
```

**2. 동사 사용 허용 (실용적 접근)**

```http
# 간단한 상태 변경은 동사 허용
POST /orders/1/cancel
POST /orders/1/approve
POST /posts/1/publish
POST /posts/1/unpublish
```

**3. PATCH로 상태만 변경**

```http
# 상태 변경은 PATCH 사용
PATCH /orders/1
{
  "status": "CANCELLED"
}

PATCH /posts/1
{
  "status": "PUBLIC"
}
```

**권장 방법:**
- **상태 변경**: PATCH 사용 (일부 필드 수정)
- **복잡한 액션**: POST + 동사 허용 (`/orders/1/cancel`)
- **리소스 생성**: POST + 하위 리소스 (`/orders/1/cancellations`)

---

### **케이스 4: 일대다 관계의 복잡한 작업**

#### **문제 상황**

**예시: 여러 사용자 일괄 삭제, 여러 주문 일괄 취소**

```http
# 어떻게 설계해야 할까?
DELETE /users/1,2,3?         # 여러 ID를 어떻게?
POST /users/batch-delete?    # 동사 사용?
```

#### **해결 방법**

**1. POST로 일괄 작업 (권장)**

```http
# 여러 사용자 일괄 삭제
POST /users/batch-delete
{
  "ids": [1, 2, 3]
}

# 또는
POST /users/delete
{
  "ids": [1, 2, 3]
}
```

**2. 쿼리 파라미터 사용 (간단한 경우)**

```http
# ID 목록으로 삭제
DELETE /users?id=1&id=2&id=3

# 하지만 DELETE는 보통 Request Body를 지원하지 않음
```

**3. 별도 리소스 생성**

```http
# 삭제 작업을 리소스로 표현
POST /users/deletions
{
  "userIds": [1, 2, 3]
}
```

**권장 방법:**
- **일괄 작업**: POST + 동사 허용 (`/users/batch-delete`)
- **단일 작업**: DELETE (`/users/1`)

---

### **케이스 5: 파일 업로드/다운로드**

#### **문제 상황**

**예시: 파일 업로드, 이미지 다운로드**

```http
# 어떻게 설계해야 할까?
POST /files?                 # 파일 리소스?
POST /users/1/avatar?        # 사용자 아바타?
GET  /files/1/download?      # 다운로드는 리소스인가?
```

#### **해결 방법**

**1. 파일을 리소스로 표현**

```http
# 파일 업로드
POST /users/1/avatar
Content-Type: multipart/form-data
{
  "file": <binary>
}

# 파일 다운로드
GET /users/1/avatar
GET /files/1

# 파일 메타데이터 조회
GET /files/1/metadata
```

**2. 별도 엔드포인트 사용**

```http
# 업로드
POST /users/1/avatar/upload

# 다운로드
GET /users/1/avatar/download
또는
GET /files/1/download
```

**권장 방법:**
- **파일 업로드**: POST + 리소스 (`/users/1/avatar`)
- **파일 다운로드**: GET + 리소스 (`/users/1/avatar`)
- **파일 메타데이터**: GET + 하위 리소스 (`/files/1/metadata`)

---

### **케이스 6: 통계/집계 작업**

#### **문제 상황**

**예시: 사용자 통계, 매출 집계**

```http
# 어떻게 설계해야 할까?
GET /users/statistics?      # 통계는 리소스인가?
GET /users/stats?            # 동사 축약?
POST /users/aggregate?      # 집계는 액션?
```

#### **해결 방법**

**1. 하위 리소스로 표현 (권장)**

```http
# 사용자 통계
GET /users/statistics
GET /users/stats

# 특정 기간 통계
GET /users/statistics?from=2024-01-01&to=2024-12-31

# 매출 집계
GET /orders/revenue
GET /orders/revenue?year=2024&month=1
```

**2. 쿼리 파라미터로 집계**

```http
# 집계 옵션을 쿼리 파라미터로
GET /users?aggregate=count,sum,avg
GET /orders?groupBy=status&aggregate=sum(amount)
```

**권장 방법:**
- **통계/집계**: GET + 하위 리소스 (`/users/statistics`)
- **복잡한 집계**: POST + Request Body

---

### **케이스 7: 중첩 리소스의 깊은 계층**

#### **문제 상황**

**예시: 사용자 > 게시글 > 댓글 > 좋아요**

```http
# 계층이 너무 깊어짐
GET /users/1/posts/2/comments/3/likes
```

#### **해결 방법**

**1. 최상위 리소스로 직접 접근**

```http
# 깊은 계층 대신 최상위 리소스 사용
GET /likes?commentId=3
GET /comments/3/likes        # 2단계까지만 허용
```

**2. 쿼리 파라미터로 관계 표현**

```http
# 관계를 쿼리 파라미터로
GET /likes?userId=1&postId=2&commentId=3
```

**권장 방법:**
- **2단계까지만**: `/users/1/posts`
- **3단계 이상**: 최상위 리소스 + 쿼리 파라미터

---

### **케이스 8: 부분 업데이트 vs 전체 업데이트**

#### **문제 상황**

**예시: 사용자 정보 수정**

```http
# PUT vs PATCH 언제 사용?
PUT  /users/1    # 전체 수정?
PATCH /users/1   # 일부 수정?
```

#### **해결 방법**

**1. PATCH 사용 (일반적)**

```http
# 일부 필드만 수정
PATCH /users/1
{
  "name": "홍길동"
}

# 여러 필드 수정
PATCH /users/1
{
  "name": "홍길동",
  "email": "hong@example.com"
}
```

**2. PUT 사용 (전체 교체)**

```http
# 모든 필드를 제공해야 함
PUT /users/1
{
  "id": 1,
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 30,
  "role": "USER"
}
```

**권장 방법:**
- **일부 수정**: PATCH (실무에서 대부분)
- **전체 교체**: PUT (드물게 사용)

---

## 3. 실무 설계 가이드라인

### **기본 원칙**

1. **일관성 우선**: 팀 내 일관된 규칙 유지
2. **실용성**: 완벽한 REST보다 실용성 우선
3. **명확성**: URI만 봐도 의미 파악 가능
4. **문서화**: 애매한 경우 문서로 명확히 정의

### **HTTP 메서드 선택 기준**

| 메서드 | 사용 시나리오 | 예시 |
|--------|-------------|------|
| **GET** | 리소스 조회 | `GET /users/1` |
| **POST** | 리소스 생성, 복잡한 작업 | `POST /users`, `POST /users/search` |
| **PUT** | 리소스 전체 교체 | `PUT /users/1` |
| **PATCH** | 리소스 일부 수정 | `PATCH /users/1` |
| **DELETE** | 리소스 삭제 | `DELETE /users/1` |

### **URI 설계 체크리스트**

- ✅ 명사 사용 (동사 지양)
- ✅ 복수형 사용 (`/users` not `/user`)
- ✅ 소문자 사용
- ✅ 하이픈(-) 사용 (언더스코어 _ 지양)
- ✅ 계층 구조 명확히 (최대 2-3단계)
- ✅ 쿼리 파라미터는 필터링/정렬/페이지네이션에만

---

## 4. 실무 예시: 전자상거래 API 설계

### **사용자 관리**

```http
# 기본 CRUD
GET    /users
GET    /users/1
POST   /users
PATCH  /users/1
DELETE /users/1

# 인증
POST   /auth/login
POST   /auth/logout
POST   /auth/refresh-token

# 비밀번호
POST   /auth/password-reset
POST   /auth/password-reset/confirm
PATCH  /users/1/password
```

### **상품 관리**

```http
# 기본 CRUD
GET    /products
GET    /products/1
POST   /products
PATCH  /products/1
DELETE /products/1

# 검색 (복잡한 조건)
POST   /products/search
{
  "filters": { ... },
  "sort": [ ... ],
  "pagination": { ... }
}

# 상태 변경
PATCH  /products/1/status
{
  "status": "PUBLISHED"
}
또는
POST   /products/1/publish
```

### **주문 관리**

```http
# 기본 CRUD
GET    /orders
GET    /orders/1
POST   /orders
PATCH  /orders/1

# 주문 취소
POST   /orders/1/cancel
또는
POST   /orders/1/cancellations

# 일괄 작업
POST   /orders/batch-cancel
{
  "orderIds": [1, 2, 3]
}

# 통계
GET    /orders/statistics
GET    /orders/revenue?year=2024
```

### **파일 관리**

```http
# 파일 업로드
POST   /products/1/images
Content-Type: multipart/form-data

# 파일 다운로드
GET    /products/1/images/1

# 파일 메타데이터
GET    /products/1/images/1/metadata
```

---

## 요약

- **RESTful 설계의 핵심**: 리소스를 URI로 표현하고, HTTP 메서드로 행위를 표현
- **애매한 경우의 해결 방법**:
  - **인증/인가**: `/auth/login`, `/auth/logout` (동사 허용)
  - **복잡한 검색**: POST + Request Body
  - **상태 변경**: PATCH 또는 POST + 동사
  - **일괄 작업**: POST + 동사 (`/users/batch-delete`)
  - **파일**: 리소스로 표현 (`/users/1/avatar`)
  - **통계**: 하위 리소스 (`/users/statistics`)
  - **깊은 계층**: 최상위 리소스 + 쿼리 파라미터
- **실무 원칙**: 일관성 > 완벽한 REST, 실용성 우선
- **문서화**: 애매한 경우 팀 내 규칙으로 명확히 정의

---

## 참고 자료

- [RESTful API Design Best Practices](https://restfulapi.net/)
- [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines)
- [Google Cloud API Design Guide](https://cloud.google.com/apis/design)

---

### 예상 꼬리질문 정리

#### 1. RESTful 설계에서 동사를 사용해도 되나요?

**답변:**
- 일반적으로는 명사만 사용하지만, 실무에서는 특정 경우에 동사를 허용합니다.
- **동사 허용이 적절한 경우**:
  - 인증/인가: `/auth/login`, `/auth/logout`
  - 복잡한 액션: `/orders/1/cancel`, `/posts/1/publish`
  - 일괄 작업: `/users/batch-delete`
- **원칙**: 팀 내 일관성을 유지하고, 문서로 명확히 정의

#### 2. 복잡한 검색은 GET과 POST 중 어느 것을 사용하나요?

**답변:**
- **간단한 검색**: GET + 쿼리 파라미터
  ```http
  GET /users?name=홍길동&role=ADMIN
  ```
- **복잡한 검색**: POST + Request Body
  ```http
  POST /users/search
  {
    "filters": { ... },
    "sort": [ ... ]
  }
  ```
- **이유**: GET의 쿼리 파라미터 길이 제한, 복잡한 조건 표현 어려움

#### 3. 상태 변경은 PUT, PATCH, POST 중 어느 것을 사용하나요?

**답변:**
- **일부 필드 수정**: PATCH (가장 일반적)
  ```http
  PATCH /users/1
  { "name": "홍길동" }
  ```
- **전체 교체**: PUT
  ```http
  PUT /users/1
  { "id": 1, "name": "...", "email": "...", ... }
  ```
- **복잡한 상태 변경**: POST + 동사
  ```http
  POST /orders/1/cancel
  POST /posts/1/publish
  ```

#### 4. 일괄 작업은 어떻게 설계하나요?

**답변:**
- **POST + 동사 사용** (가장 일반적)
  ```http
  POST /users/batch-delete
  { "ids": [1, 2, 3] }
  ```
- **이유**: DELETE는 여러 리소스를 한 번에 삭제하기 어려움

#### 5. 파일 업로드는 어떻게 설계하나요?

**답변:**
- **리소스로 표현** (권장)
  ```http
  POST /users/1/avatar
  Content-Type: multipart/form-data
  ```
- **다운로드**
  ```http
  GET /users/1/avatar
  ```

#### 6. 중첩 리소스의 깊이는 어디까지 허용하나요?

**답변:**
- **2-3단계까지만 권장**
  ```http
  GET /users/1/posts          # 2단계: OK
  GET /users/1/posts/2        # 3단계: OK
  ```
- **3단계 이상**: 최상위 리소스 + 쿼리 파라미터
  ```http
  GET /comments?userId=1&postId=2
  ```

#### 7. 통계나 집계 작업은 어떻게 설계하나요?

**답변:**
- **하위 리소스로 표현**
  ```http
  GET /users/statistics
  GET /orders/revenue?year=2024
  ```
- **쿼리 파라미터로 집계 옵션 전달**

#### 8. RESTful 설계에서 가장 중요한 것은 무엇인가요?

**답변:**
- **일관성**: 팀 내 일관된 규칙 유지
- **명확성**: URI만 봐도 의미 파악 가능
- **실용성**: 완벽한 REST보다 실용성 우선
- **문서화**: 애매한 경우 명확히 정의

---

## 관련 노트

- **[[RESTful API (REST API)|RESTful API]]** - RESTful API의 기본 개념
- **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]** - Spring에서의 RESTful API 구현
- **[[스프링 부트 (Spring Boot)|스프링 부트]]** - Spring Boot에서의 RESTful API 구현

---

**태그:** #spring #rest #restful #api #api-design #웹개발 #면접 #개념정리

