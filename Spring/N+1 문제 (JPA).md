# N+1 문제 (JPA)

#spring #jpa #orm #n+1 #성능최적화 #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[RESTful API (REST API)|RESTful API]]

> [!note] 이어서 읽기
> N+1 문제를 이해하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 연관관계와 지연 로딩 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

JPA에서 발생하는 N+1 문제의 원인을 이해하고, 패치 조인(Fetch Join)을 통한 해결 방법과 페이징 시 주의사항을 학습한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- N+1 문제는 JPA를 사용하는 애플리케이션에서 가장 흔히 발생하는 성능 문제 중 하나로, 하나의 요청이 예상보다 훨씬 많은 쿼리를 발생시켜 데이터베이스 부하를 증가시키고 응답 시간을 지연시킵니다.

- 특히 일대다(One-to-Many, 1:N) 연관관계에서 발생하며, 지연 로딩(Lazy Loading)과 즉시 로딩(Eager Loading) 모두에서 발생할 수 있어 개발자가 반드시 이해하고 해결해야 하는 문제입니다.

- 패치 조인(Fetch Join)을 통해 N+1 문제를 해결할 수 있지만, 페이징과 함께 사용할 경우 예상치 못한 부작용이 발생할 수 있어 주의가 필요합니다.

---

### **Core Concept (핵심 개념 정리)**

**N+1 문제**는 ORM(Object-Relational Mapping)을 사용하여 관련 데이터를 조회할 때 발생하는 비효율적인 쿼리 패턴으로, 한 번의 쿼리로 해결할 수 있는 작업을 여러 번의 쿼리로 실행하여 성능 저하를 일으키는 현상을 말합니다.

**패치 조인(Fetch Join)**은 JPQL의 `join fetch` 구문을 사용하여 연관된 데이터까지 한 번의 쿼리(JOIN)로 가져오는 방법으로, 프록시가 아닌 실제 엔티티를 한 번에 조회하므로 반복문에서 추가 쿼리가 발생하지 않습니다.

> [!tip] 핵심 포인트
> N+1 문제는 JPA의 다양한 연관 관계 중, 특히 일대다(One-to-Many, 1:N) 관계에서 중점적으로 발생하며, Eager Loading, Batch Loading, Fetch Join 등의 방법으로 해결할 수 있습니다. 다만 Fetch Join은 페이징과 함께 사용할 경우 주의가 필요합니다.

---

## 1. N+1 문제란 무엇인가?

### 정의

**N+1 문제**는 ORM(Object-Relational Mapping)을 사용하여 관련 데이터를 조회할 때 발생하는 비효율적인 쿼리 패턴으로, 한 번의 쿼리로 해결할 수 있는 작업을 여러 번의 쿼리로 실행하여 성능 저하를 일으키는 현상을 말합니다.

- 하나의 요청이 한 개의 쿼리로 처리되기를 기대했지만, N개의 추가 쿼리가 발생하여 총 **1 + N개의 쿼리**가 나가는 현상입니다.
- JPA의 다양한 연관 관계 중, 특히 **일대다(One-to-Many, 1:N) 관계**에서 중점적으로 발생합니다.

### 발생 예시 (게시판)

게시물(Post) 목록을 조회하면서 각 게시물의 댓글(Comment) 개수도 함께 표시하려는 상황을 예로 들겠습니다.

```java
// Post와 Comment는 1:N 관계
@Entity
public class Post {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;
    private String content;
    
    @OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
    private List<Comment> comments;
}

@Entity
public class Comment {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String content;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "post_id")
    private Post post;
}
```

```java
// N+1 문제 발생 코드
List<Post> posts = postRepository.findAll();  // 쿼리 1개 실행

for (Post post : posts) {
    List<Comment> comments = post.getComments();  // 각 Post마다 쿼리 실행 (N개)
    // Post가 10개면 총 1 + 10 = 11개의 쿼리 실행
}
```

**실행되는 쿼리:**
```sql
-- 1번째 쿼리: Post 목록 조회
SELECT * FROM post;

-- 2번째 쿼리: 첫 번째 Post의 댓글 조회
SELECT * FROM comment WHERE post_id = 1;

-- 3번째 쿼리: 두 번째 Post의 댓글 조회
SELECT * FROM comment WHERE post_id = 2;

-- ... (각 Post마다 쿼리 실행)
-- 총 1 + N개의 쿼리 실행
```

---

## 2. N+1 문제 발생 원인

N+1 문제는 **지연 로딩(Lazy Loading)**과 **즉시 로딩(Eager Loading)** 모두에서 발생할 수 있습니다.

### A. 지연 로딩(Lazy Loading)일 경우

#### 지연 로딩이란?

- 엔티티를 조회할 때 연관된 데이터 로딩을 실제 사용할 때까지 미루는 전략입니다.
- 연관된 데이터는 프록시(Proxy) 객체로 가져옵니다.

#### 발생 과정

1. **Post 목록을 조회하는 쿼리 1개가 실행됩니다.**
   - 연관된 Comment 목록은 프록시 객체로 가져옵니다.

```java
List<Post> posts = postRepository.findAll();
// 실행되는 쿼리:
// SELECT * FROM post;
```

2. **Post 목록을 순회(반복문)하며 각 게시물의 Comment 목록에 접근하면, 프록시 객체가 초기화되면서 DB에 쿼리를 날립니다.**

```java
for (Post post : posts) {
    List<Comment> comments = post.getComments();  // 프록시 초기화 → 쿼리 실행
    // 실행되는 쿼리:
    // SELECT * FROM comment WHERE post_id = ?;
}
```

3. **Post가 N개일 경우, 게시물마다 쿼리가 실행되어 1 (Post 조회) + N (각 Post의 Comment 조회) 형태의 쿼리가 발생합니다.**

```
총 쿼리 수: 1 + N개
- Post 조회: 1개
- 각 Post의 Comment 조회: N개 (Post가 10개면 10개)
```

### B. 즉시 로딩(Eager Loading)일 경우

#### 문제점

- 즉시 로딩(`fetch = Eager`)은 엔티티 조회 시 연관된 데이터를 즉시 함께 가져오지만, **목록 조회(예: findAll) 상황에서는 N+1 문제를 해결하지 못하고 오히려 강제로 발생시킵니다.**

#### 발생 과정

1. **목록을 조회하는 1개의 쿼리가 나간 후**, JPA는 즉시 로딩 설정을 확인하고 목록에 있는 N개의 엔티티 각각에 대해 추가 쿼리(Comment 조회)를 N번 실행합니다.

```java
@Entity
public class Post {
    @OneToMany(mappedBy = "post", fetch = FetchType.EAGER)  // 즉시 로딩
    private List<Comment> comments;
}

// findAll() 호출 시
List<Post> posts = postRepository.findAll();
// 실행되는 쿼리:
// 1. SELECT * FROM post;
// 2. SELECT * FROM comment WHERE post_id = 1;
// 3. SELECT * FROM comment WHERE post_id = 2;
// 4. SELECT * FROM comment WHERE post_id = 3;
// ... (각 Post마다 쿼리 실행)
```

---

## 3. N+1 문제 해결 방안

N+1 문제를 해결하는 방법은 크게 3가지가 있습니다: **Eager Loading**, **Batch Loading**, **Fetch Join**입니다.

### 1. Eager Loading (즉시 로딩)

#### 방식

ORM 기능을 사용하여 연관된 모든 데이터를 JOIN 등을 통해 한 번의 쿼리로 가져오는 방법입니다.

#### 장점

- ✅ 쿼리 횟수가 가장 적습니다 (1번)
- ✅ 한 번의 쿼리로 모든 데이터를 가져옵니다

#### 단점

- ❌ 사용하지 않는 데이터까지 모두 가져오는 **Over-fetching 문제**로 데이터 사이즈 및 로딩 시간이 증가할 수 있습니다
- ❌ 연관된 엔티티가 많으면 JOIN으로 인한 성능 저하가 발생할 수 있습니다
- ❌ 목록 조회(예: findAll) 상황에서는 N+1 문제를 해결하지 못하고 오히려 강제로 발생시킵니다

#### 사용 예시

```java
@Entity
public class Post {
    @OneToMany(mappedBy = "post", fetch = FetchType.EAGER)  // 즉시 로딩
    private List<Comment> comments;
}

// 단일 엔티티 조회 시에는 효과적
Post post = postRepository.findById(1L);  // Post와 Comment를 한 번에 조회
```

> [!warning] 주의사항
> Eager Loading은 단일 엔티티 조회에서는 효과적이지만, 목록 조회에서는 N+1 문제를 해결하지 못합니다. 따라서 지연 로딩을 기본 전략으로 사용하고, 필요할 때만 Fetch Join을 사용하는 것이 권장됩니다.

### 2. Batch Loading (배치 로딩)

#### 방식

우선 필요한 데이터를 조회한 후, 연관된 데이터를 불러오기 위해 필요한 ID 값들을 모두 모아서 일괄적으로 두 번째 조회를 진행합니다.

#### 특징

- 개별적으로 N번 조회하는 대신, 필요한 값들을 모아 한 번에(IN 절 등을 활용하여) 조회함으로써 쿼리 횟수를 획기적으로 줄입니다
- N+1 문제를 완전히 해결하지는 못하지만, 쿼리 횟수를 크게 줄일 수 있습니다

#### 사용 방법

```java
@Entity
public class Post {
    @OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
    @BatchSize(size = 10)  // 10개씩 배치로 조회
    private List<Comment> comments;
}
```

#### 실행되는 쿼리

```sql
-- 1번째 쿼리: Post 목록 조회
SELECT * FROM post;

-- 2번째 쿼리: Comment 배치 조회 (IN 절 사용)
SELECT * FROM comment WHERE post_id IN (1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
```

#### 전역 설정

```yaml
spring:
  jpa:
    properties:
      hibernate:
        jdbc:
          batch_size: 10
```

### 3. Fetch Join (패치 조인)

#### 방식

JPQL의 `join fetch` 구문을 사용하여 연관된 데이터까지 **한 번의 쿼리(JOIN)**로 가져옵니다.

#### 결과

- 프록시가 아닌 실제 엔티티를 한 번에 조회하므로, 반복문에서 추가 쿼리가 발생하지 않습니다
- N+1 문제를 완전히 해결합니다

#### 사용 방법

```java
// JPQL에서 패치 조인 사용
@Query("SELECT p FROM Post p JOIN FETCH p.comments")
List<Post> findAllWithComments();

// DISTINCT 사용 (중복 제거)
@Query("SELECT DISTINCT p FROM Post p JOIN FETCH p.comments")
List<Post> findAllWithComments();
```

#### 실행되는 쿼리

```sql
-- 실행되는 쿼리 (1개)
SELECT p.*, c.*
FROM post p
INNER JOIN comment c ON p.id = c.post_id;
```

#### 효과

```java
List<Post> posts = postRepository.findAllWithComments();  // 쿼리 1개만 실행

for (Post post : posts) {
    List<Comment> comments = post.getComments();  // DB 접근 없이 바로 반환
    // 추가 쿼리 없음!
}
```

#### 주의사항: DISTINCT 사용

- 패치 조인을 사용하면 조인으로 인해 결과가 중복될 수 있습니다
- `DISTINCT`를 사용하여 중복을 제거해야 합니다

```java
@Query("SELECT DISTINCT p FROM Post p JOIN FETCH p.comments")
List<Post> findAllWithComments();
```

---

## 4. 주의 사항: Fetch Join과 페이징 (Paging)

### 문제점

**1:N 관계(컬렉션)를 Fetch Join 하면서 페이징(limit, offset)을 시도하면 문제가 발생합니다.**

### DB와 객체의 차이

- **DB는 row 단위로 데이터를 반환**하므로 1:N 조인 시 데이터 수가 뻥튀기됩니다
- 이 상태에서 DB 레벨의 limit을 걸면 자식 데이터가 잘리거나 데이터 정합성이 깨질 수 있습니다

#### 예시

```
Post 테이블          Comment 테이블
id | title         id | post_id | content
1  | 게시물1       1  | 1       | 댓글1
2  | 게시물2       2  | 1       | 댓글2
                  3  | 1       | 댓글3
                  4  | 2       | 댓글4
```

**Fetch Join 시 조인 결과:**

```
조인 결과 (Post + Comment)
post_id | post_title | comment_id | comment_content
1       | 게시물1     | 1          | 댓글1
1       | 게시물1     | 2          | 댓글2
1       | 게시물1     | 3          | 댓글3
2       | 게시물2     | 4          | 댓글4
```

### 문제점

#### 1. 결과 누락 및 데이터 정합성 문제

- DB의 `LIMIT`는 **로우(Row) 기준**으로 작동하기 때문에, 조인된 상태에서 리미트를 걸면 1개의 엔티티에 속한 로우가 잘려서 엔티티가 불완전해지거나, 기대한 숫자보다 적은 수의 엔티티가 반환됩니다

```java
// 페이징과 패치 조인 함께 사용 (문제 발생)
@Query("SELECT p FROM Post p JOIN FETCH p.comments")
Page<Post> findAllWithComments(Pageable pageable);

// 예상: 10개의 Post를 가져오려고 했지만
// 실제: 게시물1의 댓글이 3개라서 게시물1만 3개 로우로 표현됨
// LIMIT 10을 걸면 게시물1만 가져오고 게시물2는 누락될 수 있음
```

#### 2. 메모리 부하 (Out Of Memory)

- 하이버네이트는 경고 로그를 남기고 **모든 데이터를 DB에서 읽어와 메모리에서 페이징 처리를 합니다**

```java
// 경고 메시지
// HHH000104: firstResult/maxResults specified with collection fetch; applying in memory!
```

- 데이터가 많다면 **Out Of Memory(OOM) 장애**로 이어질 수 있습니다
- 데이터가 100만 건 이상일 경우, 이 전체 데이터를 메모리에서 관리하게 되어 **메모리 부하(Memory Overload)**를 일으킵니다

### 해결 방안

#### 1. 다대일(Many-to-One, N:1) 관계일 때 페이징 처리

- 다대일 관계에서는 패치 조인과 페이징을 함께 사용해도 문제가 없습니다.

```java
// Comment → Post (N:1) 관계에서 페이징
@Query("SELECT c FROM Comment c JOIN FETCH c.post")
Page<Comment> findAllWithPost(Pageable pageable);  // ✅ 문제 없음
```

#### 2. Batch Size 설정 사용

- `@BatchSize` 어노테이션을 사용하여 연관된 엔티티를 배치로 조회할 수 있습니다.
- Batch Size 설정 등을 활용하는 방법이 권장됩니다.

```java
@Entity
public class Post {
    @OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
    @BatchSize(size = 10)  // 10개씩 배치로 조회
    private List<Comment> comments;
}
```

- 설정 후 쿼리 실행:

```sql
-- Post 조회
SELECT * FROM post LIMIT 10 OFFSET 0;

-- Comment 배치 조회 (IN 절 사용)
SELECT * FROM comment WHERE post_id IN (1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
```

- `application.yml`에서 전역 설정도 가능합니다:

```yaml
spring:
  jpa:
    properties:
      hibernate:
        jdbc:
          batch_size: 10
```

#### 3. 별도 쿼리로 분리

- 페이징이 필요한 경우, 패치 조인 대신 별도의 쿼리로 연관 데이터를 조회합니다.

```java
// 1단계: Post 페이징 조회
Page<Post> posts = postRepository.findAll(pageable);

// 2단계: Post ID 목록 추출
List<Long> postIds = posts.getContent().stream()
    .map(Post::getId)
    .collect(Collectors.toList());

// 3단계: 해당 Post들의 Comment 조회
List<Comment> comments = commentRepository.findByPostIdIn(postIds);

// 4단계: 수동으로 매핑
Map<Long, List<Comment>> commentMap = comments.stream()
    .collect(Collectors.groupingBy(comment -> comment.getPost().getId()));

posts.getContent().forEach(post -> 
    post.setComments(commentMap.getOrDefault(post.getId(), Collections.emptyList()))
);
```

---

## 5. 정리 및 결론

### **핵심 요약**

1. **N+1 문제의 정의**:
   - 하나의 요청이 한 개의 쿼리로 처리되기를 기대했지만, N개의 추가 쿼리가 발생하여 총 1 + N개의 쿼리가 나가는 현상입니다.
   - JPA의 다양한 연관 관계 중, 특히 일대다(One-to-Many, 1:N) 관계에서 중점적으로 발생합니다.

2. **N+1 문제 발생 원인**:
   - **지연 로딩**: 엔티티 목록을 조회한 후 반복문에서 연관된 데이터에 접근할 때 프록시 객체가 초기화되면서 각 엔티티마다 추가 쿼리가 실행됩니다.
   - **즉시 로딩**: 목록 조회 시 JPA가 즉시 로딩 설정을 확인하고 목록에 있는 N개의 엔티티 각각에 대해 추가 쿼리를 N번 실행합니다.

3. **N+1 문제 해결 방법**:
   - **Eager Loading**: ORM 기능을 사용하여 연관된 모든 데이터를 JOIN 등을 통해 한 번의 쿼리로 가져오는 방법입니다. 쿼리 횟수가 가장 적지만, Over-fetching 문제와 목록 조회 시 N+1 문제가 발생할 수 있습니다.
   - **Batch Loading**: 우선 필요한 데이터를 조회한 후, 연관된 데이터를 불러오기 위해 필요한 ID 값들을 모두 모아서 일괄적으로 두 번째 조회를 진행합니다. IN 절을 활용하여 쿼리 횟수를 획기적으로 줄입니다.
   - **Fetch Join**: JPQL의 `join fetch` 구문을 사용하여 연관된 데이터까지 한 번의 쿼리(JOIN)로 가져옵니다. 프록시가 아닌 실제 엔티티를 한 번에 조회하므로 N+1 문제를 완전히 해결합니다.

4. **Fetch Join의 주의사항**:
   - 1:N 관계(컬렉션)를 Fetch Join 하면서 페이징(limit, offset)을 시도하면 문제가 발생합니다.
   - DB는 row 단위로 데이터를 반환하므로 1:N 조인 시 데이터 수가 뻥튀기되고, DB 레벨의 limit을 걸면 자식 데이터가 잘리거나 데이터 정합성이 깨질 수 있습니다.
   - 하이버네이트는 경고 로그를 남기고 모든 데이터를 DB에서 읽어와 메모리에서 페이징 처리를 하므로, 데이터가 많다면 Out Of Memory(OOM) 장애로 이어질 수 있습니다.
   - 다대일 관계에서는 패치 조인과 페이징을 함께 사용해도 문제가 없습니다.

---

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "즉시 로딩(Eager Loading)을 사용하면 N+1 문제가 해결된다"는 생각은 잘못되었습니다. 즉시 로딩은 단일 엔티티 조회에서는 연관 데이터를 함께 가져오지만, 목록 조회(예: findAll) 상황에서는 오히려 N+1 문제를 강제로 발생시킵니다. 지연 로딩을 기본 전략으로 사용하고, 필요할 때만 패치 조인을 사용하는 것이 권장됩니다.
> 
> "패치 조인을 사용하면 항상 N+1 문제가 해결된다"는 생각도 주의가 필요합니다. 패치 조인은 일대다 관계에서 페이징과 함께 사용할 경우 결과 누락이나 메모리 부하 문제가 발생할 수 있습니다. 페이징이 필요한 경우, Batch Size 설정이나 별도 쿼리로 분리하는 방법을 고려해야 합니다.

#### N+1 문제 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **데이터베이스 부하 증가**: 하나의 요청이 예상보다 훨씬 많은 쿼리를 발생시켜 데이터베이스 부하가 증가합니다.
> - **응답 시간 지연**: N+1 문제로 인해 쿼리 실행 시간이 길어져 사용자 경험이 저하됩니다.
> - **메모리 부하**: 페이징과 패치 조인을 함께 사용할 경우, 전체 데이터를 메모리에서 처리하려고 시도하여 메모리 부하가 발생할 수 있습니다.
> - **결과 누락**: 페이징과 패치 조인을 함께 사용할 경우, DB의 LIMIT가 로우 기준으로 작동하여 예상보다 적은 수의 엔티티가 반환될 수 있습니다.
> - **성능 저하**: 대량의 데이터를 처리하는 경우, N+1 문제로 인해 애플리케이션 성능이 크게 저하될 수 있습니다.

---

### 출처

- [JPA N+1 문제 해결 방법](https://jojoldu.tistory.com/165)
- [Hibernate - Batch Fetching](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#batch-fetching)


---

## 요약

- **N+1 문제**는 ORM을 사용하여 관련 데이터를 조회할 때 발생하는 비효율적인 쿼리 패턴으로, 한 번의 쿼리로 해결할 수 있는 작업을 여러 번의 쿼리로 실행하여 성능 저하를 일으키는 현상입니다.
- **발생 원인**: 지연 로딩과 즉시 로딩 모두에서 발생할 수 있으며, 특히 일대다(One-to-Many, 1:N) 연관관계에서 중점적으로 발생합니다.
- **해결 방법**: Eager Loading, Batch Loading, Fetch Join 등의 방법을 사용할 수 있습니다.
- **Eager Loading**: 쿼리 횟수가 가장 적지만, Over-fetching 문제와 목록 조회 시 N+1 문제가 발생할 수 있습니다.
- **Batch Loading**: 필요한 ID 값들을 모아서 IN 절로 일괄 조회하여 쿼리 횟수를 획기적으로 줄입니다.
- **Fetch Join**: JPQL의 `join fetch` 구문을 사용하여 프록시가 아닌 실제 엔티티를 한 번에 조회하므로 N+1 문제를 완전히 해결합니다.
- **주의사항**: Fetch Join은 1:N 관계에서 페이징과 함께 사용할 경우 결과 누락이나 Out Of Memory(OOM) 장애가 발생할 수 있습니다.
- **권장 사항**: 지연 로딩을 기본 전략으로 사용하고, 필요할 때만 Fetch Join을 사용하는 것이 권장됩니다.

---

### 예상 꼬리질문정리

1. **N+1 문제란 무엇인가요?**
   - ORM을 사용하여 관련 데이터를 조회할 때 발생하는 비효율적인 쿼리 패턴으로, 한 번의 쿼리로 해결할 수 있는 작업을 여러 번의 쿼리로 실행하여 성능 저하를 일으키는 현상입니다. JPA의 다양한 연관 관계 중, 특히 일대다(One-to-Many, 1:N) 관계에서 중점적으로 발생합니다.

2. **N+1 문제는 언제 발생하나요?**
   - 지연 로딩의 경우, 엔티티 목록을 조회한 후 반복문에서 연관된 데이터에 접근할 때 프록시 객체가 초기화되면서 각 엔티티마다 추가 쿼리가 실행됩니다. 즉시 로딩의 경우, 목록 조회 시 JPA가 즉시 로딩 설정을 확인하고 목록에 있는 N개의 엔티티 각각에 대해 추가 쿼리를 N번 실행합니다.

3. **N+1 문제를 어떻게 해결하나요?**
   - **Eager Loading**: ORM 기능을 사용하여 연관된 모든 데이터를 JOIN 등을 통해 한 번의 쿼리로 가져오는 방법입니다. 쿼리 횟수가 가장 적지만, Over-fetching 문제와 목록 조회 시 N+1 문제가 발생할 수 있습니다.
   - **Batch Loading**: 필요한 ID 값들을 모아서 IN 절로 일괄 조회하여 쿼리 횟수를 획기적으로 줄입니다.
   - **Fetch Join**: JPQL의 `join fetch` 구문을 사용하여 프록시가 아닌 실제 엔티티를 한 번에 조회하므로 N+1 문제를 완전히 해결합니다.

4. **Fetch Join과 일반 조인의 차이는 무엇인가요?**
   - 일반 조인은 연관된 엔티티를 함께 조회하지만, 연관된 엔티티는 프록시로 가져와 실제 사용 시 추가 쿼리가 발생합니다. Fetch Join은 연관된 엔티티를 진짜 객체로 가져와 추가 쿼리 없이 바로 사용할 수 있습니다.

5. **Fetch Join을 사용할 때 주의사항은 무엇인가요?**
   - Fetch Join을 사용하면 조인으로 인해 결과가 중복될 수 있으므로 DISTINCT를 사용해야 합니다. 또한 1:N 관계에서 페이징과 함께 사용할 경우, DB의 LIMIT가 로우 기준으로 작동하여 결과 누락이나 Out Of Memory(OOM) 장애가 발생할 수 있습니다.

6. **페이징과 Fetch Join을 함께 사용하면 왜 문제가 되나요?**
   - DB는 row 단위로 데이터를 반환하므로 1:N 조인 시 데이터 수가 뻥튀기됩니다. 이 상태에서 DB 레벨의 limit을 걸면 자식 데이터가 잘리거나 데이터 정합성이 깨질 수 있습니다. 또한 하이버네이트는 경고 로그를 남기고 모든 데이터를 DB에서 읽어와 메모리에서 페이징 처리를 하므로, 데이터가 많다면 Out Of Memory(OOM) 장애로 이어질 수 있습니다.

7. **페이징이 필요한 경우 N+1 문제를 어떻게 해결하나요?**
   - 다대일(Many-to-One, N:1) 관계일 때는 Fetch Join과 페이징을 함께 사용해도 문제가 없습니다. 일대다 관계의 경우, Batch Size 설정을 사용하여 연관된 엔티티를 배치로 조회하거나, 별도 쿼리로 분리하여 해결할 수 있습니다.

8. **Batch Size 설정이란 무엇인가요?**
   - @BatchSize 어노테이션을 사용하여 연관된 엔티티를 배치로 조회하는 기능입니다. 예를 들어 size를 10으로 설정하면, 연관된 엔티티를 10개씩 묶어서 IN 절을 사용한 쿼리로 조회하여 N+1 문제를 완화할 수 있습니다.

9. **지연 로딩과 즉시 로딩 중 어떤 것을 사용해야 하나요?**
   - 지연 로딩을 기본 전략으로 사용하고, 데이터가 많이 필요할 때만 Fetch Join을 함께 쓰는 것이 권장됩니다. 즉시 로딩(Eager Loading)은 단일 엔티티 조회에서는 연관 데이터를 함께 가져오지만, 목록 조회 상황에서는 오히려 N+1 문제를 강제로 발생시키기 때문에 피해야 합니다.

10. **Eager Loading의 장단점은 무엇인가요?**
    - **장점**: 쿼리 횟수가 가장 적습니다 (1번). 한 번의 쿼리로 모든 데이터를 가져옵니다.
    - **단점**: 사용하지 않는 데이터까지 모두 가져오는 Over-fetching 문제로 데이터 사이즈 및 로딩 시간이 증가할 수 있으며, 연관된 엔티티가 많으면 JOIN으로 인한 성능 저하가 발생할 수 있습니다. 또한 목록 조회 시 N+1 문제를 해결하지 못하고 오히려 강제로 발생시킵니다.

11. **Batch Loading이란 무엇인가요?**
    - 우선 필요한 데이터를 조회한 후, 연관된 데이터를 불러오기 위해 필요한 ID 값들을 모두 모아서 일괄적으로 두 번째 조회를 진행하는 방법입니다. 개별적으로 N번 조회하는 대신, 필요한 값들을 모아 한 번에(IN 절 등을 활용하여) 조회함으로써 쿼리 횟수를 획기적으로 줄입니다.

12. **@EntityGraph와 Fetch Join의 차이는 무엇인가요?**
    - @EntityGraph는 Spring Data JPA에서 제공하는 기능으로, Fetch Join과 유사한 효과를 제공합니다. 하지만 @EntityGraph는 페이징과 함께 사용할 수 있는 옵션이 있어, 일부 상황에서 더 유연하게 사용할 수 있습니다.

---

## 관련 노트

- **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]** - JPA의 기본 개념과 연관관계
- **[[RESTful API (REST API)|RESTful API]]** - JPA를 활용한 REST API 개발

---

**태그:** #spring #jpa #orm #n+1 #성능최적화 #면접 #개념정리

