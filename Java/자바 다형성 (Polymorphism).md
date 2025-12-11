# 자바 다형성 (Polymorphism)

#java #다형성 #polymorphism #오버로딩 #오버라이딩 #면접 #개념정리

**관련 개념:** [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[상속과 컴포지션 (Inheritance vs Composition)|상속]] [[인터페이스]] [[추상 클래스]]

> [!note] 이어서 읽기
> 다형성을 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**과 **[[상속과 컴포지션 (Inheritance vs Composition)|상속]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

자바의 다형성이 무엇인지, 왜 필요한지, 그리고 어떻게 구현하는지 개념적으로 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 다형성을 사용하지 않으면 각 타입마다 별도의 메서드를 작성해야 하며, 새로운 타입이 추가될 때마다 기존 코드를 수정해야 합니다. 다형성이 없으면 코드의 확장성이 떨어지고, if-else나 switch 문이 많아져 유지보수가 어려워집니다. 또한 타입별로 다른 동작을 구현하기 위해 중복 코드가 발생하고, 객체지향의 핵심 원칙인 개방-폐쇄 원칙을 지킬 수 없습니다.

- 다형성은 동일한 인터페이스로 다양한 구현을 사용할 수 있게 하여 코드의 유연성과 확장성을 크게 향상시킵니다. 하나의 메서드 호출로 다양한 객체의 동작을 처리할 수 있어 코드 중복을 줄이고, 새로운 타입을 추가해도 기존 코드를 수정하지 않고 확장할 수 있습니다. 이를 통해 개방-폐쇄 원칙을 실현하고, 결합도를 낮춰 유지보수성을 높입니다.

- 지원자가 다형성의 개념과 구현 방법을 이해하고 있는지, 오버로딩과 오버라이딩의 차이를 알고 있는지, 런타임 다형성과 컴파일 타임 다형성을 구분할 수 있는지 확인하려 합니다. 또한 인터페이스와 추상 클래스를 통한 다형성 구현 능력과 실무에서 다형성을 활용하여 확장 가능한 코드를 작성할 수 있는 능력을 평가합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. 도입: 다형성의 정의와 이해

**다형성(Polymorphism)**은 서로 다른 객체가 동일한 메시지에 대해 각자의 방식으로 반응할 수 있는 능력입니다.

**그리스어 어원**: "Poly(다양한)" + "Morph(형태)" = "다양한 형태"

**C 언어와의 차이**:
- **C 언어**: 함수 오버로딩 없음, 타입별로 다른 함수명 사용
- **Java**: 오버로딩과 오버라이딩을 통한 다형성 지원

**다형성의 목표**:
1. 코드의 유연성 향상
2. 확장성 향상 (새로운 타입 추가 용이)
3. 결합도 감소
4. 코드 재사용성 향상

> [!tip] 핵심 포인트
> 다형성은 "하나의 인터페이스, 여러 구현"을 통해 코드의 유연성과 확장성을 제공합니다. 이를 통해 개방-폐쇄 원칙을 실현할 수 있습니다.

#### 2. 다형성의 종류: 컴파일 타임 vs 런타임

##### 2.1 컴파일 타임 다형성 (Compile-time Polymorphism)

**정의**: 컴파일 시점에 어떤 메서드가 호출될지 결정되는 다형성

**종류**:
- **메서드 오버로딩 (Method Overloading)**: 같은 클래스 내에서 메서드 이름은 같지만 매개변수가 다른 메서드들

**예시**:
```java
class Calculator {
    // 오버로딩: 같은 메서드명, 다른 매개변수
    public int add(int a, int b) {
        return a + b;
    }
    
    public double add(double a, double b) {
        return a + b;
    }
    
    public int add(int a, int b, int c) {
        return a + b + c;
    }
}

// 컴파일 타임에 어떤 메서드가 호출될지 결정
Calculator calc = new Calculator();
calc.add(1, 2);        // int add(int, int) 호출
calc.add(1.0, 2.0);    // double add(double, double) 호출
calc.add(1, 2, 3);     // int add(int, int, int) 호출
```

**특징**:
- 컴파일 시점에 메서드 결정
- 반환 타입만 다른 것은 오버로딩 아님
- 매개변수의 타입, 개수, 순서가 달라야 함

##### 2.2 런타임 다형성 (Runtime Polymorphism)

**정의**: 실행 시점에 어떤 메서드가 호출될지 결정되는 다형성

**종류**:
- **메서드 오버라이딩 (Method Overriding)**: 상속 관계에서 자식 클래스가 부모 클래스의 메서드를 재정의

**예시**:
```java
// 부모 클래스
class Pitcher {
    public Ball throwBall() {
        return new Ball(140, "default");
    }
}

// 자식 클래스들
class FastballPitcher extends Pitcher {
    @Override
    public Ball throwBall() {
        return new Ball(150, "fastball");  // 직구
    }
}

class CurveballPitcher extends Pitcher {
    @Override
    public Ball throwBall() {
        return new Ball(120, "curveball");  // 커브
    }
}

// 런타임에 실제 객체의 메서드가 호출됨
Pitcher pitcher1 = new FastballPitcher();
Pitcher pitcher2 = new CurveballPitcher();

pitcher1.throwBall();  // FastballPitcher의 throwBall() 호출
pitcher2.throwBall();  // CurveballPitcher의 throwBall() 호출
```

**특징**:
- 런타임에 실제 객체 타입에 따라 메서드 결정
- 동적 바인딩 (Dynamic Binding)
- 상속과 인터페이스 구현을 통해 구현

#### 3. 다형성 구현 방법

##### 3.1 메서드 오버로딩 (Method Overloading)

**정의**: 같은 클래스 내에서 메서드 이름은 같지만 매개변수가 다른 메서드들을 정의하는 것

**조건**:
- 메서드 이름이 같아야 함
- 매개변수의 타입, 개수, 순서가 달라야 함
- 반환 타입은 오버로딩과 무관 (반환 타입만 다른 것은 불가능)

**예시**:
```java
class BaseballTeam {
    // 오버로딩: 매개변수 개수 다름
    public void playGame() {
        System.out.println("경기 시작");
    }
    
    public void playGame(String opponent) {
        System.out.println(opponent + "와 경기 시작");
    }
    
    // 오버로딩: 매개변수 타입 다름
    public void addPlayer(String name) {
        System.out.println(name + " 선수 추가");
    }
    
    public void addPlayer(int number) {
        System.out.println(number + "번 선수 추가");
    }
    
    // 오버로딩: 매개변수 순서 다름
    public void setScore(int home, int away) {
        System.out.println("홈: " + home + ", 원정: " + away);
    }
    
    public void setScore(String home, String away) {
        System.out.println("홈: " + home + ", 원정: " + away);
    }
}
```

**오버로딩이 아닌 경우**:
```java
// ❌ 반환 타입만 다른 것은 오버로딩 아님
public int calculate(int a, int b) { ... }
public double calculate(int a, int b) { ... }  // 컴파일 에러!
```

##### 3.2 메서드 오버라이딩 (Method Overriding)

**정의**: 상속 관계에서 자식 클래스가 부모 클래스의 메서드를 재정의하는 것

**조건**:
- 상속 관계여야 함
- 메서드 이름, 매개변수, 반환 타입이 같아야 함
- 접근 제어자는 부모보다 넓거나 같아야 함
- `@Override` 어노테이션 사용 권장

**예시**:
```java
// 부모 클래스
class Player {
    public void play() {
        System.out.println("선수가 경기를 합니다");
    }
    
    protected void train() {
        System.out.println("선수가 훈련합니다");
    }
}

// 자식 클래스
class Pitcher extends Player {
    @Override
    public void play() {
        System.out.println("투수가 공을 던집니다");
    }
    
    @Override
    public void train() {  // protected → public 가능 (넓어짐)
        System.out.println("투수가 투구 훈련을 합니다");
    }
}

class Batter extends Player {
    @Override
    public void play() {
        System.out.println("타자가 공을 칩니다");
    }
}

// 다형성 활용
Player player1 = new Pitcher();
Player player2 = new Batter();

player1.play();  // "투수가 공을 던집니다" 출력
player2.play();  // "타자가 공을 칩니다" 출력
```

##### 3.3 인터페이스를 통한 다형성

**정의**: 인터페이스를 구현한 여러 클래스가 동일한 인터페이스 타입으로 사용될 수 있는 것

**예시**:
```java
// 인터페이스 정의
interface Pitcher {
    Ball throwBall();
    void warmUp();
}

// 다양한 구현
class FastballPitcher implements Pitcher {
    @Override
    public Ball throwBall() {
        return new Ball(150, "fastball");
    }
    
    @Override
    public void warmUp() {
        System.out.println("직구 연습");
    }
}

class CurveballPitcher implements Pitcher {
    @Override
    public Ball throwBall() {
        return new Ball(120, "curveball");
    }
    
    @Override
    public void warmUp() {
        System.out.println("커브 연습");
    }
}

// 다형성 활용
Pitcher pitcher1 = new FastballPitcher();
Pitcher pitcher2 = new CurveballPitcher();

// 동일한 인터페이스로 다양한 구현 사용
pitcher1.throwBall();  // 직구
pitcher2.throwBall();  // 커브

// 배열이나 컬렉션에 다양한 타입 저장 가능
Pitcher[] pitchers = {
    new FastballPitcher(),
    new CurveballPitcher()
};

for (Pitcher pitcher : pitchers) {
    pitcher.throwBall();  // 각자의 방식으로 투구
}
```

**인터페이스 다형성의 장점**:
- 결합도 감소 (구현 클래스가 아닌 인터페이스에 의존)
- 확장성 향상 (새로운 구현 추가 용이)
- 테스트 용이 (Mock 객체 사용 가능)

##### 3.4 추상 클래스를 통한 다형성

**정의**: 추상 클래스를 상속한 여러 클래스가 추상 클래스 타입으로 사용될 수 있는 것

**예시**:
```java
// 추상 클래스
abstract class Player {
    protected String name;
    
    public Player(String name) {
        this.name = name;
    }
    
    // 추상 메서드 (구현 없음)
    public abstract void play();
    
    // 일반 메서드 (공통 기능)
    public void introduce() {
        System.out.println("저는 " + name + "입니다");
    }
}

// 구체 클래스
class Pitcher extends Player {
    public Pitcher(String name) {
        super(name);
    }
    
    @Override
    public void play() {
        System.out.println(name + "이 공을 던집니다");
    }
}

class Batter extends Player {
    public Batter(String name) {
        super(name);
    }
    
    @Override
    public void play() {
        System.out.println(name + "이 공을 칩니다");
    }
}

// 다형성 활용
Player player1 = new Pitcher("류현진");
Player player2 = new Batter("이승엽");

player1.introduce();  // 공통 메서드
player1.play();       // 각자의 구현
player2.introduce();
player2.play();
```

#### 4. 다형성의 동작 원리

##### 4.1 동적 바인딩 (Dynamic Binding)

**정의**: 런타임에 실제 객체 타입에 따라 메서드가 결정되는 메커니즘

**동작 과정**:
```
1. 컴파일 타임
   Pitcher pitcher = new FastballPitcher();
   pitcher.throwBall();
   → 컴파일러는 Pitcher 타입의 throwBall() 메서드 호출로 인식
   
2. 런타임
   → JVM이 실제 객체 타입(FastballPitcher)을 확인
   → FastballPitcher의 throwBall() 메서드를 호출
   → 동적 바인딩 완료
```

**예시**:
```java
class Pitcher {
    public void throwBall() {
        System.out.println("기본 투구");
    }
}

class FastballPitcher extends Pitcher {
    @Override
    public void throwBall() {
        System.out.println("직구 투구");
    }
}

// 동적 바인딩
Pitcher pitcher = new FastballPitcher();
pitcher.throwBall();  // "직구 투구" 출력 (실제 객체 타입의 메서드 호출)
```

##### 4.2 가상 메서드 테이블 (Virtual Method Table, VMT)

**정의**: 각 클래스마다 메서드 호출 정보를 저장하는 테이블

**동작 원리**:
- 각 클래스는 자신의 VMT를 가짐
- 오버라이딩된 메서드는 자식 클래스의 VMT에 저장
- 메서드 호출 시 VMT를 참조하여 실제 메서드 찾음

**예시**:
```
Pitcher 클래스 VMT:
- throwBall() → Pitcher.throwBall()

FastballPitcher 클래스 VMT:
- throwBall() → FastballPitcher.throwBall() (오버라이딩)

Pitcher pitcher = new FastballPitcher();
pitcher.throwBall();
→ FastballPitcher의 VMT를 참조하여 FastballPitcher.throwBall() 호출
```

#### 5. 정리 및 결론

**핵심 요약**:

1. **다형성의 정의**:
   - 서로 다른 객체가 동일한 메시지에 대해 각자의 방식으로 반응할 수 있는 능력
   - "하나의 인터페이스, 여러 구현"

2. **다형성의 종류**:
   - **컴파일 타임 다형성**: 메서드 오버로딩 - 컴파일 시점에 메서드 결정
   - **런타임 다형성**: 메서드 오버라이딩 - 실행 시점에 메서드 결정

3. **다형성 구현 방법**:
   - 메서드 오버로딩: 같은 클래스 내에서 매개변수가 다른 메서드들
   - 메서드 오버라이딩: 상속 관계에서 메서드 재정의
   - 인터페이스: 인터페이스를 통한 다형성
   - 추상 클래스: 추상 클래스를 통한 다형성

4. **다형성의 동작 원리**:
   - 동적 바인딩: 런타임에 실제 객체 타입에 따라 메서드 결정
   - 가상 메서드 테이블: 메서드 호출 정보를 저장하는 테이블

**개인적 인사이트**:
- 다형성은 객체지향 프로그래밍의 핵심 원리 중 하나입니다.
- 다형성을 통해 개방-폐쇄 원칙을 실현하고 확장 가능한 코드를 작성할 수 있습니다.
- 인터페이스를 통한 다형성은 결합도를 낮추고 테스트 가능한 코드를 만듭니다.

**추가 학습 제안**:
- 개방-폐쇄 원칙 (Open-Closed Principle)
- 전략 패턴 (Strategy Pattern)
- 팩토리 패턴 (Factory Pattern)
- 의존성 주입 (Dependency Injection)

---

### 설정 시 반드시 고려해야 할 파라미터

- **인터페이스 설계**: 
  - 다형성을 위한 인터페이스는 명확하고 간결하게 설계
  - 인터페이스 분리 원칙 (Interface Segregation Principle) 준수

- **오버라이딩 규칙**: 
  - 접근 제어자는 부모보다 넓거나 같아야 함
  - 예외는 부모보다 좁거나 같아야 함
  - `@Override` 어노테이션 사용으로 실수 방지

- **오버로딩 주의사항**: 
  - 매개변수 타입이 모호하면 컴파일 에러 발생 가능
  - 자동 타입 변환으로 인한 예상치 못한 메서드 호출 주의

- **성능 고려**: 
  - 동적 바인딩은 약간의 오버헤드가 있지만 무시할 수준
  - 가상 메서드 테이블을 통한 빠른 메서드 조회

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> 1. **"오버로딩과 오버라이딩이 같은 것"**: 
>    - 오버로딩은 같은 클래스 내에서 매개변수가 다른 메서드, 오버라이딩은 상속 관계에서 메서드 재정의입니다.
> 
> 2. **"다형성은 상속에서만 가능"**: 
>    - 인터페이스를 통해서도 다형성을 구현할 수 있으며, 오히려 더 유연합니다.
> 
> 3. **"동적 바인딩은 느리다"**: 
>    - 가상 메서드 테이블을 통해 빠르게 메서드를 찾을 수 있어 오버헤드는 미미합니다.
> 
> 4. **"필드도 다형성이 적용된다"**: 
>    - 다형성은 메서드에만 적용되고, 필드는 변수 타입에 따라 결정됩니다.

#### 다형성 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **높은 결합도**: 구체 클래스에 직접 의존하여 확장이 어려움
> - **코드 중복**: 타입별로 별도의 메서드를 작성해야 함
> - **확장성 부족**: 새로운 타입 추가 시 기존 코드 수정 필요
> - **if-else 남발**: 타입 체크를 위한 조건문이 많아짐
> - **테스트 어려움**: 구체 클래스에 의존하여 Mock 객체 사용 어려움
> - **유지보수 어려움**: 변경 사항이 여러 곳에 영향을 미침

---

### 예상 꼬리질문 정리

#### 1. 오버로딩과 오버라이딩의 차이는?

- **오버로딩 (Overloading)**: 
  - 같은 클래스 내에서 메서드 이름은 같지만 매개변수가 다른 메서드들
  - 컴파일 타임에 메서드 결정
  - 반환 타입은 무관
  
- **오버라이딩 (Overriding)**: 
  - 상속 관계에서 자식 클래스가 부모 클래스의 메서드를 재정의
  - 런타임에 메서드 결정
  - 메서드 시그니처가 동일해야 함

#### 2. 컴파일 타임 다형성과 런타임 다형성의 차이는?

- **컴파일 타임 다형성**: 
  - 메서드 오버로딩
  - 컴파일 시점에 어떤 메서드가 호출될지 결정
  - 정적 바인딩
  
- **런타임 다형성**: 
  - 메서드 오버라이딩
  - 실행 시점에 실제 객체 타입에 따라 메서드 결정
  - 동적 바인딩

#### 3. 필드에도 다형성이 적용되나요?

- 아니요, 다형성은 메서드에만 적용됩니다.
- 필드는 변수 타입에 따라 결정됩니다.

**예시**:
```java
class Parent {
    String name = "Parent";
    public void print() {
        System.out.println(name);
    }
}

class Child extends Parent {
    String name = "Child";  // 필드 숨김 (다형성 아님)
    
    @Override
    public void print() {
        System.out.println(name);
    }
}

Parent p = new Child();
System.out.println(p.name);  // "Parent" 출력 (변수 타입에 따라 결정)
p.print();  // "Child" 출력 (실제 객체 타입에 따라 결정)
```

#### 4. 동적 바인딩은 어떻게 동작하나요?

- **가상 메서드 테이블 (VMT)** 사용
- 각 클래스마다 메서드 호출 정보를 저장하는 테이블
- 메서드 호출 시 실제 객체 타입의 VMT를 참조하여 메서드 찾음
- 오버헤드는 매우 작아 성능에 큰 영향 없음

#### 5. 인터페이스와 추상 클래스의 다형성 차이는?

- **인터페이스**: 
  - 다중 구현 가능
  - 구현 없이 메서드 시그니처만 정의
  - 다형성 구현에 더 유연
  
- **추상 클래스**: 
  - 단일 상속만 가능
  - 일부 구현 포함 가능
  - 공통 기능을 정의할 때 사용

#### 6. 다형성을 사용하면 성능이 저하되나요?

- 동적 바인딩은 약간의 오버헤드가 있지만 무시할 수 있는 수준입니다.
- 가상 메서드 테이블을 통한 빠른 메서드 조회로 성능 저하는 미미합니다.
- 다형성의 장점(확장성, 유지보수성)이 성능 오버헤드보다 훨씬 큽니다.

#### 7. 다형성과 개방-폐쇄 원칙의 관계는?

- **개방-폐쇄 원칙**: 확장에는 열려있고, 수정에는 닫혀있어야 함
- 다형성을 통해 새로운 타입을 추가해도 기존 코드를 수정하지 않고 확장 가능
- 인터페이스나 추상 클래스를 통해 확장 포인트를 제공

---

## 관련 노트

- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - 다형성의 기반이 되는 개념
- **[[상속과 컴포지션 (Inheritance vs Composition)|상속]]** - 다형성 구현을 위한 상속
- **[[인터페이스]]** - 인터페이스를 통한 다형성
- **[[추상 클래스]]** - 추상 클래스를 통한 다형성
- **[[오버로딩]]** - 메서드 오버로딩
- **[[오버라이딩]]** - 메서드 오버라이딩

---

> [!tip] 다음 단계
> 다형성을 이해했다면, **[[SOLID 원칙]]**과 **[[디자인 패턴]]**을 학습하여 더 나은 설계를 할 수 있습니다.

---

**태그:** #java #다형성 #polymorphism #오버로딩 #오버라이딩 #면접 #개념정리

