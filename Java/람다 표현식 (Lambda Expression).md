#java #lambda #함수형프로그래밍 #익명클래스 #면접 #개념정리

**관련 개념:** [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[자바 다형성 (Polymorphism)|다형성]] [[JavaScript 함수|함수]]

> [!note] 이어서 읽기
> 람다 표현식을 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

람다 표현식(Lambda Expression)이 무엇인지, 왜 사용하는지, 그리고 함수형 인터페이스와 함께 어떻게 활용하는지 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 익명 클래스를 사용하면 일회용 구현을 위해 클래스를 만들어야 하며, 특히 함수형 인터페이스처럼 메서드가 하나뿐인 경우 코드가 길고 반복적입니다. 이는 클래스 파일 증가, 가독성 저하, 유지보수 어려움으로 이어집니다.

- 람다 표현식은 메서드를 이름 없이, 짧게 표현하는 문법으로 클래스 없이, 객체 생성 없이, 동작(로직)만 전달할 수 있게 합니다. 이를 통해 코드 간결성과 가독성을 크게 향상시키고, 함수형 프로그래밍 스타일을 Java에 도입하여 스트림 API, Optional 등과 함께 현대적인 Java 개발을 가능하게 합니다.

- 익명 클래스와 람다의 차이, 함수형 인터페이스의 개념, 람다 표현식의 문법과 활용 방법, 그리고 실무에서 자주 사용하는 함수형 인터페이스들을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. 람다 표현식이란?

**람다 표현식(Lambda Expression)**은 메서드를 이름 없이, 짧게 표현하는 문법입니다. 클래스 없이, 객체 생성 없이, 동작(로직)만 전달하는 방법입니다.

**람다 표현식의 특징:**
- 함수형 인터페이스에서만 사용 가능
- 코드 간결성과 가독성 향상
- 함수형 프로그래밍 스타일 지원

> [!tip] 핵심 포인트
> 람다 표현식은 함수형 인터페이스의 추상 메서드를 간결하게 구현하는 문법입니다. 익명 클래스의 장점을 유지하면서 코드를 훨씬 간결하게 작성할 수 있습니다.

---

#### 2. 익명 클래스

##### 2.1 익명 클래스란?

**익명 클래스(Anonymous Class)**는 이름 없는 클래스를 즉석에서 정의하고, 동시에 객체를 생성하는 문법입니다.

**익명 클래스가 나오기 전 방식:**

```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("실행");
    }
}

Runnable r = new MyRunnable();
```

**문제점:**
- `run()`이라는 추상 메서드를 가진 인터페이스조차 사용하기 위해 클래스를 만들어야 함
- 클래스 파일이 증가하고, 일회용 클래스여도 따로 만들어야 함
- 코드가 길고 반복적

**익명 클래스를 사용하면:**

```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("실행");
    }
};
```

**장점:**
- 클래스 이름 없이 사용 시점에 바로 정의
- 한 번 쓰고 버리는 구현이 가능
- 별도의 클래스 파일 생성 불필요

##### 2.2 익명 클래스의 한계

**메서드 여러 개를 구현하는 경우:**

```java
interface MyInterface {
    void a();
    void b();
}

MyInterface obj = new MyInterface() {
    @Override
    public void a() {
        System.out.println("a");
    }

    @Override
    public void b() {
        System.out.println("b");
    }
};
```

**특징:**
- 익명 클래스는 메서드 개수 제한 없음, 전부 구현하기만 하면 됨
- 단, 핵심 로직에 비해 문법이 과하고 반복해서 쓰기 버거움
- 특히! `Runnable`처럼 구현해야 할 메서드가 하나일 때 더 불편함

---

#### 3. 함수형 인터페이스

**함수형 인터페이스(Functional Interface)**는 추상 메서드가 딱 1개인 인터페이스입니다.

**함수형 인터페이스 예시:**

```java
@FunctionalInterface
public interface Runnable {
    void run();
}
```

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

**특징:**
- 추상 메서드가 1개인 것을 말함
- `default` / `static` 메서드는 여러 개 있어도 무관
- `@FunctionalInterface` 어노테이션으로 명시 (선택적이지만 권장)

**함수형 인터페이스가 아닌 예시:**

```java
interface MyInterface {
    void a();
    void b();  // 추상 메서드가 2개 → 함수형 인터페이스 아님
}
```

> [!important] 핵심 원리
> 함수형 인터페이스는 추상 메서드가 하나뿐이기 때문에, 람다 표현식으로 어떤 메서드를 구현하는지 자동으로 추론할 수 있습니다.

---

#### 4. 람다 표현식이 왜 나왔을까?

익명 클래스는 유연하지만 코드가 길고, 함수형 인터페이스는 메서드가 하나뿐이기 때문에 이를 더 간결하게 표현할 방법이 필요해졌습니다.

**람다 표현식의 목적:**
- 메서드를 이름 없이, 짧게 표현하는 문법
- 클래스 없이, 객체 생성 없이, 동작(로직)만 전달하는 방법

**람다 기본 문법:**

```java
(매개변수) -> { 실행문 }
```

**가장 단순한 형태:**

```java
() -> System.out.println("실행");
```

- `()`: 매개변수
- `->`: "이렇게 실행해라"
- `{}`: 실행할 코드 (단일 문장일 경우 생략 가능)

---

#### 5. 람다 표현식 문법

##### 5.1 기본 문법

**Before (익명 클래스):**

```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("실행");
    }
};
```

**After (람다):**

```java
Runnable r = () -> {
    System.out.println("실행");
};
```

- **`run()` 구현**이라는 게 자동으로 결정됨
- 함수형 인터페이스이므로 어떤 메서드를 구현하는지 추론 가능

##### 5.2 매개변수가 있는 경우

**인터페이스 정의:**

```java
interface MyFunction {
    int add(int a, int b);
}
```

**람다 구현:**

```java
MyFunction f = (a, b) -> {
    return a + b;
};
```

**더 줄이면 (단일 표현식):**

```java
MyFunction f = (a, b) -> a + b;
```

- 단일 표현식일 경우 `return` 키워드와 중괄호 생략 가능

##### 5.3 문법 요약

| 상황 | 문법 | 예시 |
| --- | --- | --- |
| 매개변수 없음 | `() -> { }` | `() -> System.out.println("Hello")` |
| 매개변수 1개 | `(a) -> { }` 또는 `a -> { }` | `x -> x * 2` |
| 매개변수 여러 개 | `(a, b) -> { }` | `(a, b) -> a + b` |
| 단일 표현식 | `() -> 값` | `() -> 42` |
| 여러 문장 | `() -> { 문장1; 문장2; }` | `() -> { System.out.println("a"); System.out.println("b"); }` |

---

#### 6. 람다는 아무 데서나 못 쓴다

**중요한 제약사항:**

- ✅ **함수형 인터페이스에서만 사용 가능**
- ✅ 추상 메서드가 딱 1개인 인터페이스에서만 사용 가능
- ✅ 그래서 람다는 "어떤 메서드를 구현하는지" 자동으로 추론 가능

**람다를 사용할 수 없는 경우:**

```java
interface MyInterface {
    void a();
    void b();  // 추상 메서드가 2개 → 람다 사용 불가
}

// 컴파일 에러!
MyInterface obj = () -> System.out.println("a");  // 어떤 메서드를 구현하는지 모호함
```

**람다를 사용할 수 있는 경우:**

```java
@FunctionalInterface
interface MyFunction {
    int calculate(int x);  // 추상 메서드 1개
}

MyFunction f = x -> x * 2;  // calculate 메서드 구현
```

---

#### 7. 자주 쓰는 함수형 인터페이스

##### 7.1 Runnable

**인터페이스 정의:**

```java
@FunctionalInterface
public interface Runnable {
    void run();
}
```

**사용 예시:**

```java
// 익명 클래스
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("실행");
    }
};

// 람다
Runnable r2 = () -> System.out.println("실행");

// 스레드에서 사용
Thread thread = new Thread(() -> System.out.println("스레드 실행"));
thread.start();
```

##### 7.2 Comparator

**인터페이스 정의:**

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

**사용 예시:**

```java
List<String> list = List.of("Java", "Spring", "JPA");

// 익명 클래스
list.sort(new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.compareTo(b);
    }
});

// 람다
list.sort((a, b) -> a.compareTo(b));

// 메서드 참조 (더 간결)
list.sort(String::compareTo);
```

##### 7.3 Consumer

**인터페이스 정의:**

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}
```

**사용 예시:**

```java
List<String> list = List.of("Java", "Spring", "JPA");

// forEach와 함께 사용
list.forEach(s -> System.out.println(s));

// 메서드 참조
list.forEach(System.out::println);
```

##### 7.4 Function

**인터페이스 정의:**

```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```

**사용 예시:**

```java
Function<String, Integer> lengthFunction = s -> s.length();
Integer length = lengthFunction.apply("Hello");  // 5

// Stream API와 함께 사용
List<String> words = List.of("Java", "Spring", "JPA");
List<Integer> lengths = words.stream()
    .map(s -> s.length())
    .collect(Collectors.toList());
```

##### 7.5 Predicate

**인터페이스 정의:**

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
```

**사용 예시:**

```java
Predicate<String> isEmpty = s -> s.isEmpty();
boolean result = isEmpty.test("");  // true

// Stream API와 함께 사용
List<String> words = List.of("Java", "", "Spring", "", "JPA");
List<String> nonEmpty = words.stream()
    .filter(s -> !s.isEmpty())
    .collect(Collectors.toList());
```

##### 7.6 Supplier

**인터페이스 정의:**

```java
@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```

**사용 예시:**

```java
Supplier<String> supplier = () -> "Hello World";
String value = supplier.get();  // "Hello World"

// Optional과 함께 사용
Optional<String> optional = Optional.empty();
String result = optional.orElseGet(() -> "기본값");
```

---

#### 장점/단점

**장점:**
- 코드 간결성과 가독성 향상
- 함수형 프로그래밍 스타일 지원
- 스트림 API와 함께 사용 시 강력한 기능
- 병렬 처리 지원 (병렬 스트림)
- 메서드 참조와 함께 사용 시 더욱 간결

**단점:**
- 함수형 인터페이스에서만 사용 가능 (제약사항)
- 디버깅이 어려울 수 있음 (스택 트레이스)
- 과도한 사용 시 가독성 저하 가능
- 람다 내부에서 외부 변수 수정 제한 (effectively final)

---

#### 필요 조건

- **Java 8 이상**: 람다 표현식은 Java 8에서 도입
- **함수형 인터페이스**: 추상 메서드가 1개인 인터페이스
- **함수형 프로그래밍 이해**: 함수를 값으로 다루는 개념
- **스트림 API 이해**: 람다와 함께 사용되는 주요 API

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 람다 표현식 사용 시 확인사항

- [ ] 함수형 인터페이스인지 확인했는가?
- [ ] 람다 내부에서 외부 변수를 수정하려고 하지 않는가? (effectively final)
- [ ] 코드가 너무 복잡하지 않은가? (복잡하면 메서드로 분리)
- [ ] 메서드 참조를 사용할 수 있는지 확인했는가?

#### 실무 활용 예시

**1. 컬렉션 순회**

```java
List<String> list = List.of("Java", "Spring", "JPA");

// 전통적인 방식
for (String item : list) {
    System.out.println(item);
}

// 람다 방식
list.forEach(item -> System.out.println(item));

// 메서드 참조
list.forEach(System.out::println);
```

**2. 정렬**

```java
List<String> list = new ArrayList<>(List.of("Java", "Spring", "JPA"));

// 전통적인 방식
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.compareTo(b);
    }
});

// 람다 방식
list.sort((a, b) -> a.compareTo(b));

// 메서드 참조
list.sort(String::compareTo);
```

**3. 필터링**

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// 짝수만 필터링
List<Integer> evens = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());
```

**4. 변환**

```java
List<String> words = List.of("Java", "Spring", "JPA");

// 대문자로 변환
List<String> upper = words.stream()
    .map(s -> s.toUpperCase())
    .collect(Collectors.toList());

// 메서드 참조
List<String> upper2 = words.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

**5. 조건 검사**

```java
List<String> words = List.of("Java", "Spring", "JPA");

// 모든 문자열이 비어있지 않은지 확인
boolean allNonEmpty = words.stream()
    .allMatch(s -> !s.isEmpty());

// 하나라도 "Java"를 포함하는지 확인
boolean hasJava = words.stream()
    .anyMatch(s -> s.contains("Java"));
```

#### 주의사항

**1. Effectively Final 변수만 사용 가능**

```java
int count = 0;
List<String> list = List.of("Java", "Spring", "JPA");

// 컴파일 에러!
list.forEach(s -> count++);  // 외부 변수 수정 불가

// 해결 방법: AtomicInteger 사용
AtomicInteger counter = new AtomicInteger(0);
list.forEach(s -> counter.incrementAndGet());
```

**2. 복잡한 로직은 메서드로 분리**

```java
// 나쁜 예: 람다가 너무 복잡
list.forEach(s -> {
    if (s != null && !s.isEmpty()) {
        String upper = s.toUpperCase();
        System.out.println(upper);
        // ... 더 많은 로직
    }
});

// 좋은 예: 메서드로 분리
list.forEach(this::processString);

private void processString(String s) {
    if (s != null && !s.isEmpty()) {
        String upper = s.toUpperCase();
        System.out.println(upper);
    }
}
```

**3. 메서드 참조 활용**

```java
List<String> list = List.of("Java", "Spring", "JPA");

// 람다
list.forEach(s -> System.out.println(s));

// 메서드 참조 (더 간결)
list.forEach(System.out::println);
```

---

### 설정 시 반드시 고려해야 할 파라미터

- **Java 버전**: 람다 표현식은 Java 8 이상 필요
- **함수형 인터페이스 확인**: `@FunctionalInterface` 어노테이션으로 명시
- **외부 변수 사용**: effectively final 변수만 사용 가능
- **성능 고려**: 람다 자체는 성능 오버헤드가 거의 없지만, 스트림의 경우 상황에 따라 성능 차이 발생 가능

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "람다는 모든 곳에서 사용할 수 있다"는 생각은 잘못되었습니다. 람다는 함수형 인터페이스(추상 메서드가 1개인 인터페이스)에서만 사용 가능합니다. 또한 "람다를 사용하면 항상 성능이 좋아진다"는 생각도 잘못되었습니다. 람다 자체는 성능 오버헤드가 거의 없지만, 스트림의 경우 상황에 따라 전통적인 반복문보다 느릴 수 있습니다.
> 
> "람다 내부에서 외부 변수를 자유롭게 수정할 수 있다"는 생각도 잘못되었습니다. 람다 내부에서 사용하는 외부 변수는 effectively final이어야 하며, 수정할 수 없습니다. 수정이 필요하면 AtomicInteger 같은 클래스를 사용해야 합니다.

#### 람다 표현식 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **코드 중복**: 익명 클래스를 반복해서 작성하여 코드가 길어짐
> - **가독성 저하**: 불필요하게 긴 코드로 인한 가독성 저하
> - **유지보수 어려움**: 반복적인 패턴을 인식하지 못하여 유지보수 어려움
> - **현대적 Java 기능 미활용**: 스트림 API, Optional 등과 함께 사용할 수 있는 강력한 기능을 활용하지 못함
> - **함수형 프로그래밍 이해 부족**: 함수를 값으로 다루는 개념을 이해하지 못함

---

## 요약

- **람다 표현식의 정의**: 메서드를 이름 없이, 짧게 표현하는 문법으로 클래스 없이, 객체 생성 없이, 동작(로직)만 전달하는 방법입니다.

- **익명 클래스의 한계**: 일회용 구현을 위해 클래스를 만들어야 하며, 특히 함수형 인터페이스처럼 메서드가 하나뿐인 경우 코드가 길고 반복적입니다.

- **함수형 인터페이스**: 추상 메서드가 딱 1개인 인터페이스로, 람다 표현식은 함수형 인터페이스에서만 사용 가능합니다.

- **람다 표현식 문법**: `(매개변수) -> { 실행문 }` 형태로, 단일 표현식일 경우 중괄호와 return 키워드를 생략할 수 있습니다.

- **자주 쓰는 함수형 인터페이스**: Runnable, Comparator, Consumer, Function, Predicate, Supplier 등이 있으며, 각각 특정 용도에 맞게 설계되었습니다.

- 람다 표현식은 코드 간결성과 가독성을 크게 향상시키며, 함수형 프로그래밍 스타일을 Java에 도입합니다.
- 스트림 API, Optional 등과 함께 사용하면 현대적인 Java 개발이 가능합니다.
- 함수형 인터페이스에서만 사용 가능하며, effectively final 변수만 사용할 수 있다는 제약사항을 이해해야 합니다.

---

### 예상 꼬리질문정리

#### 1. 람다 표현식과 익명 클래스의 차이는?

- **코드 간결성**: 람다는 훨씬 간결한 문법 제공
- **사용 범위**: 람다는 함수형 인터페이스에서만 사용 가능, 익명 클래스는 모든 인터페이스/클래스에서 사용 가능
- **성능**: 람다는 거의 성능 오버헤드 없음, 익명 클래스는 별도의 클래스 파일 생성
- **외부 변수 접근**: 둘 다 effectively final 변수만 접근 가능

#### 2. 함수형 인터페이스란 무엇인가요?

- **정의**: 추상 메서드가 딱 1개인 인터페이스
- **특징**: `default` / `static` 메서드는 여러 개 있어도 무관
- **예시**: Runnable, Comparator, Consumer, Function, Predicate 등
- **목적**: 람다 표현식으로 어떤 메서드를 구현하는지 자동으로 추론 가능

#### 3. 람다 표현식은 어디서만 사용할 수 있나요?

- **함수형 인터페이스에서만 사용 가능**: 추상 메서드가 1개인 인터페이스
- **변수에 할당**: `Runnable r = () -> System.out.println("Hello");`
- **메서드 매개변수**: `list.forEach(s -> System.out.println(s));`
- **반환값**: `return () -> System.out.println("Hello");`

#### 4. 람다 내부에서 외부 변수를 수정할 수 있나요?

- **제한사항**: effectively final 변수만 사용 가능
- **수정 불가**: 람다 내부에서 외부 변수를 수정할 수 없음
- **해결 방법**: AtomicInteger, 배열 등을 사용하여 우회 가능
- **이유**: 스레드 안전성과 예측 가능한 동작 보장

#### 5. 메서드 참조란 무엇인가요?

- **정의**: 람다 표현식을 더 간결하게 표현하는 방법
- **종류**: 
  - 정적 메서드 참조: `String::valueOf`
  - 인스턴스 메서드 참조: `System.out::println`
  - 생성자 참조: `ArrayList::new`
- **장점**: 람다보다 더 간결하고 가독성 향상

#### 6. 람다와 스트림 API의 관계는?

- **스트림 API**: 컬렉션을 함수형 스타일로 처리하는 API
- **람다 활용**: 스트림의 연산(map, filter, reduce 등)에 람다를 전달
- **예시**: `list.stream().filter(x -> x > 10).map(x -> x * 2).collect(Collectors.toList())`
- **장점**: 선언적 프로그래밍, 병렬 처리 지원

#### 7. 람다 표현식의 성능은?

- **람다 자체**: 거의 성능 오버헤드 없음 (invokedynamic 사용)
- **스트림**: 상황에 따라 전통적인 반복문보다 느릴 수 있음
- **병렬 스트림**: 대용량 데이터 처리 시 성능 향상 가능
- **권장**: 성능이 중요한 경우 벤치마크 테스트 수행

#### 8. 람다를 사용하지 않아도 되는 경우는?

- **복잡한 로직**: 여러 줄의 복잡한 로직은 메서드로 분리하는 것이 좋음
- **재사용 가능한 로직**: 여러 곳에서 사용하는 로직은 메서드로 분리
- **가독성**: 람다가 오히려 가독성을 해치는 경우 전통적인 방식 사용

#### 9. @FunctionalInterface 어노테이션의 역할은?

- **목적**: 함수형 인터페이스임을 명시적으로 표시
- **효과**: 컴파일러가 추상 메서드가 1개인지 검증
- **선택사항**: 없어도 동작하지만 명시하는 것이 좋음
- **문서화**: 다른 개발자에게 의도를 명확히 전달

#### 10. 람다와 클로저의 관계는?

- **클로저**: 외부 변수를 캡처하여 사용하는 함수
- **람다**: Java의 람다는 클로저의 특성을 가짐 (외부 변수 캡처)
- **제한사항**: effectively final 변수만 캡처 가능
- **차이점**: Java는 순수 클로저가 아닌 제한된 클로저

#### 11. 람다 표현식의 타입 추론은 어떻게 동작하나요?

- **컨텍스트 기반 추론**: 변수 타입이나 메서드 시그니처로부터 타입 추론
- **명시적 타입 지정**: 필요 시 명시적으로 타입 지정 가능 `(String s) -> s.length()`
- **제네릭과 함께**: 제네릭 타입도 자동으로 추론
- **예시**: `Function<String, Integer> f = s -> s.length();`

#### 12. 람다를 사용한 예외 처리는?

- **체크 예외**: 람다 내부에서 체크 예외를 던지려면 try-catch로 처리하거나 래핑 필요
- **런타임 예외**: 언체크 예외는 그대로 던질 수 있음
- **예시**: `list.forEach(s -> { try { process(s); } catch (Exception e) { ... } });`
- **대안**: 예외를 던지는 함수형 인터페이스 사용 (커스텀)

#### 13. 람다와 병렬 처리의 관계는?

- **병렬 스트림**: `parallelStream()`을 사용하여 병렬 처리 가능
- **주의사항**: 스레드 안전성 고려 필요, 상태 공유 시 문제 발생 가능
- **적용 시기**: 대용량 데이터, CPU 집약적 작업에 적합
- **예시**: `list.parallelStream().filter(x -> x > 10).collect(Collectors.toList())`

#### 14. 람다 표현식의 디버깅은?

- **스택 트레이스**: 람다는 익명 클래스와 달리 별도 클래스 파일 생성하지 않음
- **디버깅 어려움**: 스택 트레이스가 복잡할 수 있음
- **해결 방법**: 복잡한 로직은 메서드로 분리하여 디버깅 용이하게
- **IDE 지원**: 최신 IDE는 람다 디버깅을 잘 지원

#### 15. 람다를 사용한 디자인 패턴 적용은?

- **전략 패턴**: 함수형 인터페이스로 전략을 람다로 전달
- **템플릿 메서드 패턴**: 람다로 훅 메서드 구현
- **옵저버 패턴**: Consumer로 옵저버 구현
- **장점**: 코드 간결성과 유연성 향상

---

## 참고 자료

- [Oracle Java Tutorials - Lambda Expressions](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) - Oracle 공식 람다 표현식 튜토리얼
- [Baeldung - Java Lambda Expressions](https://www.baeldung.com/java-8-lambda-expressions-tips) - 람다 표현식 가이드
- [Java 8 in Action](https://www.manning.com/books/java-8-in-action) - 함수형 프로그래밍과 람다 관련 서적

---

## 관련 노트

- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - 람다와 객체지향의 관계
- **[[자바 다형성 (Polymorphism)|다형성]]** - 함수형 인터페이스와 다형성
- **[[JavaScript 함수|함수]]** - 다른 언어의 함수형 프로그래밍과 비교

---

**태그:** #java #lambda #함수형프로그래밍 #익명클래스 #면접 #개념정리
