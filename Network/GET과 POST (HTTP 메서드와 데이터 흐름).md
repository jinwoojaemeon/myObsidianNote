#network #http #get #post #restful #api #면접 #개념정리

**관련 개념:** [[HTTP와 HTTPS의 차이점]] [[RESTful API (REST API)]] [[OSI 7계층 모델]]

> [!note] 이어서 읽기
> GET과 POST는 HTTP 메서드입니다. [[HTTP와 HTTPS의 차이점]]과 [[RESTful API (REST API)]]를 함께 확인하세요.

---

## Topic (오늘의 주제)

**GET**과 **POST**는 HTTP(HyperText Transfer Protocol)에서 가장 많이 사용되는 두 가지 메서드입니다. GET은 서버로부터 데이터를 조회할 때 사용하고, POST는 서버에 데이터를 생성하거나 전송할 때 사용합니다. 각 메서드의 특성과 데이터 전송 방식, 그리고 사용 사례를 이해하는 것이 중요합니다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- GET과 POST는 웹 개발에서 가장 기본적이면서도 중요한 HTTP 메서드입니다. 각 메서드의 특성(멱등성, 안전성, 캐싱 가능 여부)과 데이터 전송 방식을 이해하면 RESTful API를 올바르게 설계할 수 있고, 보안과 성능을 고려한 애플리케이션을 개발할 수 있습니다.

- GET과 POST의 차이점(데이터 전송 위치, 용도, 캐싱, 브라우저 히스토리, 멱등성, 안전성), 각 메서드의 데이터 흐름, 그리고 언제 어떤 메서드를 사용해야 하는지 이해해야 합니다.

---

## 1. HTTP 메서드란?

### **HTTP 메서드의 정의**

**HTTP 메서드(HTTP Method)**는 클라이언트가 서버에 요청할 때 수행하고자 하는 동작을 나타내는 명령어입니다.

### **주요 HTTP 메서드**

- **GET**: 리소스 조회
- **POST**: 리소스 생성
- **PUT**: 리소스 전체 수정
- **PATCH**: 리소스 부분 수정
- **DELETE**: 리소스 삭제

---

## 2. GET 메서드

### **GET의 정의**

**GET**은 서버로부터 리소스를 조회(Read)할 때 사용하는 메서드입니다.

### **GET의 주요 특징**

1. **데이터 전송 위치: URL의 쿼리 스트링(Query String)**
   ```
   GET /users?id=123&name=홍길동 HTTP/1.1
   Host: example.com
   ```

2. **멱등성(Idempotent)**: 여러 번 요청해도 결과가 동일
   ```
   GET /users?id=123 (1번 요청) → 사용자 정보 반환
   GET /users?id=123 (2번 요청) → 같은 사용자 정보 반환
   GET /users?id=123 (3번 요청) → 같은 사용자 정보 반환
   ```

3. **안전성(Safe)**: 서버의 상태를 변경하지 않음
   - 조회만 수행하므로 서버 데이터에 영향 없음

4. **캐싱 가능**: 브라우저나 프록시 서버에서 캐싱 가능
   - 같은 요청은 캐시에서 응답 가능

5. **브라우저 히스토리 저장**: URL이 히스토리에 저장됨

6. **북마크 가능**: URL을 북마크할 수 있음

### **GET 요청의 데이터 흐름**

```
클라이언트                    서버
    |                         |
    |---- GET /users?id=123 -->|  [요청: URL에 데이터 포함]
    |                         |
    |                         |  [서버: 데이터 조회]
    |                         |
    |<---- 200 OK (데이터) -----|  [응답: 조회된 데이터]
    |                         |
```

**요청 예시:**
```http
GET /api/users?id=123&name=홍길동 HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: application/json
```

**응답 예시:**
```http
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 156

{
  "id": 123,
  "name": "홍길동",
  "email": "hong@example.com"
}
```

### **GET의 사용 사례**

1. **리소스 조회**
   ```
   GET /users/123          # 사용자 정보 조회
   GET /products?category=electronics  # 상품 목록 조회
   GET /articles/456       # 게시글 조회
   ```

2. **검색**
   ```
   GET /search?q=spring    # 검색어로 검색
   GET /filter?price=1000  # 필터링
   ```

3. **페이지 조회**
   ```
   GET /home               # 홈 페이지
   GET /about              # 소개 페이지
   ```

---

## 3. POST 메서드

### **POST의 정의**

**POST**는 서버에 데이터를 전송하여 리소스를 생성(Create)하거나 처리할 때 사용하는 메서드입니다.

### **POST의 주요 특징**

1. **데이터 전송 위치: HTTP 요청 본문(Request Body)**
   ```http
   POST /users HTTP/1.1
   Host: example.com
   Content-Type: application/json
   Content-Length: 45
   
   {
     "name": "홍길동",
     "email": "hong@example.com"
   }
   ```

2. **비멱등성(Non-idempotent)**: 같은 요청을 여러 번 하면 다른 결과 발생 가능
   ```
   POST /users (1번 요청) → 사용자 1 생성
   POST /users (2번 요청) → 사용자 2 생성 (다른 결과)
   POST /users (3번 요청) → 사용자 3 생성 (다른 결과)
   ```

3. **안전성 없음(Unsafe)**: 서버의 상태를 변경함
   - 데이터 생성, 수정, 삭제 등 서버 상태 변경

4. **캐싱 불가능**: 일반적으로 캐싱하지 않음
   - 서버 상태를 변경하므로 캐시 사용 불가

5. **브라우저 히스토리 저장 안 함**: 일반적으로 히스토리에 저장하지 않음

6. **북마크 불가능**: 요청 본문이 포함되어 있어 북마크 의미 없음

### **POST 요청의 데이터 흐름**

```
클라이언트                    서버
    |                         |
    |---- POST /users -------->|  [요청: 본문에 데이터 포함]
    |    Body: {name, email}   |
    |                         |
    |                         |  [서버: 데이터 처리/생성]
    |                         |
    |<---- 201 Created --------|  [응답: 생성된 리소스 정보]
    |    Location: /users/123  |
    |                         |
```

**요청 예시:**
```http
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/json
Content-Length: 45

{
  "name": "홍길동",
  "email": "hong@example.com"
}
```

**응답 예시:**
```http
HTTP/1.1 201 Created
Location: /api/users/123
Content-Type: application/json
Content-Length: 156

{
  "id": 123,
  "name": "홍길동",
  "email": "hong@example.com",
  "createdAt": "2024-01-01T00:00:00Z"
}
```

### **POST의 사용 사례**

1. **리소스 생성**
   ```
   POST /users              # 새 사용자 생성
   POST /articles           # 새 게시글 작성
   POST /orders             # 새 주문 생성
   ```

2. **데이터 전송 및 처리**
   ```
   POST /login              # 로그인 (인증 정보 전송)
   POST /upload             # 파일 업로드
   POST /payment            # 결제 처리
   ```

3. **복잡한 쿼리**
   ```
   POST /search             # 복잡한 검색 조건 (본문에 JSON)
   POST /filter             # 복잡한 필터링
   ```

---

## 4. GET vs POST 비교

### **핵심 차이점**

| 구분 | GET | POST |
|:---:|:---:|:---:|
| **용도** | 리소스 조회 | 리소스 생성/처리 |
| **데이터 위치** | URL 쿼리 스트링 | HTTP 요청 본문 |
| **데이터 크기 제한** | 있음 (URL 길이 제한) | 없음 (본문 크기 제한만) |
| **멱등성** | 멱등 (Idempotent) | 비멱등 (Non-idempotent) |
| **안전성** | 안전 (Safe) | 안전하지 않음 (Unsafe) |
| **캐싱** | 가능 | 불가능 |
| **브라우저 히스토리** | 저장됨 | 저장 안 됨 |
| **북마크** | 가능 | 불가능 |
| **보안** | URL에 노출 (민감 정보 부적합) | 본문에 포함 (상대적으로 안전) |
| **사용 예시** | 검색, 조회 | 생성, 로그인, 파일 업로드 |

### **데이터 전송 위치 비교**

**GET:**
```
GET /api/users?id=123&name=홍길동 HTTP/1.1
Host: example.com
[데이터가 URL에 포함]
```

**POST:**
```
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "id": 123,
  "name": "홍길동"
}
[데이터가 본문에 포함]
```

---

## 5. 데이터 흐름 상세 분석

### **GET 요청의 전체 흐름**

```
1. 클라이언트가 URL에 데이터를 포함하여 요청
   GET /api/users?id=123 HTTP/1.1
   Host: example.com
      ↓
2. 네트워크를 통해 서버로 전송
   [TCP/IP 패킷]
      ↓
3. 서버가 요청을 받아 처리
   - URL에서 쿼리 파라미터 추출 (id=123)
   - 데이터베이스에서 조회
      ↓
4. 서버가 응답 반환
   HTTP/1.1 200 OK
   Content-Type: application/json
   
   {"id": 123, "name": "홍길동"}
      ↓
5. 클라이언트가 응답 수신 및 처리
   - JSON 파싱
   - 화면에 표시
```

### **POST 요청의 전체 흐름**

```
1. 클라이언트가 본문에 데이터를 포함하여 요청
   POST /api/users HTTP/1.1
   Host: example.com
   Content-Type: application/json
   
   {"name": "홍길동", "email": "hong@example.com"}
      ↓
2. 네트워크를 통해 서버로 전송
   [TCP/IP 패킷]
      ↓
3. 서버가 요청을 받아 처리
   - 본문에서 데이터 추출 (JSON 파싱)
   - 데이터 검증
   - 데이터베이스에 저장
      ↓
4. 서버가 응답 반환
   HTTP/1.1 201 Created
   Location: /api/users/123
   
   {"id": 123, "name": "홍길동", ...}
      ↓
5. 클라이언트가 응답 수신 및 처리
   - 생성된 리소스 정보 확인
   - 화면 업데이트
```

---

## 6. 언제 GET을 사용하고 언제 POST를 사용할까?

### **GET을 사용해야 하는 경우**

- ✅ **리소스 조회**: 서버에서 데이터를 읽어올 때
- ✅ **검색**: 검색어, 필터 조건을 전송할 때
- ✅ **멱등성이 필요한 작업**: 같은 요청을 여러 번 해도 같은 결과
- ✅ **캐싱이 필요한 경우**: 자주 조회하는 데이터
- ✅ **URL 공유가 필요한 경우**: 북마크, 공유 가능

**예시:**
```
GET /users/123              # 사용자 정보 조회
GET /products?category=electronics  # 상품 목록 조회
GET /search?q=spring         # 검색
```

### **POST를 사용해야 하는 경우**

- ✅ **리소스 생성**: 새 데이터를 서버에 저장할 때
- ✅ **서버 상태 변경**: 데이터 수정, 삭제
- ✅ **민감한 정보 전송**: 비밀번호, 개인정보
- ✅ **대용량 데이터**: URL 길이 제한을 피하기 위해
- ✅ **복잡한 데이터 구조**: JSON 객체 등

**예시:**
```
POST /users                  # 새 사용자 생성
POST /login                  # 로그인 (비밀번호 전송)
POST /articles               # 새 게시글 작성
POST /upload                 # 파일 업로드
```

---

## 7. 보안 고려사항

### **GET의 보안 문제**

**문제점:**
- URL에 데이터가 노출됨
- 브라우저 히스토리에 저장됨
- 서버 로그에 기록됨
- 프록시 서버 로그에 기록됨

**예시:**
```
GET /login?username=admin&password=1234
[비밀번호가 URL에 노출됨!]
```

**해결:**
- 민감한 정보는 POST 사용
- 인증 정보는 POST로 전송

### **POST의 보안 고려사항**

**장점:**
- 본문에 데이터가 포함되어 URL에 노출되지 않음
- HTTPS와 함께 사용하면 암호화 가능

**주의사항:**
- HTTPS를 사용하지 않으면 네트워크에서 데이터를 볼 수 있음
- CSRF(Cross-Site Request Forgery) 공격에 취약할 수 있음

---

## 요약

- **GET**은 리소스를 조회할 때 사용하며, 데이터를 URL의 쿼리 스트링에 포함하여 전송합니다. 멱등성과 안전성을 가지며, 캐싱이 가능하고 브라우저 히스토리에 저장됩니다.

- **POST**는 리소스를 생성하거나 처리할 때 사용하며, 데이터를 HTTP 요청 본문에 포함하여 전송합니다. 비멱등성이며 서버 상태를 변경하므로 캐싱이 불가능합니다.

- **데이터 흐름**: GET은 URL에 데이터를 포함하여 전송하고, POST는 본문에 데이터를 포함하여 전송합니다. POST는 대용량 데이터와 민감한 정보 전송에 적합합니다.

- **선택 기준**: 조회는 GET, 생성/수정/삭제는 POST를 사용합니다. 민감한 정보는 반드시 POST를 사용하고 HTTPS와 함께 사용해야 합니다.

---

## 참고 자료

- [RFC 7231 - HTTP/1.1 Semantics and Content](https://tools.ietf.org/html/rfc7231)
- [MDN Web Docs - HTTP Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [RESTful API Design Guidelines](https://restfulapi.net/)

---

### 예상 꼬리질문 정리

#### 1. GET 요청에도 본문을 포함할 수 있나?

**기술적으로는 가능하지만 권장하지 않음:**

- HTTP 스펙상 GET 요청에도 본문을 포함할 수 있음
- 하지만 대부분의 서버, 프록시, 캐싱 시스템이 GET 본문을 무시함
- GET의 의미(조회)와 맞지 않음

**권장 사항:**
- GET은 URL 쿼리 스트링만 사용
- 본문이 필요하면 POST 사용

#### 2. POST도 멱등하게 만들 수 있나?

**가능하지만 POST의 본래 의미와 맞지 않음:**

POST는 본래 비멱등성을 가정하지만, 구현에 따라 멱등하게 만들 수 있습니다.

**예시:**
```
POST /users
{"id": 123, "name": "홍길동"}

[서버가 id를 확인하여 이미 존재하면 업데이트, 없으면 생성]
→ 멱등하게 동작 가능
```

**하지만 권장하지 않음:**
- PUT이나 PATCH를 사용하는 것이 더 적절
- RESTful API 설계 원칙에 맞지 않음

#### 3. GET과 POST의 데이터 크기 제한은?

**GET:**
- URL 길이 제한: 브라우저마다 다름 (일반적으로 2048자)
- 서버 설정에 따라 다를 수 있음

**POST:**
- 본문 크기 제한: 서버 설정에 따라 다름
- 일반적으로 수 MB ~ 수 GB까지 가능
- 파일 업로드 시 더 큰 크기 가능

**권장:**
- GET은 작은 데이터만 전송
- 대용량 데이터는 POST 사용

#### 4. PUT, PATCH, DELETE와의 차이는?

| 메서드 | 용도 | 멱등성 | 안전성 |
|:---:|:---:|:---:|:---:|
| **GET** | 조회 | ✅ | ✅ |
| **POST** | 생성 | ❌ | ❌ |
| **PUT** | 전체 수정 | ✅ | ❌ |
| **PATCH** | 부분 수정 | ❌ | ❌ |
| **DELETE** | 삭제 | ✅ | ❌ |

**POST vs PUT:**
- POST: 리소스 생성 (비멱등)
- PUT: 리소스 전체 수정 (멱등)

**PUT vs PATCH:**
- PUT: 리소스 전체를 교체
- PATCH: 리소스 일부만 수정

#### 5. RESTful API에서 GET과 POST의 역할은?

**RESTful API 설계 원칙:**

```
GET    /users          # 사용자 목록 조회
GET    /users/123      # 사용자 상세 조회
POST   /users          # 새 사용자 생성
PUT    /users/123      # 사용자 전체 수정
PATCH  /users/123      # 사용자 부분 수정
DELETE /users/123      # 사용자 삭제
```

**GET과 POST의 역할:**
- **GET**: 리소스 조회 (Read)
- **POST**: 리소스 생성 (Create)

**RESTful 원칙 준수:**
- 각 메서드의 의미에 맞게 사용
- 멱등성과 안전성 고려
- 적절한 HTTP 상태 코드 반환

---

## 관련 노트

- **[[HTTP와 HTTPS의 차이점]]** - HTTP 프로토콜의 보안
- **[[RESTful API (REST API)]]** - RESTful API 설계 원칙
- **[[OSI 7계층 모델]]** - HTTP의 계층 위치

---

**태그:** #network #http #get #post #restful #api #면접 #개념정리
