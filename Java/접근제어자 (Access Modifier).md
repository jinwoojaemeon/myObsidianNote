# 접근제어자 (Access Modifier)

#java #접근제어자 #access-modifier #캡슐화 #면접 #개념정리

**관련 개념:** [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[캡슐화]] [[상속과 컴포지션 (Inheritance vs Composition)|상속]]

> [!note] 이어서 읽기
> 접근제어자를 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**과 **[[캡슐화]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

Java의 접근제어자가 무엇인지, 각 접근제어자의 접근 범위와 사용 시나리오, 그리고 캡슐화와의 관계를 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 접근제어자가 없으면 클래스의 모든 멤버가 외부에서 자유롭게 접근 가능하여, 데이터 무결성이 훼손되고 내부 구현이 노출됩니다. 클라이언트 코드가 내부 구현에 의존하게 되어 구현 변경 시 연쇄적인 수정이 필요하며, 잘못된 사용으로 인한 버그가 발생할 수 있습니다.

- 접근제어자는 캡슐화를 실현하여 클래스의 내부 구현을 숨기고 외부 인터페이스만 노출합니다. 이를 통해 데이터 무결성을 보장하고, 내부 구현 변경이 외부 코드에 영향을 주지 않도록 하며, 잘못된 사용을 방지합니다. 또한 객체지향의 정보 은닉 원칙을 지켜 유지보수성과 확장성을 높입니다.

- 접근제어자의 종류와 접근 범위, 각 접근제어자의 사용 시나리오, 그리고 실무에서의 적절한 접근제어자 선택 능력을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**접근제어자(Access Modifier)**는 클래스, 메서드, 변수 등의 접근 범위를 제한하는 키워드입니다. Java에서는 `public`, `protected`, `default`(package-private), `private` 4가지 접근제어자를 제공합니다.

**접근 범위 비교**:

| 접근제어자 | 같은 클래스 | 같은 패키지 | 다른 패키지 상속 | 다른 패키지 |
|------------|-------------|-------------|------------------|-------------|
| **public** | ✅ | ✅ | ✅ | ✅ |
| **protected** | ✅ | ✅ | ✅ | ❌ |
| **default** | ✅ | ✅ | ❌ | ❌ |
| **private** | ✅ | ❌ | ❌ | ❌ |

> [!tip] 핵심 포인트
> 접근제어자는 객체지향의 캡슐화를 실현하는 핵심 메커니즘입니다. 적절한 접근제어자 사용은 코드의 안정성과 유지보수성을 크게 향상시킵니다.

---

#### 1. public

##### 개념 정의

**public**은 모든 클래스에서 접근 가능한 접근제어자입니다. 가장 넓은 접근 범위를 가지며, 어디서든 접근할 수 있습니다.

##### 접근 범위

- 같은 클래스 내: ✅ 접근 가능
- 같은 패키지 내: ✅ 접근 가능
- 다른 패키지의 상속 관계: ✅ 접근 가능
- 다른 패키지: ✅ 접근 가능

##### 사용 시나리오

- 외부에 공개해야 하는 API
- 클라이언트가 사용해야 하는 메서드
- 상수 (public static final)
- 메인 메서드 (public static void main)

##### 예시

```java
// 다른 패키지에서도 접근 가능
package com.example.model;

public class User {
    public String name;        // public 필드
    public int age;            // public 필드
    
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public void setName(String name) {  // public 메서드
        this.name = name;
    }
    
    public String getName() {  // public 메서드
        return name;
    }
}

// 다른 패키지에서 사용
package com.example.controller;

import com.example.model.User;

public class UserController {
    public void example() {
        User user = new User("홍길동", 30);
        user.name = "김철수";  // ✅ 접근 가능
        user.setName("이영희");  // ✅ 접근 가능
    }
}
```

##### 주의사항

- public 필드는 직접 노출되므로 캡슐화 원칙에 위배될 수 있음
- 가능하면 public 메서드를 통해 접근하도록 설계 권장

---

#### 2. protected

##### 개념 정의

**protected**는 같은 패키지 내 또는 상속 관계에 있는 클래스에서 접근 가능한 접근제어자입니다.

##### 접근 범위

- 같은 클래스 내: ✅ 접근 가능
- 같은 패키지 내: ✅ 접근 가능
- 다른 패키지의 상속 관계: ✅ 접근 가능
- 다른 패키지: ❌ 접근 불가능

##### 사용 시나리오

- 상속 관계에서 자식 클래스가 접근해야 하는 멤버
- 패키지 내부에서만 사용하되, 상속을 허용해야 하는 경우
- 추상 메서드나 템플릿 메서드 패턴에서 사용

##### 예시

```java
// 부모 클래스
package com.example.model;

public class Animal {
    protected String name;  // protected 필드
    
    protected void makeSound() {  // protected 메서드
        System.out.println("동물이 소리를 냅니다.");
    }
}

// 같은 패키지: 접근 가능
package com.example.model;

public class Dog extends Animal {
    public void bark() {
        this.name = "멍멍이";  // ✅ 접근 가능 (상속 관계)
        this.makeSound();      // ✅ 접근 가능 (상속 관계)
    }
}

// 다른 패키지: 상속 관계에서만 접근 가능
package com.example.other;

import com.example.model.Animal;

public class Cat extends Animal {
    public void meow() {
        this.name = "야옹이";  // ✅ 접근 가능 (상속 관계)
        this.makeSound();      // ✅ 접근 가능 (상속 관계)
    }
}

// 다른 패키지: 상속 관계가 아니면 접근 불가
package com.example.other;

import com.example.model.Animal;

public class Test {
    public void example() {
        Animal animal = new Animal();
        // animal.name = "test";  // ❌ 컴파일 에러
        // animal.makeSound();    // ❌ 컴파일 에러
    }
}
```

##### 주의사항

- protected는 상속 관계에서 자주 사용되지만, 패키지 내부 접근도 허용하므로 주의 필요
- 외부 패키지에서 상속만 허용하고 직접 접근은 막고 싶다면 다른 방법 고려

---

#### 3. default (package-private)

##### 개념 정의

**default**는 접근제어자를 명시하지 않을 때 적용되는 기본 접근제어자입니다. 같은 패키지 내에서만 접근 가능합니다.

##### 접근 범위

- 같은 클래스 내: ✅ 접근 가능
- 같은 패키지 내: ✅ 접근 가능
- 다른 패키지의 상속 관계: ❌ 접근 불가능
- 다른 패키지: ❌ 접근 불가능

##### 사용 시나리오

- 패키지 내부에서만 사용하는 유틸리티 클래스
- 내부 구현 세부사항
- 패키지 레벨의 캡슐화가 필요한 경우

##### 예시

```java
// 같은 패키지
package com.example.util;

class StringUtil {  // default 클래스
    static String capitalize(String str) {  // default 메서드
        if (str == null || str.isEmpty()) {
            return str;
        }
        return str.substring(0, 1).toUpperCase() + str.substring(1);
    }
}

// 같은 패키지에서 사용
package com.example.util;

public class Main {
    public void example() {
        String result = StringUtil.capitalize("hello");  // ✅ 접근 가능
    }
}

// 다른 패키지에서 사용
package com.example.other;

import com.example.util.StringUtil;  // ❌ 컴파일 에러: 접근 불가능

public class Test {
    public void example() {
        // StringUtil.capitalize("hello");  // ❌ 접근 불가능
    }
}
```

##### 주의사항

- default는 키워드가 아니라 접근제어자를 명시하지 않을 때의 기본값
- 패키지 레벨의 캡슐화를 제공하지만, 상속 관계에서도 접근 불가능

---

#### 4. private

##### 개념 정의

**private**은 같은 클래스 내에서만 접근 가능한 가장 제한적인 접근제어자입니다.

##### 접근 범위

- 같은 클래스 내: ✅ 접근 가능
- 같은 패키지 내: ❌ 접근 불가능
- 다른 패키지의 상속 관계: ❌ 접근 불가능
- 다른 패키지: ❌ 접근 불가능

##### 사용 시나리오

- 클래스의 내부 구현 세부사항
- 외부에서 직접 접근하면 안 되는 필드
- 헬퍼 메서드나 내부 로직
- 캡슐화를 위한 데이터 은닉

##### 예시

```java
public class BankAccount {
    private String accountNumber;  // private 필드
    private double balance;        // private 필드
    
    public BankAccount(String accountNumber, double balance) {
        this.accountNumber = accountNumber;
        this.balance = balance;
    }
    
    // public 메서드를 통한 접근
    public double getBalance() {
        return balance;
    }
    
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;  // ✅ 같은 클래스 내에서 접근 가능
        }
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;  // ✅ 같은 클래스 내에서 접근 가능
        }
    }
    
    private void validateAccount() {  // private 메서드
        // 내부 검증 로직
    }
}

// 다른 클래스에서 사용
public class Main {
    public void example() {
        BankAccount account = new BankAccount("123-456", 1000);
        // account.balance = 5000;  // ❌ 컴파일 에러: 접근 불가능
        // account.accountNumber = "999";  // ❌ 컴파일 에러: 접근 불가능
        
        account.deposit(500);  // ✅ public 메서드로 접근
        double balance = account.getBalance();  // ✅ public 메서드로 접근
    }
}
```

##### 주의사항

- private 멤버는 상속 관계에서도 접근 불가능
- 내부 구현을 완전히 숨기고 싶을 때 사용
- Getter/Setter 패턴과 함께 사용

---

#### 5. 클래스 레벨 접근제어자

##### public 클래스

```java
// public 클래스: 모든 패키지에서 접근 가능
package com.example.model;

public class User {
    // ...
}
```

##### default 클래스

```java
// default 클래스: 같은 패키지에서만 접근 가능
package com.example.util;

class StringUtil {  // 접근제어자 없음 = default
    // ...
}
```

##### 주의사항

- 클래스는 `public` 또는 `default`만 가능
- `private` 또는 `protected` 클래스는 불가능 (내부 클래스 제외)
- 하나의 파일에는 하나의 public 클래스만 가능

---

#### 6. 접근제어자와 캡슐화

##### 캡슐화의 실현

접근제어자는 객체지향의 캡슐화를 실현하는 핵심 메커니즘입니다.

**캡슐화의 목적**:
- 데이터 무결성 보장
- 내부 구현 숨기기
- 외부 인터페이스만 노출
- 변경 영향 범위 최소화

**예시**:
```java
// ❌ 나쁜 예: 캡슐화 없음
public class User {
    public String name;
    public int age;
    public String password;  // 위험: 비밀번호 노출
}

// ✅ 좋은 예: 캡슐화 적용
public class User {
    private String name;
    private int age;
    private String password;  // private으로 보호
    
    // Getter/Setter를 통한 접근
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        if (name != null && !name.isEmpty()) {
            this.name = name;  // 검증 로직 포함 가능
        }
    }
    
    // 비밀번호는 Getter 제공 안 함 (보안)
    public boolean checkPassword(String inputPassword) {
        return this.password.equals(inputPassword);
    }
}
```

---

### **Interview Answer Version (면접 답변식 요약)**

접근제어자는 클래스, 메서드, 변수 등의 접근 범위를 제한하는 키워드로, Java에서는 public, protected, default, private 4가지를 제공합니다.

**public**은 모든 클래스에서 접근 가능하며, 외부에 공개해야 하는 API에 사용합니다. **protected**는 같은 패키지 내 또는 상속 관계에 있는 클래스에서 접근 가능하며, 상속 관계에서 자식 클래스가 접근해야 하는 멤버에 사용합니다.

**default**는 접근제어자를 명시하지 않을 때 적용되며, 같은 패키지 내에서만 접근 가능합니다. 패키지 내부에서만 사용하는 유틸리티 클래스에 사용합니다.

**private**은 같은 클래스 내에서만 접근 가능하며, 클래스의 내부 구현 세부사항이나 외부에서 직접 접근하면 안 되는 필드에 사용합니다.

접근제어자는 객체지향의 캡슐화를 실현하는 핵심 메커니즘으로, 적절한 사용은 데이터 무결성을 보장하고 유지보수성을 높입니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 접근제어자 선택 가이드

**1. 최소 권한 원칙**
- 필요한 최소한의 접근 범위만 허용
- 처음에는 private으로 시작하고 필요시 점진적으로 확장

**2. 필드는 private, 메서드는 public**
```java
// ✅ 좋은 예
public class User {
    private String name;  // 필드는 private
    private int age;
    
    public String getName() {  // 접근이 필요한 경우 public 메서드
        return name;
    }
    
    public void setName(String name) {  // 수정이 필요한 경우 public 메서드
        this.name = name;
    }
}
```

**3. 상속 관계에서의 접근제어자**
```java
// 부모 클래스
public class Animal {
    protected String name;  // 자식 클래스에서 접근 가능
    
    protected void makeSound() {  // 자식 클래스에서 오버라이딩 가능
        System.out.println("동물 소리");
    }
}

// 자식 클래스
public class Dog extends Animal {
    public void bark() {
        this.name = "멍멍이";  // ✅ protected 필드 접근 가능
        this.makeSound();      // ✅ protected 메서드 접근 가능
    }
}
```

#### 실무 활용 예시

**1. Getter/Setter 패턴**
```java
public class Product {
    private String name;
    private double price;
    private int stock;
    
    // Getter
    public String getName() {
        return name;
    }
    
    public double getPrice() {
        return price;
    }
    
    // Setter with validation
    public void setPrice(double price) {
        if (price >= 0) {
            this.price = price;
        } else {
            throw new IllegalArgumentException("가격은 0 이상이어야 합니다.");
        }
    }
    
    // Business method
    public void reduceStock(int quantity) {
        if (quantity > 0 && quantity <= stock) {
            stock -= quantity;
        } else {
            throw new IllegalArgumentException("재고가 부족합니다.");
        }
    }
}
```

**2. Builder 패턴과 접근제어자**
```java
public class User {
    private String name;
    private String email;
    private int age;
    
    // private 생성자
    private User(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.age = builder.age;
    }
    
    // public static Builder
    public static Builder builder() {
        return new Builder();
    }
    
    // 내부 Builder 클래스
    public static class Builder {
        private String name;
        private String email;
        private int age;
        
        public Builder name(String name) {
            this.name = name;
            return this;
        }
        
        public Builder email(String email) {
            this.email = email;
            return this;
        }
        
        public Builder age(int age) {
            this.age = age;
            return this;
        }
        
        public User build() {
            return new User(this);
        }
    }
}
```

**3. 패키지 레벨 캡슐화**
```java
// 패키지 내부 유틸리티
package com.example.util;

class InternalUtil {  // default 클래스
    static void helperMethod() {
        // 패키지 내부에서만 사용
    }
}

// 외부에 공개하는 API
package com.example.util;

public class PublicAPI {
    public void publicMethod() {
        InternalUtil.helperMethod();  // 같은 패키지에서 접근 가능
    }
}
```

#### 주의사항

**1. 상속에서의 접근제어자**
```java
class Parent {
    private void method() { }  // private은 오버라이딩 불가능
    protected void method2() { }  // protected는 오버라이딩 가능
}

class Child extends Parent {
    // @Override
    // private void method() { }  // ❌ 컴파일 에러
    
    @Override
    protected void method2() { }  // ✅ 가능
    // 또는
    @Override
    public void method2() { }  // ✅ 가능 (더 넓은 접근제어자)
}
```

**2. 인터페이스의 접근제어자**
```java
public interface MyInterface {
    // 인터페이스의 메서드는 기본적으로 public
    void method();  // public abstract void method();와 동일
    
    // Java 9+ 부터 private 메서드 가능
    private void helper() {
        // 내부 구현 메서드
    }
}
```

---

### 예상 꼬리질문정리

1. **접근제어자의 접근 범위를 구체적으로 설명해주세요.**
   - public은 모든 클래스에서 접근 가능하고, protected는 같은 패키지 내 또는 상속 관계에서 접근 가능합니다. default는 같은 패키지 내에서만 접근 가능하고, private은 같은 클래스 내에서만 접근 가능합니다.

2. **protected와 default의 차이는 무엇인가요?**
   - protected는 같은 패키지 내 또는 상속 관계에 있는 클래스에서 접근 가능하지만, default는 같은 패키지 내에서만 접근 가능합니다. 즉, protected는 다른 패키지의 상속 관계에서도 접근 가능하지만 default는 불가능합니다.

3. **왜 필드는 private으로 하고 Getter/Setter를 사용하나요?**
   - 필드를 private으로 하면 직접 접근을 막아 데이터 무결성을 보장할 수 있고, Getter/Setter를 통해 검증 로직을 추가하거나 접근을 제어할 수 있습니다. 또한 내부 구현 변경이 외부 코드에 영향을 주지 않도록 할 수 있습니다.

4. **상속 관계에서 접근제어자를 변경할 수 있나요?**
   - 자식 클래스에서 오버라이딩할 때 접근제어자를 더 넓게 할 수 있지만, 더 좁게 할 수는 없습니다. 예를 들어 protected 메서드를 public으로 오버라이딩할 수 있지만, private으로는 할 수 없습니다. 이는 리스코프 치환 원칙 때문입니다.

5. **클래스 레벨 접근제어자는 무엇이 있나요?**
   - 클래스는 public 또는 default만 가능합니다. public 클래스는 모든 패키지에서 접근 가능하고, default 클래스는 같은 패키지에서만 접근 가능합니다. private이나 protected 클래스는 불가능하지만, 내부 클래스(Inner Class)는 가능합니다.

6. **접근제어자와 캡슐화의 관계는?**
   - 접근제어자는 캡슐화를 실현하는 핵심 메커니즘입니다. private으로 내부 구현을 숨기고, public 메서드를 통해 외부 인터페이스만 노출함으로써 데이터 무결성을 보장하고 변경 영향 범위를 최소화합니다.

7. **패키지 레벨 캡슐화란 무엇인가요?**
   - default 접근제어자를 사용하여 같은 패키지 내에서만 접근 가능하게 하는 것을 말합니다. 패키지 내부의 유틸리티 클래스나 내부 구현 세부사항을 외부 패키지로부터 보호할 수 있습니다.

8. **인터페이스의 메서드 접근제어자는?**
   - 인터페이스의 메서드는 기본적으로 public abstract입니다. Java 9부터는 private 메서드도 선언할 수 있어 내부 구현 메서드로 사용할 수 있습니다.

9. **내부 클래스(Inner Class)의 접근제어자는?**
   - 내부 클래스는 private, protected, default, public 모두 가능합니다. 외부 클래스의 private 멤버에도 접근할 수 있어 캡슐화를 더 세밀하게 제어할 수 있습니다.

10. **접근제어자를 선택할 때 고려사항은?**
    - 최소 권한 원칙을 따르고, 처음에는 private으로 시작하여 필요시 점진적으로 확장합니다. 필드는 private으로 하고 메서드를 통해 접근하도록 하며, 상속 관계를 고려하여 protected를 적절히 사용합니다.

---

## 관련 노트

- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - 접근제어자가 적용되는 객체지향의 핵심 개념
- **[[캡슐화]]** - 접근제어자를 통한 캡슐화 실현
- **[[상속과 컴포지션 (Inheritance vs Composition)|상속]]** - protected 접근제어자와 상속의 관계

---

**태그:** #java #접근제어자 #access-modifier #캡슐화 #면접 #개념정리

