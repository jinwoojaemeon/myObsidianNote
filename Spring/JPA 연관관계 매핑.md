#jpa #연관관계 #매핑 #실무 #면접 #개념정리

**관련 개념:** [[JPA 엔티티 매핑|JPA 엔티티 매핑]] [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[N+1 문제 (JPA)|N+1 문제]]

> [!note] 이어서 읽기
> JPA 연관관계 매핑을 이해하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

JPA 연관관계 매핑이 무엇인지, 왜 필요한지, 그리고 어떻게 사용하는지 이해한다. 객체지향 세계와 관계형 데이터베이스 세계의 패러다임 불일치를 해결하는 방법을 학습한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 객체지향 세계와 관계형 데이터베이스 세계는 관계를 표현하는 방식이 다릅니다. 객체는 참조를 통해 관계를 표현하지만, 데이터베이스는 외래 키를 통해 관계를 표현합니다. 이 패러다임 불일치를 해결하지 않으면 객체 간 협력이 불가능하고, 코드가 데이터베이스 설계에 종속되어 유지보수가 복잡해집니다.

- JPA 연관관계 매핑은 객체 참조를 사용하여 자연스러운 객체 탐색을 가능하게 하고, JPA의 지연 로딩, 캐시 기능을 활용할 수 있게 합니다. 이를 통해 개발자는 SQL에 집중하지 않고 객체 그래프 탐색에 집중할 수 있어 생산성이 크게 향상됩니다.

- 실무에서 연관관계 매핑을 올바르게 설계하지 않으면 N+1 문제, 성능 저하, 무한 루프 등의 문제가 발생할 수 있습니다. 다중성, 방향성, 연관관계의 주인을 정확히 이해하고 적용하는 능력을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. 연관관계가 필요한 이유

##### 패러다임 불일치 문제

객체지향 세계와 관계형 데이터베이스 세계는 관계를 표현하는 방식이 다릅니다.

| 구분 | 객체 지향 세계 | 관계형 데이터베이스 세계 |
| --- | --- | --- |
| 관계 표현 | 객체 참조 (필드) | 외래 키 (Foreign Key) |
| 탐색 방식 | 객체 그래프 탐색 | JOIN 연산 |
| 관계 설정 | 코드로 참조 직접 연결 | 외래 키 값 지정 |
| 연관 관리 | 컬렉션 & 참조 | PK, FK로 관리 |

##### 문제 상황 예시

**잘못된 설계 (외래 키 직접 관리)**
```java
@Entity
public class Board {
    @Id @GeneratedValue
    private Long boardId;
    
    private String boardTitle;
    private String boardWriter; // 작성자 ID만 관리 (객체 참조 없음)
}
```

**문제점:**
- 객체 간 협력 불가 → 무조건 별도의 조회 필요
- 코드가 데이터베이스 설계에 종속
- 유지보수 복잡

**올바른 설계 (객체 참조 사용)**
```java
@Entity
public class Board {
    @Id @GeneratedValue
    private Long boardId;
    
    private String boardTitle;
    
    @ManyToOne
    @JoinColumn(name = "board_writer")
    private Member member; // 객체 참조
}
```

**장점:**
- 자연스러운 객체 탐색: `board.getMember().getUserName()`
- 객체 간 협력 관계 명확
- 유지보수 용이
- JPA의 지연 로딩, 캐시 기능 활용 가능

> [!tip] 핵심 포인트
> 연관관계 매핑의 목적은 객체 참조를 통해 객체지향적으로 설계하면서도, 데이터베이스의 외래 키 관계를 자동으로 관리하는 것입니다.

---

#### 2. 연관관계 매핑 3가지 핵심 요소

##### 2.1 다중성 (Multiplicity)

두 엔티티가 어떤 관계를 맺고 있는지, **관계의 수**를 명확히 정하는 것이 가장 중요합니다.

| 관계 종류 | 어노테이션 | 설명 | 프로젝트 예시 |
| --- | --- | --- | --- |
| 다대일 (N:1) | `@ManyToOne` | 여러 엔티티가 하나의 엔티티를 참조 | BOARD → MEMBER (작성자) |
| 일대다 (1:N) | `@OneToMany` | 하나의 엔티티가 여러 엔티티를 참조 | (현재 프로젝트 미사용) |
| 일대일 (1:1) | `@OneToOne` | 하나의 엔티티가 하나의 엔티티를 참조 | PROFILE → MEMBER |
| 다대다 (N:M) | `@ManyToMany` | 여러 엔티티가 여러 엔티티를 참조 | (중간 엔티티로 해결) |

##### 2.2 방향성 (Direction)

**현재 프로젝트는 모두 단방향 관계를 사용**

| 방향성 | 특징 | 프로젝트 예시 |
| --- | --- | --- |
| 단방향 | 한쪽에서만 참조 | Board → Member |
| 양방향 | 양쪽 모두 참조 | (현재 미사용) |

**단방향의 장점:**
- 설계 단순, 구현 쉬움
- 관계 방향 명확
- 무한 루프 위험 없음

**양방향의 특징:**
- 양쪽 모두 참조 가능
- 연관관계의 주인 설정 필요
- `@ToString.Exclude`로 무한 루프 방지 필요

##### 2.3 연관관계의 주인 (Owner)

**현재 프로젝트는 모두 단방향이므로 주인 개념 불필요**

양방향 관계 시:
- **주인**: 외래 키가 있는 쪽 (`@JoinColumn` 사용)
- **비주인**: `mappedBy`로 주인 지정
- **규칙**: 주인에만 값 설정, 비주인은 읽기 전용

> [!important] 핵심 원리
> 다중성을 정확히 파악하는 것이 가장 중요합니다. 관계의 수를 명확히 정하면 방향성과 주인 설정은 자연스럽게 결정됩니다.

---

#### 3. 관계 유형별 매핑 방법

##### 3.1 다대일 (N:1) - 가장 많이 사용 ⭐

**프로젝트에서 사용 중:**
- `Board → Member`
- `Reply → Board`
- `Reply → Member`
- `Notice → Member`

**예시 코드:**
```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "board_writer", referencedColumnName = "userId", nullable = false)
@OnDelete(action = OnDeleteAction.CASCADE)
private Member member;
```

**핵심 포인트:**
- ✅ `fetch = FetchType.LAZY` 필수 (성능 최적화)
- ✅ `referencedColumnName`은 엔티티 필드명 사용 (camelCase)
- ✅ `@JoinColumn(name)`은 DB 컬럼명 (snake_case)
- ✅ `nullable = false`로 필수 관계 명시

**왜 LAZY를 사용하나?**
- EAGER는 N+1 문제 발생 가능
- 작성자 정보가 항상 필요한 것은 아님
- 필요할 때만 조회하는 것이 효율적

**@JoinColumn의 referencedColumnName**

**❌ 잘못된 사용:**
```java
@JoinColumn(name = "board_writer", referencedColumnName = "user_id")
```

**✅ 올바른 사용:**
```java
@JoinColumn(name = "board_writer", referencedColumnName = "userId")
```

**이유:**
- `referencedColumnName`은 **엔티티 필드명** (camelCase)
- `name`은 **DB 컬럼명** (snake_case)
- `CamelCaseToUnderscoresNamingStrategy`가 자동 변환

##### 3.2 일대일 (1:1) - 조건부 사용

**프로젝트에서 사용 중:**
- `Profile → Member`

**예시 코드:**
```java
@OneToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "user_id", referencedColumnName = "userId", nullable = false, unique = true)
@OnDelete(action = OnDeleteAction.CASCADE)
private Member member;
```

**외래 키 위치:**
- 현재 프로젝트: **대상 테이블(PROFILE)에 외래 키**
- 장점: 구조 변경에 유리
- 단점: 항상 즉시 로딩 발생 (프록시 한계)

**대안 (주 테이블에 외래 키):**
```java
// Member 엔티티에 추가
@OneToOne
@JoinColumn(name = "profile_id")
private Profile profile;
```

**선택 기준:**
- 주 테이블: 조회 편리, 매핑 간단
- 대상 테이블: 구조 변경에 유리

##### 3.3 다대다 (N:M) - 중간 엔티티로 해결 ⚠️

**프로젝트에서 사용 중:**
- `Board ↔ Tag` → `BoardTag` 중간 엔티티

**예시 코드:**
```java
@IdClass(BoardTag.BoardTagId.class)
public class BoardTag {
    @Id
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "board_no", referencedColumnName = "boardId", nullable = false)
    @OnDelete(action = OnDeleteAction.CASCADE)
    private Board board;

    @Id
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "tag_id", referencedColumnName = "tagId", nullable = false)
    @OnDelete(action = OnDeleteAction.CASCADE)
    private Tag tag;
}
```

**왜 중간 엔티티를 사용하나?**
- `@ManyToMany`는 실무에서 거의 사용 안 함
- 중간 테이블에 추가 컬럼 필요 시 대응 불가
- 복합키로 관리하여 유연성 확보

---

#### 4. 현재 프로젝트의 연관관계 구조

##### 프로젝트 엔티티 관계도

```
MEMBER (1)
  ├── BOARD (N) - 다대일 단방향
  ├── REPLY (N) - 다대일 단방향  
  ├── NOTICE (N) - 다대일 단방향
  └── PROFILE (1) - 일대일 단방향

BOARD (1)
  ├── REPLY (N) - 다대일 단방향
  └── BOARD_TAG (N) - 다대일 (중간 엔티티)

TAG (1)
  └── BOARD_TAG (N) - 다대일 (중간 엔티티)
```

##### 실제 코드 구조

**1. Board → Member (다대일 단방향)**
```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "board_writer", referencedColumnName = "userId", nullable = false)
@OnDelete(action = OnDeleteAction.CASCADE)
private Member member;
```

**특징:**
- `@ManyToOne`: 다대일 관계
- `fetch = FetchType.LAZY`: 지연 로딩 (성능 최적화)
- `@OnDelete(CASCADE)`: 회원 삭제 시 게시글도 삭제

**2. Reply → Board, Member (다대일 단방향)**
```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "ref_bno", referencedColumnName = "boardId", nullable = false)
@OnDelete(action = OnDeleteAction.CASCADE)
private Board board;

@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "reply_writer", referencedColumnName = "userId", nullable = false)
@OnDelete(action = OnDeleteAction.CASCADE)
private Member member;
```

**특징:**
- 댓글은 게시글과 작성자 모두 참조
- 양쪽 모두 CASCADE 삭제 설정

**3. Profile → Member (일대일 단방향)**
```java
@OneToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "user_id", referencedColumnName = "userId", nullable = false, unique = true)
@OnDelete(action = OnDeleteAction.CASCADE)
private Member member;
```

**특징:**
- `@OneToOne`: 일대일 관계
- `unique = true`: 외래 키에 유니크 제약조건
- 외래 키를 대상 테이블(PROFILE)에 둠

**4. BoardTag (다대다 관계의 중간 엔티티)**
```java
@IdClass(BoardTag.BoardTagId.class)
public class BoardTag {
    @Id
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "board_no", referencedColumnName = "boardId", nullable = false)
    @OnDelete(action = OnDeleteAction.CASCADE)
    private Board board;

    @Id
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "tag_id", referencedColumnName = "tagId", nullable = false)
    @OnDelete(action = OnDeleteAction.CASCADE)
    private Tag tag;
}
```

**특징:**
- 복합키 사용 (`@IdClass`)
- 다대다 관계를 중간 엔티티로 해결
- 실무 권장 패턴

---

#### 5. 실무 권장 패턴

##### ✅ 권장 사항

| 관계 유형 | 권장 여부 | 프로젝트 적용 |
| --- | --- | --- |
| 다대일 (N:1) | ⭐⭐⭐ 적극 사용 | Board, Reply, Notice |
| 일대일 (1:1) | ⭐⭐ 조건부 사용 | Profile |
| 일대다 (1:N) | ⚠️ 비권장 | 미사용 |
| 다대다 (N:M) | ❌ 사용 금지 | BoardTag로 해결 |

##### 5.1 다대일 (N:1) 중심 설계

**현재 프로젝트의 핵심 패턴:**
```java
// 모든 다대일 관계에 적용
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "...", referencedColumnName = "...", nullable = false)
@OnDelete(action = OnDeleteAction.CASCADE)
private Member member;
```

**장점:**
- 성능 최적화 (LAZY 로딩)
- 외래 키 관리 명확
- 코드 간결

##### 5.2 일대일 (1:1) 외래 키 위치 전략

**현재 프로젝트: 대상 테이블에 외래 키**
```java
// Profile 엔티티
@OneToOne
@JoinColumn(name = "user_id", unique = true)
private Member member;
```

**대안: 주 테이블에 외래 키 (권장)**
```java
// Member 엔티티에 추가
@OneToOne
@JoinColumn(name = "profile_id")
private Profile profile;
```

**선택 기준:**
- 주 테이블: 조회 편리, 매핑 간단
- 대상 테이블: 구조 변경에 유리

##### 5.3 다대다 (N:M) 중간 엔티티 필수

**프로젝트 적용:**
- `BoardTag` 엔티티로 다대다 관계 해결
- 복합키(`@IdClass`) 사용
- 추가 컬럼 확장 가능

---

#### 6. 주의사항 및 실전 팁

##### 6.1 Enum 타입 매핑

**프로젝트 적용:**
```java
@Enumerated(EnumType.STRING)
@Builder.Default
@Column(nullable = false, columnDefinition = "CHAR(1) DEFAULT 'Y'")
private Status status = Status.Y;

public enum Status {
    Y, N;
}
```

**핵심:**
- ✅ `EnumType.STRING` 사용 (가독성, 유지보수)
- ❌ `EnumType.ORDINAL` 사용 금지 (순서 변경 시 문제)

##### 6.2 CASCADE 삭제 설정

**프로젝트 적용:**
```java
@OnDelete(action = OnDeleteAction.CASCADE)
private Member member;
```

**효과:**
- 회원 삭제 시 관련 게시글, 댓글 자동 삭제
- DB 레벨에서 처리 (안전)

##### 6.3 BaseTimeEntity 활용

**프로젝트 구조:**
```java
@MappedSuperclass
public abstract class BaseTimeEntity {
    @CreationTimestamp
    @Column(updatable = false)
    private LocalDateTime createdAt;

    @UpdateTimestamp
    private LocalDateTime updatedAt;
}
```

**사용:**
- `Board`, `Reply`, `Notice`가 상속
- 공통 시간 필드 관리

##### 6.4 양방향 관계 시 주의사항 (현재 미사용)

**만약 양방향을 사용한다면:**

```java
// Board (주인)
@ManyToOne
@JoinColumn(name = "board_writer")
private Member member;

// Member (비주인)
@OneToMany(mappedBy = "member")
@ToString.Exclude // 무한 루프 방지
private List<Board> boards = new ArrayList<>();
```

**주의:**
- 주인에만 값 설정: `board.setMember(member)`
- `mappedBy`는 비주인에만 사용
- `@ToString.Exclude`로 무한 루프 방지

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 연관관계 설계 시 확인사항

- [ ] 다중성 정확히 파악했는가? (N:1, 1:1, N:M)
- [ ] 단방향/양방향 결정했는가?
- [ ] 주인 설정했는가? (양방향 시)
- [ ] `fetch = FetchType.LAZY` 설정했는가?
- [ ] `referencedColumnName`에 엔티티 필드명 사용했는가?
- [ ] CASCADE 삭제 필요 시 설정했는가?
- [ ] Enum 타입은 `EnumType.STRING` 사용했는가?

#### 실무 활용 예시

**1. 지연 로딩 활용**
```java
// Board 조회 시 Member는 조회하지 않음
Board board = boardRepository.findById(1L).orElseThrow();

// Member 접근 시에만 쿼리 실행 (지연 로딩)
String writerName = board.getMember().getUserName();
```

**2. N+1 문제 해결**
```java
// ❌ N+1 문제 발생
List<Board> boards = boardRepository.findAll();
boards.forEach(board -> board.getMember().getUserName()); // 각 Board마다 Member 조회

// ✅ Fetch Join으로 해결
@Query("SELECT b FROM Board b JOIN FETCH b.member")
List<Board> findAllWithMember();
```

**3. CASCADE 삭제 활용**
```java
// 회원 삭제 시 관련 게시글, 댓글 자동 삭제
memberRepository.delete(member);
// @OnDelete(CASCADE)로 인해 자동 처리
```

---

### 예상 꼬리질문정리

#### 1. 왜 객체 참조를 사용해야 하나요?

- **패러다임 불일치 해결**: 객체지향 세계와 관계형 데이터베이스 세계의 차이를 해결
- **자연스러운 객체 탐색**: `board.getMember().getUserName()`처럼 객체 그래프 탐색 가능
- **JPA 기능 활용**: 지연 로딩, 캐시 기능을 활용할 수 있음
- **유지보수성 향상**: 코드가 데이터베이스 설계에 종속되지 않음

#### 2. 다중성, 방향성, 주인 중 무엇이 가장 중요하나요?

- **다중성이 가장 중요**: 관계의 수를 정확히 파악하는 것이 설계의 시작
- **방향성**: 단방향이 단순하고 안전, 양방향은 필요 시에만 사용
- **주인**: 양방향 관계에서만 필요, 단방향에서는 불필요

#### 3. 왜 LAZY 로딩을 기본으로 사용하나요?

- **N+1 문제 방지**: EAGER는 연관된 모든 엔티티를 조회하여 성능 저하
- **필요 시에만 조회**: 작성자 정보가 항상 필요한 것은 아님
- **성능 최적화**: 필요한 데이터만 조회하여 메모리 효율성 향상

#### 4. @JoinColumn의 referencedColumnName은 왜 엔티티 필드명을 사용하나요?

- **JPA의 네이밍 전략**: `CamelCaseToUnderscoresNamingStrategy`가 자동으로 변환
- **엔티티 중심 설계**: JPA는 엔티티 필드명을 기준으로 동작
- **일관성 유지**: 엔티티 필드명과 매핑 정보를 일치시켜 혼란 방지

#### 5. 다대다 관계를 왜 중간 엔티티로 해결하나요?

- **@ManyToMany의 한계**: 중간 테이블에 추가 컬럼 추가 불가
- **유연성 확보**: 중간 엔티티에 추가 필드나 로직 추가 가능
- **실무 요구사항**: 대부분의 경우 중간 테이블에 추가 정보가 필요함

#### 6. 양방향 관계에서 주인은 어떻게 결정하나요?

- **외래 키가 있는 쪽이 주인**: `@JoinColumn`을 사용하는 쪽
- **일대다 관계**: 다(N) 쪽이 주인 (외래 키 보유)
- **일대일 관계**: 외래 키를 가진 쪽이 주인

#### 7. CASCADE 삭제와 JPA의 CascadeType.REMOVE의 차이는?

- **@OnDelete(CASCADE)**: DB 레벨에서 처리, 안전하고 확실
- **CascadeType.REMOVE**: JPA 레벨에서 처리, 트랜잭션 내에서만 동작
- **권장**: `@OnDelete(CASCADE)` 사용 (DB 제약조건으로 보장)

#### 8. 일대일 관계에서 외래 키 위치는 어떻게 결정하나요?

- **주 테이블에 외래 키**: 조회 편리, 매핑 간단, 지연 로딩 가능
- **대상 테이블에 외래 키**: 구조 변경에 유리, 지연 로딩 제한
- **권장**: 주 테이블에 외래 키 (실무에서 더 많이 사용)

#### 9. 연관관계 매핑 시 성능 최적화 방법은?

- **지연 로딩 사용**: `fetch = FetchType.LAZY`
- **Fetch Join 활용**: N+1 문제 해결
- **Batch Size 설정**: `@BatchSize`로 연관 엔티티 일괄 조회
- **엔티티 그래프**: `@EntityGraph`로 필요한 연관관계만 조회

#### 10. 양방향 관계에서 무한 루프를 방지하는 방법은?

- **@ToString.Exclude**: `toString()` 메서드에서 제외
- **@JsonIgnore**: JSON 직렬화 시 제외 (REST API)
- **편의 메서드 사용**: 양쪽 모두 업데이트하는 메서드 제공

---

## 요약

- **연관관계 매핑의 목적**: 객체 참조를 통해 객체지향적으로 설계하면서도, 데이터베이스의 외래 키 관계를 자동으로 관리
- **3가지 핵심 요소**: 다중성(가장 중요), 방향성, 연관관계의 주인(양방향 시)
- **다대일(N:1) 중심 설계**: 실무에서 가장 많이 사용, `fetch = FetchType.LAZY` 필수
- **지연 로딩 활용**: 성능 최적화와 N+1 문제 방지를 위해 LAZY 로딩 기본 사용
- **다대다 관계**: `@ManyToMany` 대신 중간 엔티티로 해결
- **@JoinColumn 설정**: `name`은 DB 컬럼명, `referencedColumnName`은 엔티티 필드명
- **CASCADE 삭제**: `@OnDelete(CASCADE)`로 DB 레벨에서 안전하게 처리

---

## 참고 자료

- [JPA 공식 문서 - 연관관계 매핑](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#associations)
- [김영한 - JPA 프로그래밍 기본편](https://www.inflearn.com/course/ORM-JPA-Basic)
- [Baeldung - JPA Relationships](https://www.baeldung.com/jpa-relationships)

---

## 관련 노트

- **[[JPA 엔티티 매핑|JPA 엔티티 매핑]]** - 엔티티 기본 매핑 방법
- **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]** - JPA의 기본 개념과 필요성
- **[[N+1 문제 (JPA)|N+1 문제]]** - 연관관계 매핑 시 발생하는 성능 문제와 해결 방법

---

**태그:** #jpa #연관관계 #매핑 #실무 #면접 #개념정리
