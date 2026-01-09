#spring #jpa #persistence-context #entitymanager #hibernate #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[JPA 엔티티 매핑|JPA 엔티티 매핑]] [[spring-boot-starter-data-jpa와 영속성 관리|spring-boot-starter-data-jpa와 영속성 관리]]

> [!note] 이어서 읽기
> 영속성 컨텍스트를 이해하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**영속성 컨텍스트(Persistence Context)** 는 JPA의 핵심 메커니즘으로, 엔티티를 메모리에 저장하고 관리하는 공간입니다. 1차 캐시, 변경 감지, 쓰기 지연 등의 기능을 통해 개발자가 SQL을 직접 작성하지 않고도 객체의 상태 변화를 자동으로 데이터베이스에 반영할 수 있게 해줍니다. 영속성 컨텍스트를 이해하면 JPA의 동작 원리를 완전히 파악할 수 있습니다.

---

### **Why (왜 사용하는가? 왜 중요한가?)** 

- 영속성 컨텍스트는 JPA가 객체의 상태 변화를 자동으로 추적하고 데이터베이스와 동기화하는 핵심 메커니즘입니다. 개발자가 UPDATE 쿼리를 직접 작성하지 않아도 엔티티 필드 값만 변경하면 자동으로 데이터베이스에 반영되며, 1차 캐시를 통해 같은 트랜잭션 내에서 동일한 엔티티 인스턴스를 보장합니다.

- 영속성 컨텍스트의 개념과 구조, 1차 캐시와 엔티티 동일성 보장, 변경 감지(Dirty Checking) 메커니즘, 쓰기 지연(Write-Behind) 전략, 플러시(Flush) 동작 원리, 그리고 엔티티 생명주기를 이해해야 합니다.

---

## 1. 영속성 컨텍스트란?

### **영속성 컨텍스트의 정의**

**영속성 컨텍스트(Persistence Context)** 는 **엔티티를 영구 저장하는 환경**을 의미합니다. 쉽게 말해, JPA가 엔티티를 관리하는 **1차 캐시 공간**입니다.

### **영속성 컨텍스트의 구조**

```
영속성 컨텍스트 (Persistence Context)
├── 1차 캐시 (Entity Cache)
│   └── 엔티티를 @Id를 키로 저장
├── 쓰기 지연 저장소 (Write-Behind Queue)
│   └── INSERT, UPDATE, DELETE 쿼리 저장
└── 스냅샷 (Snapshot)
    └── 엔티티의 최초 상태 저장 (변경 감지용)
```

### **영속성 컨텍스트의 특징**

1. **엔티티 관리**: 영속 상태의 엔티티를 메모리에 저장하고 관리
2. **1차 캐시**: 같은 트랜잭션 내에서 동일한 엔티티를 재사용
3. **변경 감지**: 엔티티의 상태 변화를 자동으로 추적
4. **쓰기 지연**: 쿼리를 모아서 한 번에 실행
5. **엔티티 동일성 보장**: 같은 트랜잭션 내에서 같은 엔티티 인스턴스 보장

### **엔티티 매니저와 영속성 컨텍스트의 관계**

**엔티티 매니저(EntityManager)** 는 영속성 컨텍스트를 생성하고 관리하는 인터페이스입니다.

```java
EntityManager em = emf.createEntityManager();
// em을 통해 영속성 컨텍스트에 접근
```

**관계:**
- 엔티티 매니저 1개 = 영속성 컨텍스트 1개
- 영속성 컨텍스트는 엔티티 매니저를 통해 접근

---

## 2. 1차 캐시 (First-Level Cache)

### **1차 캐시란?**

**1차 캐시**는 영속성 컨텍스트 내부에 있는 엔티티 저장소로, **@Id를 키로 엔티티를 저장**합니다.

### **1차 캐시의 동작**

#### **조회 흐름**

```java
// 1차 조회: DB에서 조회 후 1차 캐시에 저장
Member member1 = em.find(Member.class, "member1");
// SELECT 쿼리 실행 → 1차 캐시에 저장

// 2차 조회: 1차 캐시에서 조회 (DB 접근 없음)
Member member2 = em.find(Member.class, "member1");
// DB 접근 없이 1차 캐시에서 반환
```

**동작 순서:**
1. 1차 캐시에서 조회 시도
2. 있으면 → 캐시에서 반환 (DB 접근 없음)
3. 없으면 → DB 조회 → 1차 캐시에 저장 → 반환

### **1차 캐시의 장점**

#### **1. 성능 최적화**

```java
// 같은 트랜잭션 내에서 같은 엔티티를 여러 번 조회해도
// DB에 한 번만 접근
Member m1 = em.find(Member.class, "member1");  // DB 조회
Member m2 = em.find(Member.class, "member1");  // 1차 캐시에서 조회
Member m3 = em.find(Member.class, "member1");  // 1차 캐시에서 조회
```

**장점:**
- DB 접근 횟수 감소
- 네트워크 비용 절감
- 성능 향상

#### **2. 엔티티 동일성 보장**

```java
Member m1 = em.find(Member.class, "member1");
Member m2 = em.find(Member.class, "member1");

System.out.println(m1 == m2);  // true (동일한 인스턴스)
```

**장점:**
- 같은 트랜잭션 내에서 항상 같은 엔티티 인스턴스
- 데이터 일관성 보장
- 동시성 문제 방지

### **1차 캐시의 한계**

**1차 캐시는 트랜잭션 범위 내에서만 유효합니다.**

```java
// 트랜잭션 1
em.getTransaction().begin();
Member m1 = em.find(Member.class, "member1");  // DB 조회
em.getTransaction().commit();
em.close();  // 영속성 컨텍스트 종료

// 트랜잭션 2 (새로운 영속성 컨텍스트)
EntityManager em2 = emf.createEntityManager();
em2.getTransaction().begin();
Member m2 = em2.find(Member.class, "member1");  // 다시 DB 조회
// 이전 트랜잭션의 1차 캐시는 사용 불가
```

**특징:**
- 트랜잭션이 끝나면 영속성 컨텍스트도 종료
- 1차 캐시도 함께 사라짐
- 애플리케이션 전체에 공유되는 캐시가 아님

---

## 3. 엔티티 동일성 보장

### **엔티티 동일성이란?**

**엔티티 동일성**은 같은 트랜잭션 내에서 같은 @Id를 가진 엔티티는 항상 같은 인스턴스라는 것을 보장합니다.

### **동일성 보장 예시**

```java
@Transactional
public void test() {
    Member m1 = memberRepository.findById("member1").orElseThrow();
    Member m2 = memberRepository.findById("member1").orElseThrow();
    
    System.out.println(m1 == m2);  // true
    System.out.println(m1.equals(m2));  // true (equals 구현 시)
}
```

**이유:**
- 1차 캐시에서 같은 엔티티 인스턴스를 반환
- DB에 여러 번 접근하지 않음

### **동일성 보장의 장점**

#### **1. 데이터 일관성**

```java
@Transactional
public void updateMember() {
    Member m1 = memberRepository.findById("member1").orElseThrow();
    m1.setName("홍길동");
    
    Member m2 = memberRepository.findById("member1").orElseThrow();
    System.out.println(m2.getName());  // "홍길동" (항상 최신 상태)
}
```

**장점:**
- 같은 트랜잭션 내에서 항상 최신 상태 보장
- 데이터 불일치 방지

#### **2. 메모리 효율성**

```java
// 여러 곳에서 같은 엔티티를 조회해도
// 메모리에 하나의 인스턴스만 존재
Member m1 = orderService.getOrderMember(orderId);
Member m2 = paymentService.getPaymentMember(orderId);
// m1과 m2는 같은 인스턴스
```

---

## 4. 변경 감지 (Dirty Checking)

### **변경 감지란?**

**변경 감지(Dirty Checking)** 는 영속 상태의 엔티티 필드 값을 변경하면, JPA가 자동으로 이를 감지하여 UPDATE 쿼리를 생성하고 실행하는 기능입니다.

### **변경 감지 동작 원리**

#### **1. 스냅샷 저장**

```java
Member member = em.find(Member.class, "member1");
// 이 시점에 엔티티의 최초 상태를 스냅샷으로 저장
// 스냅샷: {id: "member1", name: "홍길동", age: 25}
```

#### **2. 필드 값 변경**

```java
member.setName("김철수");  // 필드 값만 변경
member.setAge(30);
// UPDATE 쿼리를 직접 작성하지 않음!
```

#### **3. 트랜잭션 커밋 시 비교**

```java
em.getTransaction().commit();
// 1. 스냅샷과 현재 상태 비교
//    스냅샷: {name: "홍길동", age: 25}
//    현재:   {name: "김철수", age: 30}
// 2. 변경 사항 감지
// 3. UPDATE 쿼리 자동 생성 및 실행
//    UPDATE member SET name = '김철수', age = 30 WHERE id = 'member1'
```

### **변경 감지의 장점**

#### **1. 개발 편의성**

```java
// JDBC/MyBatis 방식
public void updateMember(String id, String name) {
    String sql = "UPDATE member SET name = ? WHERE id = ?";
    // PreparedStatement 설정...
}

// JPA 방식
@Transactional
public void updateMember(String id, String name) {
    Member member = memberRepository.findById(id).orElseThrow();
    member.setName(name);  // ✅ 필드만 변경하면 끝!
}
```

**장점:**
- UPDATE 쿼리 작성 불필요
- 객체지향적인 코드 작성 가능
- 실수 가능성 감소

#### **2. 선택적 업데이트**

```java
// 변경된 필드만 UPDATE 쿼리에 포함
// 변경되지 않은 필드는 UPDATE 쿼리에 포함되지 않음
member.setName("김철수");  // name만 변경
// UPDATE member SET name = '김철수' WHERE id = 'member1'
// age는 변경되지 않았으므로 UPDATE 쿼리에 포함 안 됨
```

### **변경 감지가 동작하지 않는 경우**

#### **1. 준영속 상태**

```java
Member member = em.find(Member.class, "member1");
em.detach(member);  // 준영속 상태로 전환

member.setName("김철수");  // 변경 감지 안 됨
em.getTransaction().commit();  // UPDATE 쿼리 실행 안 됨
```

**이유:**
- 준영속 상태는 영속성 컨텍스트에서 분리된 상태
- 변경 감지 대상이 아님

#### **2. 비영속 상태**

```java
Member member = new Member();  // 비영속 상태
member.setName("홍길동");
// 영속성 컨텍스트에 저장되지 않았으므로 변경 감지 안 됨
```

---

## 5. 쓰기 지연 (Write-Behind)

### **쓰기 지연이란?**

**쓰기 지연(Write-Behind)** 은 `persist()`, `remove()` 등을 호출해도 즉시 DB에 쿼리를 실행하지 않고, **쓰기 지연 저장소에 보관했다가 트랜잭션 커밋 시점에 한꺼번에 실행**하는 전략입니다.

### **쓰기 지연 동작 흐름**

#### **1. persist() 호출**

```java
em.getTransaction().begin();

Member member1 = new Member("member1", "홍길동");
em.persist(member1);  // INSERT 쿼리는 아직 실행 안 됨
// 쓰기 지연 저장소에 INSERT 쿼리 저장

Member member2 = new Member("member2", "김철수");
em.persist(member2);  // INSERT 쿼리는 아직 실행 안 됨
// 쓰기 지연 저장소에 INSERT 쿼리 추가
```

**상태:**
- 엔티티는 1차 캐시에 저장됨
- INSERT 쿼리는 쓰기 지연 저장소에 보관
- DB에는 아직 반영되지 않음

#### **2. 트랜잭션 커밋**

```java
em.getTransaction().commit();
// 1. 플러시 발생 (쓰기 지연 저장소의 쿼리 실행)
//    INSERT INTO member (id, name) VALUES ('member1', '홍길동')
//    INSERT INTO member (id, name) VALUES ('member2', '김철수')
// 2. 트랜잭션 커밋
```

**실행 순서:**
1. 플러시(Flush) → 쓰기 지연 저장소의 쿼리를 DB에 실행
2. 커밋(Commit) → 트랜잭션 완료

### **쓰기 지연의 장점**

#### **1. 성능 최적화**

```java
// 여러 개의 INSERT를 한 번에 실행
for (int i = 0; i < 100; i++) {
    Member member = new Member("member" + i, "name" + i);
    em.persist(member);  // 각각 DB 접근하지 않음
}
// 트랜잭션 커밋 시 100개의 INSERT를 한 번에 실행
em.getTransaction().commit();
```

**장점:**
- DB 접근 횟수 감소
- 네트워크 비용 절감
- 배치 처리 효과

#### **2. 트랜잭션 롤백 가능**

```java
em.getTransaction().begin();

Member member = new Member("member1", "홍길동");
em.persist(member);  // 아직 DB에 반영 안 됨

// 문제 발생 시 롤백 가능 (DB에 영향 없음)
em.getTransaction().rollback();
```

### **쓰기 지연이 동작하지 않는 경우**

#### **IDENTITY 전략 사용 시**

```java
@Entity
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;  // DB에서 키 생성
}

// IDENTITY 전략 사용 시
em.persist(member);  // 즉시 INSERT 쿼리 실행 (키 값을 얻기 위해)
```

**이유:**
- DB에서 키 값을 생성해야 함
- 키 값을 얻기 위해 즉시 INSERT 필요
- 쓰기 지연 불가능

---

## 6. 플러시 (Flush)

### **플러시란?**

**플러시(Flush)** 는 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영하는 작업입니다. 쓰기 지연 저장소에 보관된 쿼리를 DB에 실행합니다.

### **플러시와 커밋의 차이**

| 구분 | 플러시 (Flush) | 커밋 (Commit) |
|------|---------------|--------------|
| **의미** | 영속성 컨텍스트 변경 내용을 DB에 반영 | 트랜잭션을 완료하고 변경 사항 확정 |
| **트랜잭션** | 트랜잭션 유지 | 트랜잭션 종료 |
| **영속성 컨텍스트** | 유지됨 | 유지됨 (트랜잭션 범위 내) |
| **1차 캐시** | 유지됨 | 유지됨 |

**관계:**
```
플러시 → 커밋
(변경 내용 DB 반영) → (트랜잭션 완료)
```

### **플러시 발생 시점**

#### **1. 트랜잭션 커밋 시 (자동)**

```java
em.getTransaction().begin();
Member member = new Member("member1", "홍길동");
em.persist(member);

em.getTransaction().commit();
// 1. 플러시 자동 발생 (INSERT 쿼리 실행)
// 2. 커밋 (트랜잭션 완료)
```

#### **2. JPQL 실행 시 (자동)**

```java
em.persist(member1);
em.persist(member2);
em.persist(member3);

// JPQL 실행 전에 플러시 자동 발생
List<Member> members = em.createQuery("SELECT m FROM Member m", Member.class)
    .getResultList();
// 이유: JPQL은 SQL로 변환되어 실행되므로, 
//      영속성 컨텍스트의 변경 사항을 먼저 반영해야 함
```

**이유:**
- JPQL은 SQL로 변환되어 실행
- 영속성 컨텍스트의 변경 사항이 반영되지 않으면 데이터 불일치 발생 가능

#### **3. 수동 플러시**

```java
em.persist(member);
em.flush();  // 수동으로 플러시 실행
// 이 시점에 INSERT 쿼리 실행
```

**사용 시나리오:**
- DB 트리거 실행이 필요한 경우
- 쿼리 실행 순서를 제어해야 하는 경우

### **플러시 모드 설정**

```java
// 플러시 모드 설정
em.setFlushMode(FlushModeType.COMMIT);  // 커밋 시에만 플러시
em.setFlushMode(FlushModeType.AUTO);     // 기본값 (JPQL 실행 시 플러시)
```

---

## 7. 엔티티 생명주기 (Entity Lifecycle)

### **엔티티의 4가지 상태**

엔티티는 JPA에서 관리되는 동안 다음과 같은 4가지 상태를 가집니다.

| 상태 | 설명 | 특징 |
|------|------|------|
| **비영속 (New / Transient)** | 엔티티 객체가 생성만 된 상태 | JPA와 무관한 상태 |
| **영속 (Managed)** | 영속성 컨텍스트에 저장된 상태 | JPA가 관리하는 상태 |
| **준영속 (Detached)** | 영속성 컨텍스트에서 분리된 상태 | JPA가 더 이상 관리하지 않음 |
| **삭제 (Removed)** | 삭제 예정 상태 | `remove()` 호출 후 |

### **상태 전이**

```
비영속 (New)
    ↓ persist()
영속 (Managed)
    ↓ detach() / clear()
준영속 (Detached)
    ↓ merge()
영속 (Managed)
    ↓ remove()
삭제 (Removed)
```

### **1. 비영속 (New / Transient)**

**정의:** JPA와 전혀 관계 없는 순수한 자바 객체

```java
Member member = new Member();  // 비영속 상태
member.setId("member1");
member.setName("홍길동");
```

**특징:**
- 영속성 컨텍스트에 저장되지 않음
- 변경 감지 안 됨
- 1차 캐시에 없음

### **2. 영속 (Managed)**

**정의:** 영속성 컨텍스트에 저장된 상태

```java
em.persist(member);  // 비영속 → 영속
```

**특징:**
- 1차 캐시에 저장됨
- 변경 감지 대상
- 트랜잭션 커밋 시 DB에 반영

### **3. 준영속 (Detached)**

**정의:** 영속성 컨텍스트가 관리하던 엔티티를 분리한 상태

```java
// 방법 1: detach()
em.detach(member);  // 영속 → 준영속

// 방법 2: clear()
em.clear();  // 모든 엔티티를 준영속 상태로

// 방법 3: close()
em.close();  // 영속성 컨텍스트 종료
```

**특징:**
- 변경 감지 안 됨
- 1차 캐시에서 제거됨
- `merge()`로 다시 영속 상태로 만들 수 있음

### **4. 삭제 (Removed)**

**정의:** 삭제 예정 상태

```java
em.remove(member);  // 영속 → 삭제
```

**특징:**
- 트랜잭션 커밋 시 DELETE 쿼리 실행
- 영속성 컨텍스트에서 제거됨

### **준영속 상태의 활용**

#### **merge()로 다시 영속 상태로 만들기**

```java
// 준영속 엔티티
Member detachedMember = ...;  // 준영속 상태
detachedMember.setName("변경된 이름");

// merge()로 다시 영속 상태로
Member mergedMember = em.merge(detachedMember);
// mergedMember는 영속 상태 (detachedMember와는 다른 인스턴스일 수 있음)
```

**merge() 동작 원리:**
1. 준영속 엔티티의 식별자 값으로 1차 캐시에서 조회
2. 1차 캐시에 없으면 DB에서 조회하여 영속 상태로 만듦
3. 준영속 엔티티의 값을 영속 엔티티에 복사
4. 영속 엔티티를 반환

---

## 8. 영속성 컨텍스트의 전체 동작 흐름

### **전체 흐름 예시**

```java
@Transactional
public void saveAndUpdateMember() {
    // 1. 비영속 상태
    Member member = new Member();
    member.setId("member1");
    member.setName("홍길동");
    
    // 2. 영속 상태로 전환
    em.persist(member);
    // - 1차 캐시에 저장
    // - 스냅샷 저장: {id: "member1", name: "홍길동"}
    // - 쓰기 지연 저장소에 INSERT 쿼리 저장
    
    // 3. 조회 (1차 캐시에서 조회)
    Member found = em.find(Member.class, "member1");
    // DB 접근 없이 1차 캐시에서 반환
    
    // 4. 변경 감지
    found.setName("김철수");
    // 스냅샷과 비교하여 변경 사항 감지
    
    // 5. 트랜잭션 커밋
    em.getTransaction().commit();
    // - 플러시 발생
    //   - 쓰기 지연 저장소의 INSERT 쿼리 실행
    //   - 변경 감지로 UPDATE 쿼리 생성 및 실행
    // - 커밋 (트랜잭션 완료)
}
```

### **동작 순서 정리**

```
1. persist() / find()
   ↓
2. 1차 캐시에 저장 / 조회
   ↓
3. 스냅샷 저장 (persist 시)
   ↓
4. 쓰기 지연 저장소에 쿼리 저장
   ↓
5. 필드 값 변경 (변경 감지)
   ↓
6. 트랜잭션 커밋
   ↓
7. 플러시 발생
   ↓
8. DB에 쿼리 실행
   ↓
9. 커밋 완료
```

---

## 9. Spring Data JPA와 영속성 컨텍스트

### **Spring Data JPA에서의 영속성 컨텍스트**

Spring Data JPA를 사용하면 영속성 컨텍스트가 자동으로 관리됩니다.

#### **트랜잭션 범위의 영속성 컨텍스트**

```java
@Service
@Transactional
public class MemberService {
    
    @Autowired
    private MemberRepository memberRepository;
    
    public void updateMember(String id, String name) {
        Member member = memberRepository.findById(id).orElseThrow();
        member.setName(name);  // 변경 감지로 자동 UPDATE
        // @Transactional이 끝나면 자동으로 플러시 및 커밋
    }
}
```

**특징:**
- `@Transactional` 메서드가 시작되면 영속성 컨텍스트 생성
- 메서드가 끝나면 플러시 및 커밋
- 예외 발생 시 롤백

#### **OSIV (Open Session In View)**

```yaml
# application.yml
spring:
  jpa:
    open-in-view: true  # 기본값
```

**OSIV가 true일 때:**
- HTTP 요청이 시작되면 영속성 컨텍스트 생성
- HTTP 응답이 끝날 때까지 영속성 컨텍스트 유지
- 뷰 렌더링 중에도 지연 로딩 가능

**주의사항:**
- OSIV를 false로 설정하는 것을 권장 (성능 이슈 가능)
- 서비스 계층에서 모든 연관관계를 로딩하고 DTO로 변환

---

## 요약

- **영속성 컨텍스트**는 JPA가 엔티티를 관리하는 1차 캐시 공간으로, 엔티티를 메모리에 저장하고 상태 변화를 추적합니다.
- **1차 캐시**는 같은 트랜잭션 내에서 동일한 엔티티를 재사용하여 성능을 최적화하고 엔티티 동일성을 보장합니다.
- **변경 감지(Dirty Checking)** 는 엔티티 필드 값만 변경해도 자동으로 UPDATE 쿼리를 생성하여 개발 편의성을 제공합니다.
- **쓰기 지연(Write-Behind)** 은 쿼리를 모아서 트랜잭션 커밋 시점에 한꺼번에 실행하여 성능을 최적화합니다.
- **플러시(Flush)** 는 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영하는 작업으로, 트랜잭션 커밋 시나 JPQL 실행 시 자동으로 발생합니다.
- **엔티티 생명주기**는 비영속, 영속, 준영속, 삭제 4가지 상태로 구분되며, 각 상태에 따라 JPA의 동작이 달라집니다.
- **Spring Data JPA**에서는 `@Transactional`을 통해 영속성 컨텍스트가 자동으로 관리되며, 트랜잭션 범위 내에서 영속성 컨텍스트가 유지됩니다.

---

## 참고 자료

- [JPA Specification](https://jcp.org/en/jsr/detail?id=338)
- [Hibernate Documentation - Persistence Context](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#pc)
- [Spring Data JPA Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
- [Baeldung - JPA Entity Lifecycle](https://www.baeldung.com/jpa-entity-lifecycle-events)
