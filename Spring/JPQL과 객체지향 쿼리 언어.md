#jpa #jpql #쿼리 #객체지향 #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[JPA 연관관계 매핑|JPA 연관관계 매핑]] [[N+1 문제 (JPA)|N+1 문제]]

> [!note] 이어서 읽기
> JPQL을 이해하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

JPQL과 객체지향 쿼리 언어가 무엇인지, 왜 사용하는지, 그리고 SQL과의 차이점을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- SQL을 직접 사용하면 테이블 중심으로 쿼리를 작성해야 하며, 데이터베이스에 종속적인 코드가 됩니다. 객체지향 프로그래밍 패러다임과 맞지 않아 엔티티 객체를 다루기 어렵고, 데이터베이스를 변경할 때마다 쿼리를 수정해야 합니다.

- JPQL은 객체지향 프로그래밍 패러다임에 맞는 쿼리 언어로, 테이블 중심이 아닌 **엔티티 중심**으로 데이터를 조회할 수 있습니다. SQL 의존도를 줄이고 데이터베이스 독립적인 개발이 가능하며, 엔티티 객체를 직접 다룰 수 있어 객체지향적인 개발이 가능합니다.

- JPQL 기본 문법, 파라미터 바인딩, 페치 조인, 벌크 연산, 네이티브 쿼리와의 차이점, 그리고 실무에서 자주 사용하는 패턴들을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. 객체지향 쿼리 언어란?

**객체지향 쿼리 언어**는 객체지향 프로그래밍 패러다임에 맞는 쿼리 언어입니다.

**필요성:**
- 객체지향 프로그래밍 패러다임에 맞는 쿼리 필요
- 테이블 중심이 아닌 **엔티티 중심**으로 데이터 조회
- SQL 의존도를 줄이고, 데이터베이스 독립적인 개발 가능

**JPA를 통해 사용하는 주요 쿼리 방법:**

1. **JPQL (Java Persistence Query Language)**: 가장 기본적이고 표준적인 객체지향 쿼리 언어
2. **네이티브 쿼리**: JPQL로 처리 불가능한 복잡한 쿼리 또는 DB 특정 기능 사용 시
3. **QueryDSL**: 코드 기반의 쿼리 작성, 동적 쿼리에 매우 강력

> [!tip] 핵심 포인트
> JPQL은 SQL과 유사하지만 테이블이 아닌 엔티티를 대상으로 쿼리를 작성하여 객체지향적인 데이터 조회가 가능합니다.

---

#### 2. JPA의 객체지향 쿼리 언어: JPQL

**JPQL (Java Persistence Query Language)**은 JPA에서 제공하는 객체지향 쿼리 언어입니다.

**JPQL의 특징:**
- 테이블이 아닌 "엔티티 객체"를 대상으로 쿼리 수행
- SQL 의존도를 낮추고, DB 독립적 개발 가능
- SQL과 유사한 문법

##### JPQL과 SQL 차이점

| 구분 | JPQL | SQL |
| --- | --- | --- |
| **대상** | 엔티티 객체 | 테이블/컬럼 |
| **의존성** | 낮음 (DB 독립적) | 높음 |
| **문법** | 유사 | 표준 SQL |

**예시:**

```java
// JPQL: 엔티티를 대상으로 쿼리
String jpql = "SELECT m FROM Member m WHERE m.age > 18";

// SQL: 테이블을 대상으로 쿼리
String sql = "SELECT * FROM MEMBER WHERE AGE > 18";
```

---

#### 3. JPQL 기본 문법

##### 3.1 SELECT 문

**기본 문법:**
```java
SELECT [엔티티 또는 필드] FROM [엔티티명 별칭] WHERE [조건]
```

**예시:**

```java
// 엔티티 기반 쿼리 작성, Member(엔티티 이름)에는 별칭 m을 부여함 (별칭 부여는 필수)
String jpql = "SELECT m FROM Member m WHERE m.age > 18";

// 쿼리를 전달하고 영속 객체를 조회함. 결과를 list로 가져옴.
List<Member> members = em.createQuery(jpql, Member.class).getResultList();
```

**특징:**
- 엔티티 이름 사용 (테이블 이름 아님)
- 별칭은 필수
- 결과를 엔티티 객체로 반환

##### 3.2 파라미터 바인딩

**이름 기준 파라미터 바인딩:**

```java
String jpql = "SELECT m FROM Member m WHERE m.username = :username";
List<Member> result = em.createQuery(jpql, Member.class)
                       .setParameter("username", "kim")
                       .getResultList();
```

**위치 기준 파라미터 바인딩:**

```java
String jpql = "SELECT m FROM Member m WHERE m.username = ?1";
List<Member> result = em.createQuery(jpql, Member.class)
                       .setParameter(1, "kim")
                       .getResultList();
```

**IN 절 파라미터 바인딩:**

```java
String jpql = "SELECT m FROM Member m WHERE m.username IN :usernames";

List<String> usernames = List.of("kim", "lee");
List<Member> result = em.createQuery(jpql, Member.class)
                       .setParameter("usernames", usernames)  // IN 절에 리스트 전달
                       .getResultList();
```

> [!important] 핵심 원리
> 파라미터 바인딩을 사용하면 SQL Injection을 방지하고, 쿼리 재사용이 가능하며, 성능 최적화(쿼리 캐싱)가 가능합니다.

##### 3.3 필드 지정 조회

**특정 필드만 조회:**

```java
String jpql = "SELECT m.username, m.age FROM Member m";

// List<Object[]> → 한 사람(회원)의 데이터가 Object 배열에 담겨 반환됨
// Object[]의 인덱스에 조회된 컬럼이 순서대로 담김
List<Object[]> result = em.createQuery(jpql).getResultList();

for (Object[] row : result) {
    String username = (String) row[0];
    Integer age = (Integer) row[1];
}
```

**장점:**
- 메모리 사용량 감소
- 네트워크 비용 절감
- 필요한 데이터만 조회

**DTO 조회 (new 명령어):**

```java
// DTO 객체를 내부적으로 만들어서 받는 것이기 때문에 반드시 생성자가 있어야 함.
String jpql = "SELECT NEW com.example.MemberDTO(m.username, m.age) FROM Member m";
List<MemberDTO> result = em.createQuery(jpql, MemberDTO.class).getResultList();
```

**DTO 클래스 예시:**

```java
public class MemberDTO {
    private String username;
    private Integer age;
    
    // 반드시 생성자가 있어야 함
    public MemberDTO(String username, Integer age) {
        this.username = username;
        this.age = age;
    }
}
```

---

#### 4. 경로 표현식 & 조인

##### 4.1 엔티티 연관 경로 표현식

**경로 표현식의 종류:**

1. **상태 필드(일반 값)**: `m.username`
2. **단일 값 연관 필드(다른 엔티티 단일 참조 값)**: `m.team`
3. **컬렉션 값 연관 필드(다른 엔티티 다중 참조 값)**: `t.members`

##### 4.2 조인 종류

**내부 조인 (INNER JOIN):**

```java
String jpql = "SELECT m FROM Member m JOIN m.team t";
List<Member> members = em.createQuery(jpql, Member.class).getResultList();
```

- 팀 정보가 있는 멤버만 조회

**외부 조인 (LEFT JOIN):**

```java
String jpql = "SELECT m FROM Member m LEFT JOIN m.team t";
List<Member> members = em.createQuery(jpql, Member.class).getResultList();
```

- 팀이 있어도 좋고, 없어도 상관없이 **모든 멤버 다 조회**

**세타 조인 (Theta Join):**

```java
String jpql = "SELECT m FROM Member m, Team t WHERE m.username = t.name";
List<Member> members = em.createQuery(jpql, Member.class).getResultList();
```

- 모든 멤버와 모든 팀을 조합해 보고, 멤버 이름과 팀 이름이 **같은 경우만** 결과 조회
- 연관관계 없는 테이블 조인, 일반적으로 사용하지 않음

---

#### 5. 페치 조인 (Fetch Join)

**페치 조인**은 성능 최적화를 위해 연관 엔티티를 한 번에 조회하는 방법입니다.

##### 5.1 N+1 문제

**문제 상황:**

```java
List<Member> members = em.createQuery("SELECT m FROM Member m", Member.class).getResultList();

for (Member member : members) {
    // 여기에 도달할 때마다 추가 쿼리 날아감!
    System.out.println(member.getTeam().getName());
}
// 멤버가 100명 있다고 한다면 team 쿼리는 100번 호출됨 → N+1 문제 발생
```

**N+1 문제:**
- 1번의 쿼리로 Member를 조회
- 각 Member의 Team을 조회할 때마다 추가 쿼리 발생
- 총 N+1번의 쿼리 실행

##### 5.2 페치 조인 해결

**엔티티 페치 조인:**

```java
String jpql = "SELECT m FROM Member m JOIN FETCH m.team";
List<Member> members = em.createQuery(jpql, Member.class).getResultList();
```

**특징:**
- Member → Team 관계는 `@ManyToOne` 또는 `@OneToOne` 관계
- `JOIN FETCH`를 사용하면, Member와 Team을 한 번의 쿼리로 모두 가져옵니다
- `member.getTeam()`을 호출해도 추가 쿼리가 발생하지 않음

**주의사항:**
- 반대로 `@OneToMany` 관계인 Team 관점으로 조회 시 데이터가 중복으로 과도하게 생기기 때문에 조심해야 함

**컬렉션 페치 조인:**

```java
String jpql = "SELECT t FROM Team t JOIN FETCH t.members";
List<Team> teams = em.createQuery(jpql, Team.class).getResultList();
```

> [!important] 핵심 원리
> 페치 조인은 N+1 문제를 해결하고, 연관 엔티티를 한 번에 조회하여 성능을 최적화합니다.

---

#### 6. 서브쿼리

**서브쿼리**는 WHERE, HAVING 절에서만 사용 가능합니다.

**WHERE 절 사용:**

```java
// Member 테이블에서 평균 나이보다 나이가 많은 회원을 조회
String jpql = "SELECT m FROM Member m WHERE m.age > (SELECT AVG(m2.age) FROM Member m2)";
List<Member> members = em.createQuery(jpql, Member.class).getResultList();
```

**EXISTS 사용:**

```java
// Member 엔티티 중, '팀A'에 소속된 사람만 조회
String jpql = "SELECT m FROM Member m WHERE EXISTS (SELECT t FROM m.team t WHERE t.name = '팀A')";
List<Member> members = em.createQuery(jpql, Member.class).getResultList();
```

**제한사항:**
- FROM 절 서브쿼리는 불가 (Hibernate 6 이상은 지원하지만 표준 아님)

---

#### 7. 벌크 연산

**벌크 연산**은 JPQL을 이용해 한 번의 쿼리로 여러 데이터를 직접 수정/삭제하는 방법입니다.

**일반 JPA vs 벌크 연산:**

- **일반 JPA**: 엔티티 객체를 개별 관리하면서 변경 사항을 감지하고 `flush` 시점에 DB에 반영
- **벌크 연산**: 영속성 컨텍스트를 무시하고 바로 DB에 반영 → 실행 속도가 훨씬 빠름

**UPDATE 예시:**

```java
String jpql = "UPDATE Member m SET m.age = m.age + 1 WHERE m.age > :age";
int resultCount = em.createQuery(jpql)
                    .setParameter("age", 19)
                    .executeUpdate();
```

**DELETE 예시:**

```java
String jpql = "DELETE FROM Member m WHERE m.age < :age";
int resultCount = em.createQuery(jpql)
                    .setParameter("age", 20)
                    .executeUpdate();
```

**벌크 연산 주의사항:**

- 영속성 컨텍스트를 무시하고 바로 DB에 반영
- 벌크 연산 후 `em.clear()`로 영속성 컨텍스트 초기화 필요

```java
em.createQuery(jpql).executeUpdate();
em.clear();  // 영속성 컨텍스트 초기화
```

**이유:**
- 벌크 연산은 영속성 컨텍스트를 거치지 않고 DB에 직접 반영
- 영속성 컨텍스트에 있는 엔티티와 DB의 데이터가 불일치할 수 있음
- 따라서 `em.clear()`로 영속성 컨텍스트를 초기화해야 함

> [!warning] 주의
> 벌크 연산은 UPDATE와 DELETE만 지원하며, INSERT는 지원하지 않습니다.

---

#### 8. 직접 SQL을 전달하는 방식: 네이티브 SQL

**네이티브 SQL**은 JPA가 제공하는 JPQL 대신 **직접 SQL을 작성하여 실행**하는 방법입니다.

##### 8.1 언제 사용할까?

- JPQL로 표현하기 힘든 복잡한 쿼리
- DB 고유 함수, 윈도우 함수 등 JPQL 미지원 기능 사용
- 통계, 리포트, 대용량 배치 처리

##### 8.2 기본 사용법

**엔티티 매핑 결과 반환:**

```java
String sql = "SELECT * FROM MEMBER WHERE AGE > ?";
List<Member> members = em.createNativeQuery(sql, Member.class)
                        .setParameter(1, 20)
                        .getResultList();
```

**특징:**
- `createNativeQuery`: 순수 SQL 실행하는 함수
- `Member.class`: 결과를 `Member` 엔티티로 매핑
- `setParameter(1, 20)`: 첫 번째 `?` 자리에 값 20을 바인딩
- `getResultList()`: 결과를 리스트로 반환

**값만 가져오기 (Object[] 반환):**

```java
String sql = "SELECT USERNAME, AGE FROM MEMBER";
List<Object[]> results = em.createNativeQuery(sql).getResultList();

for (Object[] row : results) {
    String username = (String) row[0];
    Integer age = (Integer) row[1];
}
```

**특징:**
- 엔티티로 매핑하지 않고, **필드 값만 직접 조회**
- 반환 결과는 `Object[]` 배열로 각 컬럼 값이 순서대로 저장됨

**DTO 매핑 (@SqlResultSetMapping):**

```java
@SqlResultSetMapping(
    name = "MemberDTOMapping",
    classes = @ConstructorResult(
        targetClass = MemberDTO.class,
        columns = {
            @ColumnResult(name = "USERNAME", type = String.class),
            @ColumnResult(name = "AGE", type = Integer.class)
        }
    )
)

// 사용
String sql = "SELECT USERNAME, AGE FROM MEMBER";
List<MemberDTO> dtos = em.createNativeQuery(sql, "MemberDTOMapping").getResultList();
```

**특징:**
- 결과를 바로 DTO로 매핑할 수 있게 설정
- `@ColumnResult` 순서 vs DTO 생성자 파라미터 순서를 반드시 일치시켜야 함
- DTO 클래스에는 반드시 **해당 필드를 받는 생성자**가 있어야 함

**DML (INSERT, UPDATE, DELETE):**

```java
// INSERT
em.createNativeQuery("INSERT INTO MEMBER (USERNAME, AGE) VALUES (?, ?)")
  .setParameter(1, "kim")
  .setParameter(2, 30)
  .executeUpdate();

// UPDATE
em.createNativeQuery("UPDATE MEMBER SET AGE = AGE + 1 WHERE AGE < ?")
  .setParameter(1, 30)
  .executeUpdate();

// DELETE
em.createNativeQuery("DELETE FROM MEMBER WHERE AGE < ?")
  .setParameter(1, 20)
  .executeUpdate();

em.clear();  // 영속성 컨텍스트 초기화
```

**특징:**
- 네이티브 쿼리는 **영속성 컨텍스트를 무시**하고 바로 DB에 반영
- 따라서, 반드시 `em.clear()` 또는 `em.flush()`로 컨텍스트 초기화

##### 8.3 동적 네이티브 쿼리

```java
StringBuilder sql = new StringBuilder("SELECT * FROM MEMBER WHERE 1=1");
if (searchName != null) {
    sql.append(" AND USERNAME = :username");
}

Query query = em.createNativeQuery(sql.toString(), Member.class);
if (searchName != null) {
    query.setParameter("username", searchName);
}

List<Member> members = query.getResultList();
```

##### 8.4 네이티브 쿼리 vs JPQL 비교

| 항목 | 네이티브 SQL | JPQL |
| --- | --- | --- |
| **작성 대상** | 테이블, 컬럼명 | 엔티티, 필드명 |
| **DB 독립성** | 낮음 | 높음 |
| **복잡한 쿼리** | 가능 | 한계 있음 |
| **유지보수** | 어려움 | 용이 |
| **결과 매핑** | 수동 매핑 필요 | 자동 매핑 가능 |

**사용 원칙:**
- JPQL로 가능한 경우 **우선 JPQL 사용**
- 복잡한 리포트, 통계, 대량 배치 처리 시 네이티브 SQL 고려
- 네이티브 쿼리 사용 후, 영속성 컨텍스트 상태 동기화 주의 (`em.clear()` 필요)
- DTO 매핑은 `@SqlResultSetMapping` 활용
- 네이티브 SQL은 유지보수와 이식성 문제로 **필요할 때만 신중하게 사용**

---

#### 장점/단점

**JPQL 장점:**
- 데이터베이스 독립적
- 엔티티 중심의 객체지향적 쿼리
- 타입 안정성 (컴파일 시점 검증 가능)
- 유지보수 용이

**JPQL 단점:**
- 복잡한 쿼리 표현의 한계
- 성능 최적화가 어려울 수 있음
- 학습 곡선

**네이티브 쿼리 장점:**
- 복잡한 쿼리 가능
- DB 특정 기능 사용 가능
- 성능 최적화 가능

**네이티브 쿼리 단점:**
- 데이터베이스 종속적
- 유지보수 어려움
- 이식성 저하

---

#### 필요 조건

- **JPA 기본 이해**: 엔티티, 영속성 컨텍스트 등 기본 개념
- **SQL 기본 지식**: SELECT, JOIN, WHERE 등 기본 문법
- **엔티티 설계**: 연관관계 매핑 이해

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### JPQL 사용 시 확인사항

- [ ] 파라미터 바인딩을 사용했는가? (SQL Injection 방지)
- [ ] 페치 조인을 적절히 사용했는가? (N+1 문제 방지)
- [ ] 벌크 연산 후 영속성 컨텍스트를 초기화했는가?
- [ ] 네이티브 쿼리는 정말 필요한 경우에만 사용했는가?

#### 실무 활용 예시

**1. 페치 조인으로 N+1 문제 해결**

```java
// 나쁜 예: N+1 문제 발생
List<Member> members = em.createQuery("SELECT m FROM Member m", Member.class).getResultList();
for (Member member : members) {
    System.out.println(member.getTeam().getName());  // 각각 쿼리 발생
}

// 좋은 예: 페치 조인 사용
List<Member> members = em.createQuery(
    "SELECT m FROM Member m JOIN FETCH m.team", 
    Member.class
).getResultList();
for (Member member : members) {
    System.out.println(member.getTeam().getName());  // 추가 쿼리 없음
}
```

**2. DTO 조회**

```java
String jpql = "SELECT NEW com.example.MemberDTO(m.username, m.age) FROM Member m WHERE m.age > :age";
List<MemberDTO> dtos = em.createQuery(jpql, MemberDTO.class)
                         .setParameter("age", 18)
                         .getResultList();
```

**3. 벌크 연산**

```java
// 나이 20세 이상 회원의 나이를 1씩 증가
String jpql = "UPDATE Member m SET m.age = m.age + 1 WHERE m.age >= :age";
int updatedCount = em.createQuery(jpql)
                     .setParameter("age", 20)
                     .executeUpdate();
em.clear();  // 영속성 컨텍스트 초기화
```

**4. 복잡한 통계 쿼리 (네이티브 쿼리)**

```java
String sql = """
    SELECT 
        t.name as teamName,
        COUNT(m.id) as memberCount,
        AVG(m.age) as avgAge
    FROM TEAM t
    LEFT JOIN MEMBER m ON t.id = m.team_id
    GROUP BY t.id, t.name
    HAVING COUNT(m.id) > 5
""";

List<Object[]> results = em.createNativeQuery(sql).getResultList();
```

#### 주의사항

**1. 파라미터 바인딩 필수**

```java
// 나쁜 예: SQL Injection 위험
String jpql = "SELECT m FROM Member m WHERE m.username = '" + username + "'";

// 좋은 예: 파라미터 바인딩
String jpql = "SELECT m FROM Member m WHERE m.username = :username";
em.createQuery(jpql, Member.class).setParameter("username", username);
```

**2. 페치 조인 시 데이터 중복 주의**

```java
// @OneToMany 관계에서 페치 조인 시 데이터 중복 발생 가능
String jpql = "SELECT t FROM Team t JOIN FETCH t.members";
// Team이 1개이고 Member가 10개면 결과가 10개로 중복됨
// DISTINCT 사용 권장
String jpql = "SELECT DISTINCT t FROM Team t JOIN FETCH t.members";
```

**3. 벌크 연산 후 영속성 컨텍스트 초기화**

```java
// 벌크 연산은 영속성 컨텍스트를 무시하고 DB에 직접 반영
em.createQuery(jpql).executeUpdate();
em.clear();  // 반드시 초기화 필요
```

---

### 설정 시 반드시 고려해야 할 파라미터

- **파라미터 바인딩**: SQL Injection 방지를 위해 항상 사용
- **페치 조인**: N+1 문제 해결을 위해 적절히 사용
- **벌크 연산**: 영속성 컨텍스트 초기화 필수
- **네이티브 쿼리**: 최소한으로 사용, 필요 시에만

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "JPQL을 사용하면 모든 쿼리가 자동으로 최적화된다"는 생각은 잘못되었습니다. JPQL도 적절한 인덱스와 페치 조인을 사용해야 성능이 좋습니다. 또한 "네이티브 쿼리를 사용하면 항상 성능이 좋다"는 생각도 잘못되었습니다. 네이티브 쿼리는 유지보수와 이식성 문제가 있으므로 필요한 경우에만 사용해야 합니다.

#### JPQL 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **N+1 문제**: 연관 엔티티 조회 시 추가 쿼리 발생
> - **SQL Injection**: 문자열 결합으로 쿼리 작성 시 보안 취약점
> - **데이터베이스 종속**: 네이티브 쿼리 과다 사용으로 이식성 저하
> - **성능 저하**: 페치 조인 미사용으로 인한 성능 저하
> - **데이터 불일치**: 벌크 연산 후 영속성 컨텍스트 미초기화

---

## 요약

- **객체지향 쿼리 언어의 필요성**: 객체지향 프로그래밍 패러다임에 맞는 쿼리 필요, 테이블 중심이 아닌 엔티티 중심으로 데이터 조회, SQL 의존도 감소

- **JPQL의 정의**: JPA에서 제공하는 객체지향 쿼리 언어로, 테이블이 아닌 엔티티 객체를 대상으로 쿼리 수행

- **주요 기능**: 
  - 기본 문법: SELECT, FROM, WHERE
  - 파라미터 바인딩: 이름 기준, 위치 기준
  - 페치 조인: N+1 문제 해결
  - 벌크 연산: 대량 데이터 수정/삭제
  - 서브쿼리: 복잡한 조건 처리

- **네이티브 쿼리**: JPQL로 표현하기 어려운 복잡한 쿼리나 DB 특정 기능 사용 시 활용, 하지만 최소한으로 사용

- JPQL은 객체지향적인 데이터 조회를 가능하게 하며, 데이터베이스 독립적인 개발을 지원합니다.
- 페치 조인을 적절히 사용하면 N+1 문제를 해결하고 성능을 최적화할 수 있습니다.
- 벌크 연산과 네이티브 쿼리는 필요한 경우에만 신중하게 사용해야 합니다.

---

### 예상 꼬리질문정리

#### 1. JPQL과 SQL의 차이는?

- **대상**: JPQL은 엔티티 객체, SQL은 테이블/컬럼
- **의존성**: JPQL은 DB 독립적, SQL은 DB 종속적
- **문법**: 유사하지만 JPQL은 엔티티 이름과 필드명 사용

#### 2. 페치 조인이 왜 필요한가요?

- **N+1 문제 해결**: 연관 엔티티를 한 번에 조회하여 추가 쿼리 방지
- **성능 최적화**: 여러 번의 쿼리를 한 번으로 줄임
- **사용법**: `JOIN FETCH` 키워드 사용

#### 3. 벌크 연산 후 영속성 컨텍스트를 초기화하는 이유는?

- **데이터 불일치**: 벌크 연산은 영속성 컨텍스트를 무시하고 DB에 직접 반영
- **동기화 필요**: 영속성 컨텍스트의 엔티티와 DB 데이터가 불일치할 수 있음
- **해결**: `em.clear()`로 영속성 컨텍스트 초기화

#### 4. 네이티브 쿼리를 언제 사용하나요?

- **복잡한 쿼리**: JPQL로 표현하기 어려운 경우
- **DB 특정 기능**: 윈도우 함수, DB 고유 함수 사용
- **성능 최적화**: 특정 상황에서 성능이 중요한 경우
- **주의**: 유지보수와 이식성 문제로 최소한으로 사용

#### 5. 파라미터 바인딩을 사용해야 하는 이유는?

- **SQL Injection 방지**: 보안 취약점 방지
- **쿼리 재사용**: 쿼리 캐싱으로 성능 향상
- **타입 안정성**: 컴파일 시점 타입 검증

---

## 참고 자료

- [JPA 공식 문서 - JPQL](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#jpql) - JPA 공식 JPQL 문서
- [Baeldung - JPQL Guide](https://www.baeldung.com/jpql-hql-criteria-queries) - JPQL 가이드

---

## 관련 노트

- **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]** - JPA의 기본 개념과 필요성
- **[[JPA 연관관계 매핑|JPA 연관관계 매핑]]** - 엔티티 간 관계와 조인
- **[[N+1 문제 (JPA)|N+1 문제]]** - 페치 조인으로 해결하는 성능 문제

---

**태그:** #jpa #jpql #쿼리 #객체지향 #면접 #개념정리
