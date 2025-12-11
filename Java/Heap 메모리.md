# Heap 메모리

#java #heap #memory #jvm #gc #면접 #개념정리

**관련 개념:** [[Stack 메모리]] [[JVM (Java Virtual Machine, 자바 가상 머신)|JVM]] [[Runtime Data Area]] [[가비지 컬렉션 (Garbage Collection)|가비지 컬렉션]]

> [!note] 관련 개념
> Heap 메모리는 **[[Stack 메모리]]**와 함께 JVM의 Runtime Data Area를 구성합니다. Stack은 메서드 실행 데이터를 저장하고, Heap은 객체 인스턴스를 저장합니다. Heap은 **[[가비지 컬렉션 (Garbage Collection)|GC]]**의 주요 대상입니다.

---

## Topic (오늘의 주제)

Java의 Heap 메모리가 무엇인지, 어떤 데이터를 저장하는지, 그리고 가비지 컬렉션이 왜 필요한지 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- **실무**: Heap 메모리가 없으면 객체 인스턴스를 저장할 수 없어 객체지향 프로그래밍이 불가능합니다. 모든 객체를 Heap에 저장하므로, Heap 크기 부족이나 메모리 누수는 OutOfMemoryError로 이어져 서비스 장애를 발생시킵니다. Heap 관리는 애플리케이션 성능에 직접적인 영향을 미칩니다.

- **구조적 의미**: Heap은 모든 스레드가 공유하는 메모리 영역으로, 객체 인스턴스를 효율적으로 저장하고 관리합니다. Generational 구조를 통해 객체의 생명주기에 따라 세대별로 관리하여 가비지 컬렉션의 효율성을 극대화하며, 동적 메모리 할당을 통해 런타임에 필요한 만큼만 메모리를 사용합니다.

- **면접 의도**: JVM의 메모리 구조에 대한 이해, Heap과 Stack의 차이, 왜 Heap에서만 GC가 필요한지, 그리고 Heap의 내부 구조(Generational Heap)를 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 개념 정의

**Heap 메모리**는 모든 스레드가 공유하는 메모리 영역으로, 객체 인스턴스와 배열이 저장되는 공간입니다. `new` 키워드로 생성된 모든 객체는 Heap에 할당되며, 생명주기가 불명확하여 가비지 컬렉터가 관리합니다.

#### 동작 방식

```
객체 생성 및 Heap 할당:

1. 객체 생성
   new Object() 호출
      ↓
2. Heap 메모리 할당
   - Eden Space에 객체 할당
   - 객체 초기화
      ↓
3. 참조 변수 연결
   Stack의 참조 변수가 Heap의 객체를 가리킴
      ↓
4. 객체 사용
   참조 변수를 통해 객체 접근
      ↓
5. 참조 끊김
   참조 변수가 null이 되거나 스코프 벗어남
      ↓
6. GC 회수
   가비지 컬렉터가 사용하지 않는 객체 회수
```

#### 저장되는 데이터

**Heap에 저장되는 것**:
- 객체 인스턴스 (모든 클래스의 인스턴스)
- 배열의 실제 데이터
- 컬렉션 객체 (List, Map 등)

**Heap에 저장되지 않는 것**:
- 기본 타입 변수 (Stack에 저장)
- 참조 변수 (Stack에 저장, 객체의 주소만 저장)
- 클래스 정보 (Method Area에 저장)

#### Heap의 내부 구조 (Generational Heap)

**Generational Heap 구조**:

```
┌─────────────────────────────────────┐
│         Heap (Young + Old)          │
├─────────────────────────────────────┤
│  Young Generation                   │
│  ├── Eden Space (신규 객체)          │
│  ├── Survivor Space 0 (S0)          │
│  └── Survivor Space 1 (S1)          │
├─────────────────────────────────────┤
│  Old Generation (Tenured)           │
│  └── 오래 살아남은 객체              │
└─────────────────────────────────────┘
```

**Generational Hypothesis (세대 가설)**:
- 대부분의 객체는 생성 직후 곧바로 사용되지 않음
- 오래 살아남은 객체는 계속 살아남을 가능성이 높음
- 따라서 Heap을 세대별로 나누어 효율적으로 관리

**객체 생명주기**:
1. **Eden Space**: 새로 생성된 객체가 할당됨
2. **Minor GC**: Eden이 가득 차면 살아있는 객체를 Survivor Space로 이동
3. **Survivor Space**: S0와 S1 사이를 오가며 여러 번 GC를 견딘 객체는 Old Generation으로 승격
4. **Old Generation**: 오래 살아남은 객체 저장
5. **Major GC (Full GC)**: Old Generation이 가득 차면 전체 Heap을 정리

#### Stack vs Heap 비교

| 구분 | Stack | Heap |
|:---:|:---:|:---:|
| **저장 대상** | 기본 타입, 참조 변수 | 객체 인스턴스 |
| **할당 시점** | 메서드 호출 시 | `new` 키워드 사용 시 |
| **해제 시점** | 메서드 종료 시 자동 | GC가 회수할 때 |
| **생명주기** | 명확 (메서드 스코프) | 불명확 (참조 관계) |
| **스레드** | 스레드별 독립 | 모든 스레드 공유 |
| **크기** | 제한적 (보통 1MB) | 크게 설정 가능 |
| **속도** | 빠름 (순차 할당) | 상대적으로 느림 |
| **GC 필요** | ❌ 불필요 (자동 해제) | ✅ 필요 |

#### 장점/단점

**장점**:
- 동적 메모리 할당으로 필요한 만큼만 사용
- 모든 스레드가 공유하여 객체 공유 용이
- 크기를 크게 설정 가능 (수 GB 이상)
- Generational 구조로 GC 효율성 향상

**단점**:
- 생명주기가 불명확하여 GC 필요
- GC 실행 시 Stop-the-World 발생
- 메모리 단편화 가능
- 할당/해제 속도가 Stack보다 느림

#### 필요 조건

- **JVM 실행**: Heap은 JVM 시작 시 할당
- **Heap 크기 설정**: 
  - `-Xms`: 초기 Heap 크기 (예: `-Xms512m`)
  - `-Xmx`: 최대 Heap 크기 (예: `-Xmx2g`)
- **GC 알고리즘**: Heap 관리를 위한 GC 필요

---

### **Interview Answer Version (면접 답변식 요약)**

Heap 메모리는 모든 스레드가 공유하는 메모리 영역으로, 객체 인스턴스와 배열이 저장되는 공간입니다.

사용하는 이유는 객체지향 프로그래밍에서 동적으로 생성되는 객체들을 저장하기 위해서입니다. `new` 키워드로 생성된 모든 객체는 Heap에 할당되며, 생명주기가 불명확하여 **[[가비지 컬렉션 (Garbage Collection)|가비지 컬렉터]]**가 관리합니다.

핵심 특징은 Generational Heap 구조를 사용한다는 점입니다. 대부분의 객체가 생성 직후 곧바로 사용되지 않는다는 가설에 기반하여, Heap을 Young Generation과 Old Generation으로 나누어 효율적으로 관리합니다. 이는 **[[Stack 메모리]]**와 달리 GC가 필요한 이유이기도 합니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 설정 시 반드시 고려해야 할 파라미터

- **Heap 크기 설정**:
  - `-Xms512m`: 초기 Heap 크기 512MB
  - `-Xmx2g`: 최대 Heap 크기 2GB
  - 서버 클래스 머신: 기본값 1/64 (초기), 1/4 (최대) 물리 메모리

- **GC 알고리즘 선택**:
  - `-XX:+UseG1GC`: G1 GC (대용량, 지연 시간 중심)
  - `-XX:+UseParallelGC`: Parallel GC (처리량 중심)

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "Heap에 참조 변수를 저장한다"는 생각은 잘못되었습니다. 참조 변수는 **[[Stack 메모리]]**에 저장되고, Heap에는 실제 객체 인스턴스만 저장됩니다. 참조 변수는 Heap의 객체를 가리키는 주소만 저장합니다.

#### 적용 사례

```java
public void example() {
    // Stack: 참조 변수 obj
    // Heap: Object 인스턴스
    Object obj = new Object();
    
    // Stack: 참조 변수 arr
    // Heap: 배열의 실제 데이터
    int[] arr = new int[10];
    
    // 메서드 종료 시:
    // Stack의 변수들은 자동 해제
    // Heap의 객체는 GC가 회수할 때까지 남아있음
}
```

#### 이걸 모르고 사용하면 생기는 문제

> [!error] 이걸 모르고 사용하면
> 
> - **OutOfMemoryError**: Heap 크기 부족 또는 메모리 누수
> - **긴 GC 일시 중지**: Stop-the-World로 인한 서비스 응답 지연
> - **메모리 누수**: 참조가 끊어지지 않은 객체로 인한 누수
> - **Heap 크기 부적절**: 너무 작으면 자주 GC, 너무 크면 메모리 낭비

---

### 예상 꼬리질문정리

#### 1. 왜 Heap에서만 가비지 컬렉션이 필요한가?

- **생명주기가 불명확함**: 객체가 언제 사용되지 않는지 런타임에 결정됨
- **참조 관계가 복잡함**: 여러 참조 변수가 같은 객체를 가리킬 수 있음
- **자동 해제 불가**: **[[Stack 메모리]]**와 달리 메서드 종료 시 자동 해제되지 않음
- **결론**: 참조가 끊어진 객체를 **[[가비지 컬렉션 (Garbage Collection)|GC]]**가 식별하고 회수해야 함

#### 2. Heap의 Generational 구조는 왜 필요한가?

- **Generational Hypothesis**: 대부분의 객체는 생성 직후 곧바로 사용되지 않음
- **효율적인 GC**: Young Generation에서 자주 GC 수행 (빠른 회수)
- **성능 최적화**: Old Generation은 드물게 GC 수행 (오래 살아남은 객체)
- **결과**: 전체 Heap을 매번 정리하는 것보다 효율적

#### 3. Eden Space, Survivor Space, Old Generation의 역할은?

- **Eden Space**: 새로 생성된 객체가 할당되는 공간
- **Survivor Space**: Minor GC를 견딘 살아있는 객체가 이동하는 공간 (S0 ↔ S1)
- **Old Generation**: 여러 번 GC를 견딘 오래 살아남은 객체 저장
- **이동 경로**: Eden → Survivor → Old Generation

#### 4. Heap 크기를 어떻게 설정하나?

- **초기 크기**: `-Xms512m` (512MB)
- **최대 크기**: `-Xmx2g` (2GB)
- **기본값**: 서버 클래스 머신에서 1/64 (초기), 1/4 (최대) 물리 메모리
- **주의**: 
  - 너무 작으면 자주 GC 발생
  - 너무 크면 메모리 낭비 및 GC 시간 증가

#### 5. Stack과 Heap의 관계는?

- **Stack의 참조 변수**: Heap의 객체를 가리킴
- **메서드 종료 시**: **[[Stack 메모리]]**는 자동 해제, Heap의 객체는 GC가 회수
- **예시**:
  ```java
  Object obj = new Object();
  // Stack(obj) → Heap(Object 인스턴스)
  // obj = null; // 참조 끊김 → GC 대상
  ```

#### 6. OutOfMemoryError는 언제 발생하나?

- **Heap 크기 부족**: 할당할 메모리가 없을 때
- **메모리 누수**: 참조가 끊어지지 않은 객체가 계속 쌓일 때
- **해결 방법**:
  - Heap 크기 증가 (`-Xmx`)
  - 메모리 누수 원인 찾기
  - GC 튜닝

---

## 관련 노트

- **[[Stack 메모리]]** - 메서드 실행 데이터를 저장하는 메모리 영역 (이전 단계 권장)
- **[[JVM (Java Virtual Machine, 자바 가상 머신)|JVM]]** - Heap이 속한 JVM 환경
- **[[Runtime Data Area]]** - Stack과 Heap의 메모리 구조
- **[[가비지 컬렉션 (Garbage Collection)|가비지 컬렉션]]** - Heap 메모리 관리 메커니즘 (이어서 읽기 권장)

---

> [!tip] 이전 단계
> Heap 메모리를 이해하기 전에 **[[Stack 메모리]]**를 먼저 학습하면 메모리 구조를 더 잘 이해할 수 있습니다. Stack과 Heap의 차이와 관계를 파악하면 **[[가비지 컬렉션 (Garbage Collection)|GC]]**의 동작 원리도 명확해집니다.

---

**태그:** #java #heap #memory #jvm #gc #면접 #개념정리

