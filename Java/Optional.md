#java #optional #null-safety #npe #면접 #개념정리

**관련 개념:** [[람다 표현식 (Lambda Expression)|람다 표현식]] [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]

> [!note] 이어서 읽기
> Optional을 이해하려면 **[[람다 표현식 (Lambda Expression)|람다 표현식]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

Optional이 무엇인지, 왜 사용하는지, 그리고 NullPointerException을 방지하는 방법을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- Java에서 null 체크를 하지 않으면 NullPointerException이 발생하며, 이는 런타임 에러로 애플리케이션을 중단시킵니다. 전통적인 null 체크 방식(`if (obj != null)`)은 코드가 길어지고 가독성이 떨어지며, null 체크를 누락하기 쉽습니다.

- Optional은 **NullPointerException 방지**를 위한 자바 8 기능으로, "값이 있을 수도, 없을 수도 있다"는 것을 **명시적으로 표현**합니다. 이를 통해 코드에서 null 체크를 깔끔하게 처리하고, NPE를 방지하여 안전한 코드를 작성할 수 있으며, "값이 비어 있음"을 명확하게 표현할 수 있습니다.

- Optional의 생성 방법, 주요 메서드들의 사용법, 실무에서 자주 사용하는 패턴, 그리고 주의사항을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. Optional이란?

**Optional**은 **NullPointerException 방지**를 위한 자바 8 기능입니다.

**Optional의 목적:**
- "값이 있을 수도, 없을 수도 있다"는 것을 **명시적으로 표현**
- null 체크를 깔끔하게 처리
- NPE 방지 → **안전한 코드 작성 가능**

**기본 사용법:**

```java
Optional<String> name = Optional.of("지원");
Optional<String> empty = Optional.empty();
```

> [!tip] 핵심 포인트
> Optional은 null을 직접 다루지 않고, 값의 존재 여부를 명시적으로 표현하여 안전한 코드를 작성할 수 있게 해줍니다.

---

#### 2. 왜 써야 하나? (장점)

**전통적인 방식의 문제점:**

```java
String name = getUserName();
if (name != null) {
    System.out.println(name.length());
} else {
    System.out.println("이름이 없습니다.");
}
```

**Optional을 사용하면:**

```java
Optional<String> name = getUserName();
name.ifPresentOrElse(
    n -> System.out.println(n.length()),
    () -> System.out.println("이름이 없습니다.")
);
```

**장점:**
- ✅ 코드에서 **null 체크를 깔끔하게 처리**
- ✅ NPE 방지 → **안전한 코드 작성 가능**
- ✅ "값이 비어 있음"을 명확하게 표현
- ✅ 함수형 스타일로 처리 가능

---

#### 3. Optional 생성 방법

##### 3.1 of(value)

**값으로 Optional 생성 (null 안됨):**

```java
Optional<String> name1 = Optional.of("지원");  // OK
// Optional<String> name2 = Optional.of(null); // NullPointerException 발생
```

**특징:**
- null이 아닌 값만 허용
- null을 전달하면 즉시 `NullPointerException` 발생

##### 3.2 ofNullable(value)

**값이 null이어도 생성 (권장):**

```java
Optional<String> name = Optional.ofNullable("지원");  // 값 있음
Optional<String> emptyName = Optional.ofNullable(null);  // 빈 Optional
```

**특징:**
- null을 전달해도 안전하게 빈 Optional 생성
- **가장 많이 사용하는 방법**

##### 3.3 empty()

**비어있는 Optional 생성:**

```java
Optional<String> empty = Optional.empty();
```

**특징:**
- 명시적으로 빈 Optional 생성
- `ofNullable(null)`과 동일

---

#### 4. 주요 메서드

##### 4.1 isPresent()

**값이 있으면 true:**

```java
Optional<String> name = Optional.of("지원");
System.out.println(name.isPresent());      // true

Optional<String> empty = Optional.empty();
System.out.println(empty.isPresent());     // false
```

##### 4.2 ifPresent(Consumer)

**값이 있으면 실행:**

```java
Optional<String> name = Optional.of("지원");
name.ifPresent(n -> System.out.println("이름: " + n));  // 출력: 이름: 지원

Optional<String> empty = Optional.empty();
empty.ifPresent(n -> System.out.println("이름: " + n));  // 실행되지 않음
```

##### 4.3 get()

**값 가져오기 (값 없으면 에러):**

```java
Optional<String> name = Optional.of("지원");
String value = name.get();                  // "지원"

Optional<String> empty = Optional.empty();
// String value2 = empty.get();              // NoSuchElementException 발생
```

> [!warning] 주의
> `get()`은 값이 없으면 예외를 발생시키므로, **가능한 한 사용을 지양**하고 `orElse()` 또는 `orElseGet()`을 사용하는 것이 좋습니다.

##### 4.4 orElse(value)

**값 없으면 기본값 리턴:**

```java
Optional<String> name = Optional.of("지원");
String result1 = name.orElse("손님");        // "지원"

Optional<String> empty = Optional.empty();
String result2 = empty.orElse("손님");       // "손님"
```

**특징:**
- 값이 없을 때 기본값 반환
- 기본값이 항상 계산됨 (성능 고려 필요)

##### 4.5 orElseGet(Supplier)

**값 없으면 함수 실행해 기본값:**

```java
Optional<String> name = Optional.of("지원");
String result3 = name.orElseGet(() -> "새 사용자");    // "지원"

Optional<String> empty = Optional.empty();
String result4 = empty.orElseGet(() -> "새 사용자");   // "새 사용자"
```

**특징:**
- 값이 없을 때만 Supplier 실행
- `orElse()`보다 성능상 유리 (지연 평가)

**orElse vs orElseGet:**

```java
// orElse: 항상 실행됨
String result = optional.orElse(expensiveMethod());  // expensiveMethod() 항상 실행

// orElseGet: 값이 없을 때만 실행
String result = optional.orElseGet(() -> expensiveMethod());  // 값이 있을 때는 실행 안 됨
```

##### 4.6 orElseThrow()

**값 없으면 예외 발생:**

```java
Optional<String> name = Optional.of("지원");
String result5 = name.orElseThrow(() -> new IllegalArgumentException("값 없음")); // "지원"

Optional<String> empty = Optional.empty();
// String result6 = empty.orElseThrow(() -> new IllegalArgumentException("값 없음")); // 예외 발생
```

**특징:**
- 값이 없을 때 커스텀 예외 발생
- Java 10부터 `orElseThrow()`만으로도 가능 (기본 `NoSuchElementException`)

##### 4.7 map()과 flatMap()

**값 변환:**

```java
Optional<String> name = Optional.of("지원");
Optional<Integer> length = name.map(String::length);  // Optional.of(2)

Optional<String> empty = Optional.empty();
Optional<Integer> emptyLength = empty.map(String::length);  // Optional.empty()
```

**flatMap 사용:**

```java
Optional<String> name = Optional.of("지원");
Optional<Optional<String>> nested = name.map(s -> Optional.of(s.toUpperCase()));
Optional<String> flattened = name.flatMap(s -> Optional.of(s.toUpperCase()));
```

---

#### 5. 실무 활용 예시

##### 5.1 메서드 반환값으로 사용

```java
public Optional<User> findUserById(Long id) {
    User user = userRepository.findById(id);
    return Optional.ofNullable(user);
}

// 사용
Optional<User> user = findUserById(1L);
user.ifPresent(u -> System.out.println(u.getName()));
```

##### 5.2 null 체크 대체

```java
// 전통적인 방식
String name = getUserName();
if (name != null) {
    System.out.println(name.toUpperCase());
}

// Optional 사용
Optional.ofNullable(getUserName())
    .ifPresent(n -> System.out.println(n.toUpperCase()));
```

##### 5.3 기본값 제공

```java
String name = Optional.ofNullable(getUserName())
    .orElse("손님");

String name2 = Optional.ofNullable(getUserName())
    .orElseGet(() -> getDefaultName());
```

##### 5.4 예외 처리

```java
User user = Optional.ofNullable(findUserById(1L))
    .orElseThrow(() -> new UserNotFoundException("사용자를 찾을 수 없습니다."));
```

##### 5.5 Stream과 함께 사용

```java
List<String> names = users.stream()
    .map(User::getName)
    .filter(Optional::isPresent)
    .map(Optional::get)
    .collect(Collectors.toList());

// 더 나은 방법
List<String> names = users.stream()
    .map(User::getName)
    .flatMap(Optional::stream)  // Java 9+
    .collect(Collectors.toList());
```

---

#### 장점/단점

**장점:**
- null 체크를 깔끔하게 처리
- NPE 방지
- "값이 비어 있음"을 명확하게 표현
- 함수형 스타일로 처리 가능
- 메서드 체이닝으로 가독성 향상

**단점:**
- 성능 오버헤드 (약간의 메모리 사용)
- 과도한 사용 시 코드 복잡도 증가
- 엔티티 필드에는 사용하지 않는 것이 좋음

---

#### 필요 조건

- **Java 8 이상**: Optional은 Java 8에서 도입
- **람다 표현식 이해**: Optional은 람다와 함께 사용
- **null 안전성 이해**: null 처리의 중요성 이해

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### Optional 사용 시 확인사항

- [ ] `get()` 대신 `orElse()` 또는 `orElseGet()`을 사용했는가?
- [ ] 엔티티 필드에는 Optional을 사용하지 않았는가?
- [ ] null 체크가 필요한 곳에 Optional을 사용했는가?
- [ ] 과도하게 중첩된 Optional을 사용하지 않았는가?

#### 실무 활용 예시

**1. Repository 메서드 반환값**

```java
// 권장: Optional 반환
public Optional<Member> findById(Long id) {
    return Optional.ofNullable(memberRepository.findById(id));
}

// 사용
Optional<Member> member = memberService.findById(1L);
member.ifPresent(m -> System.out.println(m.getName()));
```

**2. null 체크 대체**

```java
// 전통적인 방식
if (user != null && user.getName() != null) {
    System.out.println(user.getName().toUpperCase());
}

// Optional 사용
Optional.ofNullable(user)
    .map(User::getName)
    .ifPresent(name -> System.out.println(name.toUpperCase()));
```

**3. 기본값 제공**

```java
String userName = Optional.ofNullable(user)
    .map(User::getName)
    .orElse("익명 사용자");
```

#### 주의사항

**1. Optional.get()은 지양!**

```java
// 나쁜 예
Optional<String> name = Optional.ofNullable(getName());
String value = name.get();  // 값이 없으면 예외 발생

// 좋은 예
String value = Optional.ofNullable(getName())
    .orElse("기본값");
```

**2. 엔티티 필드에는 사용 ❌**

```java
// 나쁜 예
public class Member {
    private Optional<String> name;  // ❌ 사용하지 않음
}

// 좋은 예
public class Member {
    private String name;  // ✅ 일반 필드 사용
}

// 반환값으로만 사용
public Optional<Member> findById(Long id) {
    return Optional.ofNullable(memberRepository.findById(id));
}
```

**3. null을 Optional로 감싸지 말 것**

```java
// 나쁜 예
return Optional.ofNullable(null);  // 의미 없음

// 좋은 예
return Optional.empty();
```

**4. 중첩된 Optional 피하기**

```java
// 나쁜 예
Optional<Optional<String>> nested = Optional.of(Optional.of("value"));

// 좋은 예
Optional<String> flat = Optional.of("value");
```

---

### 설정 시 반드시 고려해야 할 파라미터

- **성능 고려**: `orElse()` vs `orElseGet()` 선택 (기본값 계산 비용)
- **예외 처리**: `orElseThrow()`로 명확한 예외 메시지 제공
- **엔티티 필드**: Optional은 반환값으로만 사용, 엔티티 필드에는 사용하지 않음

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "Optional을 사용하면 null이 완전히 사라진다"는 생각은 잘못되었습니다. Optional은 null을 감싸는 래퍼일 뿐이며, 여전히 null을 다룰 수 있습니다. 또한 "모든 곳에 Optional을 사용해야 한다"는 생각도 잘못되었습니다. Optional은 반환값으로 사용하는 것이 좋으며, 엔티티 필드에는 사용하지 않는 것이 권장됩니다.

#### Optional 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **NullPointerException**: null 체크 누락으로 인한 런타임 에러
> - **코드 중복**: 반복적인 null 체크 코드
> - **가독성 저하**: 중첩된 if 문으로 인한 가독성 저하
> - **의도 불명확**: null이 반환될 수 있는지 명확하지 않음
> - **안전하지 않은 코드**: null 체크를 누락하기 쉬움

---

## 요약

- **Optional의 정의**: NullPointerException 방지를 위한 자바 8 기능으로, "값이 있을 수도, 없을 수도 있다"는 것을 명시적으로 표현합니다.

- **주요 메서드**: 
  - 생성: `of()`, `ofNullable()`, `empty()`
  - 확인: `isPresent()`, `ifPresent()`
  - 값 가져오기: `get()` (지양), `orElse()`, `orElseGet()`, `orElseThrow()`
  - 변환: `map()`, `flatMap()`

- **사용 원칙**: 
  - 반환값으로 사용 (권장)
  - 엔티티 필드에는 사용하지 않음
  - `get()` 대신 `orElse()` 또는 `orElseGet()` 사용

- Optional은 null을 안전하게 다루기 위한 도구로, 코드의 가독성과 안전성을 크게 향상시킵니다.
- 함수형 스타일로 null 체크를 처리할 수 있으며, 메서드 체이닝으로 깔끔한 코드 작성이 가능합니다.
- 적절히 사용하면 NPE를 방지하고 안전한 코드를 작성할 수 있습니다.

---

### 예상 꼬리질문정리

#### 1. Optional과 null의 차이는?

- **null**: 값이 없음을 나타내는 참조
- **Optional**: 값이 있을 수도, 없을 수도 있음을 명시적으로 표현하는 컨테이너
- **차이점**: Optional은 null을 감싸서 안전하게 다룰 수 있게 해줌

#### 2. Optional.get()을 사용하지 않는 이유는?

- **예외 발생**: 값이 없으면 `NoSuchElementException` 발생
- **안전하지 않음**: null 체크 없이 사용하기 어려움
- **대안**: `orElse()`, `orElseGet()`, `orElseThrow()` 사용

#### 3. orElse()와 orElseGet()의 차이는?

- **orElse()**: 기본값이 항상 계산됨 (값이 있어도 계산)
- **orElseGet()**: 값이 없을 때만 Supplier 실행 (지연 평가)
- **선택 기준**: 기본값 계산 비용이 크면 `orElseGet()` 사용

#### 4. Optional을 엔티티 필드에 사용할 수 있나요?

- **권장하지 않음**: JPA 스펙에서 Optional 필드 지원하지 않음
- **대안**: 일반 필드 사용하고, 반환값으로만 Optional 사용
- **이유**: Optional은 반환값으로 사용하는 것이 의도에 맞음

#### 5. Optional의 성능 오버헤드는?

- **메모리**: Optional 객체 자체의 메모리 사용 (약간의 오버헤드)
- **성능**: 대부분의 경우 무시할 수 있는 수준
- **트레이드오프**: 안전성과 가독성 향상이 오버헤드보다 큼

---

## 참고 자료

- [Oracle Java Tutorials - Optional](https://docs.oracle.com/javase/tutorial/java/javaOO/optional.html) - Oracle 공식 Optional 튜토리얼
- [Baeldung - Optional in Java](https://www.baeldung.com/java-optional) - Optional 가이드

---

## 관련 노트

- **[[람다 표현식 (Lambda Expression)|람다 표현식]]** - Optional과 함께 사용하는 람다 표현식
- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - null 안전성과 객체지향 설계

---

**태그:** #java #optional #null-safety #npe #면접 #개념정리
