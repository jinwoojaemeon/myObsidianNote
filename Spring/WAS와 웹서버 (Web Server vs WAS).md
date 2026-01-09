#spring #was #web-server #tomcat #nginx #apache #면접 #개념정리

**관련 개념:** [[스프링 부트 (Spring Boot)|스프링 부트]] [[RESTful API (REST API)|RESTful API]] [[클라이언트 사이드 렌더링|클라이언트 사이드 렌더링]]

> [!note] 이어서 읽기
> WAS와 웹서버를 이해하려면 **[[스프링 부트 (Spring Boot)|스프링 부트]]**의 내장 서버 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**웹서버(Web Server)** 와 **WAS(Web Application Server)** 는 웹 애플리케이션을 제공하는 서버이지만 역할과 기능이 다릅니다. 웹서버는 정적 리소스를 제공하고 요청을 전달하는 역할을 하며, WAS는 동적 처리를 위한 애플리케이션 로직을 실행하는 역할을 합니다. 실제 프로젝트에서는 두 서버를 함께 사용하여 성능과 확장성을 확보합니다.

---

### **Why (왜 사용하는가? 왜 중요한가?)** 

- 웹서버와 WAS를 구분하여 사용하면 각 서버의 장점을 최대한 활용할 수 있습니다. 웹서버는 정적 리소스(HTML, CSS, JS, 이미지)를 효율적으로 제공하고, WAS는 동적 처리(비즈니스 로직, 데이터베이스 연동)를 담당하여 역할 분리가 명확해집니다.

- 웹서버와 WAS의 차이점, 각각의 역할과 기능, 실제 사용 사례, 그리고 두 서버를 함께 사용하는 아키텍처 패턴을 이해해야 합니다.

---

## 1. 웹서버(Web Server)란?

### **웹서버의 정의**

**웹서버(Web Server)** 는 HTTP 요청을 받아서 **정적 리소스(Static Resources)** 를 제공하는 서버입니다. HTML, CSS, JavaScript, 이미지 파일 등 미리 만들어져 있는 파일을 클라이언트에게 전달합니다.

### **웹서버의 주요 기능**

1. **정적 리소스 제공**
   - HTML, CSS, JavaScript 파일
   - 이미지, 동영상 등 미디어 파일
   - 정적 파일을 빠르게 전달

2. **요청 라우팅**
   - 클라이언트의 요청을 적절한 대상으로 전달
   - 리버스 프록시 역할 수행 가능

3. **로드 밸런싱**
   - 여러 서버로 요청을 분산
   - 서버 부하 분산

### **대표적인 웹서버**

- **Apache HTTP Server**: 오픈소스 웹서버, 가장 널리 사용됨
- **Nginx**: 고성능 웹서버, 리버스 프록시로 많이 사용
- **IIS**: 마이크로소프트의 웹서버

### **웹서버 예시**

```
클라이언트 요청: GET /index.html
    ↓
웹서버 (Nginx/Apache)
    ↓
정적 파일 제공: index.html 반환
```

**특징:**
- ✅ 정적 파일을 빠르게 제공
- ✅ 동적 처리는 불가능 (단순히 파일을 전달)
- ✅ 서버 리소스 사용이 적음

---

## 2. WAS(Web Application Server)란?

### **WAS의 정의**

**WAS(Web Application Server)** 는 **동적 처리(Dynamic Processing)** 를 수행하는 애플리케이션 서버입니다. 비즈니스 로직을 실행하고, 데이터베이스와 연동하여 동적인 콘텐츠를 생성합니다.

### **WAS의 주요 기능**

1. **동적 콘텐츠 생성**
   - 비즈니스 로직 실행
   - 데이터베이스 연동
   - 사용자별 맞춤 콘텐츠 제공

2. **애플리케이션 실행 환경 제공**
   - 서블릿 컨테이너 제공
   - 세션 관리
   - 트랜잭션 관리

3. **연결 풀 관리**
   - 데이터베이스 연결 풀 관리
   - 리소스 효율적 사용

### **대표적인 WAS**

- **Tomcat**: 아파치 재단의 오픈소스 서블릿 컨테이너
- **Jetty**: 경량 WAS, 내장 서버로 많이 사용
- **Undertow**: 경량 WAS, 스프링 부트에서 사용
- **WebLogic**: 오라클의 상용 WAS
- **WebSphere**: IBM의 상용 WAS

### **WAS 예시**

```java
// 스프링 부트 애플리케이션
@RestController
public class UserController {
    
    @GetMapping("/user/{id}")
    public User getUser(@PathVariable Long id) {
        // 동적 처리: 데이터베이스에서 조회
        return userService.findById(id);
    }
}
```

**특징:**
- ✅ 동적 콘텐츠 생성 가능
- ✅ 비즈니스 로직 실행
- ✅ 데이터베이스 연동
- ⚠️ 서버 리소스 사용이 많음

---

## 3. 웹서버 vs WAS 비교

### **기능 비교**

| 구분 | 웹서버 | WAS |
|------|--------|-----|
| **주요 역할** | 정적 리소스 제공 | 동적 처리 수행 |
| **처리 내용** | HTML, CSS, JS, 이미지 | 비즈니스 로직, DB 연동 |
| **동적 처리** | ❌ 불가능 | ✅ 가능 |
| **서버 부하** | 낮음 | 높음 |
| **예시** | Nginx, Apache | Tomcat, Jetty |

### **요청 처리 방식 비교**

#### **웹서버의 요청 처리**

```
클라이언트 → 웹서버
    GET /style.css
    ↓
웹서버가 파일 시스템에서 style.css 찾기
    ↓
파일 내용을 그대로 반환
```

**특징:**
- 파일이 존재하면 바로 반환
- 파일이 없으면 404 에러
- 동적 처리는 불가능

#### **WAS의 요청 처리**

```
클라이언트 → WAS
    GET /user/123
    ↓
WAS가 컨트롤러 메서드 실행
    ↓
비즈니스 로직 수행 (DB 조회 등)
    ↓
동적으로 생성된 응답 반환
```

**특징:**
- 애플리케이션 로직 실행
- 데이터베이스 연동
- 동적 콘텐츠 생성

---

## 4. 웹서버와 WAS의 관계

### **WAS도 웹서버 기능을 포함한다**

**중요한 사실:** WAS는 웹서버의 기능도 포함하고 있습니다.

- Tomcat은 내부에 웹서버 기능이 있음
- 정적 리소스도 제공 가능
- 하지만 정적 리소스 제공은 웹서버가 더 효율적

### **왜 웹서버를 별도로 사용할까?**

**웹서버 + WAS 구조의 장점:**

1. **성능 최적화**
   - 웹서버가 정적 리소스를 처리하여 WAS 부하 감소
   - WAS는 동적 처리에만 집중

2. **확장성**
   - 웹서버는 여러 WAS 인스턴스로 요청 분산 가능
   - 로드 밸런싱으로 확장성 확보

3. **보안**
   - 웹서버가 방화벽 역할 수행
   - WAS를 직접 노출하지 않음

---

## 5. 실제 사용 아키텍처

### **아키텍처 1: 웹서버 + WAS (가장 일반적)**

```
클라이언트
    ↓
웹서버 (Nginx/Apache)
    ↓ (동적 요청만 전달)
WAS (Tomcat)
    ↓
데이터베이스
```

**동작 방식:**

1. **정적 리소스 요청**
   ```
   GET /style.css
   → 웹서버가 직접 처리 (WAS 거치지 않음)
   ```

2. **동적 요청**
   ```
   GET /api/user/123
   → 웹서버가 WAS로 전달 (프록시)
   → WAS가 처리 후 응답
   ```

**설정 예시 (Nginx):**

```nginx
# 정적 리소스는 직접 제공
location /static/ {
    root /var/www/html;
}

# 동적 요청은 WAS로 전달
location /api/ {
    proxy_pass http://localhost:8080;
}
```

### **아키텍처 2: WAS만 사용 (개발 환경)**

```
클라이언트
    ↓
WAS (Tomcat - 내장 웹서버 포함)
    ↓
데이터베이스
```

**사용 시나리오:**
- 개발 환경에서 간단하게 테스트
- 스프링 부트의 내장 서버 사용
- 소규모 프로젝트

**예시:**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        // 내장 톰캣 서버로 실행
        SpringApplication.run(Application.class, args);
    }
}
```

### **아키텍처 3: 로드 밸런서 + 웹서버 + WAS (대규모)**

```
클라이언트
    ↓
로드 밸런서 (L4/L7)
    ↓
웹서버 (Nginx) × 여러 대
    ↓
WAS (Tomcat) × 여러 대
    ↓
데이터베이스
```

**장점:**
- 고가용성 (HA)
- 수평 확장 가능
- 부하 분산

---

## 6. 스프링 부트와 WAS

### **스프링 부트의 내장 서버**

스프링 부트는 **내장 서버(Embedded Server)** 를 제공합니다.

**내장 서버의 종류:**
- **Tomcat** (기본값)
- **Jetty**
- **Undertow**

### **내장 서버 사용 예시**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        // 내장 톰캣 서버로 실행
        SpringApplication.run(Application.class, args);
        // http://localhost:8080 에서 접근 가능
    }
}
```

**특징:**
- 별도의 WAS 설치 불필요
- JAR 파일 하나로 실행 가능
- 개발 환경에 적합

### **운영 환경에서는?**

**운영 환경 권장 구조:**

```
Nginx (웹서버)
    ↓
Spring Boot 애플리케이션 (WAS)
    ↓
데이터베이스
```

**이유:**
- Nginx가 정적 리소스를 효율적으로 제공
- WAS 부하 감소
- 보안 강화

---

## 7. 실제 사용 경험 예시

### **프로젝트 구성 예시**

#### **1. 개발 환경**

```java
// 스프링 부트 내장 서버만 사용
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

**특징:**
- 간단한 설정으로 시작
- 빠른 개발 및 테스트
- 정적 리소스도 내장 서버가 처리

#### **2. 운영 환경**

**Nginx 설정:**

```nginx
server {
    listen 80;
    server_name example.com;
    
    # 정적 리소스 직접 제공
    location /static/ {
        root /var/www/html;
        expires 30d;
    }
    
    # API 요청은 WAS로 전달
    location /api/ {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    
    # 프론트엔드 라우팅
    location / {
        root /var/www/html;
        try_files $uri $uri/ /index.html;
    }
}
```

**스프링 부트 설정:**

```yaml
# application.yml
server:
  port: 8080
  servlet:
    context-path: /api
```

**장점:**
- 정적 리소스는 Nginx가 빠르게 제공
- 동적 요청만 WAS가 처리
- 서버 부하 분산

---

## 8. 웹서버와 WAS 선택 기준

### **웹서버를 사용해야 하는 경우**

1. **정적 리소스가 많은 경우**
   - 이미지, 동영상 등 미디어 파일
   - CSS, JavaScript 파일

2. **성능 최적화가 중요한 경우**
   - WAS 부하 감소 필요
   - 빠른 정적 파일 제공 필요

3. **로드 밸런싱이 필요한 경우**
   - 여러 WAS 인스턴스로 요청 분산

### **WAS만 사용해도 되는 경우**

1. **개발 환경**
   - 빠른 개발 및 테스트
   - 간단한 프로젝트

2. **소규모 프로젝트**
   - 트래픽이 적은 경우
   - 정적 리소스가 적은 경우

3. **마이크로서비스 아키텍처**
   - 각 서비스가 독립적으로 실행
   - API 서버만 제공하는 경우

---

## 요약

- **웹서버(Web Server)** 는 정적 리소스(HTML, CSS, JS, 이미지)를 제공하는 서버이며, Nginx, Apache가 대표적입니다.
- **WAS(Web Application Server)** 는 동적 처리(비즈니스 로직, DB 연동)를 수행하는 애플리케이션 서버이며, Tomcat, Jetty가 대표적입니다.
- **웹서버와 WAS의 차이**: 웹서버는 정적 리소스 제공에 특화되어 있고, WAS는 동적 콘텐츠 생성이 가능합니다.
- **실제 사용**: 운영 환경에서는 웹서버(Nginx) + WAS(Tomcat) 구조를 사용하여 성능과 확장성을 확보합니다.
- **스프링 부트**: 내장 서버(Tomcat)를 제공하여 개발 환경에서는 별도 WAS 설치 없이 실행 가능하지만, 운영 환경에서는 웹서버와 함께 사용하는 것이 권장됩니다.
- **선택 기준**: 정적 리소스가 많고 성능이 중요한 경우 웹서버를 함께 사용하고, 개발 환경이나 소규모 프로젝트는 WAS만으로도 충분합니다.

---

## 참고 자료

- [Apache HTTP Server Documentation](https://httpd.apache.org/docs/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Apache Tomcat Documentation](https://tomcat.apache.org/)
- [Spring Boot - Embedded Web Servers](https://docs.spring.io/spring-boot/docs/current/reference/html/web.html#web.embedded-web-servers)
