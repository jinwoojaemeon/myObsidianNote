#sql #database #transaction #트랜잭션 #acid #면접 #개념정리

**관련 개념:** [[RDBMS (관계형 데이터베이스 관리 시스템)|RDBMS]] [[DML, DDL, DCL (SQL 언어 분류)|DML, DDL, DCL]]

> [!note] 이어서 읽기
> 트랜잭션을 이해하려면 **[[RDBMS (관계형 데이터베이스 관리 시스템)|RDBMS]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**트랜잭션(Transaction)**은 데이터베이스에서 하나의 논리적 작업 단위를 구성하는 하나 이상의 데이터베이스 작업들의 집합이다. 트랜잭션은 **All or Nothing** 원칙에 따라 모든 작업이 성공하거나 모두 실패해야 하며, ACID 속성을 통해 데이터 무결성을 보장한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 데이터베이스에서 여러 작업을 수행할 때, 일부 작업만 성공하고 일부가 실패하면 데이터의 일관성이 깨질 수 있습니다. 예를 들어, 치킨 주문 시스템에서 주문을 생성하고 결제를 처리하는 과정에서 주문은 생성되었지만 결제가 실패하면, 주문은 존재하지만 결제되지 않은 상태가 되어 데이터 불일치가 발생합니다.

- 트랜잭션을 통해 여러 작업을 하나의 논리적 단위로 묶어서, 모든 작업이 성공하거나 모두 실패하도록 보장합니다. 이를 통해 데이터 무결성과 일관성을 유지할 수 있으며, 동시에 여러 사용자가 데이터베이스에 접근할 때도 안전하게 데이터를 관리할 수 있습니다.

- 트랜잭션의 ACID 속성, 격리 수준(Isolation Level), 동시성 제어, 데드락 등의 개념을 이해하고, 실무에서 트랜잭션을 올바르게 사용하는 방법을 알아야 합니다.

---

## 1. 트랜잭션이란?

### **트랜잭션의 정의**

**트랜잭션(Transaction)**은 데이터베이스의 상태를 변경하는 하나 이상의 작업을 논리적 단위로 묶은 것이다. 트랜잭션은 다음 중 하나의 상태로 끝난다:

- **COMMIT**: 모든 작업이 성공적으로 완료되어 변경사항이 영구적으로 저장됨
- **ROLLBACK**: 작업 중 오류가 발생하여 모든 변경사항이 취소되고 이전 상태로 복구됨

### **트랜잭션의 특징**

1. **원자성(Atomicity)**: 트랜잭션의 모든 작업이 모두 실행되거나 모두 실행되지 않아야 함
2. **일관성(Consistency)**: 트랜잭션 전후로 데이터베이스의 일관성이 유지되어야 함
3. **격리성(Isolation)**: 동시에 실행되는 트랜잭션들이 서로 영향을 주지 않아야 함
4. **지속성(Durability)**: 커밋된 트랜잭션의 결과는 영구적으로 저장되어야 함

### **트랜잭션 예시 - 치킨 주문 시스템**

```sql
-- 주문 생성 트랜잭션
BEGIN TRANSACTION;

-- 1. 주문 정보 삽입
INSERT INTO 주문 (주문ID, 고객번호, 주문일시, 총금액)
VALUES ('O100', 'C001', SYSDATE, 36000);

-- 2. 주문 상세 정보 삽입
INSERT INTO 주문상세 (주문ID, 메뉴번호, 수량, 가격)
VALUES ('O100', 'M001', 2, 36000);

-- 3. 고객 포인트 차감
UPDATE 고객
SET 포인트 = 포인트 - 1000
WHERE 고객번호 = 'C001';

-- 4. 재고 차감
UPDATE 메뉴
SET 재고수량 = 재고수량 - 2
WHERE 메뉴번호 = 'M001';

-- 모든 작업이 성공하면 커밋
COMMIT;

-- 또는 오류 발생 시 롤백
-- ROLLBACK;
```

**시나리오:**
- 모든 작업이 성공 → `COMMIT` → 모든 변경사항 저장
- 하나라도 실패 → `ROLLBACK` → 모든 변경사항 취소

---

## 2. ACID 속성

### **ACID란?**

ACID는 트랜잭션이 가져야 할 네 가지 속성의 약자입니다:

- **A**tomicity (원자성)
- **C**onsistency (일관성)
- **I**solation (격리성)
- **D**urability (지속성)

### **1. Atomicity (원자성)**

**원자성**은 트랜잭션의 모든 작업이 하나의 단위로 처리되어야 한다는 것을 의미합니다. 즉, **All or Nothing** 원칙입니다.

**예시:**

```sql
BEGIN TRANSACTION;

-- 주문 생성
INSERT INTO 주문 (주문ID, 고객번호, 총금액)
VALUES ('O100', 'C001', 36000);

-- 결제 처리 (실패 시)
UPDATE 고객
SET 잔액 = 잔액 - 36000
WHERE 고객번호 = 'C001' AND 잔액 >= 36000;
-- 잔액 부족으로 실패

-- 원자성 보장: 주문 삽입도 함께 롤백됨
ROLLBACK;
```

**원자성 보장:**
- 모든 작업 성공 → 모두 커밋
- 하나라도 실패 → 모두 롤백
- 중간 상태는 존재하지 않음

### **2. Consistency (일관성)**

**일관성**은 트랜잭션 전후로 데이터베이스의 무결성 제약조건이 유지되어야 한다는 것을 의미합니다.

**예시:**

```sql
-- 제약조건: 주문 총금액 = 주문상세 금액 합계

BEGIN TRANSACTION;

-- 주문 생성 (총금액: 36000)
INSERT INTO 주문 (주문ID, 고객번호, 총금액)
VALUES ('O100', 'C001', 36000);

-- 주문상세 추가 (합계: 40000) - 불일치!
INSERT INTO 주문상세 (주문ID, 메뉴번호, 수량, 가격)
VALUES ('O100', 'M001', 2, 40000);

-- 일관성 위반 감지 → 롤백
ROLLBACK;
```

**일관성 보장:**
- 트랜잭션 시작 전: 데이터베이스가 일관된 상태
- 트랜잭션 실행 중: 일시적으로 불일치 가능 (다른 트랜잭션에서 보이지 않음)
- 트랜잭션 완료 후: 다시 일관된 상태

### **3. Isolation (격리성)**

**격리성**은 동시에 실행되는 여러 트랜잭션이 서로 영향을 주지 않고 독립적으로 실행되어야 한다는 것을 의미합니다.

**예시 - 격리성 위반 시나리오:**

```sql
-- 트랜잭션 A: 주문 생성
BEGIN TRANSACTION;
INSERT INTO 주문 (주문ID, 고객번호, 총금액)
VALUES ('O100', 'C001', 36000);
-- 아직 커밋하지 않음

-- 트랜잭션 B: 주문 조회
SELECT * FROM 주문 WHERE 주문ID = 'O100';
-- 트랜잭션 A가 커밋하지 않았으므로 조회되지 않아야 함 (격리성)
```

**격리성 보장:**
- 각 트랜잭션은 다른 트랜잭션의 중간 상태를 볼 수 없음
- 격리 수준에 따라 격리성 강도가 달라짐

### **4. Durability (지속성)**

**지속성**은 커밋된 트랜잭션의 결과는 시스템 장애가 발생해도 영구적으로 보존되어야 한다는 것을 의미합니다.

**예시:**

```sql
BEGIN TRANSACTION;

INSERT INTO 주문 (주문ID, 고객번호, 총금액)
VALUES ('O100', 'C001', 36000);

COMMIT;  -- 커밋 완료

-- 이후 시스템 장애 발생
-- 재시작 후에도 주문 O100은 존재해야 함 (지속성)
```

**지속성 보장:**
- 커밋된 데이터는 디스크에 영구 저장
- 시스템 장애 후에도 데이터 복구 가능

---

## 3. 트랜잭션 상태

### **트랜잭션의 생명주기**

```
[시작] → [활성(Active)] → [부분 커밋(Partially Committed)] → [커밋됨(Committed)]
                              ↓
                        [실패(Failed)] → [중단됨(Aborted)] → [롤백 완료]
```

### **상태 설명**

1. **활성(Active)**: 트랜잭션이 실행 중인 상태
2. **부분 커밋(Partially Committed)**: 마지막 명령어가 실행된 후, 커밋 전 상태
3. **커밋됨(Committed)**: 트랜잭션이 성공적으로 완료된 상태
4. **실패(Failed)**: 정상적으로 실행을 계속할 수 없는 상태
5. **중단됨(Aborted)**: 트랜잭션이 롤백되고 이전 상태로 복구된 상태

### **상태 전이 예시**

```sql
-- 1. 활성 상태
BEGIN TRANSACTION;  -- 트랜잭션 시작

-- 2. 활성 상태 (작업 실행 중)
INSERT INTO 주문 (주문ID, 고객번호, 총금액)
VALUES ('O100', 'C001', 36000);

-- 3. 부분 커밋 상태
COMMIT;  -- 커밋 명령 실행

-- 4. 커밋됨 상태
-- 트랜잭션 완료, 변경사항 영구 저장
```

---

## 4. 격리 수준 (Isolation Level)

### **격리 수준이란?**

**격리 수준(Isolation Level)**은 동시에 실행되는 트랜잭션 간의 격리 정도를 설정하는 것이다. 격리 수준이 높을수록 데이터 일관성은 보장되지만 성능은 저하됩니다.

### **격리 수준 종류 (낮은 순서대로)**

#### **1. READ UNCOMMITTED (커밋되지 않은 읽기)**

가장 낮은 격리 수준으로, 다른 트랜잭션의 커밋되지 않은 데이터도 읽을 수 있습니다.

**문제점:**
- **Dirty Read**: 커밋되지 않은 데이터를 읽음
- **Non-Repeatable Read**: 같은 쿼리를 반복 실행해도 결과가 다를 수 있음
- **Phantom Read**: 새로운 행이 나타나거나 사라질 수 있음

**예시:**

```sql
-- 트랜잭션 A
BEGIN TRANSACTION;
UPDATE 메뉴 SET 가격 = 20000 WHERE 메뉴번호 = 'M001';
-- 아직 커밋하지 않음

-- 트랜잭션 B (READ UNCOMMITTED)
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SELECT 가격 FROM 메뉴 WHERE 메뉴번호 = 'M001';
-- 결과: 20000 (커밋되지 않은 데이터를 읽음 - Dirty Read)

-- 트랜잭션 A가 롤백하면?
ROLLBACK;
-- 트랜잭션 B가 읽은 데이터는 잘못된 데이터
```

#### **2. READ COMMITTED (커밋된 읽기)**

다른 트랜잭션의 커밋된 데이터만 읽을 수 있습니다. (대부분의 DBMS 기본값)

**문제점:**
- **Non-Repeatable Read**: 같은 쿼리를 반복 실행해도 결과가 다를 수 있음
- **Phantom Read**: 새로운 행이 나타나거나 사라질 수 있음

**예시:**

```sql
-- 트랜잭션 A
BEGIN TRANSACTION;
SELECT 가격 FROM 메뉴 WHERE 메뉴번호 = 'M001';
-- 결과: 18000

-- 트랜잭션 B
BEGIN TRANSACTION;
UPDATE 메뉴 SET 가격 = 20000 WHERE 메뉴번호 = 'M001';
COMMIT;

-- 트랜잭션 A (같은 쿼리 재실행)
SELECT 가격 FROM 메뉴 WHERE 메뉴번호 = 'M001';
-- 결과: 20000 (다른 결과 - Non-Repeatable Read)
```

#### **3. REPEATABLE READ (반복 가능한 읽기)**

트랜잭션 내에서 같은 쿼리를 여러 번 실행해도 항상 같은 결과를 반환합니다.

**문제점:**
- **Phantom Read**: 새로운 행이 나타나거나 사라질 수 있음

**예시:**

```sql
-- 트랜잭션 A
BEGIN TRANSACTION;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SELECT COUNT(*) FROM 메뉴 WHERE 브랜드코드 = 'B001';
-- 결과: 5

-- 트랜잭션 B
BEGIN TRANSACTION;
INSERT INTO 메뉴 (메뉴번호, 메뉴명, 브랜드코드)
VALUES ('M010', '새메뉴', 'B001');
COMMIT;

-- 트랜잭션 A (같은 쿼리 재실행)
SELECT COUNT(*) FROM 메뉴 WHERE 브랜드코드 = 'B001';
-- 결과: 5 (같은 결과 - Non-Repeatable Read 방지)
-- 하지만 실제로는 6개 행이 존재 (Phantom Read)
```

#### **4. SERIALIZABLE (직렬화 가능)**

가장 높은 격리 수준으로, 트랜잭션을 순차적으로 실행하는 것처럼 동작합니다.

**장점:**
- 모든 동시성 문제 해결 (Dirty Read, Non-Repeatable Read, Phantom Read 모두 방지)

**단점:**
- 성능 저하 (락이 많이 발생)
- 데드락 가능성 증가

**예시:**

```sql
-- 트랜잭션 A
BEGIN TRANSACTION;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
SELECT * FROM 메뉴 WHERE 브랜드코드 = 'B001';
-- 해당 범위에 락 설정

-- 트랜잭션 B (대기)
BEGIN TRANSACTION;
INSERT INTO 메뉴 (메뉴번호, 메뉴명, 브랜드코드)
VALUES ('M010', '새메뉴', 'B001');
-- 트랜잭션 A가 커밋할 때까지 대기

-- 트랜잭션 A
COMMIT;  -- 락 해제

-- 트랜잭션 B 실행 가능
```

### **격리 수준 비교표**

| 격리 수준 | Dirty Read | Non-Repeatable Read | Phantom Read | 성능 |
|-----------|------------|---------------------|--------------|------|
| **READ UNCOMMITTED** | ❌ 발생 가능 | ❌ 발생 가능 | ❌ 발생 가능 | ⭐⭐⭐⭐⭐ |
| **READ COMMITTED** | ✅ 방지 | ❌ 발생 가능 | ❌ 발생 가능 | ⭐⭐⭐⭐ |
| **REPEATABLE READ** | ✅ 방지 | ✅ 방지 | ❌ 발생 가능 | ⭐⭐⭐ |
| **SERIALIZABLE** | ✅ 방지 | ✅ 방지 | ✅ 방지 | ⭐⭐ |

---

## 5. 동시성 제어 (Concurrency Control)

### **동시성 제어란?**

**동시성 제어**는 여러 트랜잭션이 동시에 실행될 때 데이터의 일관성을 유지하기 위한 기법입니다.

### **동시성 문제**

#### **1. Lost Update (갱신 손실)**

두 트랜잭션이 동시에 같은 데이터를 수정할 때, 하나의 변경사항이 손실되는 문제입니다.

**예시:**

```sql
-- 초기값: 메뉴 M001의 재고수량 = 10

-- 트랜잭션 A
BEGIN TRANSACTION;
SELECT 재고수량 FROM 메뉴 WHERE 메뉴번호 = 'M001';  -- 10
UPDATE 메뉴 SET 재고수량 = 재고수량 - 3 WHERE 메뉴번호 = 'M001';  -- 7

-- 트랜잭션 B (동시 실행)
BEGIN TRANSACTION;
SELECT 재고수량 FROM 메뉴 WHERE 메뉴번호 = 'M001';  -- 10 (아직 A가 커밋 안 함)
UPDATE 메뉴 SET 재고수량 = 재고수량 - 5 WHERE 메뉴번호 = 'M001';  -- 5

-- 트랜잭션 A 커밋
COMMIT;  -- 재고수량 = 7

-- 트랜잭션 B 커밋
COMMIT;  -- 재고수량 = 5 (A의 변경사항 손실!)
-- 예상값: 10 - 3 - 5 = 2
```

#### **2. Dirty Read (더티 읽기)**

커밋되지 않은 데이터를 읽는 문제입니다.

#### **3. Non-Repeatable Read (반복 불가능한 읽기)**

같은 트랜잭션 내에서 같은 쿼리를 반복 실행했을 때 다른 결과가 나오는 문제입니다.

#### **4. Phantom Read (팬텀 읽기)**

트랜잭션 중에 새로운 행이 추가되거나 삭제되어 결과 집합이 달라지는 문제입니다.

### **락(Lock) 기법**

#### **1. 공유 락 (Shared Lock, S-Lock)**

읽기 작업에 사용되는 락으로, 여러 트랜잭션이 동시에 공유 락을 가질 수 있습니다.

```sql
-- 트랜잭션 A
BEGIN TRANSACTION;
SELECT * FROM 메뉴 WHERE 메뉴번호 = 'M001' LOCK IN SHARE MODE;
-- 공유 락 획득 (다른 트랜잭션도 읽기 가능)

-- 트랜잭션 B
BEGIN TRANSACTION;
SELECT * FROM 메뉴 WHERE 메뉴번호 = 'M001' LOCK IN SHARE MODE;
-- 공유 락 획득 가능 (읽기 가능)
```

#### **2. 배타 락 (Exclusive Lock, X-Lock)**

쓰기 작업에 사용되는 락으로, 한 트랜잭션만 배타 락을 가질 수 있습니다.

```sql
-- 트랜잭션 A
BEGIN TRANSACTION;
UPDATE 메뉴 SET 가격 = 20000 WHERE 메뉴번호 = 'M001';
-- 배타 락 획득

-- 트랜잭션 B
BEGIN TRANSACTION;
UPDATE 메뉴 SET 가격 = 19000 WHERE 메뉴번호 = 'M001';
-- 대기 (트랜잭션 A가 커밋할 때까지)
```

#### **3. 락 호환성**

| 현재 락 | 공유 락 요청 | 배타 락 요청 |
|---------|-------------|-------------|
| **공유 락** | ✅ 허용 | ❌ 대기 |
| **배타 락** | ❌ 대기 | ❌ 대기 |
| **락 없음** | ✅ 허용 | ✅ 허용 |

---

## 6. 데드락 (Deadlock)

### **데드락이란?**

**데드락(Deadlock)**은 두 개 이상의 트랜잭션이 서로가 가진 락을 기다리며 무한 대기하는 상태입니다.

### **데드락 발생 예시**

```sql
-- 트랜잭션 A
BEGIN TRANSACTION;
UPDATE 메뉴 SET 가격 = 20000 WHERE 메뉴번호 = 'M001';
-- 메뉴 M001에 배타 락 획득

-- 트랜잭션 B
BEGIN TRANSACTION;
UPDATE 주문 SET 총금액 = 40000 WHERE 주문ID = 'O100';
-- 주문 O100에 배타 락 획득

-- 트랜잭션 A
UPDATE 주문 SET 총금액 = 50000 WHERE 주문ID = 'O100';
-- 주문 O100의 락을 기다림 (트랜잭션 B가 가지고 있음)

-- 트랜잭션 B
UPDATE 메뉴 SET 가격 = 19000 WHERE 메뉴번호 = 'M001';
-- 메뉴 M001의 락을 기다림 (트랜잭션 A가 가지고 있음)

-- 데드락 발생! 둘 다 대기 상태
```

### **데드락 해결 방법**

1. **예방(Prevention)**: 데드락이 발생하지 않도록 락 순서를 정함
2. **회피(Avoidance)**: 락을 요청하기 전에 데드락 가능성을 확인
3. **탐지 및 복구(Detection & Recovery)**: 데드락을 탐지하고 한 트랜잭션을 롤백

**대부분의 DBMS는 탐지 및 복구 방식을 사용합니다:**

```sql
-- DBMS가 데드락을 탐지하면
-- 한 트랜잭션을 롤백하고 에러 반환

-- 트랜잭션 A
ERROR: Deadlock detected
ROLLBACK;

-- 트랜잭션 B
-- 정상적으로 실행 계속
```

---

## 7. 트랜잭션 사용 예시

### **치킨 주문 시스템 - 주문 생성 트랜잭션**

```sql
-- 주문 생성 및 결제 처리
BEGIN TRANSACTION;

BEGIN
    -- 1. 주문 정보 삽입
    INSERT INTO 주문 (주문ID, 고객번호, 주문일시, 총금액, 상태)
    VALUES ('O100', 'C001', SYSDATE, 36000, '주문완료');
    
    -- 2. 주문 상세 정보 삽입
    INSERT INTO 주문상세 (주문ID, 메뉴번호, 수량, 가격)
    VALUES ('O100', 'M001', 2, 36000);
    
    -- 3. 고객 포인트 차감
    UPDATE 고객
    SET 포인트 = 포인트 - 1000
    WHERE 고객번호 = 'C001';
    
    -- 4. 재고 차감
    UPDATE 메뉴
    SET 재고수량 = 재고수량 - 2
    WHERE 메뉴번호 = 'M001';
    
    -- 5. 재고 부족 확인
    IF (SELECT 재고수량 FROM 메뉴 WHERE 메뉴번호 = 'M001') < 0 THEN
        RAISE_APPLICATION_ERROR(-20001, '재고 부족');
    END IF;
    
    -- 모든 작업 성공
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('주문이 성공적으로 생성되었습니다.');
    
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('주문 생성 실패: ' || SQLERRM);
END;
```

### **트랜잭션 중첩 (Nested Transaction)**

일부 DBMS는 트랜잭션 중첩을 지원합니다:

```sql
-- 외부 트랜잭션
BEGIN TRANSACTION;

    -- 내부 트랜잭션 (Savepoint 사용)
    SAVEPOINT sp1;
    
    INSERT INTO 주문 (주문ID, 고객번호, 총금액)
    VALUES ('O100', 'C001', 36000);
    
    -- 오류 발생 시 이전 Savepoint로 롤백
    IF 오류발생 THEN
        ROLLBACK TO SAVEPOINT sp1;
    END IF;
    
    -- 계속 진행
    INSERT INTO 주문상세 (주문ID, 메뉴번호, 수량)
    VALUES ('O100', 'M001', 2);

COMMIT;  -- 전체 트랜잭션 커밋
```

---

## 8. 실무 가이드

### **트랜잭션 설계 원칙**

1. **최소 범위**: 트랜잭션 범위를 최소화하여 락 시간을 줄임
2. **명시적 커밋**: 자동 커밋을 사용하지 않고 명시적으로 커밋/롤백
3. **오류 처리**: 예외 발생 시 반드시 롤백
4. **데드락 방지**: 락을 획득하는 순서를 일관되게 유지

### **좋은 트랜잭션 예시**

```sql
-- ✅ 좋은 예: 트랜잭션 범위가 적절함
BEGIN TRANSACTION;
    INSERT INTO 주문 (주문ID, 고객번호, 총금액)
    VALUES ('O100', 'C001', 36000);
    
    UPDATE 고객 SET 포인트 = 포인트 - 1000 WHERE 고객번호 = 'C001';
COMMIT;
```

### **나쁜 트랜잭션 예시**

```sql
-- ❌ 나쁜 예: 트랜잭션 범위가 너무 넓음
BEGIN TRANSACTION;
    -- 복잡한 계산
    FOR i IN 1..10000 LOOP
        -- 시간이 오래 걸리는 작업
    END LOOP;
    
    INSERT INTO 주문 (주문ID, 고객번호, 총금액)
    VALUES ('O100', 'C001', 36000);
    
    -- 사용자 입력 대기 (트랜잭션 중!)
    -- 사용자가 응답할 때까지 다른 트랜잭션 대기
COMMIT;
```

### **주의사항**

1. **긴 트랜잭션 피하기**: 트랜잭션 시간이 길어지면 락이 오래 유지되어 성능 저하
2. **사용자 입력 대기 금지**: 트랜잭션 중에 사용자 입력을 기다리면 안 됨
3. **불필요한 트랜잭션 피하기**: 읽기 전용 작업은 트랜잭션 없이 실행
4. **격리 수준 적절히 설정**: 필요 이상으로 높은 격리 수준 사용 금지

---

## 요약

- **트랜잭션**은 데이터베이스 작업의 논리적 단위로, All or Nothing 원칙을 따릅니다.
- **ACID 속성**: 원자성(Atomicity), 일관성(Consistency), 격리성(Isolation), 지속성(Durability)
- **격리 수준**: READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE
- **동시성 문제**: Lost Update, Dirty Read, Non-Repeatable Read, Phantom Read
- **락 기법**: 공유 락(읽기), 배타 락(쓰기)
- **데드락**: 두 트랜잭션이 서로의 락을 기다리는 상태
- **실무**: 트랜잭션 범위를 최소화하고, 명시적으로 커밋/롤백하며, 오류 처리를 반드시 수행해야 합니다.

---

## 참고 자료

- [ACID Properties - Wikipedia](https://en.wikipedia.org/wiki/ACID)
- [Transaction Isolation Levels - Microsoft](https://docs.microsoft.com/en-us/sql/odbc/reference/develop-app/transaction-isolation-levels)
- [Database Concurrency Control - GeeksforGeeks](https://www.geeksforgeeks.org/concurrency-control-in-dbms/)
