# JPA와 ORM (SQL 중심 개발 vs ORM)

#spring #jpa #orm #sql #hibernate #면접 #개념정리

**관련 개념:** [[RESTful API (REST API)|RESTful API]] [[DTO (Data Transfer Object)|DTO]] [[H2 Database (H2 데이터베이스)|H2 Database]]

> [!note] 이어서 읽기
> JPA를 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

SQL 중심 개발과 ORM의 차이를 이해하고, 객체와 관계형 데이터베이스 간의 패러다임 불일치 문제를 JPA를 통해 어떻게 해결하는지 학습한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- SQL 중심 개발은 개발자가 비즈니스 로직보다 SQL 작성과 데이터 처리 코드에 더 많은 시간을 쓰게 하며, 객체 지향과 관계형 데이터베이스 간의 패러다임 불일치로 인해 복잡한 매핑 작업과 유지보수 어려움이 발생합니다.

- JPA(Java Persistence API)는 ORM(Object-Relational Mapping) 표준 기술로, 객체를 관계형 데이터베이스에 매핑하여 SQL을 직접 작성하지 않고도 객체를 통해 데이터베이스 작업을 할 수 있도록 합니다. 이를 통해 생산성 향상, 자동 성능 최적화, 트랜잭션과 데이터 일관성 관리의 편의성을 제공합니다.

- SQL 중심 개발의 문제점, 객체와 RDB의 차이, JPA/ORM의 개념과 장점, 그리고 JPA를 활용한 테이블 설계 방법을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**SQL 중심 개발**은 애플리케이션 개발에서 데이터 처리를 SQL 쿼리에 의존하여 설계하고 구현하는 방식입니다. 데이터를 조회, 저장, 수정, 삭제하는 모든 작업을 직접 작성한 SQL 쿼리로 수행합니다.

**ORM(Object-Relational Mapping)**은 객체(Object)와 관계형 데이터베이스(Relational Database) 사이의 불일치(패러다임 차이)를 해결하기 위한 기술입니다. 객체지향 언어(자바)의 객체와 데이터베이스의 테이블을 매핑하여, SQL을 직접 작성하지 않고도 객체를 통해 데이터베이스 작업을 할 수 있도록 도와줍니다.

**JPA(Java Persistence API)**는 자바 객체를 관계형 데이터베이스에 매핑하기 위한 ORM 표준 기술로, 대표적인 구현체로는 Hibernate가 있습니다.

> [!tip] 핵심 포인트
> JPA는 객체는 객체대로 설계하고 관계형 데이터베이스는 관계형 데이터베이스대로 설계할 수 있게 해주며, SQL 중심 개발의 문제점을 해결하여 생산성과 유지보수성을 크게 향상시킵니다.

---

## 1. SQL 중심 개발의 배경

### 우리는 왜 관계형 데이터베이스(RDB)를 사용할까?

- 데이터는 체계적이고 일관성 있게 관리되어야 한다.

- 이를 위해 오랫동안 **RDBMS**가 표준처럼 사용되어 왔다.
    - 예) Oracle, MySQL, PostgreSQL

- 그리고 데이터를 다루기 위해 **SQL**이라는 강력한 언어를 사용한다.
    - 데이터 조회: `SELECT`
    - 데이터 삽입/수정/삭제: `INSERT`, `UPDATE`, `DELETE`

➡ **결국, 데이터 처리는 "SQL" 중심으로 개발**하게 된다.

### SQL 중심 개발이란?

- 애플리케이션 개발에서 **데이터 처리를 SQL 쿼리에 의존**하여 설계하고 구현하는 방식을 말한다.

- 데이터를 조회, 저장, 수정, 삭제하는 모든 작업을 **직접 작성한 SQL 쿼리**로 수행한다.

→ 개발자는 비즈니스 로직보다 SQL 작성과 데이터 처리 코드에 더 많은 시간을 쓰게 된다.

### SQL 중심 개발의 특징

- **SQL 우선 사고방식**: 비즈니스 로직보다, "어떤 쿼리를 짜야 원하는 데이터를 얻을까?"에 집중

- **테이블 설계 주도**: 객체 설계보다 테이블 설계와 스키마가 먼저 결정됨

- **코드와 쿼리 분리 어려움**: 서비스 로직과 데이터 처리 로직이 긴밀하게 얽힘 (DAO에 SQL 하드코딩)

---

## 2. 객체와 관계형 데이터베이스(RDB)의 차이

### 1. 상속 (Inheritance)

#### 객체지향 세계에서는?

- 클래스를 상속받아 공통된 코드를 재사용하고, 필요한 부분만 오버라이딩해서 사용한다.

- 예) `Vehicle`이라는 부모 클래스가 있고, 이를 상속한 `Car`, `Bike` 클래스가 존재.

- 다형성을 이용해 부모 타입으로 자식 객체를 유연하게 처리할 수 있다.

```java
// 객체지향: 상속을 통한 코드 재사용
class Vehicle {
    void move() { }
}

class Car extends Vehicle {
    @Override
    void move() { }
}

class Bike extends Vehicle {
    @Override
    void move() { }
}

// 다형성 활용
Vehicle vehicle = new Car();  // ✅ 자연스러운 사용
```

#### 관계형 데이터베이스에서는?

- 테이블에는 상속이라는 개념이 없다.

- 상속 구조를 표현하려면 설계로 해결해야 한다.
    - **단일 테이블 전략:** 한 테이블에 모든 컬럼을 넣고, 구분 컬럼으로 타입을 식별.
    - **조인 전략:** 부모와 자식 테이블을 나누고, 조인으로 관계를 맺음.
    - **구체 클래스 전략:** 자식 테이블만 사용하고 부모 테이블은 만들지 않음.

- 설계가 복잡하고 성능 문제를 유발할 수 있다.

```sql
-- 관계형 DB: 상속 표현이 복잡함
-- 단일 테이블 전략 예시
CREATE TABLE vehicle (
    id BIGINT PRIMARY KEY,
    type VARCHAR(10),  -- 구분 컬럼
    car_specific_field VARCHAR(100),  -- Car 전용 필드
    bike_specific_field VARCHAR(100)   -- Bike 전용 필드
);
```

### 2. 연관관계 (Association)

#### 객체지향 세계에서는?

- 객체는 다음과 같이 다른 객체를 **참조 변수**로 쉽게 연결한다. 이 방식이 객체다운 모델링 방식이다.

```java
class Order {
    Member member;  // 주문한 회원 정보 참조
}

// 단순히 order.getMember() 호출만으로 회원 정보에 접근할 수 있다.
Order order = new Order();
Member member = order.getMember();  // ✅ 간단한 접근
```

#### 관계형 데이터베이스에서는?

- 테이블 간의 연관관계는 **외래 키(Foreign Key)**로 표현하여 객체를 테이블에 맞추어 모델링한다.

- 다른 테이블의 정보를 가져오기 위해서는 반드시 **JOIN** 쿼리를 사용해야 한다.

- 객체처럼 쉽게 탐색할 수 없고, 필요한 데이터를 가져오기 위해 복잡한 SQL이 필요하다.

```sql
-- 관계형 DB: JOIN 쿼리 필요
SELECT o.*, m.*
FROM orders o
JOIN members m ON o.member_id = m.id
WHERE o.id = 1;
```

### 3. 데이터 타입 (Data Type)

#### 객체지향 세계에서는?

- 문자열, 숫자, 컬렉션(List, Set), 사용자 정의 클래스, 복합 객체 등 다양한 데이터 타입을 자유롭게 사용한다.

- 예를 들어, `Order` 객체 안에 `List<Item>`을 필드로 두어 한 번에 여러 상품 정보를 관리할 수 있다.

```java
class Order {
    private List<Item> items;  // ✅ 컬렉션을 필드로 사용 가능
    private Address address;    // ✅ 복합 객체 사용 가능
}
```

#### 관계형 데이터베이스에서는?

- 정해진 데이터 타입만 사용해야 한다.

- VARCHAR, INT, DATE 등 단순한 데이터만 저장 가능하며, 컬렉션 같은 복잡한 구조는 테이블로 따로 분리해야 한다.

- 컬렉션이나 복합 객체는 별도의 테이블을 만들고, 외래 키로 연결해야 표현할 수 있다.

```sql
-- 관계형 DB: 컬렉션은 별도 테이블로 분리
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    member_id BIGINT
);

CREATE TABLE order_items (  -- 컬렉션을 별도 테이블로
    id BIGINT PRIMARY KEY,
    order_id BIGINT,
    item_id BIGINT
);
```

### 4. 데이터 식별 방법 (Identity)

#### 객체지향 세계에서는?

- 객체는 **참조값(메모리 주소)**으로 식별된다.

- 같은 값을 가진 객체라도 메모리 주소가 다르면 서로 다른 객체로 취급된다.

- 예를 들어 `new Member("홍길동")`을 두 번 실행하면 서로 다른 두 객체다.

```java
Member member1 = new Member("홍길동");
Member member2 = new Member("홍길동");
System.out.println(member1 == member2);  // false (다른 객체)
```

#### 관계형 데이터베이스에서는?

- 데이터를 식별하기 위해 **기본 키(Primary Key)**를 사용한다.

- 동일한 PK 값을 가지면 같은 엔티티로 간주하며, 이는 영속적이고 변하지 않다.

```sql
-- 관계형 DB: PK로 식별
SELECT * FROM members WHERE id = 1;  -- PK로 식별
```

---

## 3. SQL 중심 개발의 문제점

### 1. 반복적인 SQL 작성과 매핑 작업의 부담 (개발자는 매퍼가 된다)

- **CRUD 작업의 반복**: 새로운 테이블을 추가하거나 필드를 변경할 때마다 INSERT, SELECT, UPDATE, DELETE 쿼리를 작성해야 한다.

- **객체-테이블 매핑의 수작업**: 자바 객체를 데이터베이스 테이블과 매핑하기 위해 수동으로 코드를 작성해야 하며, 이는 실수의 가능성을 높인다.

> 예를 들어 `Member` 클래스에 `tel` 필드를 추가하면, 관련된 모든 SQL 쿼리와 매핑 코드를 수정해야 한다.

```java
// SQL 중심 개발: 필드 추가 시 모든 쿼리 수정 필요
public class MemberDAO {
    // INSERT 쿼리 수정
    public void insert(Member member) {
        String sql = "INSERT INTO members (email, name, age, tel) VALUES (?, ?, ?, ?)";
        // ...
    }
    
    // SELECT 쿼리 수정
    public Member findById(String email) {
        String sql = "SELECT email, name, age, tel FROM members WHERE email = ?";
        // ...
    }
    
    // UPDATE 쿼리 수정
    public void update(Member member) {
        String sql = "UPDATE members SET name = ?, age = ?, tel = ? WHERE email = ?";
        // ...
    }
}
```

### 2. 객체 지향과 관계형 데이터베이스 간의 패러다임 불일치

- **상속**: 객체 지향에서는 상속을 통해 코드 재사용과 구조화를 하지만 관계형 데이터베이스는 상속 개념이 없어, 이를 표현하려면 별도의 테이블과 조인 작업이 필요하다.

- **연관 관계**: 객체는 참조를 통해 다른 객체와 연관되지만 데이터베이스는 외래 키(FK)를 사용하여 테이블 간 관계를 설정한다.

- **데이터 탐색**: 객체는 메서드를 통해 다른 객체를 쉽게 탐색할 수 있지만 SQL 중심 개발에서는 필요한 데이터를 얻기 위해 복잡한 조인 쿼리를 작성해야 한다.

```java
// 객체지향: 간단한 탐색
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // ✅ 간단

// SQL 중심: 복잡한 조인 쿼리 필요
String sql = "SELECT o.*, m.* FROM orders o JOIN members m ON o.member_id = m.id WHERE o.id = ?";
```

### 3. 계층 간의 강한 결합과 유지보수의 어려움

- **DAO와 서비스 계층의 결합**: SQL 쿼리가 DAO에 하드코딩되어 있어, 서비스 계층에서 어떤 데이터가 조회되는지 명확하지 않다.

- **변경의 전파**: 데이터베이스 스키마나 비즈니스 로직의 변경이 전체 계층에 영향을 미쳐, 유지보수가 어렵다.

```java
// SQL 중심 개발: 계층 간 강한 결합
public class MemberService {
    private MemberDAO memberDAO;  // DAO에 의존
    
    public Member getMember(String email) {
        // DAO의 SQL 쿼리가 변경되면 서비스 로직도 영향받음
        return memberDAO.findById(email);
    }
}
```

### 4. 데이터베이스 종속성과 이식성 문제

- **DBMS 종속성**: SQL 문법은 데이터베이스 시스템에 따라 다르므로, 특정 DBMS에 종속적인 코드를 작성하게 된다.

- **이식성 저하**: 다른 DBMS로의 전환 시, 기존의 SQL 쿼리를 모두 수정해야 하는 부담이 있다.

```sql
-- MySQL 전용 문법
SELECT * FROM members LIMIT 10;

-- Oracle로 전환 시 수정 필요
SELECT * FROM members WHERE ROWNUM <= 10;
```

→ 객체답게 모델링하고 사용하면서 DB를 활용하는 방법은 없을까?

---

## 4. 자바 ORM 표준 기술 JPA

### JPA(Java Persistence API)란 무엇인가?

- 자바 객체를 관계형 데이터베이스에 매핑하기 위한 **ORM(Object-Relational Mapping) 표준 기술**이다.

- 애플리케이션과 JDBC 사이에서 동작한다.

- 대표적인 JPA 구현체로는 **Hibernate** (가장 많이 사용됨), EclipseLink, OpenJPA 등이 있다.

```
애플리케이션
    ↓
  JPA (표준 API)
    ↓
 Hibernate (구현체)
    ↓
   JDBC
    ↓
   DB
```

### ORM (Object-Relational Mapping)란?

- **ORM**은 **객체(Object)**와 **관계형 데이터베이스(Relational Database)** 사이의 불일치(패러다임 차이)를 해결하기 위한 기술이다.

- 객체지향 언어(자바)의 **객체**와 데이터베이스의 **테이블**을 매핑(mapping)하여, SQL을 직접 작성하지 않고도 객체를 통해 데이터베이스 작업을 할 수 있도록 도와준다.

- 객체는 객체대로 설계하고 관계형 데이터베이스는 관계형 데이터베이스대로 설계할 수 있게 해준다.

```
객체 (Object)          ORM          테이블 (Table)
  Member    ←─────── 매핑 ───────→   members
  Order     ←─────── 매핑 ───────→   orders
```

### SQL 중심 개발 vs ORM 비교

| 구분 | 전통적인 개발 방식 (SQL 중심) | ORM 사용 |
|------|------------------------------|---------|
| **데이터 저장** | SQL 작성: `INSERT INTO...` | 객체 저장: `em.persist(객체)` |
| **데이터 조회** | SQL 작성: `SELECT * FROM...` | 객체 탐색: `em.find(객체id)` |
| **코드 구조** | SQL + 비즈니스 로직 분리 어려움 | 객체지향적인 코드 유지 |
| **생산성** | 반복적인 SQL 작성 필요 | CRUD 자동 처리 |
| **유지보수** | 필드 변경 시 모든 쿼리 수정 | 객체 변경만으로 자동 반영 |
| **패러다임 불일치** | 개발자가 직접 해결 | ORM이 자동 처리 |

### JPA와 ORM의 관계 정리

- **ORM**: 개념 (객체-관계 매핑 기술)
- **JPA**: 자바에서 ORM을 사용하기 위한 **표준 API**
- **Hibernate**: JPA 표준에 따라 만들어진 실제 구현체 (라이브러리)

비유하자면: **ORM** = 자동차라는 개념, **JPA** = 자동차를 만들기 위한 국제 표준 (규격), **Hibernate** = JPA 표준에 따라 만들어진 실제 자동차 (라이브러리)

---

## 5. JPA, 왜 사용해야 할까?

### 1. SQL 중심적인 개발에서 객체 중심으로 개발로 생산성 향상

- 새로운 엔티티가 생길 때마다 반복되는 CRUD SQL 작성
    - `SELECT`, `INSERT`, `UPDATE`, `DELETE`…

- 필드 하나 추가할 때도 DAO, DTO, SQL 전부 수정

- JPA는 이런 반복 작업을 제거한다.
    - 저장: `em.persist()`, 조회: `em.find()`, 삭제: `em.remove()` 같은 메서드 호출로 끝!
    - Spring Data JPA 사용 시 인터페이스만 정의하면 CRUD 자동 제공

```java
// SQL 중심 개발: 반복적인 SQL 작성
public class MemberDAO {
    public void insert(Member member) {
        String sql = "INSERT INTO members (email, name, age) VALUES (?, ?, ?)";
        // PreparedStatement 설정...
    }
    
    public Member findById(String email) {
        String sql = "SELECT email, name, age FROM members WHERE email = ?";
        // ResultSet 처리...
    }
}

// JPA 사용: 간단한 메서드 호출
public class MemberService {
    @Autowired
    private MemberRepository memberRepository;
    
    public void save(Member member) {
        memberRepository.save(member);  // ✅ 간단!
    }
    
    public Member findById(String email) {
        return memberRepository.findById(email).orElse(null);  // ✅ 간단!
    }
}
```

### 2. 자동 성능 최적화

- **1차 캐시, 2차 캐시**로 쓸데없는 DB 접근 최소화함

- **지연 로딩(Lazy Loading)**, **즉시 로딩(Eager Loading)** 설정으로 원하는 시점에만 데이터를 조회함.

- 필요한 데이터를 언제, 어떻게 가져올지 전략적으로 제어가 가능함

```java
// JPA: 지연 로딩으로 성능 최적화
@Entity
public class Order {
    @ManyToOne(fetch = FetchType.LAZY)  // 지연 로딩
    private Member member;
}

// member는 실제 사용할 때만 조회됨
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // 이 시점에 DB 조회
```

### 3. 트랜잭션과 데이터 일관성 관리가 편함

- JDBC나 MyBatis를 사용할 때는 개발자가 직접 트랜잭션을 시작하고, 커밋하거나 롤백해야 한다. 이는 코드가 복잡해지고, 트랜잭션 경계를 실수로 놓치면 데이터 일관성이 깨질 수 있습니다.

- JPA는 트랜잭션 경계 내에서 **영속성 컨텍스트(Persistence Context)**를 사용해 엔티티 상태를 자동으로 관리한다.

- 개발자는 객체의 상태만 변경하면, JPA가 알아서 변경 사항을 감지하고 트랜잭션 종료 시점에 자동으로 DB에 반영합니다.

```java
// SQL 중심 개발: 직접 트랜잭션 관리
public void updateMember(String email, String name) {
    Connection conn = getConnection();
    try {
        conn.setAutoCommit(false);
        String sql = "UPDATE members SET name = ? WHERE email = ?";
        // PreparedStatement 설정...
        conn.commit();
    } catch (Exception e) {
        conn.rollback();
    }
}

// JPA: 자동 트랜잭션 관리
@Transactional
public void updateMember(String email, String name) {
    Member member = memberRepository.findById(email).orElseThrow();
    member.setName(name);  // ✅ 객체만 변경하면 자동으로 DB 반영
}
```

---

## 6. JPA로 테이블을 설계하는 방법

### 1. Spring Boot 프로젝트에 JPA 추가하기

#### 1) build.gradle 설정

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}
```

#### 2) application.yml 설정

```yaml
spring:
  jpa:
    hibernate:
      ddl-auto: create  # 개발 환경용 (운영에서는 validate 사용)
    show-sql: true      # SQL 로그 출력
    properties:
      hibernate:
        dialect: org.hibernate.dialect.H2Dialect
        format_sql: true
```

#### 3) DB 연결 확인

애플리케이션 실행 후 연결 에러가 없다면 JPA 준비 완료.

### 2. JPA로 테이블을 설계하는 기본 원칙

JPA는 **객체(Entity)**를 기반으로 테이블을 자동 생성하거나 매핑하므로 개발자 입장에서는 "객체 설계 → 테이블 생성" 흐름으로 진행하게 된다.

#### 1) Entity 클래스 만들기

- 테이블 1개 = 엔티티 클래스 1개
- PK 필수
- 기본 생성자 필수(Protected 추천)

예시: 회원(Member)

```java
@Entity
@Getter 
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor 
@Builder
public class Member {
    @Id
    private String email;        // PK — natural key 가능
    private String name;
    private int age;
}
```

#### 2) Primary Key(PK) 설계

JPA에서 PK 전략 3가지

| 전략 | 설명 | 사용 예 |
|------|------|---------|
| `IDENTITY` | DB의 AUTO_INCREMENT 사용 | MySQL |
| `SEQUENCE` | DB 시퀀스 사용 | Oracle, PostgreSQL |
| `AUTO` | DB에 따라 자동 선택 | 초기 실습용 |
| 직접 할당 | 개발자가 직접 PK 설정 | 이메일, 사용자ID |

예시

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

#### 3) 컬럼 매핑

가장 많이 쓰는 컬럼 매핑 어노테이션

| 어노테이션 | 설명 |
|-----------|------|
| `@Column(nullable=false)` | NOT NULL |
| `@Column(unique=true)` | UNIQUE |
| `@Column(length=100)` | VARCHAR 크기 지정 |
| `@Column(columnDefinition="TEXT")` | TEXT, LONGTEXT 등 직접 지정 |
| `@CreationTimestamp` | 생성시간 자동 저장 |
| `@UpdateTimestamp` | 수정시간 자동 업데이트 |

```java
@Entity
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false, length = 100)
    private String name;
    
    @Column(unique = true)
    private String email;
    
    @CreationTimestamp
    private LocalDateTime createdAt;
    
    @UpdateTimestamp
    private LocalDateTime updatedAt;
}
```

#### 4) 연관관계 설계 (Association)

JPA에서 가장 핵심 개념.

##### 1:N (Many-to-One)

```java
// Many(댓글) → One(게시글)
@Entity
public class Comment {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "board_id")
    private Board board;
}
```

##### 1:1 (One-to-One)

```java
@Entity
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private MemberProfile profile;
}
```

##### N:N (Many-to-Many)

> [!warning] 주의사항
> N:N 관계는 사용하면 안 됩니다. → 중간 테이블로 1:N + N:1로 풀어야 합니다.

```java
// ❌ 나쁜 예: N:N 직접 사용
@ManyToMany
@JoinTable(name = "member_role")
private List<Role> roles;

// ✅ 좋은 예: 중간 테이블로 분리
@Entity
public class MemberRole {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private Member member;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "role_id")
    private Role role;
}
```

### 3. JPA 테이블 설계 예시 (Board – Member)

#### Member

```java
@Entity
@Getter 
@Builder
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
public class Member {
    @Id
    private String email;
    
    @Column(nullable = false)
    private String name;
}
```

#### Board

```java
@Entity
@Getter 
@Builder
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
public class Board {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long boardId;
    
    @Column(nullable = false)
    private String title;
    
    @Column(nullable = false, columnDefinition = "TEXT")
    private String contents;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_email", nullable = false)
    private Member member;   // 연관관계
}
```

---

### **Interview Answer Version (면접 답변식 요약)**

SQL 중심 개발은 애플리케이션 개발에서 데이터 처리를 SQL 쿼리에 의존하여 설계하고 구현하는 방식으로, 개발자가 비즈니스 로직보다 SQL 작성과 데이터 처리 코드에 더 많은 시간을 쓰게 됩니다. 이는 반복적인 SQL 작성과 매핑 작업의 부담, 객체 지향과 관계형 데이터베이스 간의 패러다임 불일치, 계층 간의 강한 결합과 유지보수의 어려움, 데이터베이스 종속성과 이식성 문제를 야기합니다.

JPA(Java Persistence API)는 자바 객체를 관계형 데이터베이스에 매핑하기 위한 ORM(Object-Relational Mapping) 표준 기술로, 객체와 관계형 데이터베이스 사이의 불일치를 해결합니다. JPA를 사용하면 SQL 중심적인 개발에서 객체 중심으로 개발하여 생산성을 향상시킬 수 있고, 자동 성능 최적화(1차 캐시, 지연 로딩 등)를 제공하며, 트랜잭션과 데이터 일관성 관리가 편리해집니다.

JPA는 객체는 객체대로 설계하고 관계형 데이터베이스는 관계형 데이터베이스대로 설계할 수 있게 해주며, Entity 클래스를 기반으로 테이블을 자동 생성하거나 매핑합니다. 대표적인 JPA 구현체로는 Hibernate가 있으며, Spring Data JPA를 사용하면 인터페이스만 정의하면 CRUD가 자동으로 제공됩니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### JPA 사용 가이드

**1. Entity 설계 시 주의사항**

```java
// ✅ 좋은 예: 기본 생성자 protected
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
}

// ❌ 나쁜 예: 기본 생성자 public (무분별한 객체 생성 가능)
@Entity
public class Member {
    @Id
    private Long id;
    private String name;
    
    public Member() { }  // 위험: 불완전한 객체 생성 가능
}
```

**2. 연관관계 설정 시 지연 로딩 사용**

```java
// ✅ 좋은 예: 지연 로딩 사용
@Entity
public class Order {
    @ManyToOne(fetch = FetchType.LAZY)  // 지연 로딩
    @JoinColumn(name = "member_id")
    private Member member;
}

// ❌ 나쁜 예: 즉시 로딩 (N+1 문제 발생 가능)
@Entity
public class Order {
    @ManyToOne(fetch = FetchType.EAGER)  // 즉시 로딩
    @JoinColumn(name = "member_id")
    private Member member;
}
```

**3. N:N 관계는 피하고 중간 테이블로 분리**

```java
// ❌ 나쁜 예: N:N 직접 사용
@Entity
public class Member {
    @ManyToMany
    @JoinTable(name = "member_role")
    private List<Role> roles;
}

// ✅ 좋은 예: 중간 테이블로 분리
@Entity
public class MemberRole {
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private Member member;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "role_id")
    private Role role;
}
```

**4. Spring Data JPA 활용**

```java
// Repository 인터페이스만 정의하면 CRUD 자동 제공
public interface MemberRepository extends JpaRepository<Member, String> {
    // 쿼리 메서드 자동 생성
    List<Member> findByName(String name);
    Optional<Member> findByEmail(String email);
    
    // @Query 어노테이션으로 커스텀 쿼리
    @Query("SELECT m FROM Member m WHERE m.age > :age")
    List<Member> findMembersOlderThan(@Param("age") int age);
}
```

#### 주의사항

**1. N+1 문제**

```java
// N+1 문제 발생 예시
List<Order> orders = orderRepository.findAll();
for (Order order : orders) {
    Member member = order.getMember();  // 각 Order마다 쿼리 실행
}

// 해결: Fetch Join 사용
@Query("SELECT o FROM Order o JOIN FETCH o.member")
List<Order> findAllWithMember();
```

**2. 트랜잭션 관리**

```java
// ✅ 좋은 예: @Transactional 사용
@Transactional
public void updateMember(String email, String name) {
    Member member = memberRepository.findById(email).orElseThrow();
    member.setName(name);  // 변경 감지로 자동 업데이트
}

// ❌ 나쁜 예: 트랜잭션 없이 사용
public void updateMember(String email, String name) {
    Member member = memberRepository.findById(email).orElseThrow();
    member.setName(name);  // 변경 사항이 DB에 반영되지 않음
}
```

**3. ddl-auto 설정**

```yaml
# 개발 환경
spring:
  jpa:
    hibernate:
      ddl-auto: create  # 또는 update

# 운영 환경
spring:
  jpa:
    hibernate:
      ddl-auto: validate  # 스키마 변경 금지
```

---

### 예상 꼬리질문정리

1. **SQL 중심 개발의 문제점은 무엇인가요?**
   - 반복적인 SQL 작성과 매핑 작업의 부담, 객체 지향과 관계형 데이터베이스 간의 패러다임 불일치, 계층 간의 강한 결합과 유지보수의 어려움, 데이터베이스 종속성과 이식성 문제가 있습니다. 필드 하나 추가할 때도 DAO, DTO, SQL 전부 수정해야 하는 부담이 있습니다.

2. **객체와 관계형 데이터베이스의 차이는 무엇인가요?**
   - 상속: 객체는 상속을 통해 코드 재사용이 가능하지만, RDB는 상속 개념이 없어 별도의 테이블과 조인 작업이 필요합니다. 연관관계: 객체는 참조 변수로 쉽게 연결되지만, RDB는 외래 키와 JOIN 쿼리가 필요합니다. 데이터 타입: 객체는 컬렉션, 복합 객체를 자유롭게 사용하지만, RDB는 정해진 데이터 타입만 사용 가능합니다.

3. **ORM이란 무엇인가요?**
   - ORM(Object-Relational Mapping)은 객체(Object)와 관계형 데이터베이스(Relational Database) 사이의 불일치를 해결하기 위한 기술입니다. 객체지향 언어의 객체와 데이터베이스의 테이블을 매핑하여, SQL을 직접 작성하지 않고도 객체를 통해 데이터베이스 작업을 할 수 있도록 도와줍니다.

4. **JPA와 ORM의 관계는 무엇인가요?**
   - ORM은 개념이고, JPA는 자바에서 ORM을 사용하기 위한 표준 API입니다. Hibernate는 JPA 표준에 따라 만들어진 실제 구현체입니다. 비유하자면 ORM은 자동차라는 개념, JPA는 자동차를 만들기 위한 국제 표준, Hibernate는 실제 자동차입니다.

5. **JPA를 사용하는 이유는 무엇인가요?**
   - SQL 중심적인 개발에서 객체 중심으로 개발하여 생산성을 향상시킬 수 있고, 자동 성능 최적화(1차 캐시, 지연 로딩 등)를 제공하며, 트랜잭션과 데이터 일관성 관리가 편리합니다. 또한 객체는 객체대로 설계하고 관계형 데이터베이스는 관계형 데이터베이스대로 설계할 수 있게 해줍니다.

6. **JPA의 영속성 컨텍스트란 무엇인가요?**
   - 영속성 컨텍스트는 엔티티를 영구 저장하는 환경으로, JPA가 트랜잭션 경계 내에서 엔티티 상태를 자동으로 관리합니다. 개발자는 객체의 상태만 변경하면, JPA가 알아서 변경 사항을 감지하고 트랜잭션 종료 시점에 자동으로 DB에 반영합니다.

7. **JPA에서 지연 로딩과 즉시 로딩의 차이는 무엇인가요?**
   - 지연 로딩(Lazy Loading)은 연관된 엔티티를 실제 사용할 때 조회하는 방식이고, 즉시 로딩(Eager Loading)은 연관된 엔티티를 즉시 조회하는 방식입니다. 지연 로딩을 사용하면 필요한 데이터만 조회하여 성능을 최적화할 수 있지만, 즉시 로딩은 N+1 문제를 발생시킬 수 있습니다.

8. **N+1 문제란 무엇이고 어떻게 해결하나요?**
   - N+1 문제는 연관된 엔티티를 조회할 때 각 엔티티마다 추가 쿼리가 실행되는 문제입니다. 이를 해결하기 위해 Fetch Join을 사용하거나, @EntityGraph를 활용하여 한 번의 쿼리로 필요한 데이터를 모두 조회할 수 있습니다.

9. **JPA에서 N:N 관계를 사용하면 안 되는 이유는 무엇인가요?**
   - N:N 관계는 중간 테이블을 자동으로 생성하는데, 이 테이블에 추가 컬럼을 넣을 수 없고, 쿼리도 복잡해집니다. 따라서 중간 테이블을 엔티티로 만들어 1:N + N:1 관계로 풀어야 합니다.

10. **JPA의 ddl-auto 설정은 무엇인가요?**
    - ddl-auto는 데이터베이스 스키마를 자동으로 생성, 수정, 삭제하는 설정입니다. create는 애플리케이션 시작 시 스키마를 생성하고 종료 시 삭제하며, update는 변경된 부분만 수정합니다. 운영 환경에서는 validate를 사용하여 스키마 변경을 금지해야 합니다.

---

## 관련 노트

- **[[RESTful API (REST API)|RESTful API]]** - JPA를 활용한 REST API 개발
- **[[DTO (Data Transfer Object)|DTO]]** - JPA Entity와 DTO의 분리
- **[[H2 Database (H2 데이터베이스)|H2 Database]]** - JPA 개발 시 사용하는 인메모리 데이터베이스

---

**태그:** #spring #jpa #orm #sql #hibernate #면접 #개념정리

