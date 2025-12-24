#spring #jpa #spring-data-jpa #repository #query-method #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[JPA 엔티티 매핑|JPA 엔티티 매핑]] [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]

> [!note] 이어서 읽기
> Spring Data JPA를 이해하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**Spring Data JPA**는 JPA를 더욱 편리하게 사용할 수 있도록 도와주는 스프링의 모듈이다. Repository 인터페이스만 정의하면 CRUD, 페이징, 정렬 등의 기능을 자동으로 제공하여 개발자가 비즈니스 로직에 집중할 수 있게 해준다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- EntityManager를 직접 사용하면 `persist`, `find`, `remove`, `merge` 등의 메서드를 매번 작성해야 하고, JPQL을 직접 작성해야 하며, 페이징과 정렬을 직접 처리해야 합니다. CRUD 코드가 반복되고 복잡하여 개발 생산성이 떨어집니다.

- Spring Data JPA는 Repository 인터페이스만 정의하면 기본 CRUD 메서드를 자동으로 제공하고, 메서드 이름만으로 쿼리를 자동 생성하며, 페이징과 정렬을 간편하게 처리할 수 있습니다. 이를 통해 반복적인 코드를 제거하고 개발자가 비즈니스 로직에 집중할 수 있습니다.

- JpaRepository의 상속 효과, Query Method의 사용법, 정렬과 페이징 처리, 그리고 다양한 반환 타입을 이해해야 합니다.

---

## 1. 기존 JPA 코드의 한계

### **EntityManager 직접 사용의 문제점**

기존 JPA 방식은 EntityManager를 직접 사용하여 CRUD 작업을 수행해야 합니다.

**문제점:**
- `persist`, `find`, `remove`, `merge` 등의 메서드를 매번 작성해야 함
- JPQL을 직접 작성해야 함
- 페이징, 정렬, 조건 쿼리 등을 직접 처리해야 함
- 반복적인 코드가 많아짐

**예시:**

```java
@Repository
public class BoardRepository {
    @PersistenceContext 
    private EntityManager em;
    
    public Board save(Board board) {
        em.persist(board);
        return board;
    }
    
    public Optional<Board> findById(Long boardNo) {
        return Optional.ofNullable(em.find(Board.class, boardNo));
    }
    
    public Page<Board> findByStatus(CommonEnums.Status status, Pageable pageable) {
        // 복잡한 페이징 쿼리 직접 작성
        String jpql = "SELECT b FROM Board b WHERE b.status = :status";
        TypedQuery<Board> query = em.createQuery(jpql, Board.class);
        query.setParameter("status", status);
        
        // 페이징 처리
        query.setFirstResult((int) pageable.getOffset());
        query.setMaxResults(pageable.getPageSize());
        
        // 전체 개수 조회
        String countJpql = "SELECT COUNT(b) FROM Board b WHERE b.status = :status";
        Long total = em.createQuery(countJpql, Long.class)
            .setParameter("status", status)
            .getSingleResult();
        
        List<Board> content = query.getResultList();
        return new PageImpl<>(content, pageable, total);
    }
    
    public void delete(Board board) {
        em.remove(board);
    }
}
```

**문제점:**
- CRUD 메서드를 매번 직접 구현해야 함
- 페이징 처리가 복잡함
- 반복적인 코드가 많음
- 비즈니스 로직에 집중하기 어려움

---

## 2. Spring Data JPA 소개

### **Spring Data JPA란?**

**Spring Data JPA**는 Repository 인터페이스만 정의하면 다양한 기능을 자동으로 제공하는 스프링의 모듈입니다.

**핵심 특징:**
- `EntityManager` 없이도 CRUD, 페이징, 정렬, Auditing 등의 기능을 편리하게 사용 가능
- **메서드 이름을 규칙에 맞게 정의**하면, 복잡한 쿼리도 직접 작성 없이 자동 처리
- 구현체 없이 인터페이스만 선언해도 작동

### **기본 Repository 설정**

`JpaRepository<T, ID>`를 상속받는 인터페이스를 정의하면, Spring Data JPA는 이 인터페이스를 기반으로 다양한 기능을 자동으로 제공합니다.

```java
public interface BoardRepository extends JpaRepository<Board, Long> {
    Page<Board> findByStatus(CommonEnums.Status status, Pageable pageable);
}
```

**핵심:**
- 단순히 인터페이스만 선언해도 구현체 없이 작동
- 개발자는 **비즈니스 로직에 집중**할 수 있음

---

## 3. JpaRepository 상속의 효과

### **1. 기본 CRUD 메서드 자동 제공**

`JpaRepository`를 상속하면 다음 메서드들을 즉시 사용할 수 있습니다:

- `save(entity)`: 새 엔티티 저장 또는 병합(업데이트)
- `findById(id)`: ID 기반 단건 조회
- `findAll()`: 전체 엔티티 조회
- `delete(entity)`: 엔티티 삭제
- `count()`: 총 개수 반환
- `existsById(id)`: 존재 여부 확인

**예시:**

```java
@Autowired
private BoardRepository boardRepository;

// 저장
Board board = new Board();
boardRepository.save(board);

// 조회
Optional<Board> board = boardRepository.findById(1L);

// 전체 조회
List<Board> boards = boardRepository.findAll();

// 삭제
boardRepository.delete(board);

// 개수 조회
long count = boardRepository.count();

// 존재 여부 확인
boolean exists = boardRepository.existsById(1L);
```

> [!tip] 핵심 포인트
> 직접 EntityManager를 사용할 필요 없이 위 기능들을 즉시 사용 가능합니다.

### **2. 트랜잭션 처리 자동 적용**

- 기본적으로 조회 메서드는 `@Transactional(readOnly = true)`가 적용됨
- 변경 작업은 `@Transactional`이 자동 설정되어 있음
- 서비스 계층에서 트랜잭션 명시 없이도 안전하게 작업 가능

**주의사항:**
- 비즈니스 로직이 복잡한 경우(여러 개의 저장, 수정, 조회 등이 하나의 작업으로 처리되어야 할 때)에는 서비스 계층에 명시적으로 `@Transactional` 사용 권장

### **3. 구현체 자동 생성 및 빈 등록**

- `@Repository` 어노테이션 없이도 자동으로 Spring 컨테이너에 등록됨
- 내부적으로는 `SimpleJpaRepository`라는 구현체가 동적으로 생성되어 주입됨

```java
@Service
public class BoardService {
    @Autowired
    private BoardRepository boardRepository;  // 자동으로 주입됨
    
    public Board findBoard(Long id) {
        return boardRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("게시글을 찾을 수 없습니다."));
    }
}
```

### **4. 예외 변환 자동 적용**

- JPA의 `PersistenceException`을 스프링의 `DataAccessException` 계열로 자동 변환
- JPA, JDBC, MyBatis 등 다양한 데이터 접근 기술을 써도 **모든 예외가 공통된 구조 (`DataAccessException`)로 통일됨**
- `@Repository`가 적용된 클래스에 AOP 프록시가 생성되어 자동으로 처리

**장점:**
- 예외 처리를 일관되게 할 수 있음
- `@Transactional`과 함께 사용할 때 트랜잭션 롤백도 안전하게 작동

---

## 4. Query Method (메서드 이름으로 쿼리 생성)

### **Query Method란?**

**Query Method**는 Spring Data JPA가 제공하는 기능으로, **메서드 이름만으로도 자동으로 쿼리를 생성**하여 원하는 데이터를 조회할 수 있는 기능입니다.

**핵심:**
- 메서드 이름의 규칙을 Spring Data JPA가 파싱해서 쿼리를 자동 생성
- 정렬, 페이징도 메서드 시그니처에 포함 가능

### **기본 문법**

메서드 이름을 규칙에 맞게 작성하면 자동으로 쿼리가 생성됩니다.

**예시:**

```java
public interface BoardRepository extends JpaRepository<Board, Long> {
    // 단순 조회
    List<Board> findByBoardTitle(String title);
    
    // 문자열 포함 검색
    List<Board> findByBoardTitleContaining(String keyword);
    
    // 여러 조건 (AND)
    List<Board> findByBoardTitleContainingAndStatus(String keyword, Status status);
    
    // 여러 조건 (OR)
    List<Board> findByBoardTitleContainingOrContentContaining(String title, String content);
}
```

### **사용 가능한 키워드**

| 키워드 | 설명 | 예시 |
|--------|------|------|
| `And`, `Or` | AND / OR 연산 | `findByTitleAndStatus` |
| `Between` | 범위 조건 | `findByCreatedAtBetween` |
| `LessThan`, `GreaterThan` | 비교 연산 | `findByPriceLessThan` |
| `Like`, `Containing` | 문자열 검색 | `findByTitleContaining` |
| `StartingWith`, `EndingWith` | 문자열 시작/끝 | `findByTitleStartingWith` |
| `IsNull`, `IsNotNull` | NULL 조건 | `findByDeletedAtIsNull` |
| `In`, `NotIn` | 컬렉션 포함 여부 | `findByStatusIn` |
| `OrderBy` | 정렬 | `findByStatusOrderByCreatedAtDesc` |
| `Top`, `First` | 제한 조회 | `findTop3ByStatus` |

**복잡한 예시:**

```java
public interface BoardRepository extends JpaRepository<Board, Long> {
    // status가 일치하는 데이터를 createdAt 기준 내림차순 정렬해서 상위 3개만 조회
    List<Board> findTop3ByStatusOrderByCreatedAtDesc(Status status);
    
    // boardTitle에 title 키워드가 포함되거나
    // content에 content 키워드가 포함된 게시글을 모두 조회
    List<Board> findByBoardTitleContainingOrContentContaining(String title, String content);
    
    // 여러 조건 조합
    List<Board> findByStatusAndCreatedAtBetween(
        Status status, 
        LocalDateTime start, 
        LocalDateTime end
    );
}
```

---

## 5. 정렬과 페이징

### **정렬(Sort)**

`Sort`는 결과를 정렬할 수 있게 도와주는 객체입니다.

**기본 사용법:**

```java
public interface BoardRepository extends JpaRepository<Board, Long> {
    // Sort 파라미터 추가
    List<Board> findByStatus(Status status, Sort sort);
}
```

**사용 예시:**

```java
// 단일 필드 정렬
Sort sort = Sort.by("boardNo").descending();
List<Board> boards = boardRepository.findByStatus(Status.Y, sort);

// 여러 필드 정렬
Sort sort = Sort.by("status").ascending()
    .and(Sort.by("createdAt").descending());
List<Board> boards = boardRepository.findByStatus(Status.Y, sort);
```

### **페이징(Pageable)**

`Pageable`은 페이지 요청 정보를 담고 있는 인터페이스입니다.

**특징:**
- 내부적으로 `limit` + `offset` SQL 쿼리가 생성되어 페이지 단위로 데이터를 조회
- 반환 타입은 `Page<T>`로, 실제 콘텐츠뿐 아니라 페이지 관련 메타데이터도 함께 제공

**기본 사용법:**

```java
public interface BoardRepository extends JpaRepository<Board, Long> {
    Page<Board> findByStatus(Status status, Pageable pageable);
}
```

**사용 예시:**

```java
// PageRequest.of(page, size, sort)
PageRequest pageable = PageRequest.of(0, 10, Sort.by("boardNo").descending());
Page<Board> page = boardRepository.findByStatus(Status.Y, pageable);

// 페이지 정보 활용
List<Board> content = page.getContent();        // 실제 데이터
long totalElements = page.getTotalElements();   // 전체 개수
int totalPages = page.getTotalPages();          // 전체 페이지 수
int currentPage = page.getNumber();             // 현재 페이지 번호
boolean hasNext = page.hasNext();               // 다음 페이지 존재 여부
```

---

## 6. 반환 타입

Spring Data JPA는 다양한 반환 타입을 지원합니다.

### **1. 단건 조회 (Optional)**

```java
Optional<Board> findByBoardTitle(String title);
```

**특징:**
- 결과가 1건일 것으로 예상될 때 사용
- 결과가 없으면 `Optional.empty()` 반환
- 결과가 있으면 해당 엔티티 반환
- 결과가 2건 이상이면 `IncorrectResultSizeDataAccessException` 예외 발생

**사용 예시:**

```java
Optional<Board> board = boardRepository.findByBoardTitle("제목");
if (board.isPresent()) {
    Board found = board.get();
    // 처리
}
```

### **2. 여러 건 조회 (List)**

```java
List<Board> findByStatus(Status status);
```

**특징:**
- 조건에 맞는 **모든 결과를 리스트로 반환**
- 결과가 없으면 **null이 아니라 빈 리스트(`[]`) 반환**
- `LIMIT`이 없는 전체 검색

**사용 예시:**

```java
List<Board> boards = boardRepository.findByStatus(Status.Y);
// 결과가 없어도 빈 리스트 반환 (null 아님)
```

### **3. 페이징 처리 및 전체 정보 (Page)**

```java
Page<Board> findByStatus(Status status, Pageable pageable);
```

**특징:**
- `Page`는 다음 정보를 함께 포함:
  - 실제 데이터 (`getContent()`)
  - 전체 개수 (`getTotalElements()`)
  - 전체 페이지 수 (`getTotalPages()`)
  - 현재 페이지 번호 (`getNumber()`)
- 내부적으로 **count 쿼리**를 추가로 실행함

**사용 예시:**

```java
PageRequest pageable = PageRequest.of(0, 10);
Page<Board> page = boardRepository.findByStatus(Status.Y, pageable);

List<Board> content = page.getContent();        // 현재 페이지 데이터
long total = page.getTotalElements();           // 전체 개수
int totalPages = page.getTotalPages();          // 전체 페이지 수
```

### **4. 페이징 처리 및 다음 페이지 여부만 판단 (Slice)**

```java
Slice<Board> findByStatus(Status status, Pageable pageable);
```

**특징:**
- `Page`와 달리 **전체 개수(count) 쿼리를 실행하지 않음**
- 대신 **현재 페이지**와 **다음 페이지 1개**까지 불러와서 `hasNext()`로 **다음 페이지 존재 여부**만 판단
- 더 빠르고 가벼움

**사용 예시:**

```java
PageRequest pageable = PageRequest.of(0, 10);
Slice<Board> slice = boardRepository.findByStatus(Status.Y, pageable);

List<Board> content = slice.getContent();  // 현재 페이지 데이터
boolean hasNext = slice.hasNext();         // 다음 페이지 존재 여부
// count 쿼리 실행 안 함 → 더 빠름
```

### **반환 타입 비교**

| 반환 타입 | 특징 | count 쿼리 | 사용 시나리오 |
|----------|------|-----------|--------------|
| `Optional<T>` | 단건 조회 | ❌ | 정확히 1건만 예상될 때 |
| `List<T>` | 여러 건 조회 | ❌ | 전체 결과가 필요할 때 |
| `Page<T>` | 페이징 + 전체 정보 | ✅ | 페이지네이션 UI (전체 페이지 수 필요) |
| `Slice<T>` | 페이징 + 다음 페이지 여부 | ❌ | 무한 스크롤 (다음 페이지만 확인) |

---

## 7. Spring Data JPA의 장점 정리

### **1. 반복 코드 제거**

**기존 방식:**
```java
// 매번 CRUD 메서드를 직접 구현
public Board save(Board board) {
    em.persist(board);
    return board;
}
```

**Spring Data JPA:**
```java
// 인터페이스만 정의
public interface BoardRepository extends JpaRepository<Board, Long> {
}
// save 메서드 자동 제공
```

### **2. 쿼리 작성 간소화**

**기존 방식:**
```java
// JPQL 직접 작성
String jpql = "SELECT b FROM Board b WHERE b.status = :status";
TypedQuery<Board> query = em.createQuery(jpql, Board.class);
query.setParameter("status", status);
List<Board> boards = query.getResultList();
```

**Spring Data JPA:**
```java
// 메서드 이름만으로 쿼리 자동 생성
List<Board> findByStatus(Status status);
```

### **3. 페이징 처리 간소화**

**기존 방식:**
```java
// 복잡한 페이징 처리 직접 구현
query.setFirstResult((int) pageable.getOffset());
query.setMaxResults(pageable.getPageSize());
// count 쿼리도 별도 작성
```

**Spring Data JPA:**
```java
// Pageable만 파라미터로 전달
Page<Board> findByStatus(Status status, Pageable pageable);
```

### **4. 비즈니스 로직에 집중**

- 반복적인 CRUD 코드 작성 불필요
- 복잡한 쿼리 작성 최소화
- 개발자는 비즈니스 로직에만 집중 가능

---

## 8. @Query 어노테이션으로 쿼리 직접 작성

### **@Query란?**

`@Query`는 **Spring Data JPA 리포지토리 인터페이스에서 쿼리를 직접 정의할 수 있도록 해주는 어노테이션**입니다.

**특징:**
- JPA는 보통 메서드 이름으로 쿼리를 유추하지만, 복잡한 조건이나 커스텀한 조회가 필요한 경우 `@Query`를 사용
- 보다 **명확하고 유연하게 JPQL 쿼리 작성**이 가능
- 복잡한 조건도 유연하게 작성 가능하며 쿼리를 코드와 함께 관리 가능
- 문자열로 쿼리를 작성하므로 오타나 오류에 주의해야 함

### **기본 사용법**

```java
@Query("SELECT m FROM Member m WHERE m.username = :username")
List<Member> findByUsername(@Param("username") String username);
```

### **다양한 쿼리 예시**

#### **1. 단순 조건 조회**

```java
@Query("SELECT m FROM Member m WHERE m.age >= :age")
List<Member> findByAgeGreaterThan(@Param("age") int age);
```

#### **2. 여러 조건 조합**

```java
@Query("SELECT m FROM Member m WHERE m.username = :username AND m.age = :age")
Member findUser(@Param("username") String username, @Param("age") int age);
```

#### **3. 특정 필드만 조회 (값 조회)**

```java
@Query("SELECT m.username FROM Member m")
List<String> findUsernames(); // username 목록만 조회
```

#### **4. DTO로 직접 조회 (new 키워드 사용)**

```java
@Query("SELECT new com.example.dto.MemberDto(m.id, m.username, t.name) " +
       "FROM Member m JOIN m.team t")
List<MemberDto> findMemberDto();
```

**주의사항:**
- DTO 생성자와 필드 순서가 일치해야 함
- 생성자 방식만 가능함

#### **5. Collection 바인딩 (IN 절)**

```java
@Query("SELECT m FROM Member m WHERE m.username IN :names")
List<Member> findByUsernames(@Param("names") List<String> names);
```

---

## 9. @Query와 페이징/정렬

### **페이징 적용**

스프링 데이터 JPA는 `@Query`를 사용할 때도 **페이징(Pageable)**과 **정렬(Sort)** 기능을 함께 사용할 수 있습니다.

```java
@Query("SELECT m FROM Member m WHERE m.age >= :age")
Page<Member> findByAge(@Param("age") int age, Pageable pageable);
```

**특징:**
- `Pageable`을 파라미터로 넘기면 스프링이 자동으로 `LIMIT`, `OFFSET` 쿼리를 만들어줌
- 리턴 타입이 `Page<T>`이면 자동으로 count 쿼리도 실행됨

**사용 예시:**

```java
PageRequest pageRequest = PageRequest.of(0, 5, Sort.by(Sort.Direction.DESC, "username"));
Page<Member> page = memberRepository.findByAge(20, pageRequest);
// "username 기준으로 내림차순 정렬하며 0페이지부터 5개씩 가져오기"
```

### **정렬만 적용**

```java
@Query("SELECT m FROM Member m WHERE m.age >= :age")
List<Member> findByAge(@Param("age") int age, Sort sort);
```

**특징:**
- 이 방식은 정렬만 적용하고, 페이징은 하지 않음
- 반환 타입이 `List<T>`

### **count 쿼리 분리 (성능 최적화)**

복잡한 조인 쿼리는 count 쿼리를 별도로 작성해서 최적화할 수 있습니다.

```java
@Query(
  value = "SELECT m FROM Member m LEFT JOIN m.team t",
  countQuery = "SELECT count(m) FROM Member m"
)
Page<Member> findAllWithTeam(Pageable pageable);
```

**특징:**
- `value`: 실제 조회 쿼리
- `countQuery`: 총 데이터 수 계산용 쿼리
- 불필요한 JOIN을 줄일 수 있어 **페이징 성능**이 개선됨

---

## 10. 고급 기능

### **10.1 벌크 연산 (@Modifying)**

JPA는 보통 `select` 기반의 **조회 쿼리**를 주로 사용하지만, 대량의 데이터를 수정하거나 삭제할 때는 벌크 연산(Bulk Operation)이 필요합니다.

**특징:**
- **JPQL update/delete 문**을 사용해야 함
- **Spring Data JPA에서는 `@Modifying` 어노테이션이 필수**

```java
@Modifying
@Query("UPDATE Member m SET m.age = m.age + 1 WHERE m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```

**주의사항:**
- `@Modifying`이 없으면 실행 시 예외 발생:
  ```
  QueryExecutionRequestException: Not supported for DML operations
  ```
- 벌크 연산은 **영속성 컨텍스트를 무시하고 직접 DB를 조작**합니다
- 엔티티 상태와 DB 내용이 불일치할 수 있음

**영속성 컨텍스트 초기화:**

```java
@Modifying(clearAutomatically = true)
@Query("UPDATE Member m SET m.age = m.age + 1 WHERE m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```

- `clearAutomatically = true`: 연산 후 JPA가 **자동으로 영속성 컨텍스트를 초기화**

### **10.2 EntityGraph (페치 조인 간편 사용)**

JPA는 기본적으로 연관 엔티티를 **지연 로딩(Lazy)** 합니다.

**문제점:**
- `member.getTeam().getName()`을 호출할 때마다 SQL이 실행됨 (N+1 문제)

**해결 방법:**
- **fetch join**을 사용해야 하는데, 스프링 데이터 JPA에서는 **`@EntityGraph` 어노테이션**으로 더 간단하게 처리 가능

```java
@EntityGraph(attributePaths = {"team"})
List<Member> findAll(); // JPQL 없이 team도 함께 조회
```

**특징:**
- `attributePaths`: 조인해서 함께 조회할 연관 엔티티 지정
- 내부적으로 LEFT OUTER JOIN을 사용함

### **10.3 JPA Hint (@QueryHints)**

JPA Hint는 **SQL 쿼리에 전달되는 힌트가 아니라**, JPA 구현체(예: Hibernate)에게 **쿼리 실행 방식에 영향을 주기 위한 힌트**입니다.

**배경:**
- JPA는 기본적으로 엔티티 조회 후 변경 시 자동으로 DB에 반영하는 "변경 감지"를 수행
- 단순히 조회만 하고 수정할 계획이 없다면 "변경 감지"가 불필요
- 비용 절감을 위해 "읽기 전용"이라는 힌트를 주면 좋음

```java
@QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value = "true"))
Member findReadOnlyByUsername(String username);
```

**특징:**
- `"readOnly" = true` 설정은 **Hibernate에게 이 엔티티는 절대 수정되지 않는다는 약속**을 전달
- JPA는 해당 엔티티를 캐싱하지 않고 로딩
- `em.flush()`가 호출되더라도 **update SQL이 절대 발생하지 않음**

---

## 11. 네이티브 쿼리

### **네이티브 쿼리란?**

네이티브 쿼리는 **순수 SQL을 직접 실행**하는 쿼리입니다.

### **기본 사용법**

`@Query`에 `nativeQuery = true` 옵션을 사용합니다.

```java
@Query(value = "SELECT * FROM member WHERE username = :username", nativeQuery = true)
Member findByNativeQuery(@Param("username") String username);
```

**특징:**
- `value`: 순수 SQL 작성
- `nativeQuery = true`: JPQL이 아닌 네이티브 SQL임을 명시

### **DTO 매핑 (Interface 기반 Projections)**

네이티브 쿼리는 `Entity`뿐 아니라 `DTO`, 일부 컬럼만 조회하는 것도 가능하지만 권장하지 않습니다.

**이유:**
- 네이티브 쿼리는 **SQL 직접 실행**이라, JPA가 자동으로 DTO에 값을 채워주지 못함

**Interface 기반 Projections 사용:**

```java
public interface MemberProjection {
    String getUsername();
    String getTeamName();
}
```

```java
@Query(value = """
    SELECT m.username as username, t.name as teamName
    FROM member m
    JOIN team t ON m.team_id = t.id
    WHERE t.name = :teamName
    """, nativeQuery = true)
List<MemberProjection> findByTeamName(@Param("teamName") String teamName);
```

**주의사항:**
- interface를 구현해야 하고 직관적이지 않으므로 권장하지 않음
- **복잡한 네이티브 SQL은 JPA 대신 `JdbcTemplate` or `MyBatis` 사용 권장**

**이유:**
- 네이티브 SQL은 JPA의 ORM 매핑 기능을 활용하지 않음
- 복잡한 조인, 조건, 계산 컬럼 등을 사용할 때 JPA로 DTO 매핑은 매우 번거로움
- `MyBatis`는 SQL 중심 + 매핑 XML 기반이라 DTO 매핑에 최적화

### **네이티브 쿼리와 페이징**

네이티브 쿼리에서도 **페이징(Pageable)**을 지원합니다. 단, **countQuery**를 따로 명시해줘야 합니다.

```java
@Query(
  value = "SELECT * FROM member WHERE age >= :age",
  countQuery = "SELECT count(*) FROM member WHERE age >= :age",
  nativeQuery = true
)
Page<Member> findByAgeNative(@Param("age") int age, Pageable pageable);
```

---

## 요약

- **Spring Data JPA**는 Repository 인터페이스만 정의하면 CRUD, 페이징, 정렬 등의 기능을 자동으로 제공합니다.
- **JpaRepository 상속 효과**: 기본 CRUD 메서드 자동 제공, 트랜잭션 처리 자동 적용, 구현체 자동 생성, 예외 변환 자동 적용
- **Query Method**: 메서드 이름만으로 쿼리를 자동 생성하는 기능
- **@Query**: 복잡한 쿼리를 직접 작성할 수 있는 어노테이션
- **정렬과 페이징**: `Sort`와 `Pageable`을 사용하여 간편하게 처리
- **반환 타입**: `Optional`, `List`, `Page`, `Slice` 등 다양한 반환 타입 지원
- **벌크 연산**: `@Modifying`을 사용하여 대량 수정/삭제 처리
- **EntityGraph**: `@EntityGraph`로 페치 조인을 간편하게 사용
- **JPA Hint**: `@QueryHints`로 읽기 전용 등 최적화 힌트 제공
- **네이티브 쿼리**: `nativeQuery = true`로 순수 SQL 실행 가능 (복잡한 경우 MyBatis 권장)
- **장점**: 반복 코드 제거, 쿼리 작성 간소화, 페이징 처리 간소화, 비즈니스 로직에 집중 가능

---

## 참고 자료

- [Spring Data JPA 공식 문서](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html)
- [Spring Data JPA - Query Methods](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html)
- [JpaRepository API 문서](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html)
