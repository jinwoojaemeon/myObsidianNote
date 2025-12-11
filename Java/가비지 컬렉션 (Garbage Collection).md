
#java #gc #garbage-collection #heap #memory #jvm #면접 #개념정리

**관련 개념:** [[JVM (Java Virtual Machine, 자바 가상 머신)|JVM]] [[Runtime Data Area]] 

---

## Topic (오늘의 주제)

Java의 가비지 컬렉션이 무엇인지, 왜 필요한지, 그리고 어떻게 동작하는지 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 가비지 컬렉션이 없으면 개발자가 수동으로 메모리를 관리해야 하며, 메모리 누수나 해제된 메모리 접근 같은 심각한 버그가 발생합니다. 대규모 애플리케이션에서 수동 메모리 관리는 거의 불가능하며, GC가 없으면 OutOfMemoryError가 빈번하게 발생하여 서비스 장애로 이어집니다.

- GC는 애플리케이션의 동적 메모리 할당을 자동으로 관리하여 개발 생산성을 극대화합니다. Heap 메모리에서 사용하지 않는 객체를 자동으로 회수하여 메모리 효율성을 높이고, 개발자가 메모리 관리에 신경 쓰지 않고 비즈니스 로직에 집중할 수 있게 합니다. 다양한 GC 알고리즘을 통해 애플리케이션 특성에 맞는 성능 최적화가 가능합니다.

- JVM의 메모리 구조에 대한 이해, Stack과 Heap의 차이, GC가 Heap에서만 동작하는 이유, 그리고 GC 알고리즘 선택과 튜닝 능력을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**가비지 컬렉션(Garbage Collection)**은 JVM이 Heap 영역에서 더 이상 사용되지 않는 객체를 자동으로 회수하여 메모리를 관리하는 기능입니다.

**메모리 해제의 차이**:
- **C 언어**: 개발자가 `free()` 함수로 직접 메모리를 해제해야 함
- **Java**: `free()` 같은 메서드가 없음 → GC가 자동으로 메모리 해제

**GC의 역할**:
1. Heap 영역에서 동적으로 할당된 객체 중 불필요한 객체를 찾아 메모리에서 해제
2. 운영체제로부터 메모리를 할당받고 반환
3. 애플리케이션의 메모리 요청에 응답
4. 애플리케이션에서 아직 사용 중인 메모리 영역 식별

> [!tip] 핵심 포인트
> Java는 개발자가 메모리 해제를 신경 쓰지 않아도 되도록 GC를 통해 자동 메모리 관리를 제공합니다. 이는 개발 생산성을 높이지만, 동시에 애플리케이션 성능에도 직접적인 영향을 줍니다.

#### 2. GC 대상 선정 기준: Reachable vs Unreachable

GC는 객체의 **"도달 가능성(Reachability)"** 을 기준으로 회수 대상을 판단합니다.

**Root Set (GC Root)**:
- JVM Stack의 지역 변수, 파라미터
- Method Area의 클래스 정보, 정적 변수
- JNI (Java Native Interface) 참조
- 메서드 영역의 상수 풀

**Reachable Object (도달 가능한 객체)**:
- Root Set에서 참조 체인을 통해 도달 가능한 객체
- **살아있는 객체** → GC 대상이 아님
- 예: 스택의 변수가 참조하는 객체, 그 객체가 참조하는 다른 객체들

**Unreachable Object (도달 불가능한 객체)**:
- 어떤 Root에서도 도달할 수 없는 객체
- **가비지(Garbage)** → GC 대상
- 예: 참조가 모두 끊어진 객체

```
Reachability 판별 예시:

Stack (Root Set)
  │
  ├─→ Object A (Reachable)
  │     │
  │     └─→ Object B (Reachable)
  │
  └─→ Object C (Reachable)
        │
        └─→ Object D (Reachable)

Object E (Unreachable) ← GC 대상
  │
  └─→ Object F (Unreachable) ← GC 대상
```

> [!important] 핵심 원리
> GC는 Root Set을 시작점으로 참조 체인을 따라가며 도달 가능한 모든 객체를 찾습니다. 이 과정에서 발견되지 않은 객체는 가비지로 판단되어 회수됩니다.

#### 3. GC 동작 원리: Mark and Sweep

가장 기본적인 GC 알고리즘은 **Mark-Sweep**입니다. 이 원리를 기반으로 다양한 GC 알고리즘이 발전했습니다.

**Mark 단계 (마킹 단계)**:
1. Root Set을 시작점으로 설정
2. Root Set에서 참조하는 모든 객체를 재귀적으로 탐색
3. 도달 가능한 객체를 모두 **마킹(Mark)** 표시
4. 참조 체인을 따라가며 연결된 모든 객체를 마킹

**Sweep 단계 (청소 단계)**:
1. Heap 전체를 스캔
2. 마킹되지 않은 객체(Unreachable)를 식별
3. 해당 객체들을 Heap에서 **제거(Sweep)**
4. 메모리 공간을 재사용 가능하게 만듦

```
Mark & Sweep 동작 과정:

[Mark 단계]
Root Set → Object A (마킹) → Object B (마킹)
        → Object C (마킹) → Object D (마킹)
        → Object E (마킹 안 됨)
        → Object F (마킹 안 됨)

[Sweep 단계]
Object E, F 제거 → 메모리 회수
```

**Mark & Sweep의 특징**:
- ✅ 간단하고 직관적인 알고리즘
- ✅ 순환 참조도 처리 가능 (Root에서 도달 불가능하면 회수)
- ❌ 메모리 단편화 발생 가능
- ❌ Stop-the-World 발생 (애플리케이션 일시 중지)

#### 왜 Heap 메모리에서 GC가 동작하는가?

**Stack 메모리의 특성**:
- 메서드 호출 시 생성되고 종료 시 자동 해제
- 스레드별로 독립적이며, LIFO(Last In First Out) 구조
- 지역 변수, 매개변수, 메서드 프레임 저장
- **생명주기가 명확함**: 메서드 종료 시 자동으로 정리됨

**Heap 메모리의 특성**:
- 객체 인스턴스가 저장되는 영역
- 여러 스레드가 공유하는 공간
- **생명주기가 불명확함**: 객체가 언제 사용되지 않는지 런타임에 결정됨
- 참조가 끊어진 객체는 더 이상 접근 불가하지만 메모리는 점유된 상태

**결론**: Stack은 생명주기가 명확하여 자동 정리되지만, Heap의 객체는 참조 관계가 복잡하고 생명주기가 불명확하여 GC가 필요합니다.

#### Stack vs Heap 메모리 할당 기준

|    구분     |        Stack        |      Heap      |
| :-------: | :-----------------: | :------------: |
| **저장 대상** | 기본 타입 변수, 객체 참조(주소) |    객체 인스턴스     |
| **할당 시점** |      메서드 호출 시       | `new` 키워드 사용 시 |
| **해제 시점** |     메서드 종료 시 자동     |   GC가 회수할 때    |
| **생명주기**  |    명확 (메서드 스코프)     |  불명확 (참조 관계)   |
|  **스레드**  |       스레드별 독립       |   모든 스레드 공유    |
|  **크기**   |    제한적 (보통 1MB)     |    크게 설정 가능    |
|  **속도**   |     빠름 (순차 할당)      |    상대적으로 느림    |

**할당 예시**:
```java
public void example() {
    int num = 10;              // Stack: 기본 타입
    String str = "Hello";       // Stack: 참조 변수
    Object obj = new Object();  // Stack: 참조 변수, Heap: Object 인스턴스
    // 메서드 종료 시 Stack은 자동 해제, Heap의 Object는 GC가 회수
}
```

#### 4. JVM 힙 메모리 구조와 GC 발생 시점

**메모리 분할 이유**:
- 모든 객체를 매번 전체 스캔하면 부하가 크고 비효율적
- **Weak Generational Hypothesis (약한 세대 가설)**:
  - 대부분의 객체는 생성 직후 곧바로 사용되지 않음
  - 오래 살아남은 객체는 계속 살아남을 가능성이 높음
- 따라서 Heap을 세대별로 나누어 효율적으로 관리

**Generational Heap 구조**:
![[images.png]]

```
┌─────────────────────────────────────┐
│         Heap (Young + Old)          │
├─────────────────────────────────────┤
│  Young Generation                   │
│  ├── Eden Space (신규 객체)          │
│  ├── Survivor Space 0               │
│  └── Survivor Space 1               │
├─────────────────────────────────────┤
│  Old Generation                     │
│  └── 오래 살아남은 객체               │
└─────────────────────────────────────┤
```

##### 4.1 Young Generation

**구조**
- **Eden Space**: 새 객체가 최초로 생성되는 영역
- **Survivor Space 0 (S0)**: Minor GC 후 살아남은 객체가 이동하는 영역
- **Survivor Space 1 (S1)**: S0와 교차하여 사용되는 영역

**Minor GC 발생 시점**: Eden 영역이 가득 찼을 때

**Minor GC 동작 과정** :

```
1. Eden Space 가득 참
      ↓
2. [Mark 단계]
   - Root Set부터 시작하여 Reachable 객체 식별
   - Eden + Survivor 영역의 모든 객체를 스캔
   - Root Set → 참조 체인 → Reachable 객체 모두 마킹
      ↓
3. [Sweep 단계]
   - 마킹되지 않은 Unreachable 객체는 즉시 제거
   - 마킹된 Reachable 객체만 Survivor Space로 이동
      ↓
4. Survivor 간 교차 이동
   - S0에 있던 객체 → S1로 이동 (또는 그 반대)
   - Age 카운터 증가 (객체가 GC를 몇 번 견뎠는지)
      ↓
5. Promotion (승격)
   - 특정 Age 이상 생존한 객체 → Old Generation으로 이동
   - 기본 Age 임계값: 15번 (Java 8 기준)
```

**Minor GC 특징**:
- ✅ 빈번하게 발생하지만 **빠름** (Young Generation은 작음)
- ✅ Stop-the-World 시간이 **짧음** (수십 밀리초)
- ✅ 대부분의 객체가 이 단계에서 회수됨
- ✅ Mark & Sweep이 Young Generation만 대상으로 수행되어 효율적

##### 4.2 Old Generation

**Promotion (승격)**:
- Survivor 영역에서 특정 횟수(Age) 이상 살아남은 객체는 Old 영역으로 승격
- Age는 객체가 GC를 몇 번 견뎠는지를 나타내는 카운터
- JVM 옵션 `-XX:MaxTenuringThreshold`로 조정 가능

**Major GC (Full GC) 발생 시점**: Old Generation이 가득 찼을 때

**Major GC 동작 과정 (Mark & Sweep 적용)**:

```
1. Old Generation 가득 참
      ↓
2. [Mark 단계]
   - Root Set부터 시작하여 전체 Heap 스캔
   - Young Generation + Old Generation 모두 포함
   - Root Set → 참조 체인을 따라가며 모든 Reachable 객체 마킹
   - 전체 Heap을 순회하며 도달 가능한 모든 객체 식별
      ↓
3. [Sweep 단계]
   - 마킹되지 않은 Unreachable 객체 제거
   - 메모리 회수
      ↓
4. [Compact 단계] (일부 GC 알고리즘에서 추가)
   - 살아남은 객체를 한쪽으로 모아 단편화 해소
   - 연속된 메모리 공간 확보
   - 다음 할당을 위한 메모리 정리
```

**Major GC 특징**:
- ❌ 드물게 발생하지만 **느림** (전체 Heap 스캔)
- ❌ Stop-the-World 시간이 **김** (수백 밀리초 ~ 수초)
- ❌ 애플리케이션 성능에 **직접적인 영향**
- ⚠️ Full GC 빈도를 최소화하는 것이 성능 튜닝의 핵심
- ⚠️ Mark & Sweep이 전체 Heap을 대상으로 수행되어 비용이 큼

#### 전체 GC 동작 플로우 통합

```
객체 생성
   new Object() → Eden Space 할당
      ↓
Eden Space 가득 참
   Minor GC 발생
      ↓
[Minor GC: Mark & Sweep]
   - Mark: Root Set → Reachable 객체 식별 (Eden + Survivor)
   - Sweep: Unreachable 객체 제거
   - Reachable 객체 → Survivor Space 이동
      ↓
Survivor에서 반복 생존
   - 여러 번 GC를 견딘 객체
   - Age 카운터 증가
      ↓
Promotion (승격)
   - Age 임계값 도달 → Old Generation 이동
      ↓
Old Generation 가득 참
   Major GC (Full GC) 발생
      ↓
[Major GC: Mark & Sweep]
   - Mark: 전체 Heap의 Reachable 객체 식별
   - Sweep: Unreachable 객체 제거
   - Compact: 메모리 단편화 해소 (선택적)
   - Stop-the-World: 애플리케이션 일시 중지
```

> [!important] 핵심 정리
> - **Minor GC**: Young Generation만 대상, Mark & Sweep 빠르게 수행
> - **Major GC**: 전체 Heap 대상, Mark & Sweep 느리게 수행
> - 두 GC 모두 **Reachability 분석**과 **Mark & Sweep 원리**를 기반으로 동작

#### 장점/단점

**장점**:
- 자동 메모리 관리로 개발 생산성 향상
- 메모리 누수 방지 (참조가 끊어진 객체 자동 회수)
- 개발자가 메모리 관리에 신경 쓸 필요 없음
- 다양한 GC 알고리즘으로 애플리케이션 특성에 맞는 최적화 가능

**단점**:
- GC 실행 시 Stop-the-World로 인한 일시적 멈춤
- CPU 리소스 사용 (백그라운드 스레드)
- 메모리 오버헤드 (GC를 위한 추가 메모리)
- 튜닝이 복잡할 수 있음

#### 필요 조건

- **JVM 실행**: GC는 JVM에 내장되어 자동 실행
- **Heap 메모리**: 객체가 저장될 Heap 영역 필요
- **GC 알고리즘 선택**: 애플리케이션 특성에 맞는 GC 선택
  - Serial GC: 단일 스레드, 작은 애플리케이션
  - Parallel GC: 멀티 스레드, 처리량 중심
  - G1 GC: 대용량 Heap, 지연 시간 중심 (Java 9+ 기본)
  - ZGC/Shenandoah: 초저지연 시간

---
#### 5. 정리 및 결론

**핵심 요약**:

1. **GC의 정의와 역할**:
   - GC는 JVM이 Heap 영역에서 더 이상 사용되지 않는 객체를 자동으로 회수하여 메모리를 관리하는 기능입니다.
   - C 언어는 `free()` 함수로 직접 메모리를 해제하지만, Java는 GC가 이를 자동으로 수행합니다.

2. **GC 대상 선정 기준: Reachable vs Unreachable**:
   - GC는 Root Set(스택의 지역변수, 파라미터, Method Area 등)과의 참조 관계를 확인합니다.
   - Reachable: Root Set에서 참조 체인을 통해 도달 가능한 객체 (살아남음)
   - Unreachable: 어떤 Root에서도 도달할 수 없는 객체 (GC 대상, Garbage)

3. **GC 동작 원리: Mark and Sweep**:
   - **Mark**: Root Set부터 스캔하여 Reachable한 객체를 찾아 마킹합니다.
   - **Sweep**: 마킹되지 않은 Unreachable한 객체를 힙 영역에서 제거합니다.
   - 이 원리를 기반으로 다양한 GC 알고리즘이 발전했습니다.

4. **JVM 힙 메모리 구조와 GC 발생 시점**:
   - **Young Generation**: Eden & Survivor - 새 객체는 Eden에 생성되고, GC 후 살아남은 객체는 Survivor 영역으로 이동합니다.
   - **Minor GC**: Eden 영역이 가득 찰 때 발생하며, Mark & Sweep으로 Reachable 객체만 Survivor로 이동합니다.
   - **Old Generation**: Survivor 영역에서 특정 횟수(Age) 이상 살아남은 객체는 Old 영역으로 승격됩니다.
   - **Major GC**: Old 영역이 가득 찰 때 발생하며, Mark & Sweep으로 전체 Heap을 정리합니다.
   - 

---
- GC는 단순한 메모리 정리 도구가 아니라 JVM 애플리케이션의 성능과 안정성을 좌우하는 핵심 요소입니다.
- Reachability 분석과 Mark & Sweep 원리를 이해하면 GC 동작을 깊이 있게 이해할 수 있습니다.
- GC 로그 분석, Heap 구조 이해는 실무 성능 튜닝의 핵심 역량입니다.

**추가 학습 제안**:
- JVM 내부 구조
- GC 알고리즘 종류 (G1, ZGC, Shenandoah 등)
- GC 튜닝 전략 및 모니터링 도구

---
### 설정 시 반드시 고려해야 할 파라미터

- **Heap 크기 설정**:
  - `-Xms`: 초기 Heap 크기 (예: `-Xms512m`)
  - `-Xmx`: 최대 Heap 크기 (예: `-Xmx2g`)
  - 서버 클래스 머신: 기본값 1/64 (초기), 1/4 (최대) 물리 메모리

- **GC 알고리즘 선택**:
  - **자동 선택 (Ergonomics)**: JVM이 시스템 환경에 맞게 자동 선택
    - 서버 클래스 머신 (2개 이상 CPU, 2GB 이상 메모리): G1 GC 기본 선택
    - 클라이언트 머신: Serial GC 기본 선택
  - **수동 선택**: 애플리케이션 특성에 맞게 명시적 선택
    - `-XX:+UseG1GC`: G1 GC 사용 (대용량, 지연 시간 중심)
    - `-XX:+UseParallelGC`: Parallel GC 사용 (처리량 중심)
    - `-XX:+UseSerialGC`: Serial GC 사용 (작은 애플리케이션)

- **GC 목표 설정**:
  - `-XX:MaxGCPauseMillis=200`: 최대 GC 일시 중지 시간 목표 (밀리초)
  - `-XX:GCTimeRatio=19`: GC 시간 비율 목표 (1/20 = 5%)

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "GC가 자동으로 메모리를 관리하므로 신경 쓸 필요가 없다"는 생각은 위험합니다. 잘못된 참조나 순환 참조로 인해 메모리 누수가 발생할 수 있으며, GC 튜닝이 필요할 수 있습니다. 또한 "GC가 Stack에서도 동작한다"는 오해도 있는데, GC는 Heap 메모리에서만 동작합니다.
> 
> "GC 알고리즘을 무조건 수동으로 선택해야 한다"는 생각도 잘못되었습니다. JVM의 Ergonomics(자동 선택)가 대부분의 경우 적절한 GC를 선택해주므로, 성능 문제가 발생하지 않으면 기본값을 사용하는 것이 좋습니다. 성능 문제가 있을 때만 수동 선택과 튜닝을 고려해야 합니다.
#### GC 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **OutOfMemoryError**: Heap 크기가 부족하거나 메모리 누수 발생
> - **긴 GC 일시 중지**: Stop-the-World로 인한 서비스 응답 지연
> - **처리량 저하**: GC가 너무 자주 발생하여 애플리케이션 성능 저하
> - **메모리 누수**: 순환 참조나 잘못된 참조로 인한 메모리 누수
> - **GC 튜닝 실패**: 애플리케이션 특성에 맞지 않는 GC 선택





### 출처

https://youtu.be/FMUpVA0Vvjw?si=9tlTNxSgET0nlQGU - 우아한테크코스 유튜브 조엘의 gc
https://coding-factory.tistory.com/829 - 코딩팩토리 gc 총정리 
https://docs.oracle.com/en/java/javase/17/gctuning/introduction-garbage-collection-tuning.html#GUID-326EB4CF-8C8C-4267-8355-21AB04F0D304 - 오라클 공식문서 가비지컬렉션 Tuning Guide 

---

### 예상 꼬리질문정리

#### 1. 왜 Stack은 GC가 필요 없고 Heap만 필요한가?

- **Stack**: 메서드 호출 시 생성되고 종료 시 자동 해제되므로 생명주기가 명확함
- **Heap**: 객체의 생명주기가 불명확하고, 참조 관계가 복잡하여 언제 사용되지 않는지 런타임에 결정됨
- **결론**: Stack은 자동 정리되지만, Heap은 참조가 끊어진 객체를 GC가 식별하고 회수해야 함

#### 2. GC가 객체를 식별하는 방법은? (Reachability Analysis)

- **Reachability Analysis (도달 가능성 분석)**: 루트(GC Root)로부터 참조 가능한 객체는 살아있는 객체
- **GC Root (Root Set)**: 
  - JVM Stack의 지역 변수, 파라미터
  - Method Area의 클래스 정보, 정적 변수
  - JNI (Java Native Interface) 참조
  - 메서드 영역의 상수 풀
- **Reachable 객체**: Root Set에서 참조 체인을 통해 도달 가능한 객체 → 살아남음
- **Unreachable 객체**: 어떤 Root에서도 도달할 수 없는 객체 → 가비지 (GC 대상)

**Mark & Sweep에서의 활용**:
- Mark 단계에서 Root Set을 시작점으로 참조 체인을 따라가며 Reachable 객체를 마킹
- Sweep 단계에서 마킹되지 않은 Unreachable 객체를 제거

#### 3. Minor GC와 Major GC의 차이는? (Mark & Sweep 관점)

- **Minor GC**: 
  - Young Generation (Eden + Survivor)만 정리
  - **Mark & Sweep**: Root Set부터 Young Generation만 스캔하여 Reachable 객체 마킹
  - 빈번하게 발생하지만 빠름 (스캔 범위가 작음)
  - Stop-the-World 시간이 짧음 (수십 밀리초)
  - 대부분의 객체가 이 단계에서 회수됨
  
- **Major GC (Full GC)**:
  - 전체 Heap (Young + Old) 정리
  - **Mark & Sweep**: Root Set부터 전체 Heap을 스캔하여 Reachable 객체 마킹
  - 드물게 발생하지만 느림 (스캔 범위가 큼)
  - Stop-the-World 시간이 김 (수백 밀리초 ~ 수초)
  - 애플리케이션 성능에 큰 영향
  - Compact 단계 추가 가능 (메모리 단편화 해소)

#### 4. Mark & Sweep 알고리즘의 장단점은?

**장점**:
- ✅ 간단하고 직관적인 알고리즘
- ✅ 순환 참조도 처리 가능 (Root에서 도달 불가능하면 회수)
- ✅ 모든 GC 알고리즘의 기반이 되는 원리

**단점**:
- ❌ 메모리 단편화 발생 가능 (객체 제거 후 빈 공간 발생)
- ❌ Stop-the-World 발생 (애플리케이션 일시 중지)
- ❌ 전체 Heap 스캔 시 비용이 큼

**개선된 알고리즘**:
- **Mark-Compact**: Sweep 후 살아남은 객체를 한쪽으로 모아 단편화 해소
- **Copying**: 살아남은 객체를 다른 영역으로 복사 (Young Generation에서 사용)
- **Generational GC**: Heap을 세대별로 나누어 Mark & Sweep 효율성 향상

#### 5. Generational Hypothesis가 무엇인가?

- **가설 1**: 대부분의 객체는 생성 직후 곧바로 사용되지 않음
- **가설 2**: 오래 살아남은 객체는 계속 살아남을 가능성이 높음
- **결과**: Heap을 세대별로 나누어 효율적으로 관리
  - Young Generation: 자주 GC 수행 (빠른 회수, Mark & Sweep 범위 작음)
  - Old Generation: 드물게 GC 수행 (오래 살아남은 객체, Mark & Sweep 범위 큼)

#### 6. GC 알고리즘을 선택하는 기준은?

**자동 선택 (Ergonomics)**:
- JVM이 시스템 환경을 분석하여 자동으로 GC 선택
- 서버 클래스 머신 (2개 이상 CPU, 2GB 이상 메모리): **G1 GC** 기본 선택
- 클라이언트 머신: **Serial GC** 기본 선택
- **대부분의 경우 자동 선택으로 충분함**

**수동 선택이 필요한 경우**:
- 애플리케이션 특성에 맞는 최적화가 필요한 경우
- 성능 문제가 발생하여 튜닝이 필요한 경우
- 특정 GC 알고리즘의 장점이 필요한 경우

**GC 알고리즘별 특징**:
- **Serial GC**: 
  - 단일 스레드, 작은 애플리케이션 (100MB 이하)
  - 데스크톱 애플리케이션
  - 오버헤드가 가장 적음
  
- **Parallel GC**: 
  - 멀티 스레드, 처리량 중심
  - 배치 작업, 대용량 데이터 처리
  - 처리량 최대화
  
- **G1 GC**: 
  - 대용량 Heap (수 GB 이상), 지연 시간 중심
  - Java 9+ 기본, 서버 애플리케이션
  - 지연 시간과 처리량의 균형
  
- **ZGC/Shenandoah**: 
  - 초저지연 시간 (10ms 이하)
  - 실시간 시스템, 대규모 서비스
  - 매우 큰 Heap (수십 GB 이상)

**선택 전략**:
1. **기본값 사용**: 먼저 JVM의 자동 선택(기본값)으로 실행
2. **성능 모니터링**: GC 로그를 분석하여 문제점 파악
3. **필요 시 변경**: 성능 문제가 있을 때만 수동 선택 및 튜닝

#### 7. GC 튜닝 전략은?

- **처리량 우선**: `-XX:GCTimeRatio` 설정, Heap 크기 증가
- **지연 시간 우선**: `-XX:MaxGCPauseMillis` 설정, G1 GC 사용
- **메모리 사용량 최소화**: Heap 크기 최소화, GC 빈도 증가
- **균형**: 처리량과 지연 시간의 타협점 찾기

---

## 관련 노트

- **[[JVM (Java Virtual Machine, 자바 가상 머신)|JVM]]** - GC가 동작하는 JVM 환경
- **[[Heap 메모리]]** - GC의 주요 대상인 Heap 메모리 상세 설명 (이어서 읽기 권장)
- **[[Stack 메모리]]** - GC가 필요 없는 Stack 메모리 상세 설명
- **[[Runtime Data Area]]** - Heap과 Stack의 메모리 구조

---

**태그:** #java #gc #garbage-collection #heap #memory #jvm #면접 #개념정리

