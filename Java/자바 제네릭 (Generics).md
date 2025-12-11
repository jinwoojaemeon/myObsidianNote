# 자바 제네릭 (Generics)

#java #제네릭 #generics #타입안정성 #컬렉션 #면접 #개념정리

**관련 개념:** [[Collections Framework (컬렉션 프레임워크)|컬렉션 프레임워크]] [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]

> [!note] 이어서 읽기
> 제네릭을 이해하려면 **[[Collections Framework (컬렉션 프레임워크)|컬렉션 프레임워크]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

Java의 제네릭이 무엇인지, 왜 사용하는지, 그리고 제네릭 클래스, 메서드, 제한된 제네릭, 와일드카드의 사용법을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 제네릭이 없으면 컬렉션에 객체를 담을 때 모두 Object 타입으로 저장되어, 객체를 꺼낼 때마다 개발자가 원하는 타입으로 직접 형변환(Type Casting)을 해줘야 했습니다. 또한 여러 종류의 타입이 하나의 컬렉션에 섞여 들어갈 수 있어 타입 체크가 불가능했습니다.

- 제네릭은 클래스나 메서드에서 사용할 내부 데이터 타입을 외부에서 지정하는 방법으로, 타입 안정성을 제공하고 코드를 간결하게 만듭니다. 컴파일 시점에 타입 체크가 이루어져 런타임 에러를 방지하며, 타입 캐스팅을 생략할 수 있어 코드가 간결해집니다.

- 제네릭의 등장 배경, 제네릭 클래스와 메서드의 문법, 제한된 제네릭(Bounded Generics), 와일드카드의 이해와 활용, 그리고 실무에서의 적절한 제네릭 사용 능력을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**제네릭(Generic)**은 클래스나 메서드에서 사용할 내부 데이터 타입을 외부에서 지정하는 방법입니다. `<>` (다이아몬드 꺽쇠) 안에 타입 매개변수를 선언하여 사용하며, 해당 객체의 타입은 컴파일 타임에 지정됩니다.

**제네릭의 핵심 장점**:
- **타입 안정성 제공**: 특정 타입으로 제한함으로써 타입 안정성을 확보합니다.
- **코드 간결화**: 타입 체크나 형변환을 생략할 수 있어 코드가 간결해집니다.

> [!tip] 핵심 포인트
> 제네릭은 컴파일 시점에 타입을 체크하여 런타임 에러를 방지하고, 코드의 재사용성과 안정성을 높이는 핵심 메커니즘입니다.

---

## 1. 제네릭의 등장 배경

### 등장 시기

제네릭은 **JDK 1.5 버전**에서 등장했습니다.

### 제네릭 이전의 문제점 (JDK 1.5 이전)

#### 1. 타입 캐스팅의 불편함

컬렉션에 객체를 담을 때 모두 Object 타입으로 저장되었기 때문에, 객체를 꺼낼 때마다 개발자가 원하는 타입으로 직접 **형변환(Type Casting)** 을 해줘야 했습니다.

```java
// 제네릭 이전 코드
List list = new ArrayList();
list.add("문자열");
list.add(new Integer(100));
list.add(new Car());

// 꺼낼 때마다 형변환 필요
String str = (String) list.get(0);  // 형변환 필수
Integer num = (Integer) list.get(1);  // 형변환 필수
Car car = (Car) list.get(2);  // 형변환 필수
```

#### 2. 타입 안전성 문제

여러 종류의 타입(예: 피스, 자동차, 사다리)이 하나의 컬렉션에 섞여 들어갈 수 있어 타입 체크가 불가능했습니다. 런타임에 `ClassCastException`이 발생할 위험이 있었습니다.

```java
// 제네릭 이전 코드
List pieces = new ArrayList();
pieces.add(new King());
pieces.add(new Car());  // 잘못된 타입이 들어가도 컴파일 시점에 알 수 없음
pieces.add(new Ladder());

// 런타임 에러 발생 가능
King king = (King) pieces.get(1);  // ClassCastException 발생!
```

### 제네릭의 장점

#### 1. 타입 캐스팅 불필요

컬렉션 선언 시 타입을 지정하면 객체를 꺼낼 때 형변환이 필요 없습니다.

```java
// 제네릭 사용
List<String> list = new ArrayList<>();
list.add("문자열");
String str = list.get(0);  // 형변환 불필요!
```

#### 2. 타입 체크 자동화

컴파일 시점에 자동으로 타입 체크가 되어, 지정된 타입이 아닌 객체는 넣을 수 없게 미리 알려주어 타입 안전성을 확보합니다.

```java
// 제네릭 사용
List<King> pieces = new ArrayList<>();
pieces.add(new King());
// pieces.add(new Car());  // ❌ 컴파일 에러: 타입 불일치
// pieces.add(new Ladder());  // ❌ 컴파일 에러: 타입 불일치

King king = pieces.get(0);  // ✅ 안전하게 사용 가능
```

---

## 2. 제네릭 기초 문법

### A. 제네릭 클래스 및 인터페이스 설계

#### 선언

클래스/인터페이스 이름 뒤에 **다이아몬드 연산자 (`<>`)** 를 사용하고, 그 안에 제네릭 타입 매개변수를 지정합니다 (예: `<T>`).

```java
// 제네릭 클래스 선언
public class Box<T> {
    private T item;
    
    public void setItem(T item) {
        this.item = item;
    }
    
    public T getItem() {
        return item;
    }
}

// 사용
Box<String> stringBox = new Box<>();
Box<Integer> integerBox = new Box<>();
```

#### 사용

기존에 Object 대신 지정한 타입 매개변수(T)를 사용하면, 해당 클래스/인터페이스가 어떤 타입이든 받을 수 있는 제네릭 클래스가 됩니다.

```java
public class RandomBox<T> {
    private T item;
    
    public void setItem(T item) {
        this.item = item;
    }
    
    public T getItem() {
        return item;
    }
}

// 다양한 타입으로 사용 가능
RandomBox<String> stringBox = new RandomBox<>();
RandomBox<Integer> integerBox = new RandomBox<>();
RandomBox<Car> carBox = new RandomBox<>();
```

#### 상속 및 구현

제네릭을 사용하는 인터페이스를 구현하거나 제네릭 클래스를 상속받을 때, 상속/구현하는 클래스에도 제네릭 타입을 똑같이 적어줘야 합니다.

```java
// 제네릭 인터페이스
interface Container<T> {
    void add(T item);
    T get(int index);
}

// 구현 시 제네릭 타입 지정 필요
class ListContainer<T> implements Container<T> {
    private List<T> items = new ArrayList<>();
    
    @Override
    public void add(T item) {
        items.add(item);
    }
    
    @Override
    public T get(int index) {
        return items.get(index);
    }
}

// 상속 시 기존 제네릭 외에 새로운 제네릭 추가 가능
class ExtendedBox<T, U> extends Box<T> {
    private U extra;
    // ...
}
```

### B. 제네릭 메서드

#### 구분

제네릭 메서드는 반환 타입 앞에 다이아몬드 연산자와 제네릭 타입 매개변수를 선언한 메서드만 해당합니다.

```java
public class Util {
    // 제네릭 메서드
    public static <T> T getFirst(List<T> list) {
        return list.isEmpty() ? null : list.get(0);
    }
    
    // 일반 메서드 (클래스가 제네릭이어도 메서드는 제네릭이 아님)
    public void normalMethod() {
        // ...
    }
}
```

#### 특징

제네릭 메서드에서 선언된 타입(예: T)은 클래스 레벨에서 선언된 타입과 서로 독립된 타입으로 간주됩니다. 즉, 해당 타입은 메서드 지역 범위 내에서만 유효합니다.

```java
public class Box<T> {  // 클래스 레벨 제네릭
    private T item;
    
    // 제네릭 메서드 (클래스의 T와 독립적)
    public <U> U convertTo(Class<U> clazz) {
        // 메서드 레벨의 U 타입 사용
        return clazz.cast(item);
    }
    
    // 주의: 시그니처 충돌
    // public <T> void method(T item) { }  // ⚠️ 클래스의 T를 숨김 (경고)
    public <U> void method(U item) { }  // ✅ 다른 이름 사용 권장
}
```

> [!warning] 주의사항
> 함수의 타입 매개변수 시그니처가 클래스의 타입 매개변수 시그니처와 동일하면, 컴파일러는 **함수의 타입 매개변수가 클래스의 타입을 숨긴다(우선시된다)**고 경고합니다. 이를 피하려면 클래스와 메서드의 시그니처를 다르게 설정해야 합니다.

---

## 3. 제한된 제네릭 (Bounded Generics)

### 목적

모든 타입이 아닌 특정 타입 범위만 허용하도록 타입을 제한할 때 사용합니다.

### 상한 경계 (Upper Bound)

#### 선언 형태

`T extends 타입`

#### 의미

해당 타입과 **그 자손 타입(하위 타입)** 만 제네릭 타입으로 사용할 수 있도록 제한합니다.

```java
// Number와 그 하위 타입만 허용
public class NumberBox<T extends Number> {
    private T number;
    
    public void setNumber(T number) {
        this.number = number;
    }
    
    public T getNumber() {
        return number;
    }
    
    // Number의 메서드 사용 가능
    public double getDoubleValue() {
        return number.doubleValue();  // ✅ Number의 메서드 사용 가능
    }
}

// 사용
NumberBox<Integer> intBox = new NumberBox<>();  // ✅ 가능
NumberBox<Double> doubleBox = new NumberBox<>();  // ✅ 가능
NumberBox<Long> longBox = new NumberBox<>();  // ✅ 가능
// NumberBox<String> stringBox = new NumberBox<>();  // ❌ 컴파일 에러
```

#### 장점

1. **타입 안정성**: 상한 경계로 Number 타입을 지정하면, 함수 내에서 T 타입 변수는 Number에 구현된 모든 함수(예: `doubleValue()`)를 사용 가능하게 보장받습니다.

2. **컴파일 타임 에러 방지**: 잘못된 타입이 사용될 경우 컴파일 에러를 발생시켜 런타임 에러를 방지합니다.

```java
// Piece와 그 하위 타입만 허용
public class PieceBox<T extends Piece> {
    private T piece;
    
    public void setPiece(T piece) {
        this.piece = piece;
    }
    
    public T getPiece() {
        return piece;
    }
    
    // Piece의 메서드 사용 가능
    public void move() {
        piece.move();  // ✅ Piece의 메서드 사용 가능
    }
}

// 사용
PieceBox<King> kingBox = new PieceBox<>();  // ✅ 가능
PieceBox<Queen> queenBox = new PieceBox<>();  // ✅ 가능
// PieceBox<Car> carBox = new PieceBox<>();  // ❌ 컴파일 에러
```

---

## 4. 와일드카드 (?)의 이해와 사용

### 와일드카드 자체의 한계

#### 값 대입 불가

와일드카드로 지정된 객체 (`RandomBox<?>`)는 생성 시 타입이 확정되지 않았기 때문에, `setItem()`과 같은 값의 대입(Setter) 함수를 사용할 수 없습니다 (컴파일 에러).

```java
RandomBox<?> box = new RandomBox<String>();
// box.setItem("문자열");  // ❌ 컴파일 에러: 타입이 확정되지 않음
```

#### Getter만 가능

따라서 와일드카드가 적용된 제네릭은 값을 꺼내는(Getter) 형태로만 쓸 수 있습니다.

```java
RandomBox<?> box = new RandomBox<String>();
Object item = box.getItem();  // ✅ 가능: Object로만 받을 수 있음
```

### 경계 와일드카드

#### 경계 설정

제한된 제네릭과 마찬가지로 `? extends Type` (상한), `? super Type` (하한) 형태로 경계를 설정할 수 있습니다.

```java
// 상한 제한: Number와 그 하위 타입
List<? extends Number> numbers;

// 하한 제한: Number와 그 상위 타입
List<? super Number> numbers;
```

#### 제한된 제네릭과의 차이

- **`T extends Number`**: Number와 그 자손 타입 중 하나로 치환되어 구체적인 타입이 확정됩니다.
- **`? extends Number`**: Number와 그 하위 타입임은 보장되지만 구체적으로 어떤 타입인지는 확정 불가능하다는 의미를 가집니다.

```java
// 제한된 제네릭: 구체적인 타입 확정
public <T extends Number> void process(List<T> list) {
    T item = list.get(0);  // T 타입으로 확정
}

// 와일드카드: 구체적인 타입 불확정
public void process(List<? extends Number> list) {
    Number item = list.get(0);  // Number 타입으로만 받을 수 있음
    // T item = list.get(0);  // ❌ T가 무엇인지 알 수 없음
}
```

### 와일드카드 활용 (제네릭과의 결합)

#### 진가 발휘

와일드카드는 제한된 제네릭보다 약한 제약 강도를 가지며, 매개변수나 반환 타입 한쪽만 제한하는 특성을 활용해 제네릭과 결합할 때 복잡한 제약 요건을 부여할 수 있습니다.

#### 자식/부모 간 복사 함수 예시

**PECS 원칙 (Producer Extends, Consumer Super)**:
- **`extends`를 사용한 리스트 (`? extends T`)**: 값을 추출하는 역할 (자식 타입임을 보장)
- **`super`를 사용한 리스트 (`? super T`)**: 값을 삽입하는 역할 (부모 타입임을 보장)

```java
// 자식 클래스에서 값을 추출해 부모 클래스에 삽입
public static <T> void copy(List<? extends T> src, List<? super T> dest) {
    for (T item : src) {
        dest.add(item);  // ✅ 안전하게 복사 가능
    }
}

// 사용 예시
List<Integer> integers = Arrays.asList(1, 2, 3);
List<Number> numbers = new ArrayList<>();
copy(integers, numbers);  // ✅ Integer를 Number로 복사
```

이러한 방식으로 자식 클래스에서 값을 추출해 부모 클래스에 삽입하는 상속 관계의 복사 로직을 안정적으로 구현할 수 있습니다. 이는 Java의 **PECS(Producer Extends, Consumer Super)** 원칙과 연결됩니다.

### 제네릭과 형변환의 차이

#### 일반 객체

부모 클래스 변수에 자식 클래스 객체를 대입하는 것은 자연스럽게 허용됩니다.

```java
Piece piece = new King();  // ✅ 가능
```

#### 제네릭 객체

제네릭 타입이 지정된 컬렉션은 형변환이 허용되지 않습니다.

```java
List<King> kings = new ArrayList<>();
// List<Piece> pieces = kings;  // ❌ 컴파일 에러
```

제네릭 타입은 한 번 정해지면 더 이상 변하지 않기 때문입니다. 이를 해결하기 위해 와일드카드가 등장했습니다.

### 와일드카드 사용법

| 와일드카드 사용법 | 설명 | 예시 |
|------------------|------|------|
| **제한 없음** | 어떤 타입의 객체든 들어갈 수 있습니다 (Object와 동일) | `List<?>` |
| **상한 제한** | 지정된 클래스/인터페이스를 상속받거나 구현한 타입만 가능 | `List<? extends Piece>` |
| **하한 제한** | 지정된 클래스/인터페이스 및 그 조상(부모) 타입만 가능 | `List<? super King>` |

#### 와일드카드 사용 시 주의점

- 클래스나 제네릭 타입을 선언할 때는 와일드카드를 사용할 수 없습니다.
- 와일드카드는 변수를 사용할 때 (객체가 실제로 들어올 곳)만 사용할 수 있습니다.

```java
// ❌ 잘못된 사용
// class Box<?> { }  // 컴파일 에러

// ✅ 올바른 사용
Box<?> box = new Box<String>();  // 변수 선언 시 사용
public void process(List<? extends Number> list) { }  // 매개변수에 사용
```

---

## 5. 복잡한 제네릭 해석 예시

제네릭 메서드의 선언부를 복합적으로 해석하는 방법을 설명합니다.

```java
<K extends Comparable<? super K>, V> Map<K, V> someMethod(K key, V value)
```

### 단계별 해석

1. **제네릭 타입 선언**: 메서드에서 사용할 제네릭 타입 K와 V를 선언했습니다.

2. **K의 제한**: K는 `Comparable` 인터페이스를 구현하거나 상속받아야 합니다 (`K extends Comparable`).

3. **Comparable의 제한**: `Comparable`은 K와 동일하거나 그 상위 타입을 사용하는 제네릭 클래스여야 합니다 (`? super K`).

4. **반환 타입**: 이 메서드는 K와 V를 키와 값으로 사용하는 `Map` 객체를 반환합니다.

### 실제 사용 예시

```java
public class GenericExample {
    public static <K extends Comparable<? super K>, V> 
            Map<K, V> createMap(K key, V value) {
        Map<K, V> map = new HashMap<>();
        map.put(key, value);
        return map;
    }
}

// 사용
Map<String, Integer> map1 = createMap("key", 100);
Map<Integer, String> map2 = createMap(1, "value");
```

---

### **Interview Answer Version (면접 답변식 요약)**

제네릭은 클래스나 메서드에서 사용할 내부 데이터 타입을 외부에서 지정하는 방법으로, JDK 1.5 버전에서 등장했습니다. `<>` (다이아몬드 꺽쇠) 안에 타입 매개변수를 선언하여 사용하며, 해당 객체의 타입은 컴파일 타임에 지정됩니다.

제네릭의 주요 장점은 타입 안정성 제공과 코드 간결화입니다. 제네릭 이전에는 컬렉션에 객체를 담을 때 모두 Object 타입으로 저장되어, 객체를 꺼낼 때마다 형변환이 필요했고, 여러 종류의 타입이 섞여 들어갈 수 있어 타입 안전성 문제가 있었습니다. 제네릭을 사용하면 컴파일 시점에 타입 체크가 이루어져 타입 안전성을 확보하고, 형변환을 생략할 수 있어 코드가 간결해집니다.

제네릭 클래스는 클래스 이름 뒤에 타입 매개변수(`<T>`, `<K, V>` 등)를 위치시켜 선언하며, 제네릭 메서드는 반환 타입 사이에 타입 매개변수(`<T>`)를 위치시키는 방식으로 선언합니다. 제한된 제네릭은 `T extends 타입` 형태로 특정 타입 범위만 허용하도록 제한하며, 와일드카드(`?`)는 제네릭 객체 간의 유연한 상속 관계를 위해 사용됩니다. 와일드카드는 `? extends Type` (상한), `? super Type` (하한) 형태로 경계를 설정할 수 있으며, PECS 원칙에 따라 Producer는 extends, Consumer는 super를 사용합니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 제네릭 사용 가이드

**1. 타입 안정성을 위한 제네릭 사용**

```java
// ❌ 나쁜 예: 제네릭 없이 사용
List list = new ArrayList();
list.add("문자열");
list.add(100);  // 타입 불일치지만 컴파일 통과
String str = (String) list.get(1);  // 런타임 에러!

// ✅ 좋은 예: 제네릭 사용
List<String> list = new ArrayList<>();
list.add("문자열");
// list.add(100);  // ❌ 컴파일 에러: 타입 불일치
String str = list.get(0);  // ✅ 안전하게 사용
```

**2. 제한된 제네릭 활용**

```java
// Number와 그 하위 타입만 허용
public class Calculator<T extends Number> {
    private T number;
    
    public Calculator(T number) {
        this.number = number;
    }
    
    public double getDoubleValue() {
        return number.doubleValue();  // Number의 메서드 사용 가능
    }
    
    public <U extends Number> double add(U other) {
        return number.doubleValue() + other.doubleValue();
    }
}

// 사용
Calculator<Integer> calc1 = new Calculator<>(10);
Calculator<Double> calc2 = new Calculator<>(20.5);
// Calculator<String> calc3 = new Calculator<>("hello");  // ❌ 컴파일 에러
```

**3. 와일드카드와 PECS 원칙**

```java
// Producer Extends: 값을 읽어오는 경우
public static double sum(List<? extends Number> numbers) {
    double sum = 0.0;
    for (Number num : numbers) {
        sum += num.doubleValue();
    }
    return sum;
}

// Consumer Super: 값을 추가하는 경우
public static void addNumbers(List<? super Integer> list) {
    list.add(1);
    list.add(2);
    list.add(3);
}

// 사용
List<Integer> integers = Arrays.asList(1, 2, 3);
List<Number> numbers = new ArrayList<>();
double result = sum(integers);  // ✅ Producer: extends 사용
addNumbers(numbers);  // ✅ Consumer: super 사용
```

**4. 제네릭 메서드 활용**

```java
public class ArrayUtil {
    // 제네릭 메서드: 배열을 리스트로 변환
    public static <T> List<T> arrayToList(T[] array) {
        List<T> list = new ArrayList<>();
        for (T item : array) {
            list.add(item);
        }
        return list;
    }
    
    // 제한된 제네릭 메서드: 비교 가능한 타입만 허용
    public static <T extends Comparable<T>> T max(T[] array) {
        if (array == null || array.length == 0) {
            return null;
        }
        T max = array[0];
        for (T item : array) {
            if (item.compareTo(max) > 0) {
                max = item;
            }
        }
        return max;
    }
}

// 사용
String[] strings = {"apple", "banana", "cherry"};
List<String> stringList = ArrayUtil.arrayToList(strings);
String maxString = ArrayUtil.max(strings);
```

#### 주의사항

**1. 타입 소거 (Type Erasure)**

제네릭은 컴파일 시점에만 존재하고, 런타임에는 타입 정보가 소거됩니다.

```java
List<String> stringList = new ArrayList<>();
List<Integer> integerList = new ArrayList<>();

// 런타임에는 둘 다 List로 동일하게 취급됨
System.out.println(stringList.getClass() == integerList.getClass());  // true
```

**2. 제네릭 배열 생성 불가**

제네릭 타입으로 배열을 직접 생성할 수 없습니다.

```java
// ❌ 불가능
// T[] array = new T[10];

// ✅ 가능: Object 배열 사용 후 형변환
public class GenericArray<T> {
    private Object[] array;
    
    @SuppressWarnings("unchecked")
    public GenericArray(int size) {
        array = new Object[size];
    }
    
    @SuppressWarnings("unchecked")
    public T get(int index) {
        return (T) array[index];
    }
    
    public void set(int index, T item) {
        array[index] = item;
    }
}
```

**3. 원시 타입과 제네릭 타입 혼용 주의**

```java
// ❌ 나쁜 예: 원시 타입과 제네릭 타입 혼용
List rawList = new ArrayList();
List<String> stringList = new ArrayList<>();
rawList = stringList;  // 경고 발생

// ✅ 좋은 예: 제네릭 타입 일관성 유지
List<String> stringList1 = new ArrayList<>();
List<String> stringList2 = new ArrayList<>();
stringList1 = stringList2;  // ✅ 안전
```

---

### 예상 꼬리질문정리

1. **제네릭이 왜 필요한가요?**
   - 제네릭 이전에는 컬렉션에 객체를 담을 때 모두 Object 타입으로 저장되어, 객체를 꺼낼 때마다 형변환이 필요했고, 여러 종류의 타입이 섞여 들어갈 수 있어 타입 안전성 문제가 있었습니다. 제네릭을 사용하면 컴파일 시점에 타입 체크가 이루어져 타입 안전성을 확보하고, 형변환을 생략할 수 있어 코드가 간결해집니다.

2. **제네릭 클래스와 제네릭 메서드의 차이는 무엇인가요?**
   - 제네릭 클래스는 클래스 이름 뒤에 타입 매개변수를 선언하며, 클래스 전체에서 사용됩니다. 제네릭 메서드는 반환 타입 앞에 타입 매개변수를 선언하며, 메서드 지역 범위 내에서만 유효합니다. 제네릭 메서드의 타입은 클래스 레벨의 타입과 독립적입니다.

3. **제한된 제네릭(Bounded Generics)이란 무엇인가요?**
   - 모든 타입이 아닌 특정 타입 범위만 허용하도록 타입을 제한하는 것입니다. `T extends 타입` 형태로 선언하며, 해당 타입과 그 자손 타입만 제네릭 타입으로 사용할 수 있도록 제한합니다. 이를 통해 타입 안정성을 확보하고, 제한된 타입의 메서드를 사용할 수 있게 보장받습니다.

4. **와일드카드(?)와 제네릭 타입 매개변수(T)의 차이는 무엇인가요?**
   - 제네릭 타입 매개변수(T)는 구체적인 타입으로 치환되어 타입이 확정됩니다. 와일드카드(?)는 타입이 확정되지 않아 구체적으로 어떤 타입인지는 알 수 없지만, 경계를 통해 타입 범위는 보장됩니다. 와일드카드는 변수를 사용할 때만 사용할 수 있으며, 클래스나 제네릭 타입을 선언할 때는 사용할 수 없습니다.

5. **PECS 원칙이란 무엇인가요?**
   - PECS는 Producer Extends, Consumer Super의 약자로, 값을 읽어오는(Producer) 경우에는 `? extends Type`을 사용하고, 값을 추가하는(Consumer) 경우에는 `? super Type`을 사용하는 원칙입니다. 이를 통해 자식 클래스에서 값을 추출해 부모 클래스에 삽입하는 상속 관계의 복사 로직을 안정적으로 구현할 수 있습니다.

6. **제네릭과 형변환의 차이는 무엇인가요?**
   - 일반 객체는 부모 클래스 변수에 자식 클래스 객체를 대입하는 것이 자연스럽게 허용되지만, 제네릭 타입이 지정된 컬렉션은 형변환이 허용되지 않습니다. 제네릭 타입은 한 번 정해지면 더 이상 변하지 않기 때문입니다. 이를 해결하기 위해 와일드카드를 사용합니다.

7. **타입 소거(Type Erasure)란 무엇인가요?**
   - 제네릭은 컴파일 시점에만 존재하고, 런타임에는 타입 정보가 소거됩니다. 즉, `List<String>`과 `List<Integer>`는 런타임에는 모두 `List`로 동일하게 취급됩니다. 이는 하위 호환성을 위한 설계입니다.

8. **제네릭 배열을 생성할 수 없는 이유는 무엇인가요?**
   - 제네릭 타입으로 배열을 직접 생성할 수 없는 이유는 타입 소거로 인해 런타임에 타입 정보가 없기 때문입니다. 배열은 런타임에 타입 정보를 필요로 하므로, 제네릭 배열 생성은 허용되지 않습니다. 대신 Object 배열을 사용한 후 형변환하는 방식을 사용합니다.

9. **제네릭 메서드에서 클래스의 타입 매개변수와 메서드의 타입 매개변수가 같으면 어떻게 되나요?**
   - 함수의 타입 매개변수 시그니처가 클래스의 타입 매개변수 시그니처와 동일하면, 컴파일러는 함수의 타입 매개변수가 클래스의 타입을 숨긴다(우선시된다)고 경고합니다. 이를 피하려면 클래스와 메서드의 시그니처를 다르게 설정해야 합니다.

10. **제네릭의 등장 배경을 설명해주세요.**
    - 제네릭은 JDK 1.5 버전에서 등장했습니다. 제네릭 이전에는 컬렉션에 객체를 담을 때 모두 Object 타입으로 저장되어, 객체를 꺼낼 때마다 형변환이 필요했고, 여러 종류의 타입이 섞여 들어갈 수 있어 타입 안전성 문제가 있었습니다. 제네릭을 통해 이러한 문제를 해결하고 타입 안정성과 코드 간결성을 확보했습니다.

---

## 관련 노트

- **[[Collections Framework (컬렉션 프레임워크)|컬렉션 프레임워크]]** - 제네릭이 가장 많이 활용되는 영역
- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - 제네릭을 통한 타입 안정성 확보

---

**태그:** #java #제네릭 #generics #타입안정성 #컬렉션 #면접 #개념정리

