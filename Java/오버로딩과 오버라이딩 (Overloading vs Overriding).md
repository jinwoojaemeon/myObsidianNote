				# 오버로딩과 오버라이딩 (Overloading vs Overriding)

#java #오버로딩 #오버라이딩 #다형성 #면접 #개념정리

**관련 개념:** [[자바 다형성 (Polymorphism)|다형성]] [[상속과 컴포지션 (Inheritance vs Composition)|상속]] [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]

> [!note] 이어서 읽기
> 오버로딩과 오버라이딩을 이해하려면 **[[자바 다형성 (Polymorphism)|다형성]]**과 **[[상속과 컴포지션 (Inheritance vs Composition)|상속]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

오버로딩과 오버라이딩의 정의, 차이점, 그리고 왜 사용해야 하는지에 대해 개념적으로 이해한다.

---
### **Core Concept (핵심 개념 정리)**

#### 1. 오버로딩 (Overloading)

##### 개념 정의

**오버로딩(Overloading)** 은 한 클래스 내에 이미 사용하려는 이름과 같은 이름을 가진 메서드가 있더라도 매개변수의 개수 또는 타입이 다르면, 같은 이름을 사용해서 메서드를 정의할 수 있는 기술입니다.

##### 동작 방식

1. **컴파일 타임에 결정**: 오버로딩된 메서드 중 어떤 메서드를 호출할지는 컴파일 타임에 결정됩니다.
2. **메서드 시그니처 기반**: 메서드 이름과 매개변수의 타입, 개수, 순서를 조합한 메서드 시그니처를 기준으로 구분됩니다.
3. **반환 타입은 무관**: 반환 타입만 다른 경우는 오버로딩이 불가능합니다.

##### 필요 조건

- 같은 클래스 내에 있어야 함
- 메서드 이름이 동일해야 함
- 매개변수의 개수, 타입, 또는 순서가 달라야 함
- 반환 타입은 오버로딩 조건에 포함되지 않음

##### 예시

```java
public class Calculator {
    // 매개변수 개수가 다른 경우
    public int add(int a, int b) {
        return a + b;
    }
    
    public int add(int a, int b, int c) {
        return a + b + c;
    }
    
    // 매개변수 타입이 다른 경우
    public double add(double a, double b) {
        return a + b;
    }
    
    // 매개변수 순서가 다른 경우
    public void print(String name, int age) {
        System.out.println(name + ", " + age);
    }
    
    public void print(int age, String name) {
        System.out.println(age + ", " + name);
    }
}
```

##### 장점

- 코드의 직관성 향상: 동일한 기능을 하나의 이름으로 통일
- 개발 편의성: 메서드 이름만 기억하면 됨
- API 사용성 향상: 사용자가 쉽게 이해하고 사용 가능

##### 단점

- 과도한 사용 시 혼란 가능: 너무 많은 오버로딩은 어떤 메서드가 호출되는지 파악하기 어려울 수 있음
- 컴파일 타임에만 결정: 런타임에 동적으로 선택되지 않음

---

#### 2. 오버라이딩 (Overriding)

##### 개념 정의

**오버라이딩(Overriding)** 은 부모 클래스로부터 상속받은 메서드를 자식 클래스에서 재정의하는 것입니다. 상속받은 메서드를 그대로 사용할 수도 있지만, 자식 클래스에서 상황에 맞게 변경해야 하는 경우 오버라이딩할 필요가 생깁니다.

##### 동작 방식

1. **런타임에 결정**: 오버라이딩된 메서드 중 어떤 메서드를 호출할지는 런타임에 객체의 실제 타입에 따라 결정됩니다 (동적 바인딩).
2. **메서드 시그니처 일치**: 부모 클래스의 메서드와 완전히 동일한 시그니처를 가져야 합니다.

##### 필요 조건

- 상속 관계가 있어야 함
- 메서드 이름, 매개변수 타입, 개수, 순서가 모두 동일해야 함
- 반환 타입이 동일하거나 공변 반환 타입이어야 함(Java 5 이후)
- 접근 제어자는 부모보다 넓거나 같아야 함
- 예외는 부모보다 같거나 더 구체적이어야 함
- `static`, `final`, `private` 메서드는 오버라이딩 불가

##### 예시

```java
// 부모 클래스
class Animal {
    public void makeSound() {
        System.out.println("동물이 소리를 냅니다.");
    }
    
    public void move() {
        System.out.println("동물이 움직입니다.");
    }
    
    // 공변 반환 타입 예시
    public Animal getInstance() {
        return new Animal();
    }
}

// 자식 클래스
class Dog extends Animal {
    // 오버라이딩: 부모의 makeSound()를 재정의 (void 반환 타입)
    @Override
    public void makeSound() {
        System.out.println("멍멍!");
    }
    
    // move()는 오버라이딩하지 않음 - 부모의 메서드 사용
    
    // 공변 반환 타입: 부모의 반환 타입(Animal)의 서브타입(Dog) 반환
    @Override
    public Dog getInstance() {  // Animal 대신 Dog 반환 가능 (Java 5+)
        return new Dog();
    }
}

// 사용 예시
Animal animal = new Dog();
animal.makeSound(); // "멍멍!" 출력 (런타임에 Dog의 메서드 호출)

// 공변 반환 타입 사용 예시
Dog dog = new Dog();
Dog myDog = dog.getInstance();  // ✅ 캐스팅 없이 Dog 타입으로 받을 수 있음

Animal animal2 = new Dog();
Animal myAnimal = animal2.getInstance();  // Animal 타입으로도 받을 수 있음
```

##### 장점

- 다형성 실현: 동일한 인터페이스로 다양한 구현 제공
- 코드 재사용성: 부모 클래스의 구조를 유지하면서 특정 부분만 수정
- 확장성: 새로운 자식 클래스를 추가해도 기존 코드 수정 불필요
- 프레임워크 확장: 프레임워크의 메서드를 재정의하여 커스터마이징 가능

##### 단점

- 과도한 오버라이딩은 상속 계층 구조를 복잡하게 만들 수 있음
- 부모 클래스의 변경이 자식 클래스에 영향을 줄 수 있음

---

#### 3. 오버로딩 vs 오버라이딩 비교

| 구분 | 오버로딩 (Overloading) | 오버라이딩 (Overriding) |
| --- | --- | --- |
| **정의** | 같은 클래스 내에서 메서드 이름은 같고 매개변수가 다른 메서드를 정의 | 부모 클래스의 메서드를 자식 클래스에서 재정의 |
| **관계** | 같은 클래스 내 | 상속 관계 (부모-자식) |
| **메서드 시그니처** | 매개변수가 달라야 함 | 메서드 시그니처가 동일해야 함 |
| **반환 타입** | 달라도 됨 | 동일하거나 공변 반환 타입 |
| **접근 제어자** | 달라도 됨 | 부모보다 넓거나 같아야 함 |
| **바인딩 시점** | 컴파일 타임 (정적 바인딩) | 런타임 (동적 바인딩) |
| **다형성** | 컴파일 타임 다형성 | 런타임 다형성 |
| **`@Override` 어노테이션** | 사용 불가 | 사용 권장 (선택적) |

---

### **왜 오버로딩과 오버라이딩을 사용해야 할까?**

#### 오버로딩을 사용하는 이유

가장 대표적인 오버로딩 예시로는 `System.out.print()` 함수가 있습니다. 만약 오버로딩이 없었다면 개발자들은 `System.out.print()`를 사용하려면 `System.out.printInt()`, `System.out.printChar()`, `System.out.printString()`, `System.out.printObject()` 같은 각각의 상황에 맞는 print 관련 함수를 찾아서 써야 했을 것입니다.

오버로딩은 이를 방지하여, 매개변수의 개수, 타입을 다르게 하지만 동일한 이름의 `System.out.print()`를 사용할 수 있게 해주는 역할을 하여 코드 직관성 및 편의성을 증가시켜줍니다.

**실제 Java 예시:**

```java
// System.out.print()의 오버로딩
System.out.print(10);           // int
System.out.print('A');          // char
System.out.print("Hello");      // String
System.out.print(3.14);         // double
System.out.print(true);         // boolean
System.out.print(new Object()); // Object
```

모두 같은 `print` 메서드 이름을 사용하지만, 매개변수 타입에 따라 적절한 메서드가 자동으로 선택됩니다.

#### 오버라이딩을 사용하는 이유

만약 오버라이딩이 없는 상황에서 부모 클래스에서 상속받은 메서드를 자식 클래스에서 추가한 필드 및 메서드에 맞게 오버로딩으로 해결하려고 한다면 어떻게 될까요?

오버로딩은 매개변수가 달라야 하므로, 부모와 **동일한 시그니처**로 메서드를 재정의할 수 없습니다.

**시나리오 1: 동일한 시그니처로 재정의하려는 경우**
```java
// 부모 클래스
class Parent {
    public void print() {
        System.out.println("Parent");
    }
}

// 자식 클래스 (오버라이딩이 없다면?)
class Child extends Parent {
    // ❌ 동일한 시그니처로 재정의 불가능
    // public void print() { ... }  // 오버라이딩이 없으면 불가능
    
    // ✅ 오버로딩은 가능하지만, 시그니처가 달라야 함
    public void print(String message) {  // 매개변수가 추가됨 - 다른 메서드
        System.out.println("Child: " + message);
    }
}

// 사용 시 문제 발생
Child child = new Child();
child.print();  // 부모의 print() 호출 (자식의 동작으로 변경 불가능)
child.print("hello");  // 자식의 print(String) 호출 (다른 메서드)
```

**시나리오 2: 완전히 새로운 메서드 이름 사용**
```java
// 부모 클래스
class Parent {
    public void print() {
        System.out.println("Parent");
    }
}

// 자식 클래스
class Child extends Parent {
    // ❌ print()를 재정의할 수 없으므로
    // ✅ 다른 이름의 메서드를 만들어야 함
    public void print2() {  // 또는 printChild(), customPrint() 등
        System.out.println("Child");
    }
}

// 사용 시 문제 발생
Parent parent = new Child();
parent.print();  // 부모의 print()만 호출 가능
// parent.print2();  // ❌ Parent 타입에는 print2()가 없음
```

오버로딩은 상속 관계에서도 가능하지만, **동일한 시그니처로 재정의**할 수 없습니다. 매개변수를 다르게 하면 다른 메서드가 되어 호출 방식이 달라지고, 완전히 새로운 메서드 이름을 사용하면 부모 타입으로는 접근할 수 없어 다형성을 활용할 수 없습니다.

**가장 큰 문제는 이러한 방식으로는 프레임워크나 라이브러리 구조가 성립할 수 없다는 점입니다.**

##### 프레임워크에서의 오버라이딩 활용

**1. Servlet 예시**

```java
// Servlet 프레임워크의 요구사항
public class MyServlet extends HttpServlet {
    // 프레임워크가 요구하는 메서드를 오버라이딩
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
        // 사용자 정의 로직
    }
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) {
        // 사용자 정의 로직
    }
}
```

Servlet에서는 사용자가 `doGet()`이나 `doPost()`를 "재정의"하도록 요구합니다. 하지만 오버라이딩이 존재하지 않는다면, 프레임워크는 이 메서드를 정확히 어떤 이름으로 구현해야 하는지 개발자에게 강제할 방법이 없습니다. 결국 개발자가 임의의 새 메서드를 만들게 되고, 프레임워크가 해당 메서드를 호출한다는 보장도 할 수 없게 됩니다.

**2. Spring Boot 예시**

```java
// Spring Boot의 ApplicationRunner 인터페이스
public interface ApplicationRunner {
    void run(ApplicationArguments args) throws Exception;
}

// 구현 클래스 (Bean으로 등록)
@Component
public class MyApplicationRunner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        // 애플리케이션 시작 시 실행할 로직
    }
}
```

**동작 원리**:
1. `@Component`로 Bean 등록: Spring이 `MyApplicationRunner`를 Bean으로 관리
2. 인터페이스 구현: `ApplicationRunner` 인터페이스의 `run()` 메서드를 **오버라이딩**하여 구현
3. Spring Boot의 자동 호출: 애플리케이션 시작 시 Spring Boot가 `ApplicationRunner` 타입의 모든 Bean을 찾아 `run()` 메서드를 자동 호출

**오버라이딩이 없다면?**
- 인터페이스의 메서드를 구현할 수 없음 → `implements ApplicationRunner` 불가능
- Bean으로 등록되어도 Spring Boot가 호출할 메서드를 찾을 수 없음
- 다른 이름의 메서드(`run2()`, `start()` 등)를 만들어도 Spring Boot는 이를 호출하지 않음

Spring Boot 또한 마찬가지로, 특정 메서드가 재정의된다는 전제하에 동작하는 구조가 많은데, 오버라이딩이 없다면 이러한 계약 자체가 성립하지 않습니다.  

**3. 객체지향 설계 원칙과의 관계**

오버라이딩이 없다면:
- **다형성 불가능**: 동일한 타입으로 다양한 객체를 다룰 수 없음
- **인터페이스 구현 불가능**: 인터페이스의 메서드를 구현할 수 없음
- **프레임워크 확장성 부재**: 프레임워크의 확장 메커니즘이 작동하지 않음
- **API 일관성 파괴**: 부모-자식 관계의 메서드 일관성이 사라짐
- **서브타이핑 불가능**: 자식 클래스가 부모의 동작을 "대체"할 수 없음
- **재사용성 저하**: 상속을 통한 코드 재사용이 사실상 불가능

결국 오버라이딩이 없다면, 객체지향의 핵심인 다형성, 인터페이스 구현, 프레임워크 확장성, API 일관성이 모두 무너지고, 오버로딩만으로는 자식 클래스가 부모의 동작을 "대체"할 수 없기 때문에 서브타이핑과 재사용성이 사실상 불가능해집니다.

---

### **Interview Answer Version (면접 답변식 요약)**

오버로딩과 오버라이딩은 모두 메서드 이름이 같다는 공통점이 있지만, 목적과 동작 방식에서 차이가 있습니다.

**오버로딩**은 같은 클래스 내에서 매개변수의 개수나 타입이 다른 메서드를 같은 이름으로 정의하는 것입니다. 컴파일 타임에 어떤 메서드를 호출할지 결정되며, 대표적인 예로 `System.out.print()`가 있습니다. 오버로딩이 없다면 타입별로 `printInt()`, `printString()`처럼 다른 이름을 사용해야 해서 코드의 직관성이 떨어집니다.

**오버라이딩**은 부모 클래스의 메서드를 자식 클래스에서 재정의하는 것입니다. 런타임에 객체의 실제 타입에 따라 호출될 메서드가 결정되며, 이를 통해 다형성을 실현합니다. 오버라이딩이 없다면 Servlet의 `doGet()`, Spring의 `ApplicationRunner`처럼 프레임워크가 요구하는 메서드를 재정의할 수 없어 프레임워크 구조 자체가 성립하지 않습니다.

핵심은 오버로딩은 코드의 편의성을, 오버라이딩은 객체지향의 다형성과 확장성을 제공한다는 점입니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 오버로딩 사용 시 주의사항

1. **과도한 오버로딩 지양**: 너무 많은 오버로딩은 어떤 메서드가 호출되는지 파악하기 어려울 수 있습니다.

```java
// 나쁜 예: 과도한 오버로딩
public void process(int a) { }
public void process(int a, int b) { }
public void process(int a, int b, int c) { }
public void process(int a, int b, int c, int d) { }
// ... 계속 증가

// 좋은 예: 가변 인자 활용
public void process(int... numbers) {
    // 가변 인자로 처리
}
```

2. **모호한 오버로딩 피하기**: 자동 타입 변환으로 인해 어떤 메서드가 호출될지 모호한 경우를 피해야 합니다.

```java
// 모호한 오버로딩 예시
public void method(int a, double b) { }
public void method(double a, int b) { }

// 호출 시 컴파일 에러 발생
method(10, 10); // 어떤 메서드를 호출할지 모호함
```

3. **반환 타입만 다른 경우는 불가능**: 반환 타입만 다르게 해서 오버로딩할 수 없습니다.

```java
// 컴파일 에러
public int calculate() { return 0; }
public double calculate() { return 0.0; } // 불가능!
```

#### 오버라이딩 사용 시 주의사항

1. **`@Override` 어노테이션 사용**: 실수로 메서드 시그니처를 잘못 작성하는 것을 방지합니다.

```java
class Parent {
    public void method() { }
}

class Child extends Parent {
    @Override
    public void method() { } // 올바른 오버라이딩
    
    // @Override
    // public void method(int a) { } // 컴파일 에러: 부모에 없는 메서드
}
```

2. **접근 제어자 제약 준수**: 자식 클래스의 접근 제어자는 부모보다 넓거나 같아야 합니다.

```java
class Parent {
    protected void method() { }
}

class Child extends Parent {
    // public void method() { } // 가능: 더 넓은 접근 제어자
    // private void method() { } // 불가능: 더 좁은 접근 제어자
}
```

3. **예외 처리 규칙**: 자식 클래스는 부모보다 더 넓은 범위의 예외를 던질 수 없습니다.

```java
class Parent {
    public void method() throws IOException { }
}

class Child extends Parent {
    // public void method() throws Exception { } // 불가능: 더 넓은 예외
    public void method() throws FileNotFoundException { } // 가능: 더 구체적인 예외
}
```

4. **`static`, `final`, `private` 메서드는 오버라이딩 불가**: 이들은 각각의 이유로 오버라이딩할 수 없습니다.

```java
class Parent {
    public static void staticMethod() { }
    public final void finalMethod() { }
    private void privateMethod() { }
}

class Child extends Parent {
    // 오버라이딩이 아닌 새로운 메서드 정의
    public static void staticMethod() { } // 숨김(Hiding)
    // public void finalMethod() { } // 컴파일 에러
    // public void privateMethod() { } // 컴파일 에러
}
```

#### 실무 활용 예시

**1. 빌더 패턴에서의 오버로딩**

```java
public class User {
    private String name;
    private int age;
    private String email;
    
    public User(String name) {
        this.name = name;
    }
    
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public User(String name, int age, String email) {
        this.name = name;
        this.age = age;
        this.email = email;
    }
}
```

**2. 프레임워크 확장에서의 오버라이딩**

```java
// Spring의 @PostConstruct 활용
@Component
public class MyService {
    @PostConstruct
    public void init() {
        // 초기화 로직
    }
}

// JPA Entity의 equals/hashCode 오버라이딩
@Entity
public class User {
    @Override
    public boolean equals(Object o) {
        // 커스텀 equals 로직
    }
    
    @Override
    public int hashCode() {
        // 커스텀 hashCode 로직
    }
}
```

**3. 템플릿 메서드 패턴에서의 오버라이딩**

```java
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

class CSVProcessor extends DataProcessor {
    @Override
    protected void loadData() {
        // CSV 로드 로직
    }
    
    @Override
    protected void transformData() {
        // CSV 변환 로직
    }
    
    @Override
    protected void saveData() {
        // CSV 저장 로직
    }
}
```

---

### 예상 꼬리질문정리

1. **오버로딩과 오버라이딩의 바인딩 시점 차이를 설명해주세요.**
   - 오버로딩은 컴파일 타임에 정적 바인딩으로 결정되고, 오버라이딩은 런타임에 동적 바인딩으로 결정됩니다. 이는 오버로딩은 메서드 시그니처를 기준으로 컴파일러가 결정하지만, 오버라이딩은 실제 객체의 타입을 기준으로 JVM이 런타임에 결정하기 때문입니다.

2. **오버로딩에서 반환 타입만 다른 경우 왜 불가능한가요?**
   - 메서드 호출 시 반환 타입만으로는 어떤 메서드를 호출할지 결정할 수 없기 때문입니다. 예를 들어 `int result = calculate()`와 같이 호출할 때, 반환 타입만 다르면 컴파일러가 어떤 메서드를 호출해야 할지 판단할 수 없습니다.

3. **오버라이딩과 메서드 숨김(Method Hiding)의 차이는 무엇인가요?**
   - 오버라이딩은 인스턴스 메서드에 대해 동적 바인딩이 일어나지만, 메서드 숨김은 `static` 메서드에 대해 발생하며 정적 바인딩이 일어납니다. `static` 메서드는 클래스에 속하므로 참조 변수의 타입에 따라 호출되는 메서드가 결정됩니다.

4. **공변 반환 타입(Covariant Return Type)이란 무엇인가요?**
   - 자식 클래스에서 오버라이딩할 때 부모 클래스의 반환 타입의 서브타입을 반환할 수 있는 기능입니다. Java 5부터 지원되며, 예를 들어 부모가 `Object`를 반환하면 자식은 `String`을 반환할 수 있습니다.

5. **오버라이딩에서 접근 제어자를 더 좁게 할 수 없는 이유는 무엇인가요?**
   - 리스코프 치환 원칙(Liskov Substitution Principle) 때문입니다. 자식 클래스는 부모 클래스로 대체 가능해야 하는데, 접근 제어자를 더 좁게 하면 부모 클래스를 사용하는 코드에서 자식 클래스를 사용할 수 없게 됩니다.

6. **오버로딩에서 자동 타입 변환으로 인한 모호성 문제는 어떻게 발생하나요?**
   - 자동 타입 변환(autoboxing, widening)이 가능한 경우, 컴파일러가 어떤 메서드를 호출할지 결정할 수 없어 컴파일 에러가 발생합니다. 예를 들어 `method(int, double)`과 `method(double, int)`가 있을 때 `method(10, 10)`을 호출하면 두 메서드 모두 호출 가능하여 모호성이 발생합니다.

7. **오버라이딩에서 `super` 키워드의 역할은 무엇인가요?**
   - `super` 키워드는 자식 클래스에서 부모 클래스의 메서드나 생성자를 명시적으로 호출할 때 사용합니다. 오버라이딩된 메서드 내에서 `super.methodName()`을 호출하면 부모 클래스의 원본 메서드를 실행할 수 있습니다. 이를 통해 부모의 기능을 확장하면서도 기존 동작을 유지할 수 있습니다.

8. **오버로딩과 오버라이딩이 동시에 발생하는 경우는 언제인가요?**
   - 상속 관계에서 부모 클래스의 메서드를 오버라이딩하면서, 동시에 같은 클래스 내에서 매개변수가 다른 오버로딩된 메서드를 추가할 수 있습니다. 예를 들어 부모의 `method(int)`를 오버라이딩하고, 자식에서 `method(String)`을 추가하면 오버라이딩과 오버로딩이 동시에 존재합니다.

9. **제네릭 메서드에서 오버로딩이 제한되는 이유는 무엇인가요?**
   - 제네릭 타입 소거(Type Erasure)로 인해 런타임에는 제네릭 타입 정보가 사라지기 때문입니다. 예를 들어 `method(List<String>)`과 `method(List<Integer>)`는 컴파일 타임에는 다른 메서드이지만, 런타임에는 둘 다 `method(List)`가 되어 오버로딩이 불가능합니다.

10. **오버라이딩에서 `synchronized` 키워드는 어떻게 동작하나요?**
    - `synchronized` 키워드는 메서드 시그니처의 일부가 아니므로 오버라이딩 조건에 포함되지 않습니다. 부모 메서드가 `synchronized`여도 자식에서 `synchronized`를 제거하거나 추가할 수 있습니다. 하지만 동기화 정책을 변경하는 것은 위험할 수 있으므로 주의가 필요합니다.

11. **오버로딩에서 가변 인자(varargs)와의 우선순위는 어떻게 되나요?**
    - 가변 인자 메서드는 다른 오버로딩된 메서드보다 우선순위가 낮습니다. 정확히 일치하는 메서드가 있으면 그것이 선택되고, 없을 때만 가변 인자 메서드가 선택됩니다. 예를 들어 `method(int)`와 `method(int...)`가 있을 때 `method(10)`을 호출하면 `method(int)`가 선택됩니다.

12. **오버라이딩에서 브릿지 메서드(Bridge Method)란 무엇인가요?**
    - 제네릭 타입의 공변 반환 타입이나 제네릭 메서드 오버라이딩 시, 컴파일러가 자동으로 생성하는 메서드입니다. 타입 소거로 인해 발생하는 타입 불일치를 해결하기 위해 생성되며, 실제 구현 메서드를 호출하는 역할을 합니다. 개발자가 직접 작성하지 않으며 컴파일러가 자동 생성합니다.

13. **오버로딩에서 `null` 값 처리 시 어떤 문제가 발생할 수 있나요?**
    - `null`은 여러 타입으로 해석될 수 있어 모호성이 발생할 수 있습니다. 예를 들어 `method(String)`과 `method(Object)`가 있을 때 `method(null)`을 호출하면 `String`이 더 구체적인 타입이므로 `method(String)`이 선택됩니다. 하지만 `method(Integer)`와 `method(String)`이 있을 때는 모호성으로 인해 컴파일 에러가 발생합니다.

14. **오버라이딩에서 생성자와의 관계는 어떻게 되나요?**
    - 생성자는 오버라이딩할 수 없습니다. 생성자는 클래스 이름과 동일해야 하므로 다른 이름을 가질 수 없고, 상속 관계에서도 부모의 생성자를 "재정의"하는 것이 아니라 `super()`를 통해 호출만 할 수 있습니다. 생성자 오버로딩은 가능하지만 오버라이딩은 불가능합니다.

15. **오버로딩에서 박싱(Boxing)과 언박싱(Unboxing)의 우선순위는 어떻게 되나요?**
    - 정확히 일치하는 타입이 가장 우선순위가 높고, 그 다음 자동 타입 변환(widening), 박싱/언박싱 순입니다. 예를 들어 `method(int)`와 `method(Integer)`가 있을 때 `method(10)`을 호출하면 `method(int)`가 선택되고, `method(new Integer(10))`을 호출하면 `method(Integer)`가 선택됩니다.

