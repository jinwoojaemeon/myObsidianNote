#spring #jpa #mybatis #orm #sql #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[ORM (Object-Relational Mapping)|ORM]] [[영속성 컨텍스트 (Persistence Context)|영속성 컨텍스트]]

> [!note] 이어서 읽기
> JPA와 MyBatis를 비교하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**JPA(Java Persistence API)** 와 **MyBatis** 는 자바에서 데이터베이스와 상호작용하는 두 가지 주요 기술입니다. JPA는 ORM 기술로 객체 중심의 개발을 가능하게 하고, MyBatis는 SQL 매퍼 프레임워크로 SQL 중심의 개발을 지원합니다. 각각의 장단점과 사용 시나리오를 이해하여 프로젝트에 적합한 기술을 선택할 수 있어야 합니다.

---

### **Why (왜 사용하는가? 왜 중요한가?)** 

- JPA와 MyBatis는 서로 다른 철학과 접근 방식을 가지고 있어, 프로젝트의 특성에 따라 적절한 선택이 필요합니다. JPA는 객체 중심 개발로 생산성을 높이고 자동화된 기능을 제공하지만, 복잡한 쿼리나 성능 최적화가 필요한 경우에는 한계가 있습니다. MyBatis는 SQL을 직접 제어할 수 있어 복잡한 쿼리와 성능 최적화에 유리하지만, 반복적인 SQL 작성과 유지보수 부담이 있습니다.

- JPA와 MyBatis의 개념과 철학, 개발 방식의 차이, 코드 작성 방법, 장단점 비교, 성능 특성, 학습 곡선, 그리고 각각이 적합한 사용 시나리오를 이해해야 합니다.

---

## 1. JPA와 MyBatis란?

### **JPA (Java Persistence API)**

**JPA**는 자바 객체를 관계형 데이터베이스에 매핑하기 위한 **ORM(Object-Relational Mapping) 표준 기술**입니다.

**핵심 특징:**
- 객체 중심 개발
- SQL 자동 생성
- 영속성 컨텍스트를 통한 자동 관리
- 객체지향과 관계형 DB 간 패러다임 불일치 해결

**대표 구현체:**
- Hibernate (가장 널리 사용)
- EclipseLink
- OpenJPA

### **MyBatis**

**MyBatis**는 SQL 매퍼 프레임워크로, SQL과 자바 코드를 분리하고 객체 매핑을 자동화합니다.

**핵심 특징:**
- SQL 중심 개발
- SQL 직접 제어
- XML/어노테이션으로 SQL 관리
- 복잡한 쿼리 작성 용이

**버전:**
- MyBatis 3.x (현재 버전)
- MyBatis-Spring (스프링 통합)

### **기본 철학의 차이**

| 구분 | JPA | MyBatis |
|------|-----|---------|
| **철학** | 객체 중심 | SQL 중심 |
| **개발 방식** | 객체로 사고 | SQL로 사고 |
| **SQL 작성** | 자동 생성 (필요시 JPQL) | 직접 작성 |
| **제어권** | 프레임워크 | 개발자 |

---

## 2. 개발 방식의 차이

### **JPA 개발 방식**

#### **1. 객체 중심 사고**

```java
// JPA: 객체로 사고
// "어떤 객체를 조회하고, 어떤 연관관계를 탐색할까?"
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // 객체 참조로 탐색
```

**특징:**
- 객체 설계가 먼저
- 연관관계를 객체 참조로 표현
- SQL은 JPA가 자동 생성

#### **2. 엔티티 기반 개발**

```java
@Entity
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @OneToMany(mappedBy = "member")
    private List<Order> orders;
}

// 사용
memberRepository.save(member);  // INSERT 자동 생성
memberRepository.findById(id);   // SELECT 자동 생성
```

### **MyBatis 개발 방식**

#### **1. SQL 중심 사고**

```java
// MyBatis: SQL로 사고
// "어떤 쿼리를 작성해야 원하는 데이터를 얻을까?"
@Select("SELECT o.*, m.* " +
        "FROM orders o " +
        "JOIN members m ON o.member_id = m.id " +
        "WHERE o.id = #{id}")
OrderDTO findOrderWithMember(Long id);
```

**특징:**
- SQL 설계가 먼저
- 테이블 중심 사고
- 개발자가 SQL 직접 제어

#### **2. Mapper 인터페이스 기반 개발**

```java
@Mapper
public interface MemberMapper {
    void insert(Member member);
    Member findById(Long id);
    List<Member> findAll();
}

// XML 파일
// <insert id="insert">
//     INSERT INTO members (name) VALUES (#{name})
// </insert>
```

---

## 3. 코드 비교

### **회원 저장하기**

#### **JPA 방식**

```java
@Service
@Transactional
public class MemberService {
    @Autowired
    private MemberRepository memberRepository;
    
    public void save(Member member) {
        memberRepository.save(member);  // ✅ 간단!
        // JPA가 자동으로 INSERT 쿼리 생성
    }
}
```

**특징:**
- 코드가 간결함
- SQL 작성 불필요
- 필드 추가 시 엔티티만 수정

#### **MyBatis 방식**

```java
@Service
public class MemberService {
    @Autowired
    private MemberMapper memberMapper;
    
    public void save(Member member) {
        memberMapper.insert(member);
        // XML에 SQL 작성 필요
    }
}

// XML 파일
// <insert id="insert">
//     INSERT INTO members (name, email, age)
//     VALUES (#{name}, #{email}, #{age})
// </insert>
```

**특징:**
- SQL을 직접 작성
- 필드 추가 시 SQL도 수정 필요
- 쿼리 제어 가능

### **회원 수정하기**

#### **JPA 방식**

```java
@Transactional
public void updateMember(Long id, String name) {
    Member member = memberRepository.findById(id).orElseThrow();
    member.setName(name);  // ✅ 필드만 변경하면 끝!
    // JPA가 자동으로 변경 감지하여 UPDATE 쿼리 생성
}
```

**특징:**
- UPDATE 쿼리 작성 불필요
- 변경 감지(Dirty Checking)로 자동 처리
- 객체지향적인 코드

#### **MyBatis 방식**

```java
public void updateMember(Long id, String name) {
    Member member = new Member();
    member.setId(id);
    member.setName(name);
    memberMapper.update(member);
}

// XML 파일
// <update id="update">
//     UPDATE members
//     SET name = #{name}
//     WHERE id = #{id}
// </update>
```

**특징:**
- UPDATE 쿼리를 직접 작성
- 명시적인 쿼리 제어
- 필요한 컬럼만 업데이트 가능

### **연관관계 조회하기**

#### **JPA 방식**

```java
@Entity
public class Order {
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id")
    private Member member;
}

// 사용
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // ✅ 객체 참조로 간단하게
// 지연 로딩: 실제 사용 시점에 조회
```

**특징:**
- 객체 참조로 자연스럽게 탐색
- 지연 로딩으로 성능 최적화
- JOIN 쿼리 자동 생성

#### **MyBatis 방식**

```java
// 복잡한 JOIN 쿼리 필요
@Select("SELECT o.id, o.title, " +
        "m.id as member_id, m.name as member_name " +
        "FROM orders o " +
        "JOIN members m ON o.member_id = m.id " +
        "WHERE o.id = #{id}")
OrderDTO findOrderWithMember(Long id);

// DTO로 변환 필요
public class OrderDTO {
    private Long orderId;
    private String title;
    private Long memberId;
    private String memberName;
}
```

**특징:**
- JOIN 쿼리를 직접 작성
- DTO로 변환 필요
- 쿼리 최적화 가능

---

## 4. 장단점 비교

### **JPA의 장점**

#### **1. 생산성 향상**

```java
// 필드 추가 시
@Entity
public class Member {
    private String name;
    private String email;
    private String tel;  // 필드만 추가하면 끝!
}
// 관련된 모든 SQL이 자동으로 업데이트됨
```

**장점:**
- 반복적인 SQL 작성 불필요
- 필드 추가 시 엔티티만 수정
- CRUD 코드 자동 생성

#### **2. 객체지향적인 개발**

```java
// 객체 참조로 자연스럽게 탐색
Order order = orderRepository.findById(orderId);
Member member = order.getMember();
List<OrderItem> items = order.getOrderItems();
```

**장점:**
- 객체 중심 사고
- 연관관계를 객체로 표현
- 도메인 모델링에 유리

#### **3. 자동 성능 최적화**

```java
// 1차 캐시로 같은 트랜잭션 내 재사용
Member m1 = memberRepository.findById(1L);  // DB 조회
Member m2 = memberRepository.findById(1L);  // 1차 캐시에서 조회

// 지연 로딩으로 필요한 시점에만 조회
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // 이 시점에 조회
```

**장점:**
- 1차 캐시로 성능 최적화
- 지연 로딩으로 불필요한 조회 방지
- 변경 감지로 자동 UPDATE

#### **4. 데이터베이스 독립성**

```java
// DBMS에 독립적인 코드
List<Member> members = memberRepository.findAll(PageRequest.of(0, 10));
// Hibernate가 DBMS에 맞는 SQL 자동 생성
```

**장점:**
- DBMS 변경 시 코드 수정 최소화
- 표준 API 사용

### **JPA의 단점**

#### **1. 학습 곡선**

**문제점:**
- 영속성 컨텍스트, 지연 로딩, N+1 문제 등 개념 이해 필요
- 생성되는 SQL을 이해해야 함
- 성능 최적화를 위한 추가 학습 필요

#### **2. 복잡한 쿼리의 한계**

```java
// 복잡한 통계 쿼리는 네이티브 쿼리 필요
@Query(value = "SELECT department, AVG(salary) as avg_salary " +
               "FROM employees " +
               "WHERE hire_date > :date " +
               "GROUP BY department " +
               "HAVING COUNT(*) > 10", nativeQuery = true)
List<DepartmentStats> getDepartmentStats(@Param("date") LocalDate date);
```

**문제점:**
- 복잡한 쿼리는 결국 SQL 작성 필요
- 네이티브 쿼리 사용 시 DBMS 독립성 상실

#### **3. 성능 문제 가능성**

```java
// N+1 문제 발생 가능
List<Order> orders = orderRepository.findAll();
for (Order order : orders) {
    Member member = order.getMember();  // 각각 조회 쿼리 실행
}
```

**문제점:**
- N+1 문제 발생 가능
- 생성되는 SQL이 비효율적일 수 있음
- 성능 최적화를 위한 추가 작업 필요

### **MyBatis의 장점**

#### **1. SQL 직접 제어**

```java
// 복잡한 쿼리도 자유롭게 작성 가능
@Select("SELECT " +
        "  DATE_FORMAT(created_at, '%Y-%m') as month, " +
        "  COUNT(*) as count, " +
        "  SUM(amount) as total_amount " +
        "FROM orders " +
        "WHERE status = 'COMPLETED' " +
        "GROUP BY DATE_FORMAT(created_at, '%Y-%m') " +
        "ORDER BY month DESC")
List<MonthlyStats> getMonthlyStats();
```

**장점:**
- 복잡한 쿼리 작성 용이
- 쿼리 최적화 가능
- DBMS 특화 기능 활용 가능

#### **2. 명시적인 쿼리**

```java
// 실행되는 SQL이 명확함
@Select("SELECT * FROM members WHERE name LIKE #{name}")
List<Member> findByName(String name);
```

**장점:**
- 실행되는 SQL이 명확
- 디버깅 용이
- 예측 가능한 동작

#### **3. 학습 곡선이 낮음**

**장점:**
- SQL만 알면 사용 가능
- 개념이 단순
- 빠른 시작 가능

#### **4. 성능 최적화 용이**

```java
// 필요한 컬럼만 조회
@Select("SELECT id, name FROM members WHERE id = #{id}")
Member findById(Long id);

// 인덱스 활용 최적화
@Select("SELECT * FROM orders " +
        "WHERE member_id = #{memberId} " +
        "AND created_at >= #{startDate} " +
        "ORDER BY created_at DESC " +
        "LIMIT #{limit}")
List<Order> findRecentOrders(@Param("memberId") Long memberId,
                            @Param("startDate") LocalDate startDate,
                            @Param("limit") int limit);
```

**장점:**
- 필요한 컬럼만 조회 가능
- 인덱스 활용 최적화 가능
- 쿼리 튜닝 용이

### **MyBatis의 단점**

#### **1. 반복적인 SQL 작성**

```java
// 필드 추가 시 모든 SQL 수정 필요
// INSERT 쿼리 수정
// SELECT 쿼리 수정
// UPDATE 쿼리 수정
// DELETE 쿼리 수정
```

**문제점:**
- 필드 추가 시 관련된 모든 SQL 수정 필요
- 반복적인 작업
- 실수 가능성 증가

#### **2. 객체-테이블 매핑 수작업**

```java
// 복잡한 연관관계는 수동으로 처리
@Select("SELECT o.*, m.* FROM orders o JOIN members m ON o.member_id = m.id")
List<OrderDTO> findOrdersWithMembers();
// DTO로 변환하는 코드 필요
```

**문제점:**
- 연관관계 매핑 수작업
- DTO 변환 코드 필요
- 유지보수 부담

#### **3. 패러다임 불일치 해결 불가**

**문제점:**
- 객체지향과 관계형 DB의 차이를 개발자가 직접 해결
- 객체 중심 설계 어려움

---

## 5. 성능 비교

### **JPA 성능 특성**

#### **1. 1차 캐시 활용**

```java
@Transactional
public void test() {
    Member m1 = memberRepository.findById(1L);  // DB 조회
    Member m2 = memberRepository.findById(1L);  // 1차 캐시에서 조회 (DB 접근 없음)
    // 같은 트랜잭션 내에서 성능 최적화
}
```

**장점:**
- 같은 트랜잭션 내 재사용
- DB 접근 횟수 감소

#### **2. 지연 로딩**

```java
@ManyToOne(fetch = FetchType.LAZY)
private Member member;

// 실제 사용 시점에만 조회
Order order = orderRepository.findById(orderId);
Member member = order.getMember();  // 이 시점에 조회
```

**장점:**
- 필요한 시점에만 조회
- 불필요한 조회 방지

#### **3. 배치 처리**

```java
// 쓰기 지연으로 배치 처리 효과
for (int i = 0; i < 100; i++) {
    Member member = new Member("member" + i);
    memberRepository.save(member);  // 각각 DB 접근하지 않음
}
// 트랜잭션 커밋 시 한 번에 실행
```

**장점:**
- 쿼리를 모아서 한 번에 실행
- 배치 처리 효과

### **MyBatis 성능 특성**

#### **1. 필요한 컬럼만 조회**

```java
// 필요한 컬럼만 조회하여 성능 최적화
@Select("SELECT id, name FROM members WHERE id = #{id}")
Member findById(Long id);
```

**장점:**
- 필요한 데이터만 조회
- 네트워크 비용 절감

#### **2. 쿼리 최적화**

```java
// 인덱스 활용, JOIN 최적화 등 직접 제어
@Select("SELECT o.* FROM orders o " +
        "WHERE o.member_id = #{memberId} " +
        "AND o.status = 'ACTIVE' " +
        "ORDER BY o.created_at DESC " +
        "LIMIT 10")
List<Order> findRecentActiveOrders(Long memberId);
```

**장점:**
- 쿼리 최적화 직접 제어
- 인덱스 활용 가능

#### **3. 명시적인 쿼리**

**장점:**
- 실행되는 SQL이 명확
- 예측 가능한 성능

### **성능 비교 요약**

| 구분 | JPA | MyBatis |
|------|-----|---------|
| **단순 조회** | 1차 캐시 활용으로 빠름 | 직접 쿼리로 빠름 |
| **복잡한 쿼리** | 네이티브 쿼리 필요 | 최적화된 쿼리 작성 가능 |
| **대량 처리** | 쓰기 지연으로 배치 효과 | 배치 처리 직접 구현 |
| **연관관계 조회** | 지연 로딩으로 최적화 가능 | JOIN 쿼리 직접 작성 |

---

## 6. 학습 곡선과 복잡도

### **JPA 학습 곡선**

#### **초기 학습 내용**

1. **기본 개념**
   - 엔티티 매핑
   - 연관관계 매핑
   - Repository 사용법

2. **중급 개념**
   - 영속성 컨텍스트
   - 변경 감지
   - 지연 로딩 vs 즉시 로딩

3. **고급 개념**
   - N+1 문제 해결
   - 성능 최적화
   - 복잡한 쿼리 작성

**학습 난이도:** 높음 (개념이 많고 추상적)

### **MyBatis 학습 곡선**

#### **초기 학습 내용**

1. **기본 개념**
   - Mapper 인터페이스
   - XML 매핑
   - 동적 SQL

2. **중급 개념**
   - resultMap 활용
   - 복잡한 쿼리 작성
   - 성능 최적화

**학습 난이도:** 낮음 (SQL만 알면 시작 가능)

### **복잡도 비교**

| 구분 | JPA | MyBatis |
|------|-----|---------|
| **초기 설정** | 복잡 (엔티티 설계 필요) | 간단 (Mapper만 작성) |
| **코드 복잡도** | 낮음 (간결한 코드) | 중간 (SQL 작성 필요) |
| **개념 복잡도** | 높음 (추상적 개념 많음) | 낮음 (직관적) |
| **디버깅** | 어려움 (생성 SQL 확인 필요) | 쉬움 (SQL이 명확) |

---

## 7. 사용 시나리오 비교

### **JPA가 적합한 경우**

#### **1. CRUD 중심의 애플리케이션**

```java
// 대부분의 작업이 단순 CRUD
memberRepository.save(member);
memberRepository.findById(id);
memberRepository.delete(member);
```

**이유:**
- JPA의 자동 CRUD 기능 활용
- 반복 코드 제거

#### **2. 객체지향적인 도메인 모델링**

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

**이유:**
- 객체 중심 설계 가능
- 연관관계 탐색이 자연스러움

#### **3. 빠른 프로토타이핑**

**이유:**
- 개발 속도가 중요
- 초기에는 ORM으로 빠르게 개발

#### **4. 데이터베이스 변경 가능성**

**이유:**
- DBMS 독립성 제공
- 마이그레이션 용이

### **MyBatis가 적합한 경우**

#### **1. 복잡한 통계 쿼리**

```java
// 복잡한 통계 쿼리
@Select("SELECT " +
        "  department, " +
        "  COUNT(*) as emp_count, " +
        "  AVG(salary) as avg_salary, " +
        "  SUM(CASE WHEN status = 'ACTIVE' THEN 1 ELSE 0 END) as active_count " +
        "FROM employees " +
        "WHERE hire_date BETWEEN #{start} AND #{end} " +
        "GROUP BY department " +
        "HAVING COUNT(*) > 100")
List<DepartmentStats> getDepartmentStats(@Param("start") LocalDate start,
                                         @Param("end") LocalDate end);
```

**이유:**
- 복잡한 쿼리 작성 용이
- 쿼리 최적화 가능

#### **2. 성능이 매우 중요한 경우**

**이유:**
- 최적화된 SQL 직접 작성
- 필요한 컬럼만 조회
- 인덱스 활용 최적화

#### **3. 기존 레거시 시스템**

**이유:**
- 기존 SQL 쿼리 재사용
- 복잡한 스키마와의 매핑

#### **4. SQL 중심 개발이 익숙한 팀**

**이유:**
- 학습 곡선이 낮음
- 빠른 시작 가능

---

## 8. 하이브리드 접근법

### **JPA + MyBatis 혼용**

실제 프로젝트에서는 **JPA와 MyBatis를 함께 사용**하는 하이브리드 접근이 효과적입니다.

#### **전략**

1. **일반적인 CRUD**: JPA 사용
2. **복잡한 쿼리**: MyBatis 사용
3. **성능이 중요한 부분**: 최적화된 SQL 직접 작성

#### **예시**

```java
// JPA: 일반적인 CRUD
@Service
public class MemberService {
    @Autowired
    private MemberRepository memberRepository;  // JPA
    
    public void save(Member member) {
        memberRepository.save(member);  // JPA 사용
    }
}

// MyBatis: 복잡한 통계 쿼리
@Service
public class StatisticsService {
    @Autowired
    private StatisticsMapper statisticsMapper;  // MyBatis
    
    public List<DepartmentStats> getDepartmentStats() {
        return statisticsMapper.getDepartmentStats();  // MyBatis 사용
    }
}
```

**장점:**
- 각 기술의 장점 활용
- 상황에 맞는 최적의 선택
- 유연한 개발

---

## 9. 실제 사용 경험

### **JPA 사용 시 주의사항**

#### **1. N+1 문제 해결**

```java
// 문제: N+1 문제 발생
List<Order> orders = orderRepository.findAll();
for (Order order : orders) {
    Member member = order.getMember();  // 각각 조회
}

// 해결: Fetch Join 사용
@Query("SELECT o FROM Order o JOIN FETCH o.member")
List<Order> findAllWithMember();
```

#### **2. 생성되는 SQL 확인**

```yaml
# application.yml
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

### **MyBatis 사용 시 주의사항**

#### **1. SQL 인젝션 방지**

```java
// ❌ 위험: SQL 인젝션 가능
@Select("SELECT * FROM members WHERE name = '" + name + "'")

// ✅ 안전: 파라미터 바인딩 사용
@Select("SELECT * FROM members WHERE name = #{name}")
List<Member> findByName(String name);
```

#### **2. 동적 SQL 활용**

```xml
<!-- 동적 SQL로 유연한 쿼리 작성 -->
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

---

## 요약

- **JPA**는 ORM 기술로 객체 중심 개발을 가능하게 하며, SQL 자동 생성과 자동 성능 최적화를 제공합니다.
- **MyBatis**는 SQL 매퍼 프레임워크로 SQL 중심 개발을 지원하며, 복잡한 쿼리 작성과 성능 최적화에 유리합니다.
- **개발 방식**: JPA는 객체 중심 사고, MyBatis는 SQL 중심 사고
- **장점**: JPA는 생산성 향상과 객체지향 개발, MyBatis는 SQL 직접 제어와 명시적인 쿼리
- **단점**: JPA는 학습 곡선과 복잡한 쿼리의 한계, MyBatis는 반복적인 SQL 작성과 객체-테이블 매핑 수작업
- **성능**: JPA는 1차 캐시와 지연 로딩으로 최적화, MyBatis는 쿼리 최적화 직접 제어
- **학습 곡선**: JPA는 높음 (추상적 개념 많음), MyBatis는 낮음 (SQL만 알면 시작 가능)
- **사용 시나리오**: JPA는 CRUD 중심과 객체지향 도메인 모델링, MyBatis는 복잡한 통계 쿼리와 성능이 중요한 경우
- **하이브리드 접근**: JPA와 MyBatis를 함께 사용하여 각 기술의 장점을 활용하는 것이 실용적입니다.

---

## 참고 자료

- [JPA Specification](https://jcp.org/en/jsr/detail?id=338)
- [MyBatis Documentation](https://mybatis.org/mybatis-3/)
- [Hibernate Documentation](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html)
- [Spring Data JPA Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
