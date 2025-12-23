#sql #database #dml #ddl #dcl #면접 #개념정리

**관련 개념:** [[RDBMS (관계형 데이터베이스 관리 시스템)|RDBMS]] [[정규화 (Normalization)|정규화]]

> [!note] 이어서 읽기
> SQL 언어 분류를 이해하려면 **[[RDBMS (관계형 데이터베이스 관리 시스템)|RDBMS]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**SQL(Structured Query Language)**은 데이터베이스와 상호작용하기 위한 표준 언어로, 용도에 따라 **DML(Data Manipulation Language)**, **DDL(Data Definition Language)**, **DCL(Data Control Language)**로 분류된다. 각 언어의 역할과 사용 시점을 이해하는 것이 중요하다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 데이터베이스를 다룰 때 단순히 데이터를 조회하거나 수정하는 것만이 아니라, 테이블 구조를 생성하고, 사용자 권한을 관리하는 등 다양한 작업이 필요합니다. 이러한 작업들을 목적에 따라 구분하여 사용해야 데이터베이스를 효율적으로 관리할 수 있습니다.

- DML, DDL, DCL을 구분하여 사용하면 데이터베이스 작업의 목적을 명확히 할 수 있고, 권한 관리와 보안을 체계적으로 수행할 수 있습니다. 또한 각 언어의 특성(트랜잭션 처리, 자동 커밋 등)을 이해하면 데이터 무결성을 보장하고 안전하게 데이터베이스를 운영할 수 있습니다.

- 개발자와 DBA는 각 SQL 언어의 역할과 차이점을 명확히 이해하고, 언제 어떤 언어를 사용해야 하는지 판단할 수 있어야 합니다.

---

## 1. SQL 언어 분류 개요

SQL은 데이터베이스 작업의 목적에 따라 세 가지로 분류됩니다:

| 분류 | 약어 | 의미 | 주요 명령어 | 자동 커밋 |
|------|------|------|-----------|----------|
| **DML** | Data Manipulation Language | 데이터 조작 언어 | SELECT, INSERT, UPDATE, DELETE | ❌ (수동 커밋) |
| **DDL** | Data Definition Language | 데이터 정의 언어 | CREATE, ALTER, DROP, TRUNCATE | ✅ (자동 커밋) |
| **DCL** | Data Control Language | 데이터 제어 언어 | GRANT, REVOKE | ✅ (자동 커밋) |

---

## 2. DML (Data Manipulation Language) - 데이터 조작 언어

### **DML이란?**

**DML(Data Manipulation Language, 데이터 조작 언어)**는 데이터베이스에 저장된 데이터를 조회, 삽입, 수정, 삭제하는 데 사용하는 언어입니다. 즉, **이미 존재하는 테이블의 데이터를 다루는** 언어입니다.

### **DML의 특징**

1. **트랜잭션 처리**: DML 명령어는 트랜잭션 내에서 실행되며, `COMMIT` 또는 `ROLLBACK`으로 확정하거나 취소할 수 있습니다.
2. **수동 커밋**: DML 명령어는 자동으로 커밋되지 않으며, 명시적으로 `COMMIT`을 해야 변경사항이 영구적으로 저장됩니다.
3. **롤백 가능**: `ROLLBACK`을 통해 변경사항을 취소할 수 있습니다.

### **주요 DML 명령어**

#### **1. SELECT - 데이터 조회**

```sql
-- 치킨 주문 시스템 예시

-- 모든 메뉴 조회
SELECT * FROM 메뉴;

-- 특정 조건으로 조회
SELECT 메뉴명, 가격 
FROM 메뉴 
WHERE 브랜드코드 = 'B001' AND 가격 < 20000;

-- 조인을 사용한 조회
SELECT 
    o.주문ID,
    c.고객이름,
    m.메뉴명,
    m.가격,
    o.주문일시
FROM 주문 o
JOIN 고객 c ON o.고객번호 = c.고객번호
JOIN 메뉴 m ON o.메뉴번호 = m.메뉴번호;
```

#### **2. INSERT - 데이터 삽입**

```sql
-- 단일 행 삽입
INSERT INTO 메뉴 (메뉴번호, 메뉴명, 가격, 브랜드코드)
VALUES ('M005', '허니콤보', 22000, 'B001');

-- 여러 행 삽입
INSERT INTO 메뉴 (메뉴번호, 메뉴명, 가격, 브랜드코드)
VALUES 
    ('M006', '뿌링클', 21000, 'B001'),
    ('M007', '맛초킹', 20000, 'B002');

-- 서브쿼리를 사용한 삽입
INSERT INTO 주문 (주문ID, 고객번호, 메뉴번호, 주문일시)
SELECT 
    주문ID_SEQ.NEXTVAL,
    'C001',
    메뉴번호,
    SYSDATE
FROM 메뉴
WHERE 브랜드코드 = 'B001';
```

#### **3. UPDATE - 데이터 수정**

```sql
-- 단일 행 수정
UPDATE 메뉴
SET 가격 = 19000
WHERE 메뉴번호 = 'M001';

-- 여러 컬럼 수정
UPDATE 메뉴
SET 가격 = 20000, 메뉴명 = '후라이드치킨(대)'
WHERE 메뉴번호 = 'M001';

-- 조건부 수정
UPDATE 메뉴
SET 가격 = 가격 * 1.1  -- 10% 가격 인상
WHERE 브랜드코드 = 'B001';

-- 서브쿼리를 사용한 수정
UPDATE 주문
SET 배달예정시간 = SYSDATE + 1
WHERE 고객번호 IN (
    SELECT 고객번호 
    FROM 고객 
    WHERE 배달주소 LIKE '서울시%'
);
```

#### **4. DELETE - 데이터 삭제**

```sql
-- 특정 행 삭제
DELETE FROM 메뉴
WHERE 메뉴번호 = 'M005';

-- 조건부 삭제
DELETE FROM 주문
WHERE 주문일시 < SYSDATE - 30;  -- 30일 이전 주문 삭제

-- 서브쿼리를 사용한 삭제
DELETE FROM 주문
WHERE 고객번호 IN (
    SELECT 고객번호 
    FROM 고객 
    WHERE 전화번호 IS NULL
);
```

### **DML 트랜잭션 예시**

```sql
-- 트랜잭션 시작 (명시적)
BEGIN TRANSACTION;

-- 주문 추가
INSERT INTO 주문 (주문ID, 고객번호, 메뉴번호, 주문일시)
VALUES ('O100', 'C001', 'M001', SYSDATE);

-- 주문 상세 추가
INSERT INTO 주문상세 (주문ID, 수량, 가격)
VALUES ('O100', 2, 36000);

-- 모든 작업이 성공하면 커밋
COMMIT;

-- 또는 문제가 발생하면 롤백
-- ROLLBACK;
```

---

## 3. DDL (Data Definition Language) - 데이터 정의 언어

### **DDL이란?**

**DDL(Data Definition Language, 데이터 정의 언어)**는 데이터베이스의 구조를 정의하고 변경하는 데 사용하는 언어입니다. 즉, **테이블, 인덱스, 뷰 등의 스키마를 생성, 수정, 삭제하는** 언어입니다.

### **DDL의 특징**

1. **자동 커밋**: DDL 명령어는 실행 시 자동으로 커밋됩니다. 롤백할 수 없습니다.
2. **암시적 커밋**: DDL 명령어 실행 전에 대기 중인 모든 트랜잭션이 자동으로 커밋됩니다.
3. **롤백 불가**: DDL 명령어는 실행 후 롤백할 수 없으므로 주의해서 사용해야 합니다.

### **주요 DDL 명령어**

#### **1. CREATE - 객체 생성**

```sql
-- 테이블 생성
CREATE TABLE 메뉴 (
    메뉴번호 VARCHAR(10) PRIMARY KEY,
    메뉴명 VARCHAR(50) NOT NULL,
    가격 NUMBER(10) NOT NULL,
    브랜드코드 VARCHAR(10),
    칼로리 NUMBER(5),
    등록일시 DATE DEFAULT SYSDATE,
    CONSTRAINT FK_메뉴_브랜드 FOREIGN KEY (브랜드코드) 
        REFERENCES 브랜드(브랜드코드)
);

-- 인덱스 생성
CREATE INDEX IDX_메뉴_브랜드 ON 메뉴(브랜드코드);

-- 뷰 생성
CREATE VIEW V_인기메뉴 AS
SELECT 
    m.메뉴명,
    m.가격,
    b.브랜드명,
    COUNT(o.주문ID) AS 주문횟수
FROM 메뉴 m
JOIN 브랜드 b ON m.브랜드코드 = b.브랜드코드
LEFT JOIN 주문 o ON m.메뉴번호 = o.메뉴번호
GROUP BY m.메뉴명, m.가격, b.브랜드명
ORDER BY 주문횟수 DESC;

-- 시퀀스 생성
CREATE SEQUENCE 주문ID_SEQ
START WITH 1
INCREMENT BY 1
MAXVALUE 999999;
```

#### **2. ALTER - 객체 수정**

```sql
-- 컬럼 추가
ALTER TABLE 메뉴
ADD (설명 VARCHAR(200));

-- 컬럼 수정
ALTER TABLE 메뉴
MODIFY (가격 NUMBER(12, 2));  -- 소수점 2자리까지 허용

-- 컬럼 삭제
ALTER TABLE 메뉴
DROP COLUMN 칼로리;

-- 제약조건 추가
ALTER TABLE 메뉴
ADD CONSTRAINT CK_메뉴_가격 CHECK (가격 > 0);

-- 제약조건 삭제
ALTER TABLE 메뉴
DROP CONSTRAINT CK_메뉴_가격;

-- 인덱스 추가
ALTER TABLE 메뉴
ADD INDEX IDX_메뉴_가격 (가격);
```

#### **3. DROP - 객체 삭제**

```sql
-- 테이블 삭제
DROP TABLE 메뉴;

-- 뷰 삭제
DROP VIEW V_인기메뉴;

-- 인덱스 삭제
DROP INDEX IDX_메뉴_브랜드;

-- 시퀀스 삭제
DROP SEQUENCE 주문ID_SEQ;
```

#### **4. TRUNCATE - 테이블 데이터 전체 삭제**

```sql
-- 테이블의 모든 데이터 삭제 (구조는 유지)
TRUNCATE TABLE 주문;

-- TRUNCATE vs DELETE 차이점:
-- TRUNCATE: 빠름, 롤백 불가, 자동 커밋, 테이블 구조 유지
-- DELETE: 느림, 롤백 가능, 수동 커밋, WHERE 조건 가능
```


---

## 4. DCL (Data Control Language) - 데이터 제어 언어

### **DCL이란?**

**DCL(Data Control Language, 데이터 제어 언어)**는 데이터베이스의 접근 권한을 제어하는 데 사용하는 언어입니다. 즉, **사용자에게 권한을 부여하거나 회수하는** 언어입니다.

### **DCL이 존재하는 이유**

1. **보안과 접근 제어**: 데이터베이스에는 민감한 정보가 많아 모든 사용자가 모든 데이터에 접근할 수 있으면 보안 문제가 발생합니다. DCL로 필요한 권한만 부여하여 보안을 강화합니다.

2. **최소 권한 원칙**: 각 사용자/역할에 필요한 최소 권한만 부여하여 실수나 악의적 행위로 인한 피해를 줄입니다.

3. **데이터 무결성 보호**: 모든 사용자가 모든 데이터를 수정/삭제할 수 있으면 데이터 무결성이 깨질 수 있습니다. 읽기 전용 사용자는 SELECT만 가능하도록 하여 실수로 데이터 변경을 방지합니다.

4. **감사와 책임 추적**: 권한을 부여하면 누가 무엇을 할 수 있는지 기록되어 문제 발생 시 추적이 가능합니다.

5. **역할 기반 접근 제어**: 비슷한 권한을 가진 사용자들을 역할(Role)로 묶어 효율적으로 관리할 수 있습니다.

### **DCL의 특징**

1. **자동 커밋**: DCL 명령어는 실행 시 자동으로 커밋됩니다.
2. **보안 관리**: 데이터베이스 보안과 접근 제어의 핵심입니다.
3. **권한 관리**: 사용자별로 적절한 권한을 부여하여 데이터 보안을 유지합니다.

### **주요 DCL 명령어**

#### **1. GRANT - 권한 부여**

```sql
-- 특정 테이블에 대한 SELECT 권한 부여
GRANT SELECT ON 메뉴 TO 사용자1;

-- 여러 권한 동시 부여
GRANT SELECT, INSERT, UPDATE ON 주문 TO 사용자2;

-- 모든 권한 부여
GRANT ALL PRIVILEGES ON 메뉴 TO 사용자3;

-- 특정 컬럼에 대한 권한만 부여
GRANT SELECT (메뉴명, 가격) ON 메뉴 TO 사용자4;

-- 권한을 다른 사용자에게도 부여할 수 있는 권한
GRANT SELECT ON 메뉴 TO 사용자5 WITH GRANT OPTION;

-- 역할(Role)에 권한 부여
GRANT SELECT ON 메뉴 TO 역할_읽기전용;

-- 사용자에게 역할 부여
GRANT 역할_읽기전용 TO 사용자6;
```

#### **2. REVOKE - 권한 회수**

```sql
-- 특정 권한 회수
REVOKE SELECT ON 메뉴 FROM 사용자1;

-- 여러 권한 동시 회수
REVOKE INSERT, UPDATE ON 주문 FROM 사용자2;

-- 모든 권한 회수
REVOKE ALL PRIVILEGES ON 메뉴 FROM 사용자3;

-- 역할에서 권한 회수
REVOKE SELECT ON 메뉴 FROM 역할_읽기전용;

-- 사용자에게서 역할 회수
REVOKE 역할_읽기전용 FROM 사용자6;
```

### **권한 종류**

| 권한 | 설명 | 예시 |
|------|------|------|
| **SELECT** | 데이터 조회 | `GRANT SELECT ON 메뉴 TO 사용자1;` |
| **INSERT** | 데이터 삽입 | `GRANT INSERT ON 주문 TO 사용자2;` |
| **UPDATE** | 데이터 수정 | `GRANT UPDATE ON 메뉴 TO 사용자3;` |
| **DELETE** | 데이터 삭제 | `GRANT DELETE ON 주문 TO 사용자4;` |
| **ALTER** | 테이블 구조 변경 | `GRANT ALTER ON 메뉴 TO 관리자;` |
| **INDEX** | 인덱스 생성/삭제 | `GRANT INDEX ON 메뉴 TO 관리자;` |
| **REFERENCES** | 외래키 생성 | `GRANT REFERENCES ON 메뉴 TO 개발자;` |
| **ALL** | 모든 권한 | `GRANT ALL ON 메뉴 TO 관리자;` |

### **DCL 사용 예시**

```sql
-- 1. 읽기 전용 사용자 생성
CREATE USER 읽기전용사용자 IDENTIFIED BY password123;
GRANT SELECT ON 메뉴 TO 읽기전용사용자;
GRANT SELECT ON 주문 TO 읽기전용사용자;
GRANT SELECT ON 고객 TO 읽기전용사용자;

-- 2. 개발자 권한 부여
CREATE USER 개발자 IDENTIFIED BY dev123;
GRANT SELECT, INSERT, UPDATE, DELETE ON 메뉴 TO 개발자;
GRANT SELECT, INSERT, UPDATE, DELETE ON 주문 TO 개발자;
GRANT SELECT ON 고객 TO 개발자;  -- 고객 정보는 읽기만 가능

-- 3. 관리자 권한 부여
CREATE USER 관리자 IDENTIFIED BY admin123;
GRANT ALL PRIVILEGES ON 메뉴 TO 관리자;
GRANT ALL PRIVILEGES ON 주문 TO 관리자;
GRANT ALL PRIVILEGES ON 고객 TO 관리자;

-- 4. 권한 회수
REVOKE UPDATE ON 메뉴 FROM 개발자;  -- 개발자의 수정 권한 회수
```

---

## 5. DML, DDL, DCL 비교

### **주요 차이점**

| 항목         | DML                            | DDL                           | DCL           |
| ---------- | ------------------------------ | ----------------------------- | ------------- |
| **목적**     | 데이터 조작                         | 스키마 정의                        | 권한 제어         |
| **자동 커밋**  | ❌ (수동 커밋)                      | ✅ (자동 커밋)                     | ✅ (자동 커밋)     |
| **롤백 가능**  | ✅                              | ❌                             | ❌             |
| **트랜잭션**   | 트랜잭션 내 실행                      | 트랜잭션 밖 실행                     | 트랜잭션 밖 실행     |
| **주요 명령어** | SELECT, INSERT, UPDATE, DELETE | CREATE, ALTER, DROP, TRUNCATE | GRANT, REVOKE |
| **사용 빈도**  | 매우 높음                          | 중간                            | 낮음            |
| **권한 요구**  | 일반 사용자                         | DBA/관리자                       | DBA/관리자       |

### **자동 커밋 동작 방식**

```sql
-- 시나리오 1: DML만 사용
BEGIN TRANSACTION;
INSERT INTO 메뉴 VALUES ('M001', '후라이드치킨', 18000, 'B001', SYSDATE);
-- 아직 커밋되지 않음
COMMIT;  -- 수동으로 커밋해야 함

-- 시나리오 2: DDL 실행 시
BEGIN TRANSACTION;
INSERT INTO 메뉴 VALUES ('M001', '후라이드치킨', 18000, 'B001', SYSDATE);
-- 아직 커밋되지 않음

CREATE TABLE 새테이블 (컬럼1 VARCHAR(10));
-- 위 INSERT가 자동 커밋됨
-- CREATE TABLE도 자동 커밋됨

-- 시나리오 3: DCL 실행 시
BEGIN TRANSACTION;
INSERT INTO 메뉴 VALUES ('M001', '후라이드치킨', 18000, 'B001', SYSDATE);
-- 아직 커밋되지 않음

GRANT SELECT ON 메뉴 TO 사용자1;
-- 위 INSERT가 자동 커밋됨
-- GRANT도 자동 커밋됨
```

---

## 6. 실무 가이드

### **언제 어떤 언어를 사용해야 할까?**

#### **DML 사용 시점**

- ✅ 데이터 조회, 삽입, 수정, 삭제가 필요할 때
- ✅ 비즈니스 로직에서 데이터를 다룰 때
- ✅ 애플리케이션에서 가장 많이 사용

```sql
-- 애플리케이션 코드에서 주로 사용
SELECT * FROM 메뉴 WHERE 브랜드코드 = ?;
INSERT INTO 주문 VALUES (?, ?, ?, ?);
UPDATE 메뉴 SET 가격 = ? WHERE 메뉴번호 = ?;
```

#### **DDL 사용 시점**

- ✅ 데이터베이스 스키마를 설계하거나 변경할 때
- ✅ 테이블 구조를 수정해야 할 때
- ✅ 인덱스나 뷰를 생성/삭제할 때
- ⚠️ 주의: 프로덕션 환경에서는 신중하게 사용

```sql
-- 데이터베이스 초기 설정 시
CREATE TABLE 메뉴 (...);
CREATE INDEX IDX_메뉴_브랜드 ON 메뉴(브랜드코드);

-- 스키마 변경 시 (마이그레이션)
ALTER TABLE 메뉴 ADD (설명 VARCHAR(200));
```

#### **DCL 사용 시점**

- ✅ 사용자 계정을 생성하고 권한을 부여할 때
- ✅ 보안 정책을 적용할 때
- ✅ 역할 기반 접근 제어를 설정할 때
- ⚠️ 주의: 최소 권한 원칙을 따라야 함

```sql
-- 사용자 생성 및 권한 부여
CREATE USER 개발자 IDENTIFIED BY password;
GRANT SELECT, INSERT, UPDATE, DELETE ON 메뉴 TO 개발자;

-- 권한 회수
REVOKE DELETE ON 메뉴 FROM 개발자;
```

### **주의사항**

1. **DDL은 자동 커밋**: DDL 실행 시 이전 트랜잭션이 자동 커밋되므로 주의
2. **롤백 불가**: DDL과 DCL은 롤백할 수 없으므로 신중하게 실행
3. **권한 최소화**: DCL 사용 시 최소 권한 원칙을 따라 필요한 권한만 부여
4. **트랜잭션 관리**: DML은 트랜잭션 내에서 실행되므로 COMMIT/ROLLBACK 명시

---

## 요약

- **DML (Data Manipulation Language)**: 데이터 조작 언어로, SELECT, INSERT, UPDATE, DELETE를 포함합니다. 트랜잭션 내에서 실행되며 수동 커밋이 필요합니다.
- **DDL (Data Definition Language)**: 데이터 정의 언어로, CREATE, ALTER, DROP, TRUNCATE를 포함합니다. 자동 커밋되며 롤백할 수 없습니다.
- **DCL (Data Control Language)**: 데이터 제어 언어로, GRANT, REVOKE를 포함합니다. 권한 관리를 담당하며 자동 커밋됩니다.
- **자동 커밋**: DDL과 DCL은 실행 시 자동으로 커밋되며, 실행 전 대기 중인 트랜잭션도 함께 커밋됩니다.
- **실무**: DML은 애플리케이션에서 가장 많이 사용되며, DDL과 DCL은 관리자 권한이 필요하고 신중하게 사용해야 합니다.

---

## 참고 자료

- [SQL Language Reference - Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/)
- [SQL Tutorial - W3Schools](https://www.w3schools.com/sql/)
- [MySQL Documentation](https://dev.mysql.com/doc/)
