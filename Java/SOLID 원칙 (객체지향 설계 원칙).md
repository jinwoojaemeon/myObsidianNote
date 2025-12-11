# SOLID 원칙 (객체지향 설계 원칙)

#java #solid #객체지향 #설계원칙 #면접 #개념정리 #SRP #OCP #LSP #ISP #DIP

**관련 개념:** [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[상속과 컴포지션 (Inheritance vs Composition)|상속]] [[자바 다형성 (Polymorphism)|다형성]]

> [!note] 이어서 읽기
> SOLID 원칙을 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**, **[[상속과 컴포지션 (Inheritance vs Composition)|상속]]**, **[[자바 다형성 (Polymorphism)|다형성]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

SOLID 원칙의 정의, 각 원칙의 목적과 이유, 그리고 실무에서 어떻게 적용해야 하는지에 대해 개념적으로 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- SOLID 원칙이 없으면 객체지향 설계의 핵심 가치인 유지보수성, 확장성, 재사용성이 크게 저하됩니다. 단일 책임 원칙 위반으로 인한 거대한 클래스, 개방-폐쇄 원칙 위반으로 인한 기존 코드 수정의 연쇄 반응, 리스코프 치환 원칙 위반으로 인한 상속 계층 구조의 불안정성, 인터페이스 분리 원칙 위반으로 인한 불필요한 의존성, 의존 관계 역전 원칙 위반으로 인한 하위 레벨 변화의 상위 전파 등이 발생하여 코드의 복잡도가 급증하고 변경에 취약한 시스템이 됩니다.

- SOLID 원칙은 객체지향 설계의 품질을 보장하는 핵심 가이드라인으로, 각 원칙이 서로 연관되어 작동하여 유연하고 확장 가능하며 유지보수하기 쉬운 코드를 만들 수 있게 합니다. 단일 책임 원칙으로 변경의 영향 범위를 최소화하고, 개방-폐쇄 원칙으로 확장에 열려있고 변경에 닫혀있는 구조를 만들며, 리스코프 치환 원칙으로 안정적인 상속 관계를 보장하고, 인터페이스 분리 원칙으로 불필요한 결합을 제거하며, 의존 관계 역전 원칙으로 추상화에 의존하는 안정적인 아키텍처를 구축할 수 있습니다.

- 객체지향 설계 원칙에 대한 이해, 각 원칙의 적용 방법과 실무 활용 사례, 원칙 간의 상호 관계, 그리고 원칙 위반 시 발생하는 문제점과 해결 방법을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**SOLID**는 객체 지향 설계의 5가지 핵심 원칙의 첫 글자를 따서 만든 약어입니다.

#### 1. S: 단일 책임 원칙 (Single Responsibility Principle, SRP)

##### 개념 정의

**단일 책임 원칙(SRP)**은 객체는 한 가지 역할만 가져야 하며, 객체가 변경되는 이유는 단 한 가지여야 한다는 원칙입니다.

##### 원칙의 이유

책임이 변경의 축(axis of change)이기 때문에, 한 객체가 많은 책임을 가지면 클래스가 커지고 책임 간의 결합도가 높아져 연쇄적인 변화가 발생합니다. 하나의 책임 변경이 다른 책임에 영향을 주어 예상치 못한 버그가 발생할 수 있습니다.

##### 필요 조건

- 한 클래스는 하나의 책임만 가져야 함
- 클래스가 변경되는 이유는 단 한 가지여야 함
- 각 책임은 독립적으로 변경 가능해야 함

##### 적용 방법

추상화를 통해 객체를 설계하는 과정에서 적절한 한 가지 역할만 갖도록 구성해야 합니다. 책임 분배에는 정답이 없으므로 애플리케이션 설계에 따라 잘 분리해 보는 연습이 중요합니다.

##### 예시

```java
// 나쁜 예: 여러 책임을 가진 클래스
class User {
    private String name;
    private String email;
    
    // 사용자 정보 관리 책임
    public void setName(String name) { this.name = name; }
    public void setEmail(String email) { this.email = email; }
    
    // 데이터베이스 접근 책임 (위반)
    public void saveToDatabase() { /* DB 저장 로직 */ }
    
    // 이메일 전송 책임 (위반)
    public void sendEmail(String message) { /* 이메일 전송 로직 */ }
}

// 좋은 예: 책임 분리
class User {
    private String name;
    private String email;
    
    // 사용자 정보 관리 책임만
    public void setName(String name) { this.name = name; }
    public void setEmail(String email) { this.email = email; }
}

class UserRepository {
    // 데이터베이스 접근 책임
    public void save(User user) { /* DB 저장 로직 */ }
}

class EmailService {
    // 이메일 전송 책임
    public void sendEmail(String to, String message) { /* 이메일 전송 로직 */ }
}
```

##### 장점

- 변경의 영향 범위 최소화: 한 책임 변경이 다른 부분에 영향 없음
- 코드 가독성 향상: 클래스의 역할이 명확함
- 테스트 용이성: 작은 단위로 테스트 가능
- 재사용성 향상: 독립적인 책임은 재사용하기 쉬움

##### 단점

- 과도한 분리는 오히려 복잡도를 증가시킬 수 있음
- 책임 분리 기준이 명확하지 않아 설계자의 판단이 필요함

---

#### 2. O: 개방-폐쇄 원칙 (Open/Closed Principle, OCP)

##### 개념 정의

**개방-폐쇄 원칙(OCP)**은 객체는 확장에는 열려 있고(Open), 변경에는 닫혀 있어야(Closed) 한다는 원칙입니다.

##### 의미

새로운 기능이 추가될 때(확장) 기존 소스 코드의 변경을 초래하지 않아야 합니다. 기존 코드는 수정하지 않고 새로운 기능을 추가할 수 있어야 합니다.

##### 효과

코드 변경 없이 쉽게 확장이 가능해져 유연성, 재사용성, 유지보수성을 얻을 수 있습니다. 기존 코드의 안정성을 유지하면서 새로운 기능을 추가할 수 있습니다.

##### 필요 조건

- 기존 코드는 수정하지 않음
- 새로운 기능은 확장을 통해 추가
- 추상화에 의존해야 함

##### 적용 방법

추상화에 의존함으로써 달성할 수 있습니다. 잘 변하지 않는 **추상화(인터페이스)**에 의존하고, 확장이 필요할 경우 추상화의 구현체만 추가하여 기존 서비스 코드의 수정 없이 확장을 이룹니다.

##### 예시

```java
// 나쁜 예: 새로운 결제 방식 추가 시 기존 코드 수정 필요
class PaymentProcessor {
    public void processPayment(String type, double amount) {
        if (type.equals("credit")) {
            // 신용카드 처리 로직
        } else if (type.equals("paypal")) {
            // 페이팔 처리 로직
        }
        // 새로운 결제 방식 추가 시 여기 수정 필요 (위반)
    }
}

// 좋은 예: 추상화에 의존, 확장에는 열려있고 변경에는 닫혀있음
interface PaymentMethod {
    void pay(double amount);
}

class CreditCardPayment implements PaymentMethod {
    @Override
    public void pay(double amount) {
        // 신용카드 처리 로직
    }
}

class PayPalPayment implements PaymentMethod {
    @Override
    public void pay(double amount) {
        // 페이팔 처리 로직
    }
}

class PaymentProcessor {
    // 기존 코드 수정 없이 새로운 PaymentMethod 구현체만 추가하면 됨
    public void processPayment(PaymentMethod method, double amount) {
        method.pay(amount);
    }
}

// 새로운 결제 방식 추가: 기존 코드 수정 없이 확장만
class BitcoinPayment implements PaymentMethod {
    @Override
    public void pay(double amount) {
        // 비트코인 처리 로직
    }
}
```

##### 장점

- 기존 코드 안정성 유지
- 확장성 향상: 새로운 기능 추가가 용이
- 버그 발생 가능성 감소: 기존 코드 수정 없음
- 유지보수성 향상: 변경 영향 범위 최소화

##### 단점

- 초기 설계 시 추상화 수준 결정이 어려울 수 있음
- 과도한 추상화는 복잡도를 증가시킬 수 있음

---

#### 3. L: 리스코프 치환 원칙 (Liskov Substitution Principle, LSP)

##### 개념 정의

**리스코프 치환 원칙(LSP)**은 서브 타입(하위 클래스)은 자신의 기반 타입(상위 클래스)으로 교체할 수 있어야 한다는 원칙입니다.

##### 의미

하위 클래스는 상위 클래스가 정해둔 **약속(규약)**을 지켜야 합니다. 상위 클래스의 인스턴스를 하위 클래스의 인스턴스로 교체해도 프로그램의 정확성이 깨지지 않아야 합니다.

##### 원칙의 이유

LSP는 규약이 준수된 상속 구조를 보장하여 OCP가 위반되지 않도록 합니다. LSP가 깨지면 기반 타입으로의 대체가 힘들어져 확장의 역할 수행이 불가능해지고 OCP 또한 위배됩니다.

##### 필요 조건

- 하위 클래스는 상위 클래스의 모든 메서드를 구현해야 함
- 하위 클래스는 상위 클래스의 사전 조건을 약화시키지 않아야 함
- 하위 클래스는 상위 클래스의 사후 조건을 강화하지 않아야 함
- 하위 클래스는 상위 클래스가 던지는 예외보다 더 넓은 예외를 던지지 않아야 함

##### 적용 방법

'하위 클래스는 상위 클래스이다' 및 '구현 클래스는 인터페이스이다'의 관계가 잘 지켜져야 합니다. **행위에 대한 서브타이핑(행위적 서브타이핑)**도 고려해야 합니다. 상위 타입으로 치환했을 때 사용자가 기대하는 행위 약속이 깨져서는 안 됩니다.

##### 예시

```java
// 나쁜 예: LSP 위반
class Rectangle {
    protected int width;
    protected int height;
    
    public void setWidth(int width) { this.width = width; }
    public void setHeight(int height) { this.height = height; }
    public int getArea() { return width * height; }
}

class Square extends Rectangle {
    // 정사각형은 너비와 높이가 같아야 함
    @Override
    public void setWidth(int width) {
        this.width = width;
        this.height = width; // LSP 위반: Rectangle의 행위와 다름
    }
    
    @Override
    public void setHeight(int height) {
        this.width = height;
        this.height = height; // LSP 위반
    }
}

// 사용 코드
void resize(Rectangle rect) {
    rect.setWidth(10);
    rect.setHeight(5);
    // Rectangle이면 50이지만, Square면 25가 됨 (예상과 다름)
}

// 좋은 예: LSP 준수
abstract class Shape {
    public abstract int getArea();
}

class Rectangle extends Shape {
    private int width;
    private int height;
    
    public void setWidth(int width) { this.width = width; }
    public void setHeight(int height) { this.height = height; }
    
    @Override
    public int getArea() { return width * height; }
}

class Square extends Shape {
    private int side;
    
    public void setSide(int side) { this.side = side; }
    
    @Override
    public int getArea() { return side * side; }
}
```

##### 장점

- 안정적인 상속 계층 구조
- 다형성의 올바른 활용
- OCP 준수를 위한 기반 제공
- 예측 가능한 동작 보장

##### 단점

- 상속 관계 설계 시 고려사항이 많아짐
- 행위적 서브타이핑을 고려해야 하므로 설계가 복잡해질 수 있음

---

#### 4. I: 인터페이스 분리 원칙 (Interface Segregation Principle, ISP)

##### 개념 정의

**인터페이스 분리 원칙(ISP)**은 인터페이스는 자신의 클라이언트가 사용할 메서드만 가지고 있어야 한다는 원칙입니다. 클라이언트 입장에서 내가 쓰지 않을 메서드에 대해서는 알고 싶지 않다는 원칙입니다.

##### 원칙의 이유

인터페이스가 커지면 이를 구현하는 클라이언트 간의 결합도가 높아져 문제가 발생합니다. 특정 클라이언트를 위한 메서드가 추가될 때 사용하지 않는 다른 클라이언트에게도 구현이 강제되는 연쇄적인 문제가 발생합니다.

##### 필요 조건

- 인터페이스는 클라이언트가 실제로 사용하는 메서드만 포함해야 함
- 불필요한 메서드에 대한 의존성 제거
- 클라이언트별로 필요한 인터페이스 분리

##### 적용 방법

클라이언트가 필요로 하는 함수만 선언하여 인터페이스의 역할에 충실한 기능만 제공해야 합니다. 인터페이스가 '무언가를 가능하게 만들기 위해' 필요한 기능만 제공하도록 분리해야 합니다.

##### 예시

```java
// 나쁜 예: 거대한 인터페이스
interface Worker {
    void work();
    void eat();
    void sleep();
}

class Human implements Worker {
    @Override
    public void work() { /* 일하기 */ }
    @Override
    public void eat() { /* 먹기 */ }
    @Override
    public void sleep() { /* 자기 */ }
}

class Robot implements Worker {
    @Override
    public void work() { /* 일하기 */ }
    @Override
    public void eat() { /* 불필요: 로봇은 먹지 않음 */ }
    @Override
    public void sleep() { /* 불필요: 로봇은 자지 않음 */ }
}

// 좋은 예: 인터페이스 분리
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}

class Human implements Workable, Eatable, Sleepable {
    @Override
    public void work() { /* 일하기 */ }
    @Override
    public void eat() { /* 먹기 */ }
    @Override
    public void sleep() { /* 자기 */ }
}

class Robot implements Workable {
    @Override
    public void work() { /* 일하기 */ }
    // eat(), sleep() 구현 불필요
}
```

##### 장점

- 불필요한 의존성 제거
- 인터페이스 변경의 영향 범위 최소화
- 클라이언트별 독립적인 인터페이스
- 구현 클래스의 불필요한 메서드 구현 방지

##### 단점

- 인터페이스가 너무 많아질 수 있음
- 분리 기준이 명확하지 않을 수 있음

---

#### 5. D: 의존 관계 역전 원칙 (Dependency Inversion Principle, DIP)

##### 개념 정의

**의존 관계 역전 원칙(DIP)**은 구체적인 것이 추상화된 것에 의존해야 한다는 원칙입니다. 자주 변경되는 것에 의존하지 말라는 원칙입니다.

##### 원칙의 이유

**1. 상위 객체의 안정성 보장**
- 상위 객체(애플리케이션의 본질, 정책 결정)가 구체적인 하위 객체에 의존하면, 하위의 변화가 상위 정책에 영향을 줍니다.

**2. 추상화의 재사용성**
- 추상화(자유롭게 재사용하길 원하는 요소)가 구체적인 것에 의존한다면 재사용이 어렵게 됩니다.

**문제점 (기존 의존성)**:
- 상위 수준이 하위 수준에 의존하는 구조에서는 의존성이 타고 내려가 마지막 레이어의 변화가 연쇄적으로 상위까지 타고 올라옵니다.

##### 필요 조건

- 상위 레벨 모듈은 하위 레벨 모듈에 의존하지 않아야 함
- 둘 다 추상화에 의존해야 함
- 추상화는 세부사항에 의존하지 않아야 함
- 세부사항은 추상화에 의존해야 함

##### 적용 방법 (의존성 역전)

**1. 동일 레벨에서는 구체적인 것이 아닌 추상화에 의존**
- 인터페이스나 추상 클래스에 의존하도록 설계

**2. 구체적인 것은 하위 레벨로 내려가 상위 레벨을 의존하도록 만듦**
- 구현체는 추상화를 구현하도록 설계

이러한 의존 관계의 역전은 하위 레벨의 변화가 상위로 전파되는 것을 막고 추상화를 자유롭게 재사용할 수 있게 합니다.

##### 예시

```java
// 나쁜 예: 상위가 하위에 의존 (DIP 위반)
class MySQLDatabase {
    public void save(String data) {
        // MySQL 저장 로직
    }
}

class UserService {
    // 구체적인 MySQLDatabase에 직접 의존 (위반)
    private MySQLDatabase database = new MySQLDatabase();
    
    public void saveUser(String userData) {
        database.save(userData);
        // 다른 데이터베이스로 변경하려면 UserService 수정 필요
    }
}

// 좋은 예: 추상화에 의존 (DIP 준수)
interface Database {
    void save(String data);
}

class MySQLDatabase implements Database {
    @Override
    public void save(String data) {
        // MySQL 저장 로직
    }
}

class PostgreSQLDatabase implements Database {
    @Override
    public void save(String data) {
        // PostgreSQL 저장 로직
    }
}

class UserService {
    // 추상화(Database 인터페이스)에 의존
    private Database database;
    
    // 의존성 주입 (Dependency Injection)
    public UserService(Database database) {
        this.database = database;
    }
    
    public void saveUser(String userData) {
        database.save(userData);
        // Database 구현체 변경 시 UserService 수정 불필요
    }
}
```

##### 장점

- 하위 레벨 변경이 상위 레벨에 영향 없음
- 추상화의 재사용성 향상
- 테스트 용이성: Mock 객체 주입 가능
- 유연한 아키텍처: 구현체 교체가 용이

##### 단점

- 초기 설계 시 추상화 수준 결정이 어려울 수 있음
- 과도한 추상화는 복잡도를 증가시킬 수 있음

---

#### SOLID 원칙 간의 관계

SOLID 원칙들은 서로 연관되어 작동합니다:

- **SRP**는 각 클래스가 단일 책임을 가지도록 하여 **OCP**를 준수하기 쉽게 만듭니다.
- **LSP**는 올바른 상속 관계를 보장하여 **OCP**가 위반되지 않도록 합니다.
- **ISP**는 불필요한 의존성을 제거하여 **DIP**를 준수하기 쉽게 만듭니다.
- **DIP**는 추상화에 의존하도록 하여 **OCP**를 실현할 수 있게 합니다.

---

### **왜 SOLID 원칙을 사용해야 할까?**

#### SOLID 원칙이 없다면?

**1. 단일 책임 원칙 위반 시**
- 거대한 클래스로 인한 높은 결합도
- 한 책임 변경이 다른 책임에 연쇄적 영향
- 테스트와 유지보수의 어려움

**2. 개방-폐쇄 원칙 위반 시**
- 새로운 기능 추가 시 기존 코드 수정 필요
- 버그 발생 가능성 증가
- 확장성 저하

**3. 리스코프 치환 원칙 위반 시**
- 상속 계층 구조의 불안정성
- 다형성 활용 불가능
- 예측 불가능한 동작

**4. 인터페이스 분리 원칙 위반 시**
- 불필요한 메서드 구현 강제
- 클라이언트 간 높은 결합도
- 인터페이스 변경의 광범위한 영향

**5. 의존 관계 역전 원칙 위반 시**
- 하위 레벨 변경이 상위로 전파
- 추상화 재사용 불가능
- 테스트와 확장의 어려움

#### SOLID 원칙의 실무 활용

**프레임워크와 라이브러리**:
- Spring Framework: DIP를 통한 의존성 주입, OCP를 통한 확장성
- Java Collections: ISP를 통한 인터페이스 분리 (List, Set, Map 등)
- Servlet API: OCP를 통한 확장 메커니즘

**디자인 패턴과의 관계**:
- Strategy Pattern: OCP, DIP 준수
- Template Method Pattern: OCP, LSP 준수
- Adapter Pattern: ISP, DIP 준수

---

### **Interview Answer Version (면접 답변식 요약)**

SOLID는 객체지향 설계의 5가지 핵심 원칙으로, 유지보수성과 확장성을 높이기 위한 가이드라인입니다.

**단일 책임 원칙(SRP)**은 한 클래스가 하나의 책임만 가져야 하며, 변경 이유가 단 한 가지여야 한다는 원칙입니다. 이를 통해 변경의 영향 범위를 최소화하고 코드의 가독성과 테스트 용이성을 높입니다.

**개방-폐쇄 원칙(OCP)**은 확장에는 열려있고 변경에는 닫혀있어야 한다는 원칙입니다. 추상화에 의존함으로써 기존 코드 수정 없이 새로운 기능을 추가할 수 있어 유연성과 재사용성을 얻을 수 있습니다.

**리스코프 치환 원칙(LSP)**은 하위 클래스는 상위 클래스로 교체 가능해야 하며, 상위 클래스의 약속을 지켜야 한다는 원칙입니다. 이를 통해 안정적인 상속 계층 구조를 보장하고 OCP가 위반되지 않도록 합니다.

**인터페이스 분리 원칙(ISP)**은 클라이언트가 사용하지 않는 메서드에 의존하지 않아야 한다는 원칙입니다. 인터페이스를 분리하여 불필요한 결합을 제거하고 변경의 영향을 최소화합니다.

**의존 관계 역전 원칙(DIP)**은 구체적인 것이 추상화에 의존해야 하며, 상위 레벨이 하위 레벨에 의존하지 않아야 한다는 원칙입니다. 이를 통해 하위 레벨의 변경이 상위로 전파되는 것을 막고 추상화를 자유롭게 재사용할 수 있습니다.

핵심은 SOLID 원칙들이 서로 연관되어 작동하여 유연하고 확장 가능하며 유지보수하기 쉬운 코드를 만든다는 점입니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### SOLID 원칙 적용 시 주의사항

**1. 과도한 추상화 지양**
- 모든 것을 추상화하면 오히려 복잡도가 증가할 수 있습니다.
- 실제로 확장이 필요한 부분에만 적용하는 것이 중요합니다.

```java
// 나쁜 예: 불필요한 추상화
interface SimpleCalculator {
    int add(int a, int b);
}

class BasicCalculator implements SimpleCalculator {
    @Override
    public int add(int a, int b) { return a + b; }
}

// 좋은 예: 확장이 필요할 때만 추상화
class Calculator {
    public int add(int a, int b) { return a + b; }
}
```

**2. 책임 분리 기준 명확화**
- 책임 분리에는 정답이 없으므로 애플리케이션의 도메인과 요구사항을 고려해야 합니다.
- 너무 작게 나누면 오히려 복잡도가 증가할 수 있습니다.

**3. 원칙 간의 균형**
- 모든 원칙을 완벽하게 지키려다 보면 오히려 과도한 설계가 될 수 있습니다.
- 실용적인 판단이 필요합니다.

#### 실무 활용 예시

**1. Spring Framework에서의 DIP 활용**

```java
// 인터페이스 정의 (추상화)
public interface UserRepository {
    User findById(Long id);
    void save(User user);
}

// 구현체 (구체적인 것)
@Repository
public class JpaUserRepository implements UserRepository {
    @Override
    public User findById(Long id) {
        // JPA 구현
    }
    
    @Override
    public void save(User user) {
        // JPA 구현
    }
}

// 서비스 (상위 레벨, 추상화에 의존)
@Service
public class UserService {
    private final UserRepository userRepository; // DIP 준수
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository; // 의존성 주입
    }
    
    public User getUser(Long id) {
        return userRepository.findById(id);
    }
}
```

**2. 전략 패턴에서의 OCP 활용**

```java
// 추상화
interface DiscountStrategy {
    double calculateDiscount(double price);
}

// 다양한 할인 전략 (확장)
class RegularDiscount implements DiscountStrategy {
    @Override
    public double calculateDiscount(double price) {
        return price * 0.1; // 10% 할인
    }
}

class VIPDiscount implements DiscountStrategy {
    @Override
    public double calculateDiscount(double price) {
        return price * 0.2; // 20% 할인
    }
}

// 기존 코드 수정 없이 새로운 전략 추가 가능 (OCP)
class PremiumDiscount implements DiscountStrategy {
    @Override
    public double calculateDiscount(double price) {
        return price * 0.3; // 30% 할인
    }
}

// 사용하는 클래스 (변경에 닫혀있음)
class PriceCalculator {
    public double calculatePrice(double price, DiscountStrategy strategy) {
        return price - strategy.calculateDiscount(price);
    }
}
```

**3. ISP를 통한 인터페이스 분리**

```java
// 분리된 인터페이스
interface Readable {
    String read();
}

interface Writable {
    void write(String data);
}

interface Deletable {
    void delete();
}

// 클라이언트별 필요한 인터페이스만 구현
class ReadOnlyFile implements Readable {
    @Override
    public String read() {
        // 읽기 로직
        return "data";
    }
}

class ReadWriteFile implements Readable, Writable {
    @Override
    public String read() {
        // 읽기 로직
        return "data";
    }
    
    @Override
    public void write(String data) {
        // 쓰기 로직
    }
}
```

**4. 템플릿 메서드 패턴에서의 LSP 활용**

```java
// 상위 클래스 (템플릿 메서드)
abstract class DataProcessor {
    // 템플릿 메서드
    public final void process() {
        loadData();
        transformData();
        saveData();
    }
    
    protected abstract void loadData();
    protected abstract void transformData();
    protected abstract void saveData();
}

// 하위 클래스 (LSP 준수: 상위 클래스의 약속을 지킴)
class CSVProcessor extends DataProcessor {
    @Override
    protected void loadData() {
        // CSV 로드
    }
    
    @Override
    protected void transformData() {
        // CSV 변환
    }
    
    @Override
    protected void saveData() {
        // CSV 저장
    }
}

// 다른 하위 클래스도 동일한 약속 준수
class JSONProcessor extends DataProcessor {
    @Override
    protected void loadData() {
        // JSON 로드
    }
    
    @Override
    protected void transformData() {
        // JSON 변환
    }
    
    @Override
    protected void saveData() {
        // JSON 저장
    }
}
```

---

### 예상 꼬리질문정리

1. **SOLID 원칙 중 가장 중요하다고 생각하는 원칙은 무엇인가요?**
   - 모든 원칙이 중요하지만, 특히 **OCP**와 **DIP**가 확장성과 유연성을 결정하는 핵심 원칙입니다. OCP는 확장에 열려있고 변경에 닫혀있는 구조를 만들고, DIP는 추상화에 의존하는 안정적인 아키텍처를 구축합니다. 하지만 SRP가 기반이 되지 않으면 다른 원칙들도 제대로 적용하기 어렵습니다.

2. **SRP와 ISP의 차이는 무엇인가요?**
   - **SRP**는 클래스가 하나의 책임만 가져야 한다는 원칙으로, 클래스 레벨에서의 책임 분리를 다룹니다.
   - **ISP**는 인터페이스가 클라이언트가 사용하는 메서드만 가져야 한다는 원칙으로, 인터페이스 레벨에서의 메서드 분리를 다룹니다.
   - SRP는 "클래스는 하나의 이유로만 변경되어야 한다"는 것이고, ISP는 "클라이언트는 사용하지 않는 메서드에 의존하지 않아야 한다"는 것입니다.

3. **OCP를 위반하는 대표적인 사례는 무엇인가요?**
   - if-else나 switch-case 문을 사용하여 타입에 따라 다른 동작을 하는 경우가 대표적입니다. 새로운 타입이 추가될 때마다 기존 코드를 수정해야 하므로 OCP를 위반합니다. 이를 해결하기 위해 추상화(인터페이스나 추상 클래스)를 사용하고 다형성을 활용해야 합니다.

4. **LSP를 위반하는 대표적인 사례는 무엇인가요?**
   - 정사각형-직사각형 문제가 대표적입니다. 수학적으로 정사각형은 직사각형이지만, 프로그래밍에서 Square가 Rectangle을 상속받으면 setWidth()와 setHeight()의 행위가 예상과 다르게 동작하여 LSP를 위반합니다. 또한 하위 클래스에서 상위 클래스의 메서드를 더 제한적으로 만들거나, 예외를 더 넓게 던지는 경우도 LSP 위반입니다.

5. **DIP와 의존성 주입(Dependency Injection)의 관계는?**
   - **DIP**는 설계 원칙으로, "추상화에 의존해야 한다"는 가이드라인입니다.
   - **의존성 주입(DI)**은 DIP를 실현하는 구체적인 기법입니다. 생성자 주입, 세터 주입, 필드 주입 등의 방법으로 의존성을 외부에서 주입하여 구체적인 것에 의존하지 않도록 만듭니다.
   - Spring Framework의 @Autowired, @Inject 등이 의존성 주입을 구현한 것입니다.

6. **SOLID 원칙을 모두 지키면 항상 좋은 코드인가요?**
   - SOLID 원칙은 가이드라인이지 절대적인 규칙은 아닙니다. 과도한 추상화나 분리는 오히려 복잡도를 증가시킬 수 있습니다. 실제로 확장이 필요하거나 변경이 예상되는 부분에만 적용하는 실용적인 접근이 중요합니다. 작은 프로젝트에서는 모든 원칙을 엄격하게 적용할 필요가 없을 수 있지만, 대규모 프로젝트나 프레임워크 개발에서는 SOLID 원칙이 매우 중요합니다.

7. **SOLID 원칙과 디자인 패턴의 관계는?**
   - 많은 디자인 패턴이 SOLID 원칙을 준수하기 위해 만들어졌습니다.
   - **Strategy Pattern**: OCP, DIP 준수
   - **Template Method Pattern**: OCP, LSP 준수
   - **Adapter Pattern**: ISP, DIP 준수
   - **Factory Pattern**: DIP 준수
   - 디자인 패턴을 이해하면 SOLID 원칙을 더 잘 적용할 수 있고, SOLID 원칙을 이해하면 디자인 패턴의 목적을 더 잘 이해할 수 있습니다.

8. **실무에서 SOLID 원칙을 적용할 때 가장 어려운 점은?**
   - 책임 분리의 기준을 정하는 것이 가장 어렵습니다. 어디까지가 하나의 책임인지, 언제 추상화를 해야 하는지 판단하기 어렵습니다. 또한 팀 내에서 일관된 설계 철학을 유지하는 것도 중요합니다. 실무에서는 점진적으로 리팩토링하면서 SOLID 원칙을 적용하는 것이 현실적입니다.

---

## 관련 노트

- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - SOLID 원칙이 적용되는 객체지향의 핵심 개념
- **[[상속과 컴포지션 (Inheritance vs Composition)|상속]]** - LSP와 관련된 상속 관계 이해
- **[[자바 다형성 (Polymorphism)|다형성]]** - OCP와 DIP를 실현하는 다형성 개념

---

**태그:** #java #solid #객체지향 #설계원칙 #면접 #개념정리 #SRP #OCP #LSP #ISP #DIP

