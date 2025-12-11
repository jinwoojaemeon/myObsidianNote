# H2 Database (H2 데이터베이스)

#spring #database #h2 #jpa #개발환경 #테스트

**관련 개념:** [[Spring Data JPA]] [[JDBC]] [[데이터베이스]]

> [!note] H2 Database
> H2 Database는 Java로 작성된 가볍고 빠른 인메모리(Relational) 데이터베이스이다.
> 
> 주로 **개발, 테스트, 데모 환경**에서 사용됨.

---

## Topic (오늘의 주제)

H2 Database의 특징과 스프링 부트에서의 설정 방법을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 개발 및 테스트 환경에서 별도의 데이터베이스 서버 설치 없이 바로 사용할 수 있어 개발 생산성을 높입니다.
- 인메모리 모드로 동작하여 매우 빠른 성능을 제공하며, 애플리케이션 재시작 시 자동으로 초기화되어 테스트 환경에 적합합니다.
- 웹 콘솔을 통해 브라우저에서 직접 데이터베이스 상태를 확인하고 SQL을 실행할 수 있어 디버깅이 용이합니다.
- 스프링 부트와의 통합이 매우 간단하여 의존성 추가만으로 바로 사용 가능합니다.

---

### **Core Concept (핵심 개념 정리)**

#### H2의 주요 특징

| 항목 | 설명 |
| --- | --- |
| 경량 | 설치 없이 실행 가능 (파일 기반 또는 메모리 기반) |
| 속도 | 메모리에서 작동하므로 성능이 우수함 |
| 웹 콘솔 | 브라우저에서 테이블 확인 및 SQL 실행 가능 |
| SQL 호환 | 표준 SQL 문법 대부분 지원 (Oracle과 유사한 모드도 있음) |
| 스프링 부트와 통합 쉬움 | `spring-boot-starter-data-jpa` 또는 JDBC와 함께 사용 가능 |

---

## 스프링 부트 통합

### 의존성 추가

스프링 부트에서는 **의존성 추가만으로 바로 사용 가능.**

**Gradle:**
```groovy
dependencies {
    runtimeOnly 'com.h2database:h2'
}
```

**Maven:**
```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

---

## application.yml 설정

### 기본 설정 예시

```yaml
spring:
  datasource:
    hikari:
      url: jdbc:h2:tcp://localhost/경로/DB파일명
      driver-class-name: org.h2.Driver
      username: sa
      password:
  h2:
    console:
      enabled: true
      path: /h2-console
```

### 설정 항목 설명

| 항목 | 설명 |
| --- | --- |
| `jdbc:h2:mem:testdb` | 인메모리 모드로 동작 (종료 시 초기화됨) |
| `ddl-auto: create` | 애플리케이션 실행 시 테이블 자동 생성 |
| `h2.console.enabled` | 브라우저로 DB 웹 콘솔 접속 가능 |
| `path: /h2-console` | 접속 경로 지정 (기본: /h2-console) |

### 인메모리 모드 설정 예시

```yaml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password:
  h2:
    console:
      enabled: true
      path: /h2-console
  jpa:
    hibernate:
      ddl-auto: create
    show-sql: true
```

### 파일 기반 모드 설정 예시

```yaml
spring:
  datasource:
    url: jdbc:h2:file:./data/testdb
    driver-class-name: org.h2.Driver
    username: sa
    password:
  h2:
    console:
      enabled: true
      path: /h2-console
```

---

## H2 콘솔 접속

설정이 완료되면 다음 URL로 접속 가능:

```
http://localhost:8080/h2-console
```

**접속 정보:**
- JDBC URL: `jdbc:h2:mem:testdb` (인메모리 모드인 경우)
- User Name: `sa`
- Password: (비워두거나 설정한 값)

---

## 주요 사용 시나리오

1. **로컬 개발 환경**: 별도의 데이터베이스 서버 설치 없이 빠르게 개발 시작
2. **단위 테스트**: 테스트마다 깨끗한 데이터베이스 상태로 테스트 실행
3. **프로토타이핑**: 빠른 프로토타입 개발 및 데모
4. **학습 및 교육**: 데이터베이스 학습 시 간단한 설정으로 바로 시작

---

## 주의사항

- **프로덕션 환경에서는 사용하지 않음**: H2는 개발/테스트용으로 설계되었습니다.
- **인메모리 모드**: 애플리케이션 종료 시 모든 데이터가 사라집니다.
- **동시성 제한**: 다중 사용자 환경에서는 성능 제한이 있을 수 있습니다.

