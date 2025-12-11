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

**N+1 문제**는 하나의 요청이 한 개의 쿼리로 처리되기를 기대했지만, N개의 추가 쿼리가 발생하여 총 1 + N개의 쿼리가 나가는 현상을 말합니다.

**패치 조인(Fetch Join)**은 데이터를 한꺼번에 가져오는 기능으로, 조인 구문에 FETCH를 명시하여 사용하며, 연관 객체를 프록시가 아닌 진짜 객체로 가져와 N+1 문제를 해결합니다.

> [!tip] 핵심 포인트
> N+1 문제는 JPA의 다양한 연관 관계 중, 특히 일대다(One-to-Many, 1:N) 관계에서 중점적으로 발생하며, 패치 조인을 통해 해결할 수 있습니다. 다만 페이징과 함께 사용할 경우 주의가 필요합니다.

---

## 1. N+1 문제란 무엇인가?

### 정의

- 하나의 요청이 한 개의 쿼리로 처리되기를 기대했지만, N개의 추가 쿼리가 발생하여 총 **1 + N개의 쿼리**가 나가는 현상을 말합니다.

- JPA의 다양한 연관 관계 중, 특히 **일대다(One-to-Many, 1:N) 관계**에서 중점적으로 발생합니다.

### 발생 예시

```java
// Crew와 Task는 1:N 관계
@Entity
public class Crew {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    @OneToMany(mappedBy = "crew", fetch = FetchType.LAZY)
    private List<Task> tasks;
}

@Entity
public class Task {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "crew_id")
    private Crew crew;
}
```

```java
// N+1 문제 발생 코드
List<Crew> crews = crewRepository.findAll();  // 쿼리 1개 실행

for (Crew crew : crews) {
    List<Task> tasks = crew.getTasks();  // 각 Crew마다 쿼리 실행 (N개)
    // Crew가 10명이면 총 1 + 10 = 11개의 쿼리 실행
}
```

---

## 2. N+1 문제 발생 원인

N+1 문제는 **지연 로딩(Lazy Loading)**과 **즉시 로딩(Eager Loading)** 모두에서 발생할 수 있습니다.

### A. 지연 로딩(Lazy Loading)일 경우

#### 지연 로딩이란?

- 엔티티를 조회할 때 연관된 데이터 로딩을 실제 사용할 때까지 미루는 전략입니다.
- 연관된 데이터는 프록시(Proxy) 객체로 가져옵니다.

#### 발생 과정

1. **Crew 목록을 조회하는 쿼리 1개가 실행됩니다.**
   - 연관된 Task 목록은 프록시 객체로 가져옵니다.

```java
List<Crew> crews = crewRepository.findAll();
// 실행되는 쿼리:
// SELECT * FROM crew;
```

2. **Crew 목록을 순회(반복문)하며 각 크루의 Task 목록에 접근하면, 프록시 객체가 초기화되면서 DB에 쿼리를 날립니다.**

```java
for (Crew crew : crews) {
    List<Task> tasks = crew.getTasks();  // 프록시 초기화 → 쿼리 실행
    // 실행되는 쿼리:
    // SELECT * FROM task WHERE crew_id = ?;
}
```

3. **Crew가 N명일 경우, 크루마다 쿼리가 실행되어 1 (크루 조회) + N (각 크루의 Task 조회) 형태의 쿼리가 발생합니다.**

```
총 쿼리 수: 1 + N개
- Crew 조회: 1개
- 각 Crew의 Task 조회: N개 (Crew가 10명이면 10개)
```

### B. 즉시 로딩(Eager Loading)일 경우

#### 문제점

- 즉시 로딩(`fetch = Eager`)은 엔티티 조회 시 연관된 데이터를 즉시 함께 가져오지만, **목록 조회(예: findAll) 상황에서는 N+1 문제를 해결하지 못하고 오히려 강제로 발생시킵니다.**

#### 발생 과정

1. **목록을 조회하는 1개의 쿼리가 나간 후**, JPA는 즉시 로딩 설정을 확인하고 목록에 있는 N개의 엔티티 각각에 대해 추가 쿼리(Task 조회)를 N번 실행합니다.

```java
@Entity
public class Crew {
    @OneToMany(mappedBy = "crew", fetch = FetchType.EAGER)  // 즉시 로딩
    private List<Task> tasks;
}

// findAll() 호출 시
List<Crew> crews = crewRepository.findAll();
// 실행되는 쿼리:
// 1. SELECT * FROM crew;
// 2. SELECT * FROM task WHERE crew_id = 1;
// 3. SELECT * FROM task WHERE crew_id = 2;
// 4. SELECT * FROM task WHERE crew_id = 3;
// ... (각 Crew마다 쿼리 실행)
```

#### 권장 사항

- 즉시 로딩 대신 **지연 로딩을 기본 전략으로 사용**하고, 데이터가 많이 필요할 때만 **패치 조인(Fetch Join)**을 함께 쓰는 것이 권장됩니다.

```java
// ✅ 좋은 예: 지연 로딩 + 필요 시 패치 조인
@Entity
public class Crew {
    @OneToMany(mappedBy = "crew", fetch = FetchType.LAZY)  // 지연 로딩
    private List<Task> tasks;
}

// 필요할 때만 패치 조인 사용
@Query("SELECT c FROM Crew c JOIN FETCH c.tasks")
List<Crew> findAllWithTasks();
```

---

## 3. N+1 문제 해결 방안: 패치 조인(Fetch Join)

### 패치 조인이란?

- 데이터를 한꺼번에 가져오는 기능으로, 조인 구문에 **FETCH**를 명시하여 사용합니다.

### 사용 방법

```java
// JPQL에서 패치 조인 사용
@Query("SELECT c FROM Crew c JOIN FETCH c.tasks")
List<Crew> findAllWithTasks();

// 또는 Repository에서 직접 사용
@Query("SELECT DISTINCT c FROM Crew c JOIN FETCH c.tasks")
List<Crew> findAllWithTasks();
```

### 효과

1. **JOIN FETCH를 사용한 쿼리 1개만 실행됩니다.**

```sql
-- 실행되는 쿼리 (1개)
SELECT c.*, t.*
FROM crew c
INNER JOIN task t ON c.id = t.crew_id;
```

2. **연관 객체를 프록시가 아닌 진짜 객체로 가져오기 때문에**, 반복문 순회 시 DB를 거치지 않고 바로 데이터를 반환하여 N+1 문제가 해결됩니다.

```java
List<Crew> crews = crewRepository.findAllWithTasks();  // 쿼리 1개만 실행

for (Crew crew : crews) {
    List<Task> tasks = crew.getTasks();  // DB 접근 없이 바로 반환
    // 추가 쿼리 없음!
}
```

### 주의사항: DISTINCT 사용

- 패치 조인을 사용하면 조인으로 인해 결과가 중복될 수 있습니다.
- `DISTINCT`를 사용하여 중복을 제거해야 합니다.

```java
@Query("SELECT DISTINCT c FROM Crew c JOIN FETCH c.tasks")
List<Crew> findAllWithTasks();
```

---

## 4. 패치 조인의 부작용: 페이징(Paging) 문제

### 문제 상황

패치 조인은 N+1 문제를 해결하지만, **페이징(Paging)과 함께 사용할 경우 예상치 못한 부작용**을 일으킵니다.

### 원인

- 관계형 DB와 객체의 차이에서 발생합니다.
- DB에서는 **1개의 엔티티(크루)가 여러 개의 로우(할 일)로 표현**될 수 있습니다.

```
Crew 테이블          Task 테이블
id | name          id | crew_id | title
1  | 크루A         1  | 1       | 작업1
2  | 크루B         2  | 1       | 작업2
                  3  | 1       | 작업3
                  4  | 2       | 작업4
```

- 패치 조인 시 조인 결과:

```
조인 결과 (Crew + Task)
crew_id | crew_name | task_id | task_title
1       | 크루A     | 1       | 작업1
1       | 크루A     | 2       | 작업2
1       | 크루A     | 3       | 작업3
2       | 크루B     | 4       | 작업4
```

### 문제점

#### 1. 결과 누락

- DB의 `LIMIT`는 **로우(Row) 기준**으로 작동하기 때문에, 조인된 상태에서 리미트를 걸면 1개의 엔티티에 속한 로우가 잘려서 엔티티가 불완전해지거나, 기대한 숫자보다 적은 수의 엔티티가 반환됩니다.

```java
// 페이징과 패치 조인 함께 사용 (문제 발생)
@Query("SELECT c FROM Crew c JOIN FETCH c.tasks")
Page<Crew> findAllWithTasks(Pageable pageable);

// 예상: 10개의 Crew를 가져오려고 했지만
// 실제: 크루A의 작업이 3개라서 크루A만 3개 로우로 표현됨
// LIMIT 10을 걸면 크루A만 가져오고 크루B는 누락될 수 있음
```

#### 2. 메모리 부하

- JPA는 이 문제 회피를 위해 **SQL에 LIMIT를 걸지 않고 전체 데이터를 일단 전부 가져온 후 메모리 상에서 페이지 처리를 시도**합니다.

```java
// 경고 메시지
// HHH000104: firstResult/maxResults specified with collection fetch; applying in memory!
```

- 데이터가 100만 건 이상일 경우, 이 전체 데이터를 메모리에서 관리하게 되어 **메모리 부하(Memory Overload)**를 일으킵니다.

### 해결 방안

#### 1. 다대일(Many-to-One, N:1) 관계일 때 페이징 처리

- 다대일 관계에서는 패치 조인과 페이징을 함께 사용해도 문제가 없습니다.

```java
// Task → Crew (N:1) 관계에서 페이징
@Query("SELECT t FROM Task t JOIN FETCH t.crew")
Page<Task> findAllWithCrew(Pageable pageable);  // ✅ 문제 없음
```

#### 2. Batch Size 설정 사용

- `@BatchSize` 어노테이션을 사용하여 연관된 엔티티를 배치로 조회할 수 있습니다.

```java
@Entity
public class Crew {
    @OneToMany(mappedBy = "crew", fetch = FetchType.LAZY)
    @BatchSize(size = 10)  // 10개씩 배치로 조회
    private List<Task> tasks;
}
```

- 설정 후 쿼리 실행:

```sql
-- Crew 조회
SELECT * FROM crew;

-- Task 배치 조회 (IN 절 사용)
SELECT * FROM task WHERE crew_id IN (1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
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
// 1단계: Crew 페이징 조회
Page<Crew> crews = crewRepository.findAll(pageable);

// 2단계: Crew ID 목록 추출
List<Long> crewIds = crews.getContent().stream()
    .map(Crew::getId)
    .collect(Collectors.toList());

// 3단계: 해당 Crew들의 Task 조회
List<Task> tasks = taskRepository.findByCrewIdIn(crewIds);

// 4단계: 수동으로 매핑
Map<Long, List<Task>> taskMap = tasks.stream()
    .collect(Collectors.groupingBy(task -> task.getCrew().getId()));

crews.getContent().forEach(crew -> 
    crew.setTasks(taskMap.getOrDefault(crew.getId(), Collections.emptyList()))
);
```

---

### **Interview Answer Version (면접 답변식 요약)**

N+1 문제는 하나의 요청이 한 개의 쿼리로 처리되기를 기대했지만, N개의 추가 쿼리가 발생하여 총 1 + N개의 쿼리가 나가는 현상입니다. JPA의 다양한 연관 관계 중, 특히 일대다(One-to-Many, 1:N) 관계에서 중점적으로 발생합니다.

N+1 문제는 지연 로딩(Lazy Loading)과 즉시 로딩(Eager Loading) 모두에서 발생할 수 있습니다. 지연 로딩의 경우, 엔티티 목록을 조회한 후 반복문에서 연관된 데이터에 접근할 때 프록시 객체가 초기화되면서 각 엔티티마다 추가 쿼리가 실행됩니다. 즉시 로딩의 경우, 목록 조회 시 JPA가 즉시 로딩 설정을 확인하고 목록에 있는 N개의 엔티티 각각에 대해 추가 쿼리를 N번 실행합니다.

N+1 문제는 패치 조인(Fetch Join)을 통해 해결할 수 있습니다. 패치 조인은 조인 구문에 FETCH를 명시하여 사용하며, 연관 객체를 프록시가 아닌 진짜 객체로 가져와 한 번의 쿼리로 모든 데이터를 조회합니다. 다만 패치 조인은 페이징과 함께 사용할 경우, DB의 LIMIT가 로우 기준으로 작동하여 결과 누락이나 메모리 부하 문제가 발생할 수 있습니다. 이를 해결하기 위해 다대일 관계에서 페이징을 하거나, Batch Size 설정을 사용하거나, 별도 쿼리로 분리하는 방법을 사용할 수 있습니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### N+1 문제 확인 방법

**1. SQL 로그 확인**

```yaml
# application.yml
spring:
  jpa:
    show-sql: true
    properties:
      hibernate:
        format_sql: true
```

- 애플리케이션 실행 후 로그에서 반복되는 쿼리 패턴을 확인합니다.

**2. 성능 모니터링 도구 사용**

- P6Spy, DataSource-Proxy 등을 사용하여 실제 실행되는 쿼리를 모니터링합니다.

#### N+1 문제 해결 패턴

**1. 패치 조인 사용 (일반적인 해결책)**

```java
// ✅ 좋은 예: 패치 조인 사용
@Query("SELECT DISTINCT c FROM Crew c JOIN FETCH c.tasks")
List<Crew> findAllWithTasks();

// ❌ 나쁜 예: 일반 조회 후 반복문 접근
List<Crew> crews = crewRepository.findAll();
for (Crew crew : crews) {
    crew.getTasks();  // N+1 문제 발생
}
```

**2. @EntityGraph 사용**

```java
@EntityGraph(attributePaths = {"tasks"})
List<Crew> findAll();
```

**3. Batch Size 설정**

```java
@Entity
public class Crew {
    @OneToMany(mappedBy = "crew", fetch = FetchType.LAZY)
    @BatchSize(size = 10)
    private List<Task> tasks;
}
```

#### 주의사항

**1. 페이징과 패치 조인 함께 사용 금지**

```java
// ❌ 나쁜 예: 페이징과 패치 조인 함께 사용
@Query("SELECT c FROM Crew c JOIN FETCH c.tasks")
Page<Crew> findAllWithTasks(Pageable pageable);  // 경고 발생, 메모리 부하 가능

// ✅ 좋은 예: 페이징은 별도로, 필요 시 Batch Size 사용
Page<Crew> crews = crewRepository.findAll(pageable);
// Batch Size 설정으로 최적화
```

**2. DISTINCT 사용**

```java
// ✅ 좋은 예: DISTINCT로 중복 제거
@Query("SELECT DISTINCT c FROM Crew c JOIN FETCH c.tasks")
List<Crew> findAllWithTasks();
```

**3. 지연 로딩을 기본 전략으로 사용**

```java
// ✅ 좋은 예: 지연 로딩 + 필요 시 패치 조인
@Entity
public class Crew {
    @OneToMany(mappedBy = "crew", fetch = FetchType.LAZY)
    private List<Task> tasks;
}

// ❌ 나쁜 예: 즉시 로딩 (N+1 문제 강제 발생)
@Entity
public class Crew {
    @OneToMany(mappedBy = "crew", fetch = FetchType.EAGER)
    private List<Task> tasks;
}
```

---

### 예상 꼬리질문정리

1. **N+1 문제란 무엇인가요?**
   - 하나의 요청이 한 개의 쿼리로 처리되기를 기대했지만, N개의 추가 쿼리가 발생하여 총 1 + N개의 쿼리가 나가는 현상입니다. JPA의 다양한 연관 관계 중, 특히 일대다(One-to-Many, 1:N) 관계에서 중점적으로 발생합니다.

2. **N+1 문제는 언제 발생하나요?**
   - 지연 로딩의 경우, 엔티티 목록을 조회한 후 반복문에서 연관된 데이터에 접근할 때 프록시 객체가 초기화되면서 각 엔티티마다 추가 쿼리가 실행됩니다. 즉시 로딩의 경우, 목록 조회 시 JPA가 즉시 로딩 설정을 확인하고 목록에 있는 N개의 엔티티 각각에 대해 추가 쿼리를 N번 실행합니다.

3. **N+1 문제를 어떻게 해결하나요?**
   - 패치 조인(Fetch Join)을 사용하여 한 번의 쿼리로 모든 데이터를 조회할 수 있습니다. 조인 구문에 FETCH를 명시하여 사용하며, 연관 객체를 프록시가 아닌 진짜 객체로 가져와 N+1 문제를 해결합니다.

4. **패치 조인과 일반 조인의 차이는 무엇인가요?**
   - 일반 조인은 연관된 엔티티를 함께 조회하지만, 연관된 엔티티는 프록시로 가져와 실제 사용 시 추가 쿼리가 발생합니다. 패치 조인은 연관된 엔티티를 진짜 객체로 가져와 추가 쿼리 없이 바로 사용할 수 있습니다.

5. **패치 조인을 사용할 때 주의사항은 무엇인가요?**
   - 패치 조인을 사용하면 조인으로 인해 결과가 중복될 수 있으므로 DISTINCT를 사용해야 합니다. 또한 페이징과 함께 사용할 경우, DB의 LIMIT가 로우 기준으로 작동하여 결과 누락이나 메모리 부하 문제가 발생할 수 있습니다.

6. **페이징과 패치 조인을 함께 사용하면 왜 문제가 되나요?**
   - DB의 LIMIT는 로우 기준으로 작동하기 때문에, 조인된 상태에서 리미트를 걸면 1개의 엔티티에 속한 로우가 잘려서 엔티티가 불완전해지거나, 기대한 숫자보다 적은 수의 엔티티가 반환됩니다. 또한 JPA는 이 문제를 회피하기 위해 SQL에 LIMIT를 걸지 않고 전체 데이터를 메모리에서 처리하려고 시도하여 메모리 부하를 일으킬 수 있습니다.

7. **페이징이 필요한 경우 N+1 문제를 어떻게 해결하나요?**
   - 다대일(Many-to-One, N:1) 관계일 때는 패치 조인과 페이징을 함께 사용해도 문제가 없습니다. 일대다 관계의 경우, Batch Size 설정을 사용하여 연관된 엔티티를 배치로 조회하거나, 별도 쿼리로 분리하여 해결할 수 있습니다.

8. **Batch Size 설정이란 무엇인가요?**
   - @BatchSize 어노테이션을 사용하여 연관된 엔티티를 배치로 조회하는 기능입니다. 예를 들어 size를 10으로 설정하면, 연관된 엔티티를 10개씩 묶어서 IN 절을 사용한 쿼리로 조회하여 N+1 문제를 완화할 수 있습니다.

9. **지연 로딩과 즉시 로딩 중 어떤 것을 사용해야 하나요?**
   - 지연 로딩을 기본 전략으로 사용하고, 데이터가 많이 필요할 때만 패치 조인을 함께 쓰는 것이 권장됩니다. 즉시 로딩은 목록 조회 상황에서 N+1 문제를 강제로 발생시키기 때문에 피해야 합니다.

10. **@EntityGraph와 패치 조인의 차이는 무엇인가요?**
    - @EntityGraph는 Spring Data JPA에서 제공하는 기능으로, 패치 조인과 유사한 효과를 제공합니다. 하지만 @EntityGraph는 페이징과 함께 사용할 수 있는 옵션이 있어, 일부 상황에서 더 유연하게 사용할 수 있습니다.

---

## 관련 노트

- **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]** - JPA의 기본 개념과 연관관계
- **[[RESTful API (REST API)|RESTful API]]** - JPA를 활용한 REST API 개발

---

**태그:** #spring #jpa #orm #n+1 #성능최적화 #면접 #개념정리

