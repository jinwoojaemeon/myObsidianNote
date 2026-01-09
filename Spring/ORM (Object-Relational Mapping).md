#spring #orm #jpa #hibernate #sql #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[JPA 엔티티 매핑|JPA 엔티티 매핑]] [[JPA 연관관계 매핑|JPA 연관관계 매핑]] [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]

> [!note] 이어서 읽기
> ORM을 이해하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**ORM(Object-Relational Mapping, 객체-관계 매핑)** 은 객체지향 프로그래밍 언어의 객체와 관계형 데이터베이스의 테이블을 자동으로 매핑하여, SQL을 직접 작성하지 않고도 객체를 통해 데이터베이스 작업을 수행할 수 있게 해주는 기술입니다. ORM은 생산성 향상과 유지보수성 개선을 제공하지만, 복잡한 쿼리나 성능이 중요한 경우에는 한계가 있어 상황에 따라 적절히 선택해야 합니다.

---

### **Why (왜 사용하는가? 왜 중요한가?)** 

- ORM을 사용하면 SQL 중심 개발의 반복적인 작업을 줄이고, 객체지향적인 코드 작성이 가능해집니다. 필드 하나 추가할 때마다 관련된 모든 SQL 쿼리를 수정해야 하는 부담이 사라지고, 객체와 관계형 데이터베이스 간의 패러다임 불일치 문제를 해결할 수 있습니다.

- ORM의 개념과 동작 원리, 장점과 단점, 언제 사용해야 하는지, 그리고 ORM이 항상 최선의 선택인지에 대한 판단 기준을 이해해야 합니다.

---

## 1. ORM이란 무엇인가?

### **ORM의 정의**

**ORM(Object-Relational Mapping, 객체-관계 매핑)** 은 객체지향 프로그래밍 언어의 **객체(Object)** 와 관계형 데이터베이스의 **테이블(Table)** 을 자동으로 매핑(mapping)하여, SQL을 직접 작성하지 않고도 객체를 통해 데이터베이스 작업을 수행할 수 있게 해주는 기술입니다.

### **ORM의 핵심 개념**

```
객체지향 세계          ORM 매핑          관계형 데이터베이스
─────────────────────────────────────────────────────
클래스 (Class)    ←──────────→    테이블 (Table)
객체 (Object)     ←──────────→    행 (Row)
필드 (Field)      ←──────────→    컬럼 (Column)
참조 (Reference)  ←──────────→    외래 키 (Foreign Key)
```

### **ORM의 동작 원리**

1. **객체를 데이터베이스에 저장**
   ```java
   Member member = new Member("홍길동", 25);
   em.persist(member);  // ORM이 자동으로 INSERT SQL 생성
   ```

2. **데이터베이스에서 객체 조회**
   ```java
   Member member = em.find(Member.class, 1L);  // ORM이 자동으로 SELECT SQL 생성
   ```

3. **객체 수정**
   ```java
   member.setName("김철수");  // ORM이 자동으로 UPDATE SQL 생성
   ```

### **대표적인 ORM**

- **JPA (Java Persistence API)**: 자바의 ORM 표준
  - 구현체: Hibernate, EclipseLink, OpenJPA
- **Django ORM**: 파이썬 Django 프레임워크의 ORM
- **Entity Framework**: .NET의 ORM
- **Sequelize**: Node.js의 ORM
- **ActiveRecord**: Ruby on Rails의 ORM

---

## 2. ORM을 왜 사용하는가?

### **1. SQL 중심 개발의 문제점 해결**

#### **SQL 중심 개발의 문제**

```java
// 필드 하나 추가할 때마다 모든 SQL 수정 필요
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

**문제점:**
- 필드 추가 시 모든 SQL 쿼리 수정 필요
- 반복적인 CRUD 코드 작성
- 실수 가능성 증가

#### **ORM 사용 시**

```java
// 필드만 추가하면 끝!
@Entity
public class Member {
    private String email;
    private String name;
    private int age;
    private String tel;  // 필드 추가만 하면 ORM이 자동 처리
}

// 사용
memberRepository.save(member);  // INSERT 자동 생성
memberRepository.findById(email);  // SELECT 자동 생성
```

**장점:**
- 필드 추가 시 엔티티만 수정
- SQL 자동 생성으로 실수 감소
- 반복 코드 제거

### **2. 객체지향과 관계형 데이터베이스 간 패러다임 불일치 해결**

#### **패러다임 불일치 문제**

**객체지향 세계:**
```java
class Order {
    Member member;  // 참조로 연관관계 표현
}

Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // 간단한 탐색
```

**관계형 데이터베이스:**
```sql
-- 복잡한 JOIN 쿼리 필요
SELECT o.*, m.* 
FROM orders o 
JOIN members m ON o.member_id = m.id 
WHERE o.id = ?
```

#### **ORM으로 해결**

```java
@Entity
public class Order {
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private Member member;  // 객체 참조로 표현
}

// 사용: 객체처럼 간단하게 탐색
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // ORM이 자동으로 JOIN 처리
```

### **3. 생산성 향상**

#### **코드 비교: 회원 저장하기**

**JDBC 방식:**
```java
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
```

**ORM 방식:**
```java
public void save(Member member) {
    memberRepository.save(member);  // ✅ 간단!
}
```

### **4. 데이터베이스 독립성**

#### **DBMS 종속성 문제**

```sql
-- MySQL 전용 문법
SELECT * FROM members LIMIT 10;

-- Oracle로 전환 시 수정 필요
SELECT * FROM members WHERE ROWNUM <= 10;
```

#### **ORM으로 해결**

```java
// ORM 사용: DBMS에 독립적
List<Member> members = memberRepository.findAll(PageRequest.of(0, 10));
// Hibernate가 DBMS에 맞는 SQL 자동 생성
```

---

## 3. ORM의 장점

### **1. 생산성 향상**

- **반복 코드 제거**: CRUD 작업을 자동으로 처리
- **빠른 개발**: SQL 작성 시간 절약
- **유지보수 용이**: 필드 변경 시 엔티티만 수정

### **2. 객체지향적인 코드 작성**

- **객체 중심 개발**: SQL이 아닌 객체로 사고
- **연관관계 탐색**: 객체 참조로 자연스럽게 탐색
- **도메인 모델링**: 비즈니스 로직에 집중

### **3. 자동 성능 최적화**

- **1차 캐시**: 같은 트랜잭션 내 조회 최적화
- **지연 로딩**: 필요한 시점에만 데이터 조회
- **연결 풀 관리**: 데이터베이스 연결 효율적 관리

### **4. 데이터베이스 독립성**

- **DBMS 변경 용이**: 다른 데이터베이스로 전환 시 코드 수정 최소화
- **표준 API 사용**: JPA 같은 표준 API로 일관된 개발

### **5. 타입 안정성**

- **컴파일 타임 검증**: 엔티티 필드 변경 시 컴파일 에러로 즉시 발견
- **IDE 지원**: 자동완성, 리팩토링 지원

---

## 4. ORM의 단점과 한계

### **1. 학습 곡선**

**문제점:**
- ORM의 개념 이해 필요 (영속성 컨텍스트, 지연 로딩 등)
- 생성되는 SQL을 이해해야 함
- 성능 최적화를 위한 추가 학습 필요

**예시:**
```java
// N+1 문제 발생 가능
List<Order> orders = orderRepository.findAll();
for (Order order : orders) {
    Member member = order.getMember();  // 각각 조회 쿼리 실행
}
```

### **2. 복잡한 쿼리의 한계**

**문제점:**
- 복잡한 통계 쿼리, 대량 데이터 처리에는 부적합
- ORM이 생성하는 SQL이 비효율적일 수 있음

**예시:**
```java
// 복잡한 통계 쿼리는 네이티브 쿼리 사용
@Query(value = "SELECT department, AVG(salary) as avg_salary " +
               "FROM employees " +
               "WHERE hire_date > :date " +
               "GROUP BY department " +
               "HAVING COUNT(*) > 10", nativeQuery = true)
List<DepartmentStats> getDepartmentStats(@Param("date") LocalDate date);
```

### **3. 성능 문제**

**문제점:**
- 자동 생성 SQL이 비효율적일 수 있음
- 지연 로딩으로 인한 N+1 문제
- 불필요한 조인 발생

**예시:**
```java
// N+1 문제: 1번의 쿼리로 N번의 추가 쿼리 발생
List<Order> orders = orderRepository.findAll();  // 1번 쿼리
for (Order order : orders) {
    order.getMember().getName();  // N번 쿼리 (각 Order마다)
}
```

### **4. 디버깅 어려움**

**문제점:**
- 생성되는 SQL을 직접 확인해야 함
- 예상과 다른 쿼리가 실행될 수 있음
- 성능 문제 추적이 어려움

### **5. 과도한 추상화**

**문제점:**
- 실제 실행되는 SQL을 모르면 최적화 어려움
- 데이터베이스 특화 기능 사용 제한
- 복잡한 쿼리는 결국 SQL 작성 필요

---

## 5. ORM은 무조건적으로 좋은가?

### **결론: 상황에 따라 다릅니다**

ORM은 **항상 최선의 선택은 아닙니다**. 프로젝트의 특성, 팀의 역량, 성능 요구사항에 따라 선택해야 합니다.

### **ORM을 사용하기 좋은 경우**

#### **1. CRUD 중심의 애플리케이션**

```java
// 대부분의 작업이 단순 CRUD
memberRepository.save(member);
memberRepository.findById(id);
memberRepository.delete(member);
```

**적합한 이유:**
- ORM의 자동 CRUD 기능을 최대한 활용
- 반복 코드 제거로 생산성 향상

#### **2. 객체지향적인 도메인 모델링이 중요한 경우**

```java
// 복잡한 연관관계를 객체로 표현
@Entity
public class Order {
    @ManyToOne
    private Member member;
    
    @OneToMany
    private List<OrderItem> orderItems;
}
```

**적합한 이유:**
- 객체 중심 설계 가능
- 연관관계 탐색이 자연스러움

#### **3. 빠른 프로토타이핑이 필요한 경우**

**적합한 이유:**
- 개발 속도가 중요
- 초기에는 ORM으로 빠르게 개발
- 성능 최적화는 이후에 진행

#### **4. 데이터베이스 변경 가능성이 있는 경우**

**적합한 이유:**
- DBMS 독립성 제공
- 마이그레이션 용이

### **ORM을 사용하지 않는 것이 좋은 경우**

#### **1. 복잡한 통계 쿼리가 많은 경우**

```sql
-- 복잡한 통계 쿼리
SELECT 
    department,
    COUNT(*) as emp_count,
    AVG(salary) as avg_salary,
    SUM(CASE WHEN status = 'ACTIVE' THEN 1 ELSE 0 END) as active_count
FROM employees e
JOIN departments d ON e.dept_id = d.id
WHERE e.hire_date BETWEEN :start AND :end
GROUP BY department
HAVING COUNT(*) > 100
ORDER BY avg_salary DESC;
```

**이유:**
- ORM으로 표현하기 어려움
- 네이티브 쿼리 사용 시 ORM의 장점 감소
- MyBatis나 직접 SQL이 더 적합

#### **2. 성능이 매우 중요한 경우**

**이유:**
- 최적화된 SQL 직접 작성 필요
- ORM의 자동 생성 SQL이 비효율적일 수 있음
- 대량 데이터 처리 시 성능 이슈

#### **3. 기존 레거시 시스템과의 통합**

**이유:**
- 기존 SQL 쿼리 재사용 필요
- 복잡한 스키마와의 매핑 어려움
- 점진적 전환 필요

#### **4. 팀의 ORM 역량이 부족한 경우**

**이유:**
- 학습 곡선으로 인한 초기 생산성 저하
- 잘못된 사용으로 인한 성능 문제
- 유지보수 어려움

---

## 6. 하이브리드 접근법

### **ORM + SQL 매퍼 혼용**

실제 프로젝트에서는 **ORM과 SQL 매퍼를 함께 사용**하는 하이브리드 접근이 효과적입니다.

#### **전략**

1. **일반적인 CRUD**: ORM 사용
2. **복잡한 쿼리**: MyBatis나 네이티브 쿼리 사용
3. **성능이 중요한 부분**: 최적화된 SQL 직접 작성

#### **예시**

```java
// ORM 사용: 일반적인 CRUD
@Service
public class MemberService {
    @Autowired
    private MemberRepository memberRepository;  // JPA
    
    public void save(Member member) {
        memberRepository.save(member);  // ORM 사용
    }
}

// MyBatis 사용: 복잡한 통계 쿼리
@Mapper
public interface StatisticsMapper {
    @Select("SELECT department, AVG(salary) as avg_salary " +
            "FROM employees " +
            "GROUP BY department")
    List<DepartmentStats> getDepartmentStats();  // MyBatis 사용
}
```

**장점:**
- 각 기술의 장점 활용
- 상황에 맞는 최적의 선택
- 유연한 개발

---

## 7. ORM 사용 시 주의사항

### **1. N+1 문제 해결**

**문제:**
```java
// N+1 문제 발생
List<Order> orders = orderRepository.findAll();  // 1번 쿼리
for (Order order : orders) {
    order.getMember().getName();  // N번 쿼리
}
```

**해결:**
```java
// Fetch Join 사용
@Query("SELECT o FROM Order o JOIN FETCH o.member")
List<Order> findAllWithMember();
```

### **2. 지연 로딩 vs 즉시 로딩**

**지연 로딩 (권장):**
```java
@ManyToOne(fetch = FetchType.LAZY)  // 필요할 때만 조회
private Member member;
```

**즉시 로딩 (주의):**
```java
@ManyToOne(fetch = FetchType.EAGER)  // 항상 조회 (성능 이슈 가능)
private Member member;
```

### **3. 생성되는 SQL 확인**

**설정:**
```yaml
spring:
  jpa:
    show-sql: true  # 생성되는 SQL 로그 출력
    properties:
      hibernate:
        format_sql: true  # SQL 포맷팅
```

**중요:**
- 실제 실행되는 SQL을 항상 확인
- 예상과 다른 쿼리 실행 시 최적화 필요

### **4. 트랜잭션 관리**

```java
@Transactional  // 트랜잭션 경계 명확히
public void updateMember(String email, String name) {
    Member member = memberRepository.findById(email).orElseThrow();
    member.setName(name);  // 변경 감지로 자동 UPDATE
}
```

---

## 요약

- **ORM(Object-Relational Mapping)** 은 객체와 관계형 데이터베이스를 자동으로 매핑하여 SQL을 직접 작성하지 않고도 데이터베이스 작업을 수행할 수 있게 해주는 기술입니다.
- **ORM의 장점**: 생산성 향상, 객체지향적인 코드 작성, 자동 성능 최적화, 데이터베이스 독립성, 타입 안정성
- **ORM의 단점**: 학습 곡선, 복잡한 쿼리의 한계, 성능 문제(N+1 문제), 디버깅 어려움, 과도한 추상화
- **ORM 사용이 적합한 경우**: CRUD 중심 애플리케이션, 객체지향 도메인 모델링이 중요한 경우, 빠른 프로토타이핑, 데이터베이스 변경 가능성
- **ORM 사용이 부적합한 경우**: 복잡한 통계 쿼리가 많은 경우, 성능이 매우 중요한 경우, 기존 레거시 시스템 통합, 팀의 ORM 역량 부족
- **하이브리드 접근**: ORM과 SQL 매퍼를 함께 사용하여 각 기술의 장점을 활용하는 것이 실용적입니다.
- **ORM은 무조건적으로 좋은 것은 아니며**, 프로젝트 특성과 요구사항에 따라 적절히 선택해야 합니다.

---

## 참고 자료

- [JPA Specification](https://jcp.org/en/jsr/detail?id=338)
- [Hibernate Documentation](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html)
- [Martin Fowler - ORM Hate](https://martinfowler.com/bliki/OrmHate.html)
- [Spring Data JPA Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
