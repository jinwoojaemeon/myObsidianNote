#spring #jpa #orm #sql #hibernate #jdbc #mybatis #면접 #개념정리

**관련 개념:** [[JPA 엔티티 매핑|JPA 엔티티 매핑]] [[JPA 연관관계 매핑|JPA 연관관계 매핑]] [[RESTful API (REST API)|RESTful API]] [[DTO (Data Transfer Object)|DTO]] [[H2 Database (H2 데이터베이스)|H2 Database]]

> [!note] 이어서 읽기
> JPA를 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**ORM(Object-Relational Mapping)** 은 객체와 관계형 데이터베이스 사이의 패러다임 불일치를 해결해주는 기술입니다. 자바 진영의 ORM 표준 스펙인 **JPA(Java Persistence API)** 는 개발자가 객체 중심으로 개발할 수 있도록 내부적으로 SQL 생성, 실행, 매핑을 대신 처리해줍니다. 영속성 계층 기술은 JDBC → SQL Mapper → ORM으로 발전하며, 각 단계마다 추상화 수준이 높아져 개발 편의성이 향상되지만, ORM만이 객체와 관계형 데이터베이스 간의 패러다임 불일치 문제를 근본적으로 해결합니다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 객체 지향 언어와 관계형 데이터베이스는 데이터를 다루는 방식이 다르기 때문에 패러다임 불일치 문제가 발생합니다. 상속, 연관관계, 객체 그래프 탐색, 비교(Identity) 4가지 주요 문제로 인해 객체를 테이블에 저장하거나 테이블의 데이터를 객체로 변환할 때 복잡한 매핑 작업이 필요합니다. JDBC와 SQL Mapper는 이러한 패러다임 불일치를 해결하지 못하고, 개발자가 직접 SQL을 작성하고 매핑 코드를 작성해야 합니다.

- ORM은 객체와 관계형 데이터베이스 사이의 패러다임 불일치를 해결하는 기술로, 자동 SQL 생성, 지연 로딩, 동일성 보장 등의 기능을 제공합니다. 개발자가 객체 중심으로 개발할 수 있도록 내부적으로 SQL 생성, 실행, 매핑을 대신 처리하여, 객체는 객체대로 설계하고 관계형 데이터베이스는 관계형 데이터베이스대로 설계할 수 있게 해줍니다. 이를 통해 개발 편의성과 생산성이 크게 향상되지만, 학습 곡선이 높고 내부 동작을 이해하지 못하면 성능 이슈가 발생할 수 있습니다.

- 영속성 계층의 개념, JDBC와 SQL Mapper의 특징과 한계, 객체와 RDB 간의 패러다임 불일치 4가지 문제(상속, 연관관계, 객체 그래프 탐색, 비교), ORM이 이를 해결하는 방법(자동 SQL 생성, 지연 로딩, 동일성 보장), 그리고 각 기술의 장단점을 이해해야 합니다.

---

## 1. 영속성 계층(Persistence Layer)과 JDBC

### **영속성(Persistence)이란?**

**영속성(Persistence)** 은 프로그램이 종료되어도 데이터가 사라지지 않고 유지되는 특성을 의미합니다.

- 데이터를 영구적으로 보관하기 위해 **데이터베이스(DB)** 를 사용합니다.
- **영속성 계층(Persistence Layer)** 은 애플리케이션과 DB 사이에서 상호작용하는 역할을 담당합니다.

```
애플리케이션
    ↓
영속성 계층 (Persistence Layer)
    ↓
데이터베이스 (DB)
```

### **JDBC (Java Database Connectivity)**

**JDBC**는 자바에서 데이터베이스에 접속할 수 있도록 하는 자바 API입니다.

**JDBC의 역할:**
- DB 연결
- SQL 실행
- 결과 반환

**JDBC의 특징:**
- 순수 JDBC만으로도 DB 접근이 가능합니다.
- 하지만 반복적인 코드가 많아 비효율적입니다.

#### **JDBC 사용 예시**

```java
// JDBC: 직접 SQL 작성
String sql = "INSERT INTO members (email, name, age) VALUES (?, ?, ?)";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, member.getEmail());
pstmt.setString(2, member.getName());
pstmt.setInt(3, member.getAge());
pstmt.executeUpdate();
```

#### **JDBC의 문제점**

1. **반복적인 코드 작성**: CRUD 작업마다 비슷한 코드를 반복 작성해야 함
2. **수동적인 자원 관리**: Connection, PreparedStatement, ResultSet 등을 직접 생성하고 닫아야 함
3. **객체-테이블 매핑 수작업**: ResultSet에서 데이터를 꺼내 객체로 변환하는 작업을 수동으로 해야 함

```java
// JDBC: ResultSet을 객체로 변환하는 수작업
public Member findById(String email) {
    String sql = "SELECT email, name, age FROM members WHERE email = ?";
    // ... Connection, PreparedStatement 설정
    
    ResultSet rs = pstmt.executeQuery();
    if (rs.next()) {
        Member member = new Member();
        member.setEmail(rs.getString("email"));
        member.setName(rs.getString("name"));
        member.setAge(rs.getInt("age"));
        return member;
    }
    return null;
}
```

---

## 2. SQL Mapper (JDBC Template, MyBatis)

### **SQL Mapper란?**

**SQL Mapper**는 반복적인 작업을 추상화하여 편리함을 제공하는 **영속성 프레임워크**의 일종입니다.

**핵심 특징:**
- 개발자가 직접 작성한 SQL과 객체의 필드를 매핑해줍니다.
- SQL은 직접 작성하지만, 객체 매핑은 자동화합니다.

**발전 흐름:**
```
JDBC (순수 API)
    ↓
SQL Mapper (추상화)
    ↓
ORM (완전한 추상화)
```

### **JDBC Template**

**JDBC Template**은 스프링에서 제공하는 기술로, JDBC의 중복 코드를 줄이고 RowMapper 등을 통해 매핑 작업을 재사용할 수 있게 해줍니다.

**특징:**
- 스프링이 제공하는 템플릿 메서드 패턴 적용
- 자원 관리 자동화
- RowMapper를 통한 객체 매핑

```java
// JDBC Template: 중복 코드 제거
@Repository
public class MemberRepository {
    private final JdbcTemplate jdbcTemplate;
    
    public Member findById(String email) {
        String sql = "SELECT email, name, age FROM members WHERE email = ?";
        return jdbcTemplate.queryForObject(sql, 
            (rs, rowNum) -> {
                Member member = new Member();
                member.setEmail(rs.getString("email"));
                member.setName(rs.getString("name"));
                member.setAge(rs.getInt("age"));
                return member;
            }, 
            email);
    }
}
```

### **MyBatis**

**MyBatis**는 SQL을 코드와 분리(XML, Annotation 등)하여 관리하며, 동적 쿼리 작성이 용이합니다.

**특징:**
1. **SQL과 자바 코드 분리**: SQL을 XML 파일이나 어노테이션으로 분리하여 관리

```xml
<!-- MyBatis: SQL을 XML로 분리 -->
<mapper namespace="com.example.MemberMapper">
    <insert id="insert">
        INSERT INTO members (email, name, age)
        VALUES (#{email}, #{name}, #{age})
    </insert>
    
    <select id="findById" resultType="Member">
        SELECT email, name, age
        FROM members
        WHERE email = #{email}
    </select>
</mapper>
```

2. **자동 객체 매핑**: ResultSet을 자동으로 객체로 매핑 (resultType, resultMap 사용)

```java
// MyBatis: 자동 매핑
@Mapper
public interface MemberMapper {
    void insert(Member member);
    Member findById(String email);
}
```

3. **동적 SQL 지원**: 조건에 따라 SQL을 동적으로 생성 가능

```xml
<!-- MyBatis: 동적 SQL -->
<select id="search" resultType="Member">
    SELECT * FROM members
    <where>
        <if test="name != null">
            AND name LIKE #{name}
        </if>
        <if test="age != null">
            AND age = #{age}
        </if>
    </where>
</select>
```

### **SQL Mapper의 한계**

**SQL Mapper는 여전히 다음과 같은 한계가 있습니다:**

1. **여전히 SQL 중심**: 개발자가 SQL 작성에 많은 시간을 써야 합니다.
2. **패러다임 불일치 해결 불가**: 객체와 테이블 간의 패러다임 불일치 문제가 존재합니다.
3. **객체-테이블 매핑 수작업**: 복잡한 연관관계 매핑은 여전히 수동으로 처리해야 합니다.

```java
// MyBatis: 연관관계 조회 시 복잡한 SQL 필요
@Select("SELECT o.*, m.email, m.name " +
        "FROM orders o " +
        "JOIN members m ON o.member_id = m.id " +
        "WHERE o.id = #{id}")
OrderDTO findOrderWithMember(Long id);
// DTO로 변환하는 추가 작업 필요
```

---

## 3. JDBC/MyBatis vs JPA 비교

### **개발 방식의 차이**

| 구분 | JDBC | MyBatis | JPA |
|------|------|---------|-----|
| **SQL 작성** | 자바 코드에 직접 작성 | XML/어노테이션으로 분리 | 자동 생성 (필요시 JPQL) |
| **객체 매핑** | 수동 (ResultSet 처리) | 자동 (resultType) | 자동 (Entity 매핑) |
| **연관관계** | JOIN 쿼리 직접 작성 | JOIN 쿼리 직접 작성 | 객체 참조로 자동 처리 |
| **CRUD 작업** | 반복적인 코드 작성 | SQL 반복 작성 | 메서드 호출만으로 처리 |
| **트랜잭션** | 수동 관리 | 수동 관리 | 자동 관리 (@Transactional) |
| **성능 최적화** | 개발자가 직접 구현 | 개발자가 직접 구현 | 자동 (캐시, 지연 로딩) |

### **코드 비교: 회원 저장하기**

#### **JDBC 방식**

```java
public class MemberDAO {
    public void insert(Member member) {
        Connection conn = null;
        PreparedStatement pstmt = null;
        
        try {
            conn = getConnection();
            String sql = "INSERT INTO members (email, name, age) VALUES (?, ?, ?)";
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, member.getEmail());
            pstmt.setString(2, member.getName());
            pstmt.setInt(3, member.getAge());
            pstmt.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            close(conn, pstmt);
        }
    }
}
```

#### **MyBatis 방식**

```java
@Mapper
public interface MemberMapper {
    void insert(Member member);
}

// XML 파일
// <insert id="insert">
//     INSERT INTO members (email, name, age)
//     VALUES (#{email}, #{name}, #{age})
// </insert>
```

#### **JPA 방식**

```java
public class MemberService {
    @Autowired
    private MemberRepository memberRepository;
    
    public void save(Member member) {
        memberRepository.save(member);  // ✅ 간단!
    }
}
```

### **코드 비교: 연관관계 조회하기**

#### **JDBC/MyBatis 방식**

```java
// 복잡한 JOIN 쿼리 필요
@Select("SELECT o.id, o.title, m.email, m.name " +
        "FROM orders o " +
        "JOIN members m ON o.member_id = m.id " +
        "WHERE o.id = #{id}")
OrderDTO findOrderWithMember(Long id);

// DTO로 변환 필요
public class OrderDTO {
    private Long orderId;
    private String title;
    private String memberEmail;
    private String memberName;
}
```

#### **JPA 방식**

```java
// 객체 참조로 간단하게 접근
@Entity
public class Order {
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private Member member;
}

// 사용
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // ✅ 간단!
```

---

## 3. 객체와 관계형 데이터베이스의 패러다임 불일치

### **패러다임 불일치란?**

**패러다임 불일치**는 객체 지향 언어와 관계형 데이터베이스(RDB)가 데이터를 다루는 방식의 차이로 인해 발생하는 문제입니다.

객체지향 언어와 RDB는 서로 다른 목적과 원칙을 가지고 있어, 객체를 테이블에 저장하거나 테이블의 데이터를 객체로 변환할 때 여러 문제가 발생합니다.

### **1. 상속 (Inheritance)**

**문제:**
- **객체지향 세계**: 클래스를 상속받아 공통된 코드를 재사용하고, 다형성을 활용할 수 있습니다.
- **관계형 데이터베이스**: 테이블에는 상속이라는 개념이 없습니다.

**예시:**

```java
// 객체지향: 상속 구조
class Vehicle {
    String name;
}

class Car extends Vehicle {
    int doorCount;
}

class Bike extends Vehicle {
    int wheelCount;
}
```

**RDB에서 표현:**
- 상속 구조를 표현하려면 별도의 설계가 필요합니다.
- 단일 테이블 전략, 조인 전략, 구체 클래스 전략 등
- 설계가 복잡하고 성능 문제를 유발할 수 있습니다.

### **2. 연관관계 (Association)**

**문제:**
- **객체지향 세계**: 객체는 **참조(주소)**를 사용하여 다른 객체와 연관됩니다.
- **관계형 데이터베이스**: 테이블은 **외래 키(Foreign Key)**를 사용하여 테이블 간 관계를 설정합니다.

**예시:**

```java
// 객체지향: 참조로 연관관계 표현
class Order {
    Member member;  // 참조 변수
}

// 사용
Order order = ...;
Member member = order.getMember();  // 참조를 통해 접근
```

**RDB에서 표현:**
```sql
-- 테이블: 외래 키로 연관관계 표현
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    member_id BIGINT,  -- 외래 키
    FOREIGN KEY (member_id) REFERENCES members(id)
);

-- 다른 테이블 정보를 가져오려면 JOIN 필요
SELECT o.*, m.* 
FROM orders o 
JOIN members m ON o.member_id = m.id;
```

**차이점:**
- 객체는 참조를 통해 자유롭게 탐색 가능
- 테이블은 JOIN 쿼리를 사용해야만 다른 테이블 정보 접근 가능

### **3. 객체 그래프 탐색 (Object Graph Navigation)**

**문제:**
- **객체지향 세계**: 객체는 참조를 통해 **자유롭게 탐색**이 가능합니다.
- **관계형 데이터베이스**: 처음 실행한 SQL(조인 여부)에 따라 **탐색 범위가 제한**됩니다.

**예시:**

```java
// 객체지향: 자유로운 탐색
Order order = orderRepository.findById(orderId);
Member member = order.getMember();           // ✅ 가능
List<OrderItem> items = order.getOrderItems(); // ✅ 가능
Address address = member.getAddress();        // ✅ 가능
```

**SQL 중심 개발:**
```java
// 처음 SQL에 포함된 데이터만 접근 가능
@Select("SELECT o.*, m.* FROM orders o JOIN members m ON o.member_id = m.id")
OrderDTO findOrderWithMember(Long id);
// OrderDTO에는 member 정보만 있고, orderItems나 address는 없음
// 추가 데이터가 필요하면 새로운 쿼리 작성 필요
```

**문제점:**
- 처음 SQL에 포함되지 않은 데이터는 접근 불가
- 필요한 데이터마다 새로운 쿼리 작성 필요
- 객체처럼 자유롭게 탐색 불가능

### **4. 비교 (Identity)**

**문제:**
- **객체지향 세계**: 객체는 **인스턴스 주소(참조값)**로 동일성을 판단합니다.
- **관계형 데이터베이스**: 테이블은 **기본 키(Primary Key)**로 식별합니다.

**예시:**

```java
// 객체지향: 참조값으로 동일성 판단
Member member1 = new Member("홍길동");
Member member2 = new Member("홍길동");

System.out.println(member1 == member2);  // false (다른 인스턴스)
System.out.println(member1.equals(member2));  // equals 구현에 따라 다름
```

**RDB:**
```sql
-- 같은 PK 값을 가지면 같은 엔티티
SELECT * FROM members WHERE id = 1;
-- 항상 같은 데이터 (영속적이고 변하지 않음)
```

**문제점:**
- 같은 데이터를 조회해도 객체 주소가 다를 수 있음
- 객체 동일성 보장 어려움
- 같은 트랜잭션 내에서도 다른 인스턴스로 인식될 수 있음

---

## 5. 영속성 계층 기술의 발전 흐름과 비교

### **기술 발전 흐름**

- **CRUD 작업의 반복**: 새로운 테이블을 추가하거나 필드를 변경할 때마다 INSERT, SELECT, UPDATE, DELETE 쿼리를 작성해야 한다.
- **객체-테이블 매핑의 수작업**: 자바 객체를 데이터베이스 테이블과 매핑하기 위해 수동으로 코드를 작성해야 하며, 이는 실수의 가능성을 높인다.
    
    > 예를 들어 `Member` 클래스에 `tel` 필드를 추가하면, 관련된 모든 SQL 쿼리와 매핑 코드를 수정해야 한다.
    > 

```java
// JDBC/MyBatis: 필드 추가 시 모든 쿼리 수정 필요
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

// JDBC/MyBatis: 복잡한 조인 쿼리 필요
String sql = "SELECT o.*, m.* FROM orders o JOIN members m ON o.member_id = m.id WHERE o.id = ?";
```

### 3. 계층 간의 강한 결합과 유지보수의 어려움

- **DAO와 서비스 계층의 결합**: SQL 쿼리가 DAO에 하드코딩되어 있어, 서비스 계층에서 어떤 데이터가 조회되는지 명확하지 않다.
- **변경의 전파**: 데이터베이스 스키마나 비즈니스 로직의 변경이 전체 계층에 영향을 미쳐, 유지보수가 어렵다.

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

## 4. ORM (Object-Relational Mapping)과 JPA

### **ORM이란?**

**ORM(Object-Relational Mapping)** 은 객체와 관계형 데이터베이스 사이의 패러다임 불일치를 해결해주는 기술입니다.

**ORM의 역할:**
- 개발자가 객체 중심으로 개발할 수 있도록 내부적으로 SQL 생성, 실행, 매핑을 대신 처리해줍니다.
- 객체는 객체대로 설계하고, 관계형 데이터베이스는 관계형 데이터베이스대로 설계할 수 있게 해줍니다.

### **JPA (Java Persistence API)**

**JPA**는 자바 진영의 ORM 표준 스펙입니다.

**구조:**
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

**관계:**
- **ORM**: 개념 (객체-관계 매핑 기술)
- **JPA**: 자바에서 ORM을 사용하기 위한 **표준 API**
- **Hibernate**: JPA 표준에 따라 만들어진 실제 구현체 (라이브러리)

**대표적인 JPA 구현체:**
- Hibernate (가장 널리 사용)
- EclipseLink
- OpenJPA

### **ORM의 주요 기능 및 해결책**

#### **1. 자동 SQL 생성**

**역할:**
- 저장 시 객체를 분석해 적절한 INSERT 쿼리를 생성하고 실행합니다.
- 개발자가 SQL을 직접 작성하지 않아도 됩니다.

**예시:**

```java
// ORM 사용: 객체만 저장하면 SQL 자동 생성
Member member = new Member("홍길동", 25);
em.persist(member);  // JPA가 자동으로 INSERT 쿼리 생성 및 실행
// INSERT INTO members (name, age) VALUES ('홍길동', 25)
```

**장점:**
- SQL 작성 시간 절약
- 필드 추가 시 엔티티만 수정하면 자동으로 SQL 업데이트
- 실수 가능성 감소

#### **2. 지연 로딩 (Lazy Loading)**

**역할:**
- 연관된 데이터를 실제 사용할 때 조회 쿼리를 날려, 자유로운 객체 그래프 탐색을 보장하면서 불필요한 조인을 방지합니다.

**예시:**

```java
@Entity
public class Order {
    @ManyToOne(fetch = FetchType.LAZY)  // 지연 로딩
    private Member member;
}

// 사용
Order order = orderRepository.findById(orderId);
// 이 시점에는 Order만 조회 (Member는 조회 안 됨)

Member member = order.getMember();  // 이 시점에 Member 조회 쿼리 실행
// SELECT * FROM members WHERE id = ?
```

**장점:**
- 필요한 시점에만 데이터 조회
- 불필요한 JOIN 방지
- 객체 그래프를 자유롭게 탐색 가능

**패러다임 불일치 해결:**
- 객체 그래프 탐색 문제 해결
- 처음 SQL에 포함되지 않은 데이터도 자유롭게 접근 가능

#### **3. 동일성 보장 (Identity)**

**역할:**
- 같은 트랜잭션 내에서는 같은 데이터를 조회하면 동일한 객체임을 보장합니다.

**예시:**

```java
@Transactional
public void test() {
    Member m1 = memberRepository.findById(1L);
    Member m2 = memberRepository.findById(1L);
    
    System.out.println(m1 == m2);  // true (동일한 인스턴스)
}
```

**동작 원리:**
- 1차 캐시를 통해 같은 트랜잭션 내에서 동일한 엔티티 인스턴스 보장
- 첫 번째 조회 시 DB에서 조회 후 1차 캐시에 저장
- 두 번째 조회 시 1차 캐시에서 반환 (DB 접근 없음)

**장점:**
- 객체 동일성 보장
- 데이터 일관성 보장
- 메모리 효율성

**패러다임 불일치 해결:**
- 비교(Identity) 문제 해결
- 같은 데이터를 조회해도 동일한 객체 인스턴스 보장

### **ORM이 패러다임 불일치를 해결하는 방법**

| 패러다임 불일치          | ORM의 해결 방법                 |
| ----------------- | -------------------------- |
| **상속**            | 상속 전략 (단일 테이블, 조인, 구체 클래스) |
| **연관관계**          | 객체 참조를 외래 키로 자동 매핑         |
| **객체 그래프 탐색**     | 지연 로딩으로 자유로운 탐색 보장         |
| **비교 (Identity)** | 1차 캐시로 동일성 보장              |

> [!tip] 핵심 포인트
> ORM은 객체와 관계형 데이터베이스 간의 패러다임 불일치를 해결하여, 개발자가 객체 중심으로 개발할 수 있도록 합니다. 자동 SQL 생성, 지연 로딩, 동일성 보장 등의 기능을 통해 개발 편의성과 생산성을 크게 향상시킵니다.

---

## 6. ORM의 장단점

### **ORM의 장점**

#### **1. 개발 편의성과 생산성**

- **자동 SQL 생성**: 개발자가 SQL을 직접 작성하지 않아도 됩니다.
- **반복 작업 제거**: CRUD 작업이 자동으로 처리됩니다.
- **필드 추가 시 유지보수 용이**: 엔티티만 수정하면 관련 SQL이 자동으로 업데이트됩니다.

```java
// ORM: 필드 추가 시 엔티티만 수정
@Entity
public class Member {
    private String name;
    private String email;
    private String tel;  // 필드만 추가하면 끝!
}
// 관련된 모든 SQL이 자동으로 업데이트됨
```

#### **2. 객체 지향적인 개발**

- **객체 중심 사고**: SQL이 아닌 객체로 사고할 수 있습니다.
- **연관관계 탐색**: 객체 참조로 자연스럽게 탐색 가능합니다.
- **도메인 모델링**: 비즈니스 로직에 집중할 수 있습니다.

```java
// 객체 중심 개발
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // 객체 참조로 탐색
List<OrderItem> items = order.getOrderItems();  // 자유로운 탐색
```

#### **3. 패러다임 불일치 해결**

- **상속, 연관관계, 객체 그래프 탐색, 비교** 문제를 자동으로 해결합니다.
- 객체는 객체대로, 테이블은 테이블대로 설계 가능합니다.

### **ORM의 단점**

#### **1. 학습 곡선이 높음**

- **추상적 개념**: 영속성 컨텍스트, 지연 로딩, N+1 문제 등 개념 이해 필요
- **내부 동작 이해**: 생성되는 SQL을 이해해야 성능 최적화 가능
- **복잡한 쿼리 처리**: 복잡한 통계 쿼리 등은 여전히 SQL 작성 필요

#### **2. 성능 이슈 가능성**

- **N+1 문제**: 내부 동작을 모르고 사용하면 성능 이슈 발생 가능
- **복잡한 쿼리**: 자동 생성 SQL이 비효율적일 수 있음
- **최적화 필요**: 성능이 중요한 부분은 추가 최적화 작업 필요

```java
// N+1 문제 예시
List<Order> orders = orderRepository.findAll();  // 1번 쿼리
for (Order order : orders) {
    Member member = order.getMember();  // N번 쿼리 (각 Order마다)
}
// 총 N+1번의 쿼리 실행
```

#### **3. 디버깅 어려움**

- **생성 SQL 확인**: 실제 실행되는 SQL을 확인해야 함
- **예상과 다른 동작**: 내부 동작을 이해하지 못하면 예상과 다른 결과 발생 가능

### **결론**

**ORM의 핵심:**
- 오른쪽(JDBC → SQL Mapper → ORM)으로 갈수록 추상화 수준이 높아져 편리하지만
- 각 기술의 특징과 차이를 이해하고 상황에 맞게 학습하여 적용하는 것이 중요합니다.

**적용 시 고려사항:**
- 프로젝트 특성에 맞는 기술 선택
- 팀의 학습 곡선 고려
- 성능 요구사항 확인
- 복잡한 쿼리 처리 방법 결정

---

## 8. JPA로 테이블을 설계하는 방법

### **1. Spring Boot 프로젝트에 JPA 추가하기**

#### **1) build.gradle 설정**

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}
```

#### **2) application.yml 설정**

```yaml
spring:
  jpa:
   hibernate:
	   ddl-auto: create
   show-sql: true
   properties:
	   hibernate:
        dialect: org.hibernate.dialect.H2Dialect
        format_sql: true
```

#### **3) DB 연결 확인**

애플리케이션 실행 후 연결 에러가 없다면 JPA 준비 완료.

### **2. JPA로 테이블을 설계하는 기본 원칙**

JPA는 **객체(Entity)**를 기반으로 테이블을 자동 생성하거나 매핑하므로 개발자 입장에서는 "객체 설계 → 테이블 생성" 흐름으로 진행하게 된다.

#### **1) Entity 클래스 만들기**

- 테이블 1개 = 엔티티 클래스 1개
- PK 필수
- 기본 생성자 필수(Protected 추천)

예시: 회원(Member)

```java
@Entity
@Getter @NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor @Builder
public class Member {

    @Id
    private String email;        // PK — natural key 가능

    private String name;

    private int age;
}
```

#### **2) Primary Key(PK) 설계**

JPA에서 PK 전략 3가지

| 전략 | 설명 | 사용 예 |
| --- | --- | --- |
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

#### **3) 컬럼 매핑**

가장 많이 쓰는 컬럼 매핑 어노테이션

| 어노테이션 | 설명 |
| --- | --- |
| `@Column(nullable=false)` | NOT NULL |
| `@Column(unique=true)` | UNIQUE |
| `@Column(length=100)` | VARCHAR 크기 지정 |
| `@Column(columnDefinition="TEXT")` | TEXT, LONGTEXT 등 직접 지정 |
| `@CreationTimestamp` | 생성시간 자동 저장 |
| `@UpdateTimestamp` | 수정시간 자동 업데이트 |

#### **4) 연관관계 설계 (Association)**

JPA에서 가장 핵심 개념.

##### 1:N

```java
// Many(댓글) → One(게시글)
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "board_id")
private Board board;
```

##### 1:1

```java
@OneToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "member_id")
private Member member;
```

##### N:N (사용하면 안됨. → 중간 테이블로 1:N + N:1로 풀기)

### **3. JPA 테이블 설계 예시 (Board – Member)**

#### Member

```java
@Entity
@Getter @Builder
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
public class Member {

    @Id
    private String email;

    private String name;
}
```

#### Board

```java
@Entity
@Getter @Builder
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

## 9. JDBC/MyBatis에서 JPA로의 변화 방안

### **1. 점진적 전환 전략**

#### **단계 1: 새로운 기능부터 JPA 적용**
- 기존 JDBC/MyBatis 코드는 유지하고, 새로운 기능만 JPA로 개발
- 점진적으로 JPA의 장점을 경험하고 팀 내 학습 시간 확보

#### **단계 2: 핵심 도메인부터 전환**
- 가장 중요한 도메인(예: 회원, 주문)부터 JPA로 전환
- 복잡한 연관관계가 있는 도메인이 JPA의 장점을 가장 잘 보여줌

#### **단계 3: 레거시 코드 리팩토링**
- 기존 코드를 JPA로 점진적으로 리팩토링
- 테스트 코드를 먼저 작성하여 안전하게 전환

### **2. JPA 도입 시 고려사항**

#### **학습 곡선**
- JPA는 학습 곡선이 있으므로 팀원들의 교육 시간 필요
- 영속성 컨텍스트, 지연 로딩, N+1 문제 등 개념 이해 필요

#### **성능 최적화**
- JPA는 자동으로 SQL을 생성하지만, 복잡한 쿼리는 JPQL이나 네이티브 쿼리 사용
- 성능이 중요한 부분은 쿼리 로그를 확인하고 최적화 필요

#### **하이브리드 접근**
- JPA를 주로 사용하되, 복잡한 통계 쿼리 등은 MyBatis나 네이티브 쿼리 사용
- 각 기술의 장점을 활용하는 하이브리드 접근 가능

### **3. 변화의 핵심: SQL 중심 → 객체 중심**

#### **Before (JDBC/MyBatis)**
```java
// SQL 중심 사고
// "어떤 쿼리를 작성해야 원하는 데이터를 얻을까?"
String sql = "SELECT o.*, m.* FROM orders o JOIN members m ON o.member_id = m.id";
```

#### **After (JPA)**
```java
// 객체 중심 사고
// "어떤 객체를 조회하고, 어떤 연관관계를 탐색할까?"
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // 객체 탐색
```


#### **Entity 설계 원칙**
- 객체지향 설계 원칙을 따르며 Entity 설계
- 연관관계는 필요할 때만 설정 (과도한 연관관계는 성능 저하)

#### **Repository 패턴 활용**
- Spring Data JPA의 Repository 인터페이스 활용
- 복잡한 쿼리는 @Query로 JPQL 작성

#### **트랜잭션 관리**
- @Transactional 어노테이션으로 트랜잭션 경계 명확히
- 읽기 전용 트랜잭션은 readOnly = true 설정

---

## 요약

- **영속성 계층(Persistence Layer)** 은 애플리케이션과 DB 사이에서 상호작용하는 역할을 담당하며, 영속성 계층 기술은 JDBC → SQL Mapper → ORM으로 발전합니다.
- **JDBC**는 순수 자바 API로 DB 접근이 가능하지만 반복적인 코드가 많아 비효율적입니다.
- **SQL Mapper (JDBC Template, MyBatis)** 는 반복적인 작업을 추상화하여 편리함을 제공하지만, 여전히 SQL 작성에 많은 시간을 써야 하고 패러다임 불일치 문제가 존재합니다.
- **객체와 관계형 데이터베이스의 패러다임 불일치**는 상속, 연관관계, 객체 그래프 탐색, 비교(Identity) 4가지 주요 문제로 발생합니다.
- **ORM (Object-Relational Mapping)** 은 객체와 RDB 사이의 패러다임 불일치를 해결하는 기술로, 자동 SQL 생성, 지연 로딩, 동일성 보장 등의 기능을 제공합니다.
- **JPA**는 자바 진영의 ORM 표준 스펙이며, Hibernate가 대표적인 구현체입니다.
- **ORM의 장점**: 개발 편의성과 생산성 향상, 객체 지향적인 개발, 패러다임 불일치 해결
- **ORM의 단점**: 학습 곡선이 높고, 내부 동작을 모르고 사용하면 복잡한 쿼리 처리나 성능 이슈(N+1 문제 등)가 발생할 수 있습니다.
- **기술 발전 흐름**: 오른쪽(JDBC → SQL Mapper → ORM)으로 갈수록 추상화 수준이 높아져 편리하지만, 각 기술의 특징과 차이를 이해하고 상황에 맞게 학습하여 적용하는 것이 중요합니다.

---

## 참고 자료

- [JPA Specification](https://jcp.org/en/jsr/detail?id=338)
- [Hibernate Documentation](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html)
- [Spring Data JPA Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
