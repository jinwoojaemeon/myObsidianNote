#sql #join #inner-join #outer-join #데이터베이스 #면접 #개념정리

**관련 개념:** [[RDBMS (관계형 데이터베이스 관리 시스템)|RDBMS]] [[트랜잭션 (Transaction)|트랜잭션]]

> [!note] 이어서 읽기
> Join을 이해하려면 **[[RDBMS (관계형 데이터베이스 관리 시스템)|RDBMS]]**의 기본 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**Join(조인)**은 두 개 이상의 테이블을 연결하여 데이터를 조회하는 SQL 문법이다. Inner Join은 두 테이블의 교집합만 반환하고, Outer Join은 한쪽 테이블의 모든 데이터와 다른 쪽 테이블의 매칭되는 데이터를 반환한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- Join 없이 여러 테이블의 데이터를 조회하려면 각 테이블을 개별적으로 조회한 후 애플리케이션 레벨에서 데이터를 결합해야 하며, 이는 여러 번의 쿼리 실행과 네트워크 비용을 발생시킵니다. 또한 데이터 정합성을 보장하기 어렵고, 대량의 데이터를 메모리에서 처리해야 하는 부담이 있습니다.

- Join을 사용하면 데이터베이스 레벨에서 한 번의 쿼리로 여러 테이블의 데이터를 효율적으로 조회할 수 있습니다. 데이터베이스 엔진이 최적화된 방식으로 데이터를 결합하여 성능을 향상시키고, 데이터 정합성을 보장합니다.

- Inner Join과 Outer Join의 차이, 각 Join의 종류와 사용 시나리오, 그리고 Join의 성능 최적화 방법을 이해해야 합니다.

---

## 1. Join이란 무엇인가?

### **Join의 정의**

**Join(조인)**은 두 개 이상의 테이블을 연결하여 데이터를 조회하는 SQL 문법입니다.

**핵심 개념:**
- 두 테이블 간의 관계를 기반으로 데이터를 결합
- 공통 컬럼(보통 외래키)을 기준으로 연결
- 한 번의 쿼리로 여러 테이블의 데이터 조회

### **Join이 필요한 이유**

**Join 없이:**
```sql
-- 사용자 정보 조회
SELECT * FROM users WHERE id = 1;

-- 주문 정보 조회 (별도 쿼리)
SELECT * FROM orders WHERE user_id = 1;
```

**문제점:**
- 여러 번의 쿼리 실행
- 애플리케이션 레벨에서 데이터 결합 필요
- 네트워크 비용 증가

**Join 사용:**
```sql
-- 한 번의 쿼리로 사용자와 주문 정보 조회
SELECT u.*, o.*
FROM users u
JOIN orders o ON u.id = o.user_id
WHERE u.id = 1;
```

**장점:**
- 한 번의 쿼리로 데이터 조회
- 데이터베이스 레벨에서 최적화된 결합
- 데이터 정합성 보장

---

## 2. Inner Join (내부 조인)

### **Inner Join의 정의**

**Inner Join(내부 조인)**은 두 테이블의 **교집합**만 반환합니다. 즉, 양쪽 테이블 모두에 매칭되는 데이터만 조회합니다.

### **Inner Join 문법**

```sql
-- 명시적 Inner Join
SELECT *
FROM table1
INNER JOIN table2 ON table1.id = table2.table1_id;

-- 암시적 Inner Join (WHERE 절 사용)
SELECT *
FROM table1, table2
WHERE table1.id = table2.table1_id;
```

### **Inner Join 예시**

**테이블 구조:**
```
users 테이블          orders 테이블
id | name           id | user_id | product
1  | 홍길동         1  | 1       | 노트북
2  | 김철수         2  | 1       | 마우스
3  | 이영희         3  | 3       | 키보드
```

**Inner Join 쿼리:**
```sql
SELECT u.name, o.product
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

**결과:**
```
name  | product
홍길동 | 노트북
홍길동 | 마우스
이영희 | 키보드
```

**특징:**
- `users` 테이블의 `김철수`는 주문이 없어서 제외됨
- 양쪽 테이블 모두에 매칭되는 데이터만 반환

### **Inner Join의 특징**

- ✅ 양쪽 테이블 모두에 매칭되는 데이터만 반환
- ✅ NULL 값이 포함되지 않음
- ✅ 가장 일반적으로 사용되는 Join
- ✅ 성능이 가장 좋음

---

## 3. Outer Join (외부 조인)

### **Outer Join의 정의**

**Outer Join(외부 조인)**은 한쪽 테이블의 **모든 데이터**와 다른 쪽 테이블의 매칭되는 데이터를 반환합니다. 매칭되지 않는 데이터는 NULL로 표시됩니다.

### **Outer Join의 종류**

#### 1. LEFT OUTER JOIN (LEFT JOIN)

**LEFT JOIN**은 왼쪽 테이블의 모든 데이터와 오른쪽 테이블의 매칭되는 데이터를 반환합니다.

**문법:**
```sql
SELECT *
FROM table1
LEFT OUTER JOIN table2 ON table1.id = table2.table1_id;

-- OUTER 키워드는 생략 가능
SELECT *
FROM table1
LEFT JOIN table2 ON table1.id = table2.table1_id;
```

**예시:**
```sql
SELECT u.name, o.product
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

**결과:**
```
name  | product
홍길동 | 노트북
홍길동 | 마우스
김철수 | NULL      ← 주문이 없어도 포함됨
이영희 | 키보드
```

**특징:**
- 왼쪽 테이블(`users`)의 모든 데이터 포함
- 오른쪽 테이블(`orders`)에 매칭되지 않으면 NULL

#### 2. RIGHT OUTER JOIN (RIGHT JOIN)

**RIGHT JOIN**은 오른쪽 테이블의 모든 데이터와 왼쪽 테이블의 매칭되는 데이터를 반환합니다.

**문법:**
```sql
SELECT *
FROM table1
RIGHT OUTER JOIN table2 ON table1.id = table2.table1_id;

-- OUTER 키워드는 생략 가능
SELECT *
FROM table1
RIGHT JOIN table2 ON table1.id = table2.table1_id;
```

**예시:**
```sql
SELECT u.name, o.product
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

**결과:**
```
name  | product
홍길동 | 노트북
홍길동 | 마우스
이영희 | 키보드
NULL  | 모니터    ← users에 없는 주문도 포함됨
```

**특징:**
- 오른쪽 테이블(`orders`)의 모든 데이터 포함
- 왼쪽 테이블(`users`)에 매칭되지 않으면 NULL

#### 3. FULL OUTER JOIN (FULL JOIN)

**FULL JOIN**은 양쪽 테이블의 모든 데이터를 반환합니다.

**문법:**
```sql
SELECT *
FROM table1
FULL OUTER JOIN table2 ON table1.id = table2.table1_id;

-- OUTER 키워드는 생략 가능
SELECT *
FROM table1
FULL JOIN table2 ON table1.id = table2.table1_id;
```

**예시:**
```sql
SELECT u.name, o.product
FROM users u
FULL JOIN orders o ON u.id = o.user_id;
```

**결과:**
```
name  | product
홍길동 | 노트북
홍길동 | 마우스
김철수 | NULL      ← users에만 있는 데이터
이영희 | 키보드
NULL  | 모니터    ← orders에만 있는 데이터
```

**특징:**
- 양쪽 테이블의 모든 데이터 포함
- 매칭되지 않는 데이터는 NULL로 표시

> [!note] 주의사항
> MySQL은 FULL OUTER JOIN을 지원하지 않습니다. 대신 LEFT JOIN과 RIGHT JOIN을 UNION으로 결합하여 사용합니다.

---

## 4. Join 비교

### **Join 종류 비교**

| Join 종류 | 설명 | 결과 |
|----------|------|------|
| **INNER JOIN** | 양쪽 테이블의 교집합 | 양쪽 모두에 매칭되는 데이터만 |
| **LEFT JOIN** | 왼쪽 테이블 전체 + 오른쪽 매칭 | 왼쪽 테이블의 모든 데이터 |
| **RIGHT JOIN** | 오른쪽 테이블 전체 + 왼쪽 매칭 | 오른쪽 테이블의 모든 데이터 |
| **FULL JOIN** | 양쪽 테이블 전체 | 양쪽 테이블의 모든 데이터 |

### **시각적 비교**

```
users 테이블          orders 테이블
[1, 2, 3]           [1→1, 2→1, 3→3, 4→NULL]

INNER JOIN: [1, 3]        (교집합)
LEFT JOIN:  [1, 2, 3]     (왼쪽 전체)
RIGHT JOIN: [1, 1, 3, 4]  (오른쪽 전체)
FULL JOIN:  [1, 2, 3, 4]  (합집합)
```

---

## 5. Join 사용 시나리오

### **INNER JOIN 사용 시나리오**

**사용 경우:**
- 양쪽 테이블 모두에 데이터가 있는 경우만 필요할 때
- 주문이 있는 사용자만 조회
- 댓글이 있는 게시물만 조회

**예시:**
```sql
-- 주문이 있는 사용자만 조회
SELECT u.name, COUNT(o.id) as order_count
FROM users u
INNER JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.name;
```

### **LEFT JOIN 사용 시나리오**

**사용 경우:**
- 한쪽 테이블의 모든 데이터가 필요하고, 다른 쪽은 선택적일 때
- 모든 사용자를 조회하되 주문 정보가 있으면 함께 표시
- 모든 게시물을 조회하되 댓글 수 표시

**예시:**
```sql
-- 모든 사용자를 조회하되 주문 정보 포함
SELECT u.name, COALESCE(COUNT(o.id), 0) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.name;
```

### **RIGHT JOIN 사용 시나리오**

**사용 경우:**
- 오른쪽 테이블의 모든 데이터가 필요하고, 왼쪽은 선택적일 때
- 모든 주문을 조회하되 사용자 정보 포함

**예시:**
```sql
-- 모든 주문을 조회하되 사용자 정보 포함
SELECT o.id, o.product, COALESCE(u.name, '알 수 없음') as user_name
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

### **FULL JOIN 사용 시나리오**

**사용 경우:**
- 양쪽 테이블의 모든 데이터가 필요할 때
- 데이터 정합성 검증
- 양쪽 테이블의 차이점 확인

**예시:**
```sql
-- 양쪽 테이블의 모든 데이터 확인
SELECT 
    COALESCE(u.id, o.user_id) as id,
    u.name,
    o.product
FROM users u
FULL JOIN orders o ON u.id = o.user_id
WHERE u.id IS NULL OR o.user_id IS NULL;  -- 매칭되지 않는 데이터만
```

---

## 6. Join 성능 최적화

### **인덱스 활용**

**조인 조건에 인덱스가 없으면:**
```sql
-- 느린 쿼리 (인덱스 없음)
SELECT *
FROM users u
JOIN orders o ON u.id = o.user_id;  -- user_id에 인덱스 없음
```

**조인 조건에 인덱스 생성:**
```sql
-- 인덱스 생성
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- 빠른 쿼리
SELECT *
FROM users u
JOIN orders o ON u.id = o.user_id;  -- 인덱스 활용
```

### **조인 순서 최적화**

**작은 테이블을 먼저 조인:**
```sql
-- 좋은 예: 작은 테이블 먼저
SELECT *
FROM small_table s
JOIN large_table l ON s.id = l.small_id;
```

### **불필요한 컬럼 제거**

```sql
-- 나쁜 예: 모든 컬럼 조회
SELECT *
FROM users u
JOIN orders o ON u.id = o.user_id;

-- 좋은 예: 필요한 컬럼만 조회
SELECT u.name, o.product, o.order_date
FROM users u
JOIN orders o ON u.id = o.user_id;
```

---

## 요약

- **Join**은 두 개 이상의 테이블을 연결하여 데이터를 조회하는 SQL 문법입니다.
- **INNER JOIN**: 양쪽 테이블의 교집합만 반환합니다. 양쪽 모두에 매칭되는 데이터만 조회합니다.
- **LEFT JOIN**: 왼쪽 테이블의 모든 데이터와 오른쪽 테이블의 매칭되는 데이터를 반환합니다.
- **RIGHT JOIN**: 오른쪽 테이블의 모든 데이터와 왼쪽 테이블의 매칭되는 데이터를 반환합니다.
- **FULL JOIN**: 양쪽 테이블의 모든 데이터를 반환합니다. (MySQL 미지원)
- **성능 최적화**: 조인 조건에 인덱스 생성, 작은 테이블을 먼저 조인, 필요한 컬럼만 조회

---

### 설정 시 반드시 고려해야 할 파라미터

- **인덱스 생성**: 조인 조건에 사용되는 컬럼에 인덱스를 생성하여 성능을 향상시킵니다.
  ```sql
  CREATE INDEX idx_orders_user_id ON orders(user_id);
  ```

- **조인 순서**: 작은 테이블을 먼저 조인하여 성능을 최적화합니다.

- **NULL 처리**: OUTER JOIN 사용 시 NULL 값 처리를 고려해야 합니다.
  ```sql
  SELECT u.name, COALESCE(o.product, '주문 없음') as product
  FROM users u
  LEFT JOIN orders o ON u.id = o.user_id;
  ```

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "INNER JOIN과 OUTER JOIN의 성능 차이는 크지 않다"는 생각은 잘못되었습니다. OUTER JOIN은 INNER JOIN보다 일반적으로 느립니다. 또한 "LEFT JOIN과 RIGHT JOIN은 단순히 방향만 다른 것이다"는 생각도 정확하지 않습니다. LEFT JOIN과 RIGHT JOIN은 반대 방향이지만, 실제 사용 시나리오와 결과가 다를 수 있습니다.

#### Join 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **데이터 누락**: INNER JOIN을 잘못 사용하여 필요한 데이터가 누락될 수 있습니다.
> - **성능 저하**: 조인 조건에 인덱스가 없으면 대량의 데이터 조회 시 성능이 크게 저하됩니다.
> - **중복 데이터**: 조인 결과에 중복 데이터가 발생할 수 있습니다 (DISTINCT 사용 필요).
> - **NULL 처리 오류**: OUTER JOIN 사용 시 NULL 값 처리를 하지 않아 예상치 못한 결과가 발생할 수 있습니다.
> - **카테시안 곱**: WHERE 절 없이 조인하면 모든 행의 조합이 반환되어 성능 문제가 발생합니다.

---

### 출처

- [SQL JOIN - W3Schools](https://www.w3schools.com/sql/sql_join.asp)
- [MySQL JOIN - MySQL Documentation](https://dev.mysql.com/doc/refman/8.0/en/join.html)

---

### 예상 꼬리질문정리

#### 1. INNER JOIN과 OUTER JOIN의 차이는 무엇인가요?

- **INNER JOIN**: 양쪽 테이블의 교집합만 반환합니다. 양쪽 모두에 매칭되는 데이터만 조회합니다.
- **OUTER JOIN**: 한쪽 테이블의 모든 데이터와 다른 쪽 테이블의 매칭되는 데이터를 반환합니다. 매칭되지 않는 데이터는 NULL로 표시됩니다.

#### 2. LEFT JOIN과 RIGHT JOIN의 차이는 무엇인가요?

- **LEFT JOIN**: 왼쪽 테이블의 모든 데이터와 오른쪽 테이블의 매칭되는 데이터를 반환합니다. 오른쪽에 매칭되지 않으면 NULL로 표시됩니다.
- **RIGHT JOIN**: 오른쪽 테이블의 모든 데이터와 왼쪽 테이블의 매칭되는 데이터를 반환합니다. 왼쪽에 매칭되지 않으면 NULL로 표시됩니다.

#### 3. FULL OUTER JOIN은 무엇인가요?

- **FULL OUTER JOIN**: 양쪽 테이블의 모든 데이터를 반환합니다. 양쪽 모두에 매칭되지 않는 데이터도 포함되며, 매칭되지 않는 부분은 NULL로 표시됩니다. MySQL은 FULL OUTER JOIN을 지원하지 않으므로, LEFT JOIN과 RIGHT JOIN을 UNION으로 결합하여 사용합니다.

#### 4. Join의 성능을 최적화하는 방법은 무엇인가요?

- **인덱스 활용**: 조인 조건에 사용되는 컬럼에 인덱스를 생성하여 성능을 향상시킵니다.
- **조인 순서**: 작은 테이블을 먼저 조인하여 성능을 최적화합니다.
- **불필요한 컬럼 제거**: 필요한 컬럼만 조회하여 네트워크 비용을 줄입니다.
- **WHERE 절 활용**: 조인 전에 필터링하여 조인할 데이터 양을 줄입니다.

#### 5. INNER JOIN을 사용해야 하는 경우는 언제인가요?

- 양쪽 테이블 모두에 데이터가 있는 경우만 필요할 때
- 주문이 있는 사용자만 조회할 때
- 댓글이 있는 게시물만 조회할 때
- 데이터 정합성이 중요한 경우

#### 6. LEFT JOIN을 사용해야 하는 경우는 언제인가요?

- 한쪽 테이블의 모든 데이터가 필요하고, 다른 쪽은 선택적일 때
- 모든 사용자를 조회하되 주문 정보가 있으면 함께 표시할 때
- 모든 게시물을 조회하되 댓글 수를 표시할 때
- 통계 데이터를 조회할 때 (0건도 포함)

#### 7. Join 시 NULL 값은 어떻게 처리하나요?

- `COALESCE` 함수를 사용하여 NULL 값을 기본값으로 대체합니다.
  ```sql
  SELECT u.name, COALESCE(o.product, '주문 없음') as product
  FROM users u
  LEFT JOIN orders o ON u.id = o.user_id;
  ```
- `IS NULL` 조건을 사용하여 NULL 값만 필터링합니다.
  ```sql
  SELECT *
  FROM users u
  LEFT JOIN orders o ON u.id = o.user_id
  WHERE o.user_id IS NULL;  -- 주문이 없는 사용자만
  ```

---

## 관련 노트

- **[[RDBMS (관계형 데이터베이스 관리 시스템)|RDBMS]]** - Join이 사용되는 관계형 데이터베이스의 기본 개념
- **[[트랜잭션 (Transaction)|트랜잭션]]** - Join과 함께 사용되는 트랜잭션 개념

---

**태그:** #sql #join #inner-join #outer-join #데이터베이스 #면접 #개념정리
