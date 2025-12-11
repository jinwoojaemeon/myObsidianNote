# 상속과 컴포지션 (Inheritance vs Composition)

#java #상속 #컴포지션 #inheritance #composition #설계 #면접 #개념정리

**관련 개념:** [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[다형성]] [[캡슐화]] [[SOLID 원칙]]

> [!note] 이어서 읽기
> 상속과 컴포지션을 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

상속과 컴포지션의 차이를 이해하고, 언제 어떤 것을 사용해야 하는지 개념적으로 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 상속을 단순 코드 재사용 목적으로 사용하면 취약한 기본 클래스 문제, 불필요한 메서드 노출 문제 등이 발생합니다. 상속을 남용하면 하위 클래스가 상위 클래스의 구현에 강하게 의존하여 캡슐화가 깨지고, 상위 클래스의 변경이 모든 하위 클래스에 영향을 미칩니다. 또한 클라이언트가 기대하지 않는 메서드까지 노출되어 예상치 못한 버그가 발생할 수 있습니다.

- 컴포지션은 코드 재사용을 위한 더 안전한 방법을 제공합니다. 객체를 포함하는 방식으로 구현하여 캡슐화를 지키고, 내부 구현 변경에 대한 영향을 최소화합니다. 필요한 기능만 선택적으로 노출할 수 있어 인터페이스를 깔끔하게 유지할 수 있습니다. 상속은 다형성과 계층 구조를 위한 것이고, 컴포지션은 코드 재사용을 위한 것입니다.

- 상속과 컴포지션의 차이를 이해하고 있는지, 언제 어떤 것을 사용해야 하는지 판단할 수 있는지, 상속의 문제점과 컴포지션의 장점을 알고 있는지 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. 도입: 상속과 컴포지션의 정의

**상속(Inheritance)**은 기존 클래스의 필드와 메서드를 물려받아 새로운 클래스를 생성하는 기법입니다.

**컴포지션(Composition)**은 전체를 표현하는 클래스가 부분을 표현하는 객체를 포함하여, 해당 객체의 코드를 재사용하는 방법입니다.

**차이점**:
- **상속**: `is-a` 관계 (A는 B이다)
- **컴포지션**: `has-a` 관계 (A는 B를 가지고 있다)

> [!tip] 핵심 포인트
> 상속은 다형성과 계층 구조를 위한 것이고, 컴포지션은 코드 재사용을 위한 것입니다. 
> 단순 코드 재사용이 목적이라면 컴포지션을 사용해야 합니다.

#### 2. 상속의 장점과 문제점

##### 2.1 상속의 장점

- ✅ 중복 코드를 줄이고 코드를 쉽게 재사용
- ✅ 다양적인 클래스의 계층 구조를 만들 수 있음
- ✅ 다형성 구현 가능
- ✅ 코드의 일관성 유지

##### 2.2 상속 남용의 문제점

**문제 1: 취약한 기본 클래스 문제 (Fragile Base Class)**

- 하위 클래스가 상위 클래스의 구현에 강하게 의존하여 캡슐화가 지켜지지 않음
- 상위 클래스(부모 클래스)의 내부 구현을 변경하면, 모든 하위 클래스를 수정해야 하는 상황 발생

**예시**:
```java
// 부모 클래스
class Lotto {
    private int[] numbers;  // 배열 사용
    
    public int[] getNumbers() {
        return numbers;
    }
}

// 자식 클래스
class WinningLotto extends Lotto {
    public void printNumbers() {
        int[] numbers = getNumbers();  // 배열에 의존
        // ...
    }
}

// 부모 클래스 변경 시
class Lotto {
    private List<Integer> numbers;  // 리스트로 변경
    
    public List<Integer> getNumbers() {
        return numbers;
    }
}

// 자식 클래스에서 컴파일 에러 발생!
class WinningLotto extends Lotto {
    public void printNumbers() {
        int[] numbers = getNumbers();  // ❌ 타입 불일치
    }
}
```

**문제 2: 불필요한 메서드 노출 문제**

- 자식 클래스 입장에서 불필요하거나 내부 규칙과 맞지 않는 부모 클래스의 퍼블릭 메서드까지 어쩔 수 없이 노출
- 클라이언트가 기대하지 않는 메서드에 접근 가능하여 예상치 못한 결과 초래

**예시**:
```java
// Vector 클래스 구조 (간소화된 예시)
class Vector<E> {
    protected Object[] elementData;
    protected int elementCount;
    
    // 인덱스 기반 메서드들
    public void add(E element) {
        // 마지막에 요소 추가
        elementData[elementCount++] = element;
    }
    
    public void add(int index, E element) {
        ...
    }
    
    public E remove(int index) {
		...
    }
    
    public E get(int index) {
        ...
    }
    
    public int size() {
        ...
    }
    
    public boolean isEmpty() {
        ...
    }
    
    // ... 기타 많은 메서드들
}

// Stack이 Vector를 상속받는 경우
class Stack extends Vector {
    public void push(Object item) {
        add(item);  // Vector의 add 메서드 사용
    }
    
    public Object pop() {
        if (isEmpty()) {
            throw new EmptyStackException();
        }
        return remove(size() - 1);  // 마지막 요소 제거
    }
}

// Stack은 LIFO(Last In First Out) 규칙을 가져야 하지만
Stack stack = new Stack();
stack.push("A");  // 스택에 "A" 추가
stack.push("B");  // 스택에 "B" 추가
// 스택: ["A", "B"] (top: "B")

stack.add(0, "C");  // ❌ Vector의 add(index, element) 메서드 사용 가능
                    // 인덱스 0에 "C" 삽입 → 스택의 규칙(LIFO)에 어긋남
// >>> LIFO 규칙 위반: push/pop만으로 접근해야 하는데 add로 중간에 삽입 가능

// 더 심각한 문제
stack.remove(1);  // ❌ Vector의 remove(index) 메서드도 사용 가능
                  // 인덱스 1의 "A" 제거 → 스택 구조 완전히 깨짐
// 스택: ["C", "B"] (원래 의도와 완전히 다름!)
```

> [!warning] 상속의 문제점
> 상속을 단순 코드 재사용(서브 클래싱) 목적으로 사용하면 캡슐화가 깨지고, 유지보수가 어려워집니다.

#### 3. 컴포지션의 정의와 장점

##### 3.1 컴포지션이란?

**정의**: 전체를 표현하는 클래스가 부분을 표현하는 객체를 포함하여, 해당 객체의 코드를 재사용하는 방법입니다.
        
**구현 방식**:
- 새 클래스의 필드로 부분 클래스의 인스턴스를 참조
- 해당 인스턴스의 메서드를 호출하여 기능을 구현

**예시**:
```java
// Vector와 List의 관계 설명
// - Vector는 List 인터페이스를 구현한 클래스
// - ArrayList도 List 인터페이스를 구현한 클래스
// - 둘 다 List 타입으로 사용 가능
// 
// 상속 예시: Stack extends Vector (Vector 클래스를 상속)
// 컴포지션 예시: Stack이 List를 포함 (List 인터페이스 타입으로 사용)

// 컴포지션 사용 (Vector를 포함하는 경우)
class Stack {
    // "포함"의 의미: Stack 객체가 내부에 Vector 객체를 가지고 있음
    // - Stack은 Vector를 상속받지 않음 (is-a 관계 아님)
    // - Stack은 Vector를 필드로 가지고 있음 (has-a 관계)
    private Vector<Object> elements;  // Vector 객체를 포함
    
    public Stack() {
        // Stack 객체가 생성될 때 내부에 Vector 객체를 생성
        // Stack과 Vector는 별개의 객체이지만, Stack이 Vector를 소유
        this.elements = new Vector<>();
    }
    
    public void push(Object item) {
        // Stack이 내부에 가진 Vector 객체의 메서드를 호출
        // Stack은 Vector의 모든 메서드를 노출하지 않고, 필요한 것만 사용
        elements.add(item);  // Vector의 add(element) 메서드 사용
    }
    
    public Object pop() {
        if (elements.isEmpty()) {
            throw new EmptyStackException();
        }
        // Vector의 remove 메서드를 사용하되, 인덱스는 Stack이 제어
        // 클라이언트는 Vector의 remove(index)를 직접 호출할 수 없음
        return elements.remove(elements.size() - 1);
    }
    
    // 불필요한 메서드는 노출하지 않음
    // elements.add(index, element) 같은 메서드는 클라이언트가 접근 불가
}

// 사용 예시
Stack stack = new Stack();
stack.push("A");
stack.push("B");
// stack.add(0, "C");  // ❌ 컴파일 에러! Stack에는 add(index, element) 메서드가 없음
// stack.remove(1);    // ❌ 컴파일 에러! Stack에는 remove(index) 메서드가 없음
// Stack은 push/pop만 제공 → LIFO 규칙 보장
```

**상속 vs 컴포지션 비교**:
```
상속 (Inheritance): Stack extends Vector
┌─────────────┐
│   Stack     │
│  ┌────────┐ │
│  │ Vector │ │  ← Stack이 Vector를 포함 (상속)
│  │ 기능   │ │     Stack = Vector (is-a)
│  └────────┘ │     하나의 객체
│  push()     │
│  pop()      │
└─────────────┘
Stack instanceof Vector == true

컴포지션 (Composition): Stack has Vector
┌─────────────┐         ┌─────────────┐
│   Stack     │────────→│   Vector    │
│             │  참조   │             │
│  push()     │         │  add()      │
│  pop()      │         │  remove()   │
│  elements───┼────────→│  get()      │
└─────────────┘         └─────────────┘
별도 객체                별도 객체
Stack ≠ Vector           Stack이 참조
```

**핵심 차이**:
```
상속 (Inheritance):
┌─────────────┐
│   Stack     │  ← Stack instanceof Vector == true
│  ┌────────┐ │     Stack은 Vector 타입으로도 사용 가능
│  │ Vector │ │     Vector v = new Stack(); ✅ 가능
│  └────────┘ │     하나의 객체에 Vector 기능이 포함됨
└─────────────┘
Stack = Vector (같은 객체)

컴포지션 (Composition):
┌─────────────┐         ┌─────────────┐
│   Stack     │────────→│   Vector    │
│  elements   │  참조   │             │
│  (필드)     │         │  (별도 객체) │
└─────────────┘         └─────────────┘
Stack instanceof Vector == false
Stack은 Vector 타입으로 사용 불가
Vector v = new Stack(); ❌ 불가능
두 개의 별도 객체 (참조 관계)
```

**핵심 차이**:
- **상속**: Stack은 Vector이다 → Vector의 모든 메서드 상속 (Stack도 Vector가 됨)
- **컴포지션**: Stack은 Vector를 가지고 있다 → Vector 객체를 필드로 소유, 필요한 메서드만 사용 (Stack은 Stack, Vector는 Vector)

##### 3.2 컴포지션의 장점

- ✅ 재사용되는 객체의 내부 구현이 외부에 공개되지 않아 캡슐화를 지킬 수 있음
- ✅ 부분 객체의 내부 구현이 변경되어도 비교적 안전함
- ✅ 조합된 객체의 모든 퍼블릭 메서드를 공개하지 않고, 필요한 기능만 선택적으로 호출하여 노출 가능
- ✅ 결합도가 낮아 유지보수가 쉬움

**해결 예시 1: WinningLotto 문제 해결**
```java
// 컴포지션 사용
class Lotto {
    private List<Integer> numbers;
    
    public List<Integer> getNumbers() {
        return new ArrayList<>(numbers);  // 방어적 복사
    }
}

class WinningLotto {
    private Lotto lotto;  // 상속 대신 포함
    
    public WinningLotto(Lotto lotto) {
        this.lotto = lotto;
    }
    
    public void printNumbers() {
        List<Integer> numbers = lotto.getNumbers();  // 안전하게 사용
        // ...
    }
}
```

**해결 예시 2: Stack 문제 해결**
```java
// 컴포지션 사용
class Stack {
    private List<Object> elements;  // Vector 대신 List 사용
    
    public void push(Object item) {
        elements.add(item);
    }
    
    public Object pop() {
        if (elements.isEmpty()) {
            throw new EmptyStackException();
        }
        return elements.remove(elements.size() - 1);
    }
    
    // add 메서드 노출 안 함 → LIFO 규칙 보장
}
```

#### 4. 상속 vs 컴포지션: 올바른 사용 기준

##### 4.1 사용 기준 비교

|    구분    |                   목적                    |                          확인 질문                           |                권장 사용                 |
| :------: | :-------------------------------------: | :------------------------------------------------------: | :----------------------------------: |
|  **상속**  | 다형성(Polymorphism) 구현 및 계층 구조를 위한 서브 타이핑 | 1. 두 객체가 서로 Is-A 관계인가?<br>2. 클라이언트가 두 객체에 동일한 행동을 기대하는가? | 클라이언트 관점에서 동일하게 행동하는 인스턴스를 구현하고 싶을 때 |
| **컴포지션** |         단순 코드 재활용을 위한 서브 클래싱 지양         |                            -                             |      코드를 재활용하고 중복 코드를 없애고 싶을 때       |

##### 4.2 상속을 사용해야 하는 경우

**조건**:
1. 두 객체가 서로 **Is-A 관계**인가?
2. 클라이언트가 두 객체에 **동일한 행동을 기대**하는가?

**예시**:
```java
// ✅ 상속이 적절한 경우
// "FastballPitcher는 Pitcher이다" → Is-A 관계
// 클라이언트는 모든 Pitcher에 동일한 행동(throwBall)을 기대

interface Pitcher {
    Ball throwBall();
}

class FastballPitcher implements Pitcher {
    public Ball throwBall() {
        return new Ball(150, "fastball");
    }
}

class CurveballPitcher implements Pitcher {
    public Ball throwBall() {
        return new Ball(120, "curveball");
    }
}

// 클라이언트는 Pitcher 타입으로 사용
Pitcher pitcher = new FastballPitcher();
pitcher.throwBall();  // 다형성 활용
```

##### 4.3 컴포지션을 사용해야 하는 경우

**조건**:
- 단순히 코드를 재활용하고 중복 코드를 없애고 싶을 때
- Is-A 관계가 아닌 Has-A 관계일 때

**예시**:0
```java
// ✅ 컴포지션이 적절한 경우
// "BaseballTeam은 Pitcher를 가지고 있다" → Has-A 관계
// 단순 코드 재사용이 목적

class BaseballTeam {
    private Pitcher pitcher;  // 컴포지션
    private Batter batter;
    
    public BaseballTeam(Pitcher pitcher, Batter batter) {
        this.pitcher = pitcher;
        this.batter = batter;
    }
    
    public void playInning() {
        Ball ball = pitcher.throwBall();
        HitResult result = batter.hit(ball);
        // ...
    }
}
```

#### 5. 정리 및 결론

**핵심 요약**:

1. **상속의 정의와 문제점**:
   - 상속은 기존 클래스의 필드와 메서드를 물려받아 새로운 클래스를 생성하는 기법
   - 단순 코드 재사용 목적으로 사용하면 취약한 기본 클래스 문제, 불필요한 메서드 노출 문제 발생
   - 하위 클래스가 상위 클래스의 구현에 강하게 의존하여 캡슐화가 깨짐

2. **컴포지션의 정의와 장점**:
   - 컴포지션은 전체를 표현하는 클래스가 부분을 표현하는 객체를 포함하여 코드를 재사용하는 방법
   - 캡슐화를 지킬 수 있고, 내부 구현 변경에 안전하며, 필요한 기능만 선택적으로 노출 가능

3. **올바른 사용 기준**:
   - **상속**: Is-A 관계이고, 클라이언트가 동일한 행동을 기대할 때 (다형성, 계층 구조)
   - **컴포지션**: 단순 코드 재사용이 목적일 때 (Has-A 관계)

**개인적 인사이트**:
- 상속은 강력한 도구이지만 남용하면 문제를 일으킵니다. 항상 "Is-A 관계인가?"를 먼저 확인해야 합니다.
- 컴포지션은 상속보다 더 유연하고 안전한 코드 재사용 방법입니다.
- "상속보다 컴포지션을 선호하라(Favor composition over inheritance)"는 원칙을 기억해야 합니다.

**추가 학습 제안**:
- SOLID 원칙 (특히 Liskov Substitution Principle)
- 디자인 패턴 (Strategy, Decorator 등)
- 리팩토링 기법

---

### 설정 시 반드시 고려해야 할 파라미터

- **관계 확인**: 
  - Is-A 관계인지 Has-A 관계인지 먼저 확인
  - 클라이언트가 두 객체에 동일한 행동을 기대하는지 확인

- **캡슐화 유지**: 
  - 상속 사용 시 부모 클래스의 내부 구현 변경이 자식 클래스에 미치는 영향 고려
  - 컴포지션 사용 시 필요한 기능만 노출

- **다형성 활용**: 
  - 상속은 다형성을 위한 것이므로, 인터페이스나 추상 클래스를 통한 상속 고려
  - 클라이언트가 인터페이스에 의존하도록 설계

- **유지보수성**: 
  - 변경에 대한 영향 범위 최소화
  - 결합도를 낮게 유지

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> 1. **"상속은 코드 재사용을 위한 것이다"**: 
>    - 상속의 주된 목적은 다형성과 계층 구조 구현입니다. 단순 코드 재사용은 컴포지션을 사용해야 합니다.
> 
> 2. **"상속을 많이 사용하면 좋은 설계"**: 
>    - 상속은 강한 결합을 만들 수 있으므로, 신중하게 사용해야 합니다. 컴포지션이 더 안전한 경우가 많습니다.
> 
> 3. **"부모 클래스의 모든 메서드를 자식 클래스에서 사용할 수 있어야 한다"**: 
>    - 불필요한 메서드 노출은 캡슐화를 깨뜨리고 예상치 못한 버그를 유발할 수 있습니다.
> 
> 4. **"컴포지션은 상속보다 복잡하다"**: 
>    - 초기에는 복잡해 보일 수 있지만, 장기적으로 유지보수가 더 쉬워집니다.

#### 상속과 컴포지션 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **취약한 기본 클래스 문제**: 부모 클래스 변경 시 모든 자식 클래스에 영향
> - **불필요한 메서드 노출**: 클라이언트가 기대하지 않는 메서드에 접근 가능
> - **높은 결합도**: 상속으로 인한 강한 의존성으로 유지보수 어려움
> - **캡슐화 위반**: 내부 구현이 외부에 노출되어 안정성 저하
> - **다형성 오용**: Is-A 관계가 아닌데 상속을 사용하여 논리적 오류 발생
> - **코드 중복**: 컴포지션을 사용하지 않아 중복 코드 발생

---

### 예상 꼬리질문 정리

#### 1. 상속과 컴포지션의 차이는?

- **상속 (Inheritance)**: 
  - Is-A 관계 ("A는 B이다")
  - 부모 클래스의 필드와 메서드를 물려받음
  - 다형성과 계층 구조 구현에 적합
  - 강한 결합
  
- **컴포지션 (Composition)**: 
  - Has-A 관계 ("A는 B를 가지고 있다")
  - 객체를 포함하여 코드 재사용
  - 단순 코드 재사용에 적합
  - 느슨한 결합

#### 2. 언제 상속을 사용해야 하나요?

- **Is-A 관계**일 때
- 클라이언트가 두 객체에 **동일한 행동을 기대**할 때
- **다형성**을 구현하고 싶을 때
- **계층 구조**를 만들고 싶을 때

**예시**: 
- `FastballPitcher`는 `Pitcher`이다 → 상속 적합
- `BaseballTeam`은 `Pitcher`를 가지고 있다 → 컴포지션 적합

#### 3. 언제 컴포지션을 사용해야 하나요?

- **Has-A 관계**일 때
- **단순 코드 재사용**이 목적일 때
- 상속으로 인한 **캡슐화 위반**을 피하고 싶을 때
- **불필요한 메서드 노출**을 방지하고 싶을 때

**예시**: 
- `Stack`이 `List`의 기능을 재사용하고 싶을 때 → 컴포지션 사용
- `WinningLotto`가 `Lotto`의 기능을 재사용하고 싶을 때 → 컴포지션 사용

#### 4. 취약한 기본 클래스 문제란?

- 하위 클래스가 상위 클래스의 **구현에 강하게 의존**하는 문제
- 상위 클래스의 내부 구현을 변경하면 **모든 하위 클래스를 수정**해야 함
- **캡슐화가 지켜지지 않음**

**해결 방법**: 컴포지션 사용 또는 인터페이스를 통한 상속

#### 5. 불필요한 메서드 노출 문제란?

- 자식 클래스가 부모 클래스의 **모든 퍼블릭 메서드를 상속**받음
- 자식 클래스의 **내부 규칙과 맞지 않는 메서드**까지 노출됨
- 클라이언트가 **기대하지 않는 메서드**에 접근 가능

**예시**: `Stack`이 `Vector`를 상속받아 `add(index, element)` 메서드가 노출되어 LIFO 규칙 위반

**해결 방법**: 컴포지션 사용하여 필요한 메서드만 노출

#### 6. "상속보다 컴포지션을 선호하라"는 원칙은?

- **Favor composition over inheritance**
- 상속은 강한 결합을 만들 수 있으므로, 가능하면 컴포지션을 사용
- 컴포지션이 더 유연하고 안전한 코드 재사용 방법
- 상속은 정말 필요한 경우(Is-A 관계, 다형성)에만 사용

#### 7. 상속과 컴포지션을 함께 사용할 수 있나요?

- 네, 가능합니다. 실제로 많은 경우 두 가지를 함께 사용합니다.
- **인터페이스를 통한 상속** + **구현 클래스의 컴포지션** 조합이 일반적

**예시**:
```java
// 인터페이스 상속 (다형성)
interface Pitcher {
    Ball throwBall();
}

// 컴포지션 사용
class BaseballTeam {
    private Pitcher pitcher;  // 컴포지션
    
    public BaseballTeam(Pitcher pitcher) {
        this.pitcher = pitcher;
    }
    
    public void playGame() {
        Ball ball = pitcher.throwBall();  // 다형성 활용
    }
}
```

---

## 관련 노트

- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - 상속과 컴포지션의 기반이 되는 개념
- **[[다형성]]** - 상속을 통한 다형성 구현
- **[[캡슐화]]** - 상속과 컴포지션에서의 캡슐화
- **[[SOLID 원칙]]** - 객체지향 설계 원칙
- **[[인터페이스]]** - 인터페이스를 통한 상속

---

> [!tip] 다음 단계
> 상속과 컴포지션을 이해했다면, **[[SOLID 원칙]]**과 **[[디자인 패턴]]**을 학습하여 더 나은 설계를 할 수 있습니다.

---

**태그:** #java #상속 #컴포지션 #inheritance #composition #설계 #면접 #개념정리

