# spring-boot-starter-data-jpa와 영속성 관리

#spring #jpa #hibernate #spring-data-jpa #persistence #entitymanager #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[JPA 엔티티 매핑|JPA 엔티티 매핑]]

> [!note] 이어서 읽기
> 영속성 관리를 이해하려면 **[[JPA 엔티티 매핑|JPA 엔티티 매핑]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

spring-boot-starter-data-jpa의 구성 요소와 역할을 이해하고, JPA의 영속성 관리 메커니즘인 영속성 컨텍스트와 엔티티 매니저의 동작 원리를 학습한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- spring-boot-starter-data-jpa는 JPA 환경을 자동으로 구성하여 복잡한 설정 없이 데이터 액세스 계층을 빠르게 구축할 수 있게 합니다.

- 영속성 관리는 JPA의 핵심 메커니즘으로, 객체의 상태 변화를 자동으로 추적하고 데이터베이스와 동기화하여 개발자가 SQL을 직접 작성하지 않아도 됩니다.

- 영속성 컨텍스트의 1차 캐시, 변경 감지, 쓰기 지연 등의 기능을 통해 성능 최적화와 데이터 일관성을 자동으로 보장합니다.

---

### **Core Concept (핵심 개념 정리)**

**spring-boot-starter-data-jpa**는 Spring Boot에서 JPA 환경을 쉽게 구성할 수 있도록 도와주는 스타터 라이브러리로, Hibernate, Spring Data JPA, HikariCP, 트랜잭션 관리 등을 통합 제공합니다.

**영속성 관리**는 애플리케이션에서 객체(엔티티)의 상태 변화를 추적하고 데이터베이스와 동기화하는 과정으로, JPA는 영속성 컨텍스트를 통해 이를 자동화합니다.

**영속성 컨텍스트**는 엔티티의 변경 정보를 메모리에 저장하는 환경으로, JPA가 엔티티를 관리하는 1차 캐시 공간입니다.

**엔티티 매니저(EntityManager)**는 JPA에서 엔티티를 관리하고 데이터베이스와의 상호작용을 담당하는 핵심 객체입니다.

> [!tip] 핵심 포인트
> 영속성 컨텍스트는 JPA의 핵심 메커니즘으로, 1차 캐시, 변경 감지, 쓰기 지연 등의 기능을 통해 성능 최적화와 개발 편의성을 동시에 제공합니다.

---

## 1. spring-boot-starter-data-jpa

- `spring-boot-starter-data-jpa`는 **Spring Boot 프로젝트에서 JPA 환경을 쉽고 빠르게 구성**할 수 있도록 도와주는 스타터 라이브러리입니다.
- JPA 설정, 트랜잭션 처리, DataSource, Hibernate(기본 JPA 구현체), Spring Data JPA 기능 등을 통합 제공하여, 개발자가 복잡한 설정 없이 데이터 액세스 계층을 구축할 수 있게 합니다.

### **왜 사용하는가?**

| 구분 | 수동 설정 (Spring + JPA) | spring-boot-starter-data-jpa 사용 시 |
| --- | --- | --- |
| 의존성 관리 | JPA, Hibernate, DataSource 등 개별 설정 | 자동으로 의존성 관리 |
| DataSource 설정 | application.properties 직접 상세 설정 | 기본 HikariCP 제공, 간단 설정 가능 |
| 트랜잭션 관리 | 직접 PlatformTransactionManager 설정 | @Transactional 어노테이션만 사용 |
| EntityManager 관리 | 수동 주입 | 자동 관리 (Spring Data JPA 활용) |
| CRUD 코드 작성 | 직접 Repository 구현 | 인터페이스만 작성하면 자동 구현 |

= 설정 최소화 + 코드 간결화 + 빠른 개발 가능

---

## 2. spring-boot-starter-data-jpa의 주요 구성 요소

### **1. Hibernate (JPA 구현체)**

- JPA는 인터페이스만 제공, Hibernate가 이를 실제로 구현하여 동작 처리
- 엔티티와 테이블 간 매핑, SQL 생성, 영속성 컨텍스트 관리
- Spring Boot 사용 시 별도 설정 없이 Hibernate가 기본 적용

### **2. Spring Data JPA**

- Repository 인터페이스만 작성해도 기본적인 CRUD 자동 구현
- 복잡한 쿼리도 메서드 이름으로 자동 생성 (Query Method)
- 페이징, 정렬, @Query 어노테이션, Native Query, 동적 쿼리 지원

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
    List<Member> findByUsername(String username);
}
```

### **3. HikariCP (Connection Pool)**

- Spring Boot 2.x 이상에서 기본 커넥션 풀로 사용
- 가볍고 빠른 성능, 최소한의 리소스로 고성능 제공

> [!note] 커넥션 풀 선택
> 필요에 따라 Tomcat JDBC, HikariCP, Vibur 등의 커넥션 풀로 교체 가능하지만, HikariCP가 권장됩니다.

### **4. Spring Transaction (트랜잭션 관리)**

- Spring Transaction 관련 의존성이 포함되어 있어 `@Transactional` 어노테이션만으로 JpaTransactionManager을 활용하여 JPA 기반 트랜잭션 처리가 가능
- 트랜잭션 전파, 롤백 정책, 트랜잭션 분리 등 다양한 고급 기능 지원

```java
@Service
@Transactional
public class MemberService {
    public void register(Member member) {
        // 트랜잭션 자동 적용
    }
}
```

### **5. DataSourceAutoConfiguration (자동 설정)**

- `application.yml`이나 `application.properties` 파일에 작성한 설정을 기반으로, DataSource, EntityManagerFactory, TransactionManager 등을 자동으로 생성 및 관리

### **spring-boot-starter-data-jpa 사용 시 기대 효과**

- JPA 환경 자동 설정 (PersistenceContext, EntityManager 자동 관리)
- 데이터베이스 접속, 트랜잭션 관리, 커넥션 풀까지 자동 구성
- Spring Data JPA의 Repository 인터페이스로 CRUD 구현 생략
- 페이징, 정렬, 동적 쿼리, @Query로 JPQL/Native Query 작성 가능
- HikariCP 커넥션 풀로 기본 성능 최적화 제공

---

## 3. 영속성 관리란?

- 애플리케이션에서 **객체(엔티티)의 상태 변화를 추적하고, 데이터베이스와 동기화**하는 과정
- 자바 객체(엔티티)를 **언제, 어떻게 데이터베이스에 저장하고, 수정하고, 삭제할지 관리하는 것**을 의미함
- JPA에서는 이를 자동화하기 위해 **영속성 컨텍스트**라는 특별한 공간을 사용
- 개발자는 객체 상태만 관리하면, JPA가 알아서 INSERT, UPDATE, DELETE 쿼리를 생성하고 실행

### **영속성 컨텍스트**

- 엔티티의 변경정보를 메모리에 저장하는 환경
- 쉽게 말해, JPA가 엔티티를 관리하는 **1차 캐시 공간**
- 여기에 저장된 엔티티는 JPA가 자동으로 상태를 추적하고, 필요한 시점에 DB와 동기화

---

## 4. 엔티티 매니저(EntityManager)

- JPA에서 **엔티티를 관리**하고, 데이터베이스와의 상호작용을 담당하는 핵심 객체
- 영속성 컨텍스트를 생성하고, 엔티티의 저장, 조회, 수정, 삭제 작업을 수행
- 트랜잭션과 밀접하게 연동되어, DB 작업을 안전하게 처리

### **주요 기능**

| 메서드 | 설명 |
| --- | --- |
| `persist()` | 엔티티를 영속 상태로 전환 (INSERT 예정) |
| `find()` | 엔티티 조회 (1차 캐시 우선) |
| `remove()` | 엔티티 삭제 (DELETE 예정) |
| `detach()` | 엔티티를 영속성 컨텍스트에서 분리 (준영속 상태로 전환) |
| `clear()` | 모든 엔티티를 준영속 상태로 전환 (영속성 컨텍스트 초기화) |
| `flush()` | 영속성 컨텍스트의 변경 내용을 DB에 반영 |

### **EntityManager 사용 예제**

```java
EntityManager em = emf.createEntityManager();
em.getTransaction().begin(); // 트랜잭션을 시작

Member member = new Member("member1");
em.persist(member);  // 영속 상태로 전환

Member foundMember = em.find(Member.class, "member1"); // 조회

foundMember.setName("Updated Name");  // Dirty Checking 발생

em.getTransaction().commit();  // 플러시 → DB 반영
```

> [!note] Spring Data JPA 사용 시
> Spring Data JPA를 사용하면 EntityManager를 직접 주입받아 사용할 수도 있지만, 대부분 Repository 인터페이스를 통해 간접적으로 사용합니다.

---

## 5. 영속성 컨텍스트의 동작 흐름

### **1. 비영속 (New / Transient)**

- 엔티티 객체가 단순히 생성만 된 상태로, JPA와는 아무런 관계가 없는 상태입니다.
- 아직 DB에 저장되지 않았고, JPA가 관리하지 않습니다.

```java
Member member = new Member(); // 비영속 상태
member.setId("member1");
member.setName("지원");
```

### **2. 영속 (Managed)**

- `em.persist()`를 호출하면, 영속성 컨텍스트가 이 엔티티를 관리하기 시작합니다.
- 이때 엔티티는 1차 캐시에 저장되고, JPA가 상태 변화를 감시합니다.
- 하지만 이 시점에 **바로 DB에 INSERT 쿼리가 실행되지는 않습니다.**
    
    ```java
    em.persist(member); // 영속 상태 전환, INSERT 쿼리는 아직 실행 안 됨
    ```
    

### **3. 트랜잭션 쓰기 지연 (Write-Behind)**

- JPA는 `persist()` 호출 시 INSERT 쿼리를 즉시 실행하지 않고, **쓰기 지연 저장소(쿼리 저장소)** 에 보관해둡니다.
- 트랜잭션 커밋 시점에 한꺼번에 DB에 반영합니다.
    
    ```java
    em.getTransaction().commit(); // 이 순간 INSERT 쿼리 실행
    ```
    

**장점**: 쿼리를 모아 한 번에 실행하므로 DB 접근 횟수를 줄여 성능 최적화

> [!tip] IDENTITY 전략 예외
> `@GeneratedValue(strategy = GenerationType.IDENTITY)`를 사용하는 경우, DB에서 키 값을 생성해야 하므로 `persist()` 호출 시 즉시 INSERT 쿼리가 실행됩니다. 이 경우 쓰기 지연이 동작하지 않습니다.

### **4. 1차 캐시 (Identity Map)**

- 영속성 컨텍스트 내부에는 1차 캐시가 있어서, 동일한 엔티티를 여러 번 조회해도 DB에 다시 접근하지 않습니다.
    
    ```java
    Member m1 = em.find(Member.class, "member1");
    Member m2 = em.find(Member.class, "member1");
    
    System.out.println(m1 == m2);  // true (동일 인스턴스)
    ```
    

**장점**: 트랜잭션 내에서는 항상 같은 인스턴스를 사용하므로 데이터 일관성이 보장되고, 메모리와 DB 사용이 최적화됩니다.

### **5. 변경 감지 (Dirty Checking)**

- 엔티티 필드 값만 변경하면, JPA가 트랜잭션 커밋 시점에 스냅샷과 비교해 자동으로 UPDATE 쿼리를 생성하고 실행합니다.
    
    ```java
    Member member = em.find(Member.class, "member1");
    member.setName("변경된 지원"); // 값만 변경, UPDATE 쿼리 직접 작성 안 함
    
    em.getTransaction().commit(); // Dirty Checking → UPDATE 쿼리 실행
    ```
    

**장점**: 객체 지향적으로 코드를 작성하면서도, DB 반영이 자동으로 처리됨

> [!tip] 변경 감지 동작 원리
> 1. 엔티티를 영속성 컨텍스트에 저장할 때 최초 상태를 스냅샷으로 저장
> 2. 트랜잭션 커밋 시점에 스냅샷과 현재 상태를 비교
> 3. 변경 사항이 있으면 UPDATE 쿼리를 생성하여 실행

### **6. 플러시 (Flush)**

- 영속성 컨텍스트에 저장된 쿼리(쓰기 지연 저장소)의 내용을 실제 데이터베이스에 반영하는 작업입니다.
- 플러시 발생 시점
    
    | 상황 | 자동 호출 여부 |
    | --- | --- |
    | 트랜잭션 커밋 시 | 자동 호출 |
    | JPQL 실행 전 | 자동 호출 |
    | `em.flush()` 수동 호출 | 직접 호출 가능 |

> [!note] 플러시와 커밋의 차이
> - **플러시(Flush)**: 영속성 컨텍스트의 변경 내용을 DB에 반영 (트랜잭션은 유지)
> - **커밋(Commit)**: 트랜잭션을 완료하고 변경 사항을 최종 확정

### **7. 지연 로딩 (Lazy Loading)**

- 연관된 엔티티를 실제로 사용할 때까지 DB 조회를 지연하는 기법
- 연관 객체는 프록시 객체로 먼저 반환되고, 실제로 값에 접근하는 순간 DB 조회 쿼리가 실행됩니다.
    
    ```java
    Member member = em.find(Member.class, "member1");
    Team team = member.getTeam();  // 프록시 객체, DB 접근 없음
    
    String teamName = team.getName();  // 이 순간 DB에서 Team 조회 쿼리 실행
    ```
    

---

## 6. 영속성 컨텍스트의 역할 정리

1. **1차 캐시**
    - 동일한 엔티티를 여러 번 조회해도 DB에 재조회하지 않고, 캐시에서 반환

2. **엔티티 동일성 보장**
    - 같은 트랜잭션 내에서 같은 엔티티 인스턴스 보장 (`a == b` → true)

3. **트랜잭션 쓰기 지연 (Write-Behind)**
    - 트랜잭션 커밋 전까지 쿼리를 모아뒀다가 한 번에 실행 (플러시)

4. **변경 감지 (Dirty Checking)**
    - 엔티티 필드 값만 바꿔도, 트랜잭션 커밋 시 자동으로 UPDATE 쿼리 생성

5. **지연 로딩 (Lazy Loading)**
    - 연관된 엔티티를 실제로 사용할 때까지 로딩을 지연 (프록시 객체 사용)

---

## 7. 엔티티 생명주기

엔티티는 JPA에서 관리되는 동안 다음과 같은 4가지 상태를 가집니다.

### **엔티티 상태**

| 상태 | 설명 | 특징 |
| --- | --- | --- |
| **비영속 (New / Transient)** | 엔티티 객체가 생성만 된 상태 | JPA와 무관한 상태, 영속성 컨텍스트에 저장되지 않음 |
| **영속 (Managed)** | 영속성 컨텍스트에 저장된 상태 | JPA가 관리하는 상태, 1차 캐시에 저장됨 |
| **준영속 (Detached)** | 영속성 컨텍스트에서 분리된 상태 | JPA가 더 이상 관리하지 않음, 변경 감지 안 됨 |
| **삭제 (Removed)** | 삭제 예정 상태 | `remove()` 호출 후, 커밋 시 DELETE 쿼리 실행 |

### **상태 전이 예제**

```java
// 1. 비영속 상태
Member member = new Member();
member.setId("member1");
member.setName("지원");

// 2. 영속 상태로 전환
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();
em.persist(member);  // 비영속 → 영속

// 3. 준영속 상태로 전환
em.detach(member);  // 영속 → 준영속
// 또는
em.clear();  // 모든 엔티티를 준영속 상태로

// 4. 삭제 상태로 전환
Member foundMember = em.find(Member.class, "member1");
em.remove(foundMember);  // 영속 → 삭제

em.getTransaction().commit();
```

### **상태별 특징**

#### **비영속 (New / Transient)**
- JPA와 전혀 관계 없는 순수한 자바 객체
- 영속성 컨텍스트나 데이터베이스와 연관이 없음

#### **영속 (Managed)**
- 영속성 컨텍스트에 의해 관리되는 상태
- 1차 캐시에 저장됨
- 변경 감지(Dirty Checking) 대상
- 트랜잭션 커밋 시 DB에 반영

#### **준영속 (Detached)**
- 영속성 컨텍스트가 관리하던 엔티티를 분리한 상태
- `detach()`, `clear()`, `close()` 호출 시 발생
- 변경 감지가 동작하지 않음
- 1차 캐시에서 제거됨
- `merge()`를 통해 다시 영속 상태로 만들 수 있음

#### **삭제 (Removed)**
- 삭제 예정 상태
- `remove()` 호출 시 발생
- 트랜잭션 커밋 시 DELETE 쿼리 실행

### **상태 전이 다이어그램**

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

### **준영속 상태의 활용**

준영속 상태의 엔티티는 다음과 같은 경우에 유용합니다:

```java
// 1. 엔티티를 영속성 컨텍스트에서 분리하여 자유롭게 사용
Member member = em.find(Member.class, "member1");
em.detach(member);  // 준영속 상태

// 이제 member 객체는 자유롭게 사용 가능 (JPA 관리에서 벗어남)
member.setName("변경");  // 변경 감지 안 됨

// 2. 준영속 엔티티를 다시 영속 상태로 만들기
Member mergedMember = em.merge(member);  // 준영속 → 영속
// mergedMember는 새로운 영속 상태의 엔티티 (member와는 다른 인스턴스일 수 있음)
```

> [!tip] merge() 동작 원리
> 1. 준영속 엔티티의 식별자 값으로 1차 캐시에서 엔티티를 조회
> 2. 1차 캐시에 없으면 DB에서 조회하여 영속 상태로 만듦
> 3. 준영속 엔티티의 값을 영속 엔티티에 복사
> 4. 영속 엔티티를 반환 (준영속 엔티티는 여전히 준영속 상태)

---

## 요약

- **spring-boot-starter-data-jpa**는 JPA 환경을 자동으로 구성하여 개발 생산성을 크게 향상시킵니다.
- **영속성 컨텍스트**는 JPA가 엔티티를 관리하는 1차 캐시 공간으로, 변경 감지, 쓰기 지연 등의 기능을 제공합니다.
- **엔티티 매니저**는 영속성 컨텍스트를 생성하고 엔티티의 CRUD 작업을 수행하는 핵심 객체입니다.
- **엔티티 생명주기**는 비영속, 영속, 준영속, 삭제 4가지 상태로 구분되며, 각 상태에 따라 JPA의 동작이 달라집니다.
- 영속성 컨텍스트의 1차 캐시, 변경 감지, 쓰기 지연 기능을 통해 성능 최적화와 개발 편의성을 동시에 제공합니다.

---

## 참고 자료

- [Spring Data JPA 공식 문서](https://spring.io/projects/spring-data-jpa)
- [Hibernate 공식 문서](https://hibernate.org/orm/documentation/)
- [JPA 공식 문서](https://jakarta.ee/specifications/persistence/)

