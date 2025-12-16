#sql #database #rdbms #관계형데이터베이스 #면접 #개념정리

**관련 개념:** [[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]] [[H2 Database (H2 데이터베이스)|H2 Database]] [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]

> [!note] 이어서 읽기
> RDBMS를 이해하려면 **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

RDBMS(관계형 데이터베이스 관리 시스템)가 무엇인지, 왜 사용하는지, 그리고 관계형 데이터베이스의 핵심 개념과 특징을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 파일 시스템이나 스프레드시트로 데이터를 관리하면 데이터 중복, 일관성 부족, 무결성 문제가 발생합니다. 대규모 애플리케이션에서 수동으로 데이터를 관리하는 것은 거의 불가능하며, 데이터 무결성과 일관성을 보장할 수 없어 심각한 비즈니스 문제로 이어집니다.

- RDBMS는 관계형 모델을 기반으로 데이터를 체계적으로 관리하여 데이터 중복을 최소화하고, ACID 속성을 통해 데이터 무결성과 일관성을 보장합니다. SQL이라는 표준화된 언어를 통해 복잡한 데이터 조회와 조작을 효율적으로 수행할 수 있으며, 트랜잭션 관리, 동시성 제어, 보안 기능을 제공하여 엔터프라이즈급 애플리케이션의 핵심 인프라가 됩니다.

- 관계형 데이터베이스의 기본 개념, 정규화 원리, ACID 속성, SQL의 역할, 그리고 NoSQL과의 차이점을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. RDBMS란?

**RDBMS(Relational Database Management System, 관계형 데이터베이스 관리 시스템)**는 관계형 모델을 기반으로 데이터를 저장하고 관리하는 소프트웨어 시스템입니다.

**RDBMS의 구성 요소:**
- **데이터베이스(Database)**: 관련된 데이터의 집합
- **테이블(Table)**: 행(Row)과 열(Column)로 구성된 데이터 구조
- **관계(Relationship)**: 테이블 간의 연결 (외래 키)
- **SQL(Structured Query Language)**: 데이터 조작 언어

**주요 RDBMS:**
- **MySQL**: 오픈소스, 웹 애플리케이션에 널리 사용
- **PostgreSQL**: 고급 기능을 제공하는 오픈소스 RDBMS
- **Oracle**: 엔터프라이즈급 상용 RDBMS
- **SQL Server**: Microsoft의 상용 RDBMS
- **SQLite**: 경량 임베디드 데이터베이스

> [!tip] 핵심 포인트
> RDBMS는 관계형 모델을 통해 데이터를 체계적으로 관리하고, SQL을 통해 데이터를 조작하며, ACID 속성을 통해 데이터 무결성을 보장합니다.

---

#### 2. 관계형 데이터베이스의 핵심 개념

##### 2.1 테이블(Table)

**테이블**은 행(Row)과 열(Column)로 구성된 2차원 구조입니다.

```
┌─────────┬──────────┬─────────┬──────────┐
│ user_id │ username │  email  │   age    │  ← 열(Column)
├─────────┼──────────┼─────────┼──────────┤
│    1    │  John    │ j@...   │   25     │  ← 행(Row)
│    2    │  Jane    │ j@...   │   30     │
│    3    │  Bob     │ b@...   │   28     │
└─────────┴──────────┴─────────┴──────────┘
```

**용어 정리:**
- **행(Row)**: 하나의 레코드, 튜플(Tuple)
- **열(Column)**: 하나의 속성, 필드(Field)
- **스키마(Schema)**: 테이블 구조 정의
- **도메인(Domain)**: 각 열이 가질 수 있는 값의 범위

##### 2.2 기본 키(Primary Key, PK)

**기본 키**는 테이블의 각 행을 고유하게 식별하는 열(또는 열의 조합)입니다.

**특징:**
- ✅ **유일성(Unique)**: 중복된 값 불가
- ✅ **비어있지 않음(NOT NULL)**: NULL 값 불가
- ✅ **불변성**: 한 번 설정되면 변경하지 않는 것이 좋음

**예시:**
```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,  -- 기본 키
    username VARCHAR(50),
    email VARCHAR(100)
);
```

##### 2.3 외래 키(Foreign Key, FK)

**외래 키**는 다른 테이블의 기본 키를 참조하는 열입니다.

**목적:**
- 테이블 간 관계를 정의
- 참조 무결성(Referential Integrity) 보장
- 데이터 중복 최소화

**예시:**
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    user_id INT,  -- 외래 키
    product_name VARCHAR(100),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

**관계 표현:**
```
users (1) ────────< (N) orders
  PK: user_id         FK: user_id
```

##### 2.4 관계(Relationship)

테이블 간의 관계는 세 가지 유형이 있습니다.

**1. 일대일(1:1) 관계:**
```
users (1) ──────── (1) profiles
  PK: user_id         FK: user_id
```

**2. 일대다(1:N) 관계:**
```
users (1) ────────< (N) orders
  PK: user_id         FK: user_id
```

**3. 다대다(N:M) 관계:**
```
students (N) ────< (N) courses
                    중간 테이블: enrollments
```

> [!important] 핵심 원리
> 관계형 데이터베이스는 외래 키를 통해 테이블 간 관계를 정의하고, 이를 통해 데이터 중복을 최소화하며 참조 무결성을 보장합니다.

---

#### 3. 정규화(Normalization)

**정규화**는 데이터 중복을 제거하고 데이터 무결성을 향상시키기 위해 데이터베이스를 재구성하는 과정입니다.

##### 정규화의 목적

- ✅ 데이터 중복 제거
- ✅ 데이터 일관성 보장
- ✅ 저장 공간 효율화
- ✅ 데이터 무결성 향상

##### 정규화 단계

**제1정규형(1NF):**
- 각 열은 원자값(Atomic Value)만 가져야 함
- 중복된 행이 없어야 함

**제2정규형(2NF):**
- 1NF를 만족
- 부분 함수 종속 제거 (기본 키의 일부에만 종속되는 속성 제거)

**제3정규형(3NF):**
- 2NF를 만족
- 이행 함수 종속 제거 (기본 키가 아닌 속성에 종속되는 속성 제거)

**정규화 예시:**

**정규화 전 (비정규화):**
```
┌─────────┬──────────┬─────────────┬──────────────┐
│ order_id│ user_name│ product_name│ product_price│
├─────────┼──────────┼─────────────┼──────────────┤
│    1    │   John   │   Laptop    │    1000      │
│    1    │   John   │   Mouse     │     20      │
│    2    │   Jane   │   Keyboard  │     50      │
└─────────┴──────────┴─────────────┴──────────────┘
```

**정규화 후:**
```
orders 테이블:
┌─────────┬──────────┐
│ order_id│ user_id  │
├─────────┼──────────┤
│    1    │    1     │
│    2    │    2     │
└─────────┴──────────┘

order_items 테이블:
┌─────────┬─────────────┬──────────────┐
│ order_id│ product_name│ product_price│
├─────────┼─────────────┼──────────────┤
│    1    │   Laptop    │    1000      │
│    1    │   Mouse     │     20      │
│    2    │   Keyboard  │     50      │
└─────────┴─────────────┴──────────────┘
```

---

#### 4. ACID 속성

**ACID**는 트랜잭션이 안전하게 수행되도록 보장하는 네 가지 속성입니다.

##### 4.1 원자성(Atomicity)

**원자성**은 트랜잭션의 모든 작업이 모두 성공하거나 모두 실패하는 것을 보장합니다.

**예시:**
```sql
BEGIN TRANSACTION;
    UPDATE accounts SET balance = balance - 100 WHERE id = 1;
    UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;  -- 모두 성공 시 커밋
-- 또는
ROLLBACK;  -- 하나라도 실패 시 롤백
```

##### 4.2 일관성(Consistency)

**일관성**은 트랜잭션 전후로 데이터베이스가 항상 일관된 상태를 유지하는 것을 보장합니다.

**예시:**
- 외래 키 제약조건 위반 시 트랜잭션 실패
- 체크 제약조건 위반 시 트랜잭션 실패

##### 4.3 격리성(Isolation)

**격리성**은 동시에 실행되는 트랜잭션들이 서로 영향을 주지 않도록 격리하는 것을 보장합니다.

**격리 수준:**
- **READ UNCOMMITTED**: 가장 낮은 수준, Dirty Read 가능
- **READ COMMITTED**: 커밋된 데이터만 읽기 (대부분의 RDBMS 기본값)
- **REPEATABLE READ**: 같은 쿼리를 반복해도 같은 결과
- **SERIALIZABLE**: 가장 높은 수준, 완전한 격리

##### 4.4 지속성(Durability)

**지속성**은 커밋된 트랜잭션의 결과가 영구적으로 저장되는 것을 보장합니다.

**예시:**
- 시스템 장애가 발생해도 커밋된 데이터는 유지됨
- 로그 파일에 기록하여 복구 가능

> [!important] 핵심 정리
> ACID 속성은 데이터베이스의 신뢰성과 일관성을 보장하는 핵심 원리입니다. 트랜잭션을 통해 데이터 무결성을 유지합니다.

---

#### 5. SQL(Structured Query Language)

**SQL**은 관계형 데이터베이스에서 데이터를 조작하기 위한 표준 언어입니다.

##### SQL의 역할

- **데이터 정의(DDL)**: 테이블 생성, 수정, 삭제
- **데이터 조작(DML)**: 데이터 조회, 삽입, 수정, 삭제
- **데이터 제어(DCL)**: 권한 관리, 트랜잭션 제어

##### 주요 SQL 문법

**DDL (Data Definition Language):**
```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE
);

ALTER TABLE users ADD COLUMN age INT;
DROP TABLE users;
```

**DML (Data Manipulation Language):**
```sql
-- 조회
SELECT * FROM users WHERE age > 25;

-- 삽입
INSERT INTO users (user_id, username, email) VALUES (1, 'John', 'john@example.com');

-- 수정
UPDATE users SET age = 30 WHERE user_id = 1;

-- 삭제
DELETE FROM users WHERE user_id = 1;
```

**DCL (Data Control Language):**
```sql
GRANT SELECT ON users TO 'user1';
REVOKE SELECT ON users FROM 'user1';
```

---

#### 6. RDBMS vs NoSQL

| 구분         | RDBMS             | NoSQL                     |
| ---------- | ----------------- | ------------------------- |
| **데이터 모델** | 관계형 (테이블          | 비관계형 (문서, 키-값, 그래프 등)     |
| **스키마**    | 고정된 스키마 (정규화)     | 유연한 스키마 (비정규화)            |
| **ACID**   | 완전한 ACID 지원       | 제한적 ACID (일부만 지원)         |
| **확장성**    | 수직 확장 (Scale Up)  | 수평 확장 (Scale Out)         |
| **사용 사례**  | 구조화된 데이터, 복잡한 쿼리  | 대용량 데이터, 빠른 읽기/쓰기         |
| **예시**     | MySQL, PostgreSQL | MongoDB, Redis, Cassandra |

**RDBMS를 선택하는 경우:**
- ✅ 구조화된 데이터
- ✅ 복잡한 쿼리와 조인
- ✅ 데이터 무결성이 중요한 경우
- ✅ 트랜잭션이 중요한 경우

**NoSQL을 선택하는 경우:**
- ✅ 대용량 데이터 처리
- ✅ 빠른 읽기/쓰기 성능
- ✅ 유연한 스키마
- ✅ 수평 확장이 필요한 경우

---

#### 장점/단점

**장점:**
- 데이터 무결성 보장 (ACID 속성)
- 표준화된 SQL 언어
- 복잡한 쿼리와 조인 지원
- 참조 무결성 보장
- 정규화를 통한 데이터 중복 최소화
- 검증된 기술과 풍부한 도구

**단점:**
- 수평 확장의 어려움 (Scale Out)
- 복잡한 스키마 변경의 어려움
- 대용량 데이터 처리 시 성능 저하 가능
- 비정규화된 데이터 저장의 비효율성

---

#### 필요 조건

- **데이터베이스 서버**: MySQL, PostgreSQL 등 RDBMS 설치
- **SQL 언어 이해**: 데이터 조작을 위한 SQL 문법 학습
- **스키마 설계 능력**: 정규화와 관계 설계 이해
- **트랜잭션 이해**: ACID 속성과 트랜잭션 관리
- **인덱싱 이해**: 성능 최적화를 위한 인덱스 설계

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 데이터베이스 설계 시 확인사항

- [ ] 기본 키가 모든 테이블에 정의되어 있는가?
- [ ] 외래 키 제약조건이 올바르게 설정되었는가?
- [ ] 정규화가 적절히 수행되었는가?
- [ ] 인덱스가 필요한 열에 설정되었는가?
- [ ] 트랜잭션 경계가 올바르게 설정되었는가?

#### 실무 활용 예시

**1. 테이블 설계 예시**

```sql
-- 사용자 테이블
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 주문 테이블
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);

-- 주문 상세 테이블
CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_name VARCHAR(100) NOT NULL,
    quantity INT NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE
);
```

**2. 트랜잭션 사용 예시**

```sql
BEGIN TRANSACTION;
    -- 계좌 이체
    UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
    UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
    
    -- 잔액 확인
    SELECT balance FROM accounts WHERE account_id = 1;
    
    -- 문제 없으면 커밋
    COMMIT;
    
    -- 문제 있으면 롤백
    -- ROLLBACK;
```

**3. 인덱스 활용**

```sql
-- 인덱스 생성 (조회 성능 향상)
CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_order_date ON orders(order_date);

-- 복합 인덱스
CREATE INDEX idx_user_order ON orders(user_id, order_date);
```

---

### 설정 시 반드시 고려해야 할 파라미터

- **연결 풀 설정**:
  - 최대 연결 수: `max_connections`
  - 연결 타임아웃: `connection_timeout`
  - 예: MySQL의 `max_connections = 200`

- **트랜잭션 격리 수준**:
  - `READ COMMITTED` (기본값 권장)
  - `REPEATABLE READ`
  - `SERIALIZABLE`
	 
- **인덱스 전략**:
  - 자주 조회되는 열에 인덱스 생성
  - 외래 키에 자동 인덱스 생성
  - 과도한 인덱스는 쓰기 성능 저하

- **백업 및 복구**:
  - 정기적인 백업 스케줄 설정
  - 트랜잭션 로그 관리
  - 복구 시나리오 테스트

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "RDBMS는 모든 데이터베이스 문제를 해결할 수 있다"는 생각은 위험합니다. 대용량 비정형 데이터나 실시간 분석에는 NoSQL이 더 적합할 수 있습니다. 또한 "정규화를 무조건 많이 해야 한다"는 생각도 잘못되었습니다. 과도한 정규화는 조인 비용을 증가시켜 성능 저하를 일으킬 수 있습니다.
> 
> "인덱스를 많이 만들면 성능이 좋아진다"는 생각도 잘못되었습니다. 인덱스는 조회 성능을 향상시키지만, INSERT/UPDATE/DELETE 성능을 저하시킵니다. 필요한 곳에만 적절히 인덱스를 생성하는 것이 중요합니다.

#### RDBMS 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **데이터 중복**: 정규화 없이 설계하여 데이터 중복 발생
> - **데이터 무결성 문제**: 제약조건 없이 설계하여 잘못된 데이터 저장
> - **성능 저하**: 인덱스 없이 설계하여 조회 성능 저하
> - **트랜잭션 문제**: 트랜잭션 경계를 잘못 설정하여 데이터 일관성 문제
> - **확장성 문제**: 수평 확장의 어려움을 고려하지 않은 설계
> - **보안 취약점**: 권한 관리 없이 모든 사용자에게 모든 권한 부여

---

## 요약

- **RDBMS의 정의와 역할**: RDBMS는 관계형 모델을 기반으로 데이터를 저장하고 관리하는 시스템으로, 테이블, 관계, SQL을 통해 데이터를 체계적으로 관리합니다.

- **관계형 데이터베이스의 핵심 개념**: 테이블, 기본 키, 외래 키, 관계를 통해 데이터를 구조화하고, 정규화를 통해 데이터 중복을 최소화합니다.

- **ACID 속성**: 원자성, 일관성, 격리성, 지속성을 통해 트랜잭션의 안전성을 보장하고 데이터 무결성을 유지합니다.

- **SQL의 역할**: 표준화된 SQL 언어를 통해 데이터 정의, 조작, 제어를 수행하며, 복잡한 쿼리와 조인을 지원합니다.

- **RDBMS vs NoSQL**: 구조화된 데이터와 복잡한 쿼리가 필요한 경우 RDBMS를, 대용량 데이터와 수평 확장이 필요한 경우 NoSQL을 선택합니다.

- RDBMS는 엔터프라이즈급 애플리케이션의 핵심 인프라로, 데이터 무결성과 일관성을 보장하는 검증된 기술입니다.
- 정규화와 인덱싱을 적절히 활용하면 성능과 데이터 품질을 모두 확보할 수 있습니다.
- 트랜잭션과 ACID 속성을 이해하면 데이터베이스의 신뢰성을 보장할 수 있습니다.

---

### 예상 꼬리질문정리

#### 1. RDBMS와 DBMS의 차이는 무엇인가요?

- **DBMS**: 데이터베이스를 관리하는 시스템의 일반적인 용어
- **RDBMS**: 관계형 모델을 기반으로 한 DBMS의 한 종류
- **관계**: RDBMS는 DBMS의 하위 개념으로, 관계형 데이터베이스를 관리하는 시스템

#### 2. 기본 키와 외래 키의 차이와 역할은?

- **기본 키**: 테이블의 각 행을 고유하게 식별하는 열, 유일성과 NOT NULL 제약조건
- **외래 키**: 다른 테이블의 기본 키를 참조하는 열, 테이블 간 관계 정의
- **역할**: 기본 키는 행 식별, 외래 키는 관계 정의와 참조 무결성 보장

#### 3. 정규화를 왜 해야 하나요?

- **데이터 중복 제거**: 저장 공간 효율화
- **데이터 일관성 보장**: 중복된 데이터의 불일치 방지
- **데이터 무결성 향상**: 업데이트 시 모든 관련 데이터 일괄 수정
- **주의**: 과도한 정규화는 조인 비용 증가로 성능 저하 가능

#### 4. ACID 속성의 각 항목을 설명해주세요.

- **원자성(Atomicity)**: 트랜잭션의 모든 작업이 모두 성공하거나 모두 실패
- **일관성(Consistency)**: 트랜잭션 전후로 데이터베이스가 일관된 상태 유지
- **격리성(Isolation)**: 동시 실행되는 트랜잭션들이 서로 영향을 주지 않음
- **지속성(Durability)**: 커밋된 트랜잭션의 결과가 영구적으로 저장

#### 5. 트랜잭션 격리 수준의 차이는?

- **READ UNCOMMITTED**: 가장 낮은 수준, Dirty Read 가능
- **READ COMMITTED**: 커밋된 데이터만 읽기 (대부분의 RDBMS 기본값)
- **REPEATABLE READ**: 같은 쿼리를 반복해도 같은 결과
- **SERIALIZABLE**: 가장 높은 수준, 완전한 격리

#### 6. RDBMS와 NoSQL의 차이와 선택 기준은?

- **데이터 모델**: RDBMS는 관계형(테이블), NoSQL은 비관계형(문서, 키-값 등)
- **스키마**: RDBMS는 고정된 스키마, NoSQL은 유연한 스키마
- **확장성**: RDBMS는 수직 확장, NoSQL은 수평 확장
- **선택 기준**: 구조화된 데이터와 복잡한 쿼리는 RDBMS, 대용량 데이터와 빠른 성능은 NoSQL

#### 7. 인덱스란 무엇이고 왜 사용하나요?

- **정의**: 데이터 조회 성능을 향상시키기 위한 자료 구조
- **역할**: 특정 열의 값을 빠르게 찾을 수 있도록 정렬된 구조 제공
- **장점**: 조회 성능 향상
- **단점**: 저장 공간 사용, INSERT/UPDATE/DELETE 성능 저하

#### 8. 조인(Join)의 종류와 차이는?

- **INNER JOIN**: 두 테이블의 교집합만 반환
- **LEFT JOIN**: 왼쪽 테이블의 모든 행 + 오른쪽 테이블의 매칭 행
- **RIGHT JOIN**: 오른쪽 테이블의 모든 행 + 왼쪽 테이블의 매칭 행
- **FULL OUTER JOIN**: 두 테이블의 합집합

#### 9. 정규화와 비정규화의 트레이드오프는?

- **정규화**: 데이터 중복 제거, 일관성 보장, 하지만 조인 비용 증가
- **비정규화**: 조인 비용 감소, 조회 성능 향상, 하지만 데이터 중복과 일관성 문제
- **균형**: 조회 성능이 중요한 경우 선택적 비정규화 고려

#### 10. 트랜잭션의 롤백과 커밋은 언제 사용하나요?

- **커밋(COMMIT)**: 트랜잭션의 모든 작업이 성공적으로 완료되었을 때 변경사항을 영구 저장
- **롤백(ROLLBACK)**: 트랜잭션 중 오류 발생 시 모든 변경사항을 취소하고 이전 상태로 복구
- **사용 시기**: 데이터 무결성이 중요한 작업(계좌 이체, 주문 처리 등)에서 사용

#### 11. 뷰(View)란 무엇이고 왜 사용하나요?

- **정의**: 하나 이상의 테이블에서 데이터를 조회하는 가상 테이블
- **목적**: 복잡한 쿼리를 간단하게, 보안(특정 열만 노출), 논리적 독립성
- **특징**: 실제 데이터를 저장하지 않고 쿼리만 저장

#### 12. 저장 프로시저(Stored Procedure)의 장단점은?

- **장점**: 네트워크 트래픽 감소, 보안 향상, 재사용성
- **단점**: 데이터베이스 종속성, 디버깅 어려움, 버전 관리 어려움
- **사용 시기**: 복잡한 비즈니스 로직이 데이터베이스에 가까운 경우

#### 13. 데이터베이스 샤딩(Sharding)이란?

- **정의**: 대용량 데이터를 여러 데이터베이스로 분산 저장
- **목적**: 수평 확장을 통한 성능 향상
- **방법**: 수평 분할(행 단위 분할), 수직 분할(열 단위 분할)
- **주의**: 조인 어려움, 트랜잭션 복잡도 증가

#### 14. 데이터베이스 복제(Replication)의 목적은?

- **고가용성**: 마스터 서버 장애 시 슬레이브 서버로 전환
- **성능 향상**: 읽기 작업을 여러 서버로 분산
- **백업**: 실시간 백업 제공
- **방식**: 마스터-슬레이브, 마스터-마스터

#### 15. 데이터베이스 성능 튜닝 방법은?

- **인덱스 최적화**: 자주 조회되는 열에 인덱스 생성
- **쿼리 최적화**: EXPLAIN으로 실행 계획 분석, 불필요한 조인 제거
- **정규화/비정규화 균형**: 조회 성능이 중요한 경우 선택적 비정규화
- **연결 풀 설정**: 적절한 연결 수 유지
- **캐싱**: 자주 조회되는 데이터 캐싱

---

## 참고 자료

- [MySQL 공식 문서](https://dev.mysql.com/doc/) - MySQL 공식 문서
- [PostgreSQL 공식 문서](https://www.postgresql.org/docs/) - PostgreSQL 공식 문서
- [SQL 튜토리얼](https://www.w3schools.com/sql/) - SQL 학습 자료
- [데이터베이스 시스템 개념](https://www.db-book.com/) - 데이터베이스 이론서

---

## 관련 노트

- **[[JPA와 ORM (SQL 중심 개발 vs ORM)|JPA와 ORM]]** - RDBMS와 객체지향 프로그래밍의 패러다임 불일치 해결
- **[[H2 Database (H2 데이터베이스)|H2 Database]]** - 개발/테스트용 경량 RDBMS
- **[[JPA 엔티티 매핑|JPA 엔티티 매핑]]** - JPA를 활용한 RDBMS 테이블 매핑
- **[[JPA 연관관계 매핑|JPA 연관관계 매핑]]** - RDBMS 관계를 객체로 표현하는 방법
- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - RDBMS와 객체지향의 차이점 이해

---

**태그:** #sql #database #rdbms #관계형데이터베이스 #면접 #개념정리
