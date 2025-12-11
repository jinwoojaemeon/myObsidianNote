# JPA 엔티티 매핑

#spring #jpa #hibernate #entity #mapping #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[N+1 문제 (JPA)|N+1 문제]]

> [!note] 이어서 읽기
> JPA 엔티티 매핑을 이해하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

JPA에서 엔티티를 정의하고 데이터베이스 테이블과 매핑하는 방법, 그리고 기본 키 생성 전략과 필드 매핑 방법을 학습한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- JPA 엔티티 매핑은 객체와 데이터베이스 테이블 간의 매핑을 선언적으로 정의하여, 개발자가 SQL을 직접 작성하지 않고도 객체를 통해 데이터베이스 작업을 수행할 수 있게 합니다.

- 엔티티 매핑을 통해 데이터베이스 스키마를 자동으로 생성하고 관리할 수 있어, 개발 초기 단계에서 생산성을 크게 향상시킵니다.

- 다양한 기본 키 생성 전략과 필드 매핑 옵션을 제공하여, 다양한 데이터베이스 환경에 유연하게 대응할 수 있습니다.

---

### **Core Concept (핵심 개념 정리)**

**엔티티(Entity)**는 데이터베이스 테이블과 매핑되는 도메인 객체로, `@Entity` 어노테이션으로 선언됩니다. 엔티티는 객체의 상태를 데이터베이스에 영속화할 수 있는 객체입니다.

**매핑 어노테이션**은 엔티티 클래스의 필드와 데이터베이스 테이블의 컬럼을 연결하는 메타데이터를 제공합니다. `@Id`, `@Column`, `@Table` 등의 어노테이션을 통해 매핑 정보를 정의합니다.

**기본 키 매핑 전략**은 엔티티의 식별자를 생성하는 방법을 정의합니다. `@GeneratedValue`를 통해 IDENTITY, SEQUENCE, TABLE, AUTO 등의 전략을 선택할 수 있습니다.

> [!tip] 핵심 포인트
> 엔티티 매핑은 JPA의 핵심 기능으로, 객체지향 모델과 관계형 데이터베이스 모델 간의 차이를 해결하여 개발자가 객체 중심으로 개발할 수 있게 해줍니다.

---

## 1. 엔티티(Entity)란?

- 데이터베이스 테이블과 매핑되는 **도메인 객체**
- `@Entity` 어노테이션으로 선언
- 테이블과 1:1 대응, 객체 상태를 데이터베이스에 **영속화**
- 필수 조건:
    - 기본 생성자 필요 (public 또는 protected)
    - 식별자 필드에 `@Id` 어노테이션

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
}
```

---

## 2. 주요 매핑 어노테이션

| 어노테이션 | 설명 |
| --- | --- |
| `@Entity` | 엔티티 클래스 지정 |
| `@Table` | 매핑할 테이블명 지정 |
| `@Id` | 기본 키 필드 지정 |
| `@GeneratedValue` | 기본 키 생성 전략 설정 (`AUTO`, `IDENTITY`, `SEQUENCE`, `TABLE`) |
| `@Column` | 컬럼 속성 지정 (`nullable`, `length`, `unique` 등) |
| `@Enumerated` | Enum 매핑 방법 지정 (`STRING`, `ORDINAL`) |
| `@Temporal` | 날짜 타입 매핑 (`DATE`, `TIME`, `TIMESTAMP`) |

### **@Table 어노테이션 예시**

```java
@Entity
@Table(name = "USERS", schema = "PUBLIC")
public class User {
    // ...
}
```

- `name`: 매핑할 테이블명 지정 (기본값: 엔티티 클래스명)
- `schema`: 스키마명 지정
- `uniqueConstraints`: 유니크 제약 조건 지정

### **@Column 어노테이션 예시**

```java
@Entity
public class User {
    @Id
    private Long id;
    
    @Column(name = "USER_NAME", nullable = false, length = 50, unique = true)
    private String name;
    
    @Column(columnDefinition = "TEXT")
    private String description;
}
```

- `name`: 컬럼명 지정 (기본값: 필드명)
- `nullable`: NULL 허용 여부 (기본값: true)
- `length`: 문자열 길이 (기본값: 255)
- `unique`: 유니크 제약 조건
- `columnDefinition`: 컬럼 정의 직접 지정

---

## 3. 데이터베이스 스키마 자동 생성

- 개발 초기에는 테이블을 일일이 설계하고 SQL로 직접 작성하기 번거로움
- JPA 엔티티 설계만으로 DB 테이블, 컬럼, 제약 조건 등을 자동으로 생성
- 객체지향 모델과 관계형 모델을 빠르게 동기화

### **동작 원리**

1. **엔티티 스캔 및 분석**
    - 애플리케이션 실행 시, JPA는 `@Entity`로 선언된 클래스를 모두 스캔
    - 클래스의 필드, 어노테이션(`@Column`, `@Id`, `@Table` 등)을 기준으로 테이블과 컬럼 정보 추출

2. **Hibernate Dialect 결정**
    - 사용하는 데이터베이스 종류에 따라 방언(Dialect)을 결정
    - ex) MySQL → `MySQLDialect`, Oracle → `OracleDialect`
    - Dialect는 DDL 생성 시 사용되는 SQL 문법과 데이터 타입을 결정

3. **DDL 문 자동 생성**
    - 엔티티와 매핑 정보를 바탕으로 CREATE TABLE, ALTER TABLE 등의 SQL 문 생성
        
        ```sql
        CREATE TABLE USER (
          id BIGINT AUTO_INCREMENT PRIMARY KEY,
          name VARCHAR(50) NOT NULL
        );
        ```
        
4. **설정에 따라 DDL 실행 여부 결정**
    - application.yml (또는 properties)에서 `ddl-auto` 옵션 값에 따라 DDL을 실행하거나 검증만 함
        
        ```yaml
        spring:
          jpa:
            hibernate:
              ddl-auto: update
        ```
        
        | 옵션 | 설명 |
        | --- | --- |
        | `create` | 기존 테이블 삭제 후 생성 |
        | `create-drop` | 종료 시 테이블 삭제 |
        | `update` | 변경된 부분만 반영 |
        | `validate` | 매핑 검증, 변경 없음 |
        | `none` | 자동 생성 안 함 |
        
        > ⚠️ 운영 환경에서는 `create`, `create-drop` 사용 주의! 테이블 삭제, 컬럼 변경 등의 위험 발생

---

## 4. 기본 키 매핑 전략

### **1. 직접 할당**

```java
@Entity
public class User {
    @Id
    private String id;
}
```

- 개발자가 직접 식별자 값을 설정
- **추천하지 않음!** (값 누락 시 예외 발생)

### **2. 자동 생성**

#### **IDENTITY 전략**

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

- MySQL, MariaDB 등 Auto Increment 사용하여 데이터베이스가 직접 기본 키 값을 생성함
- 자바 코드에서는 키 값을 지정하지 않고, DB에 INSERT할 때 키가 자동으로 부여됨
- 즉시 INSERT, 트랜잭션 쓰기 지연 X

> [!tip] 트랜잭션 쓰기 지연이란?
> JPA가 성능 최적화를 위해 **INSERT, UPDATE, DELETE 쿼리를 즉시 실행하지 않고**, 트랜잭션이 커밋되거나 플러시할 때 한꺼번에 실행하는 전략입니다. 이를 **쓰기 지연 저장소(Write-Behind Storage)** 라고도 부릅니다.
> 
> **IDENTITY 전략은 DB에서 자동으로 키 값**을 만들어주기 때문에 `persist()` 호출 시 **바로 INSERT 쿼리를 실행**해야 생성된 ID 값을 받아올 수 있습니다. 그래서 **트랜잭션 쓰기 지연이 동작하지 않습니다.**

#### **SEQUENCE 전략**

```java
@SequenceGenerator(
    name = "USER_SEQ_GEN",           // 시퀀스 생성기 이름 (JPA에서 사용)
    sequenceName = "USER_SEQ",       // 실제 DB 시퀀스 이름
    initialValue = 1,                // 시작 값
    allocationSize = 1               // 증가 값 (한 번에 가져올 시퀀스 수)
)
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "USER_SEQ_GEN")
private Long id;
```

- Oracle, PostgreSQL에서 주로 사용함
- JPA가 엔티티 저장 전, 시퀀스 객체로부터 다음 값을 요청하여 엔티티의 ID로 세팅함
- **allocationSize** 조절로 한번에 여러개의 시퀀스를 미리가져와 성능 최적화 가능

#### **TABLE 전략**

```java
@TableGenerator(
    name = "USER_SEQ_GEN",
    table = "SEQUENCES",
    pkColumnValue = "USER_SEQ",
    allocationSize = 1
)
@Id
@GeneratedValue(strategy = GenerationType.TABLE, generator = "USER_SEQ_GEN")
private Long id;
```

- 키 생성 전용 테이블 사용 (DB 벤더 독립적)
- 성능상 권장되지 않음

#### **AUTO 전략**

```java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
```

- JPA(Hibernate)가 현재 연결된 DB에 맞는 전략을 **자동으로 선택함.**
- 개발자는 코드 변경 없이, DB 변경만으로 알아서 적절한 키 생성 전략을 적용하게 됨.
- MySQL → IDENTITY, Oracle → SEQUENCE로 자동 선택

---

## 5. 필드와 컬럼 매핑

### **@Enumerated - Enum 타입 매핑**

```java
@Entity
public class User {
    @Id
    private Long id;
    
    @Enumerated(EnumType.STRING)
    private UserRole role;  // "ADMIN", "USER" 등 문자열로 저장
    
    @Enumerated(EnumType.ORDINAL)
    private UserStatus status;  // 0, 1, 2 등 숫자로 저장
}

public enum UserRole {
    ADMIN, USER, GUEST
}

public enum UserStatus {
    ACTIVE, INACTIVE, SUSPENDED
}
```

- `EnumType.STRING`: Enum 이름을 문자열로 저장 (권장)
- `EnumType.ORDINAL`: Enum 순서를 숫자로 저장 (비권장 - 순서 변경 시 문제 발생)

### **@Temporal - 날짜 타입 매핑**

```java
@Entity
public class User {
    @Id
    private Long id;
    
    @Temporal(TemporalType.DATE)
    private Date birthDate;  // 날짜만 저장 (YYYY-MM-DD)
    
    @Temporal(TemporalType.TIME)
    private Date loginTime;  // 시간만 저장 (HH:MM:SS)
    
    @Temporal(TemporalType.TIMESTAMP)
    private Date createdAt;  // 날짜와 시간 모두 저장 (YYYY-MM-DD HH:MM:SS)
}
```

- `TemporalType.DATE`: 날짜만 (java.sql.Date)
- `TemporalType.TIME`: 시간만 (java.sql.Time)
- `TemporalType.TIMESTAMP`: 날짜와 시간 (java.sql.Timestamp)

> [!note] Java 8 이후
> Java 8 이후부터는 `@Temporal` 어노테이션 없이 `LocalDate`, `LocalTime`, `LocalDateTime`을 직접 사용할 수 있습니다.

```java
@Entity
public class User {
    @Id
    private Long id;
    
    private LocalDate birthDate;      // @Temporal 불필요
    private LocalTime loginTime;      // @Temporal 불필요
    private LocalDateTime createdAt;   // @Temporal 불필요
}
```

### **@Lob - 대용량 데이터 매핑**

```java
@Entity
public class Post {
    @Id
    private Long id;
    
    @Lob
    private String content;  // CLOB (Character Large Object) - 긴 텍스트
    
    @Lob
    private byte[] image;   // BLOB (Binary Large Object) - 바이너리 데이터
}
```

- `@Lob`은 대용량 데이터를 저장할 때 사용
- String 타입 → CLOB (텍스트)
- byte[] 타입 → BLOB (바이너리)

### **@Transient - 영속성 제외**

```java
@Entity
public class User {
    @Id
    private Long id;
    
    private String name;
    
    @Transient
    private String tempPassword;  // DB에 저장되지 않음
}
```

- `@Transient`가 붙은 필드는 데이터베이스에 저장되지 않음
- 임시 데이터나 계산된 값을 저장할 때 사용

### **@CreatedDate, @LastModifiedDate - 자동 날짜 관리**

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class User {
    @Id
    private Long id;
    
    @CreatedDate
    private LocalDateTime createdAt;
    
    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```

- Spring Data JPA의 Auditing 기능
- 엔티티 생성/수정 시 자동으로 날짜를 설정
- `@EntityListeners(AuditingEntityListener.class)` 필요

---

## 요약

- **엔티티**는 `@Entity`로 선언된 데이터베이스 테이블과 매핑되는 도메인 객체입니다.
- **매핑 어노테이션**을 통해 객체 필드와 데이터베이스 컬럼을 연결합니다.
- **스키마 자동 생성** 기능으로 개발 초기 생산성을 향상시킬 수 있습니다.
- **기본 키 매핑 전략**은 IDENTITY, SEQUENCE, TABLE, AUTO 중 선택할 수 있으며, DB 종류에 따라 적절한 전략을 선택해야 합니다.
- **필드 매핑**을 통해 Enum, 날짜, 대용량 데이터 등 다양한 타입을 처리할 수 있습니다.

---

## 참고 자료

- [JPA 공식 문서](https://jakarta.ee/specifications/persistence/)
- [Hibernate 공식 문서](https://hibernate.org/orm/documentation/)

