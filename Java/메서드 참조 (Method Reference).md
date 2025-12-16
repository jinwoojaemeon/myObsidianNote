#java #method-reference #람다 #함수형프로그래밍 #면접 #개념정리

**관련 개념:** [[람다 표현식 (Lambda Expression)|람다 표현식]] [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]

> [!note] 이어서 읽기
> 메서드 참조를 이해하려면 **[[람다 표현식 (Lambda Expression)|람다 표현식]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

메서드 참조(Method Reference)가 무엇인지, 왜 사용하는지, 그리고 람다 표현식을 더 간결하게 만드는 방법을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 람다 표현식에서 단순히 메서드만 호출하는 경우 코드가 길어지고 가독성이 떨어집니다. 특히 `item -> 클래스명.메서드(item)` 같은 패턴이 반복되면 불필요한 코드 중복이 발생합니다.

- 메서드 참조는 람다 표현식을 더 간결하고 명확하게 표현하기 위한 문법으로, `::` 기호를 사용하여 메서드나 생성자를 직접 참조합니다. 이를 통해 코드 가독성을 향상시키고, 의도를 더 명확하게 전달할 수 있으며, 특히 스트림 API와 함께 사용할 때 강력한 효과를 발휘합니다.

- 람다 표현식과 메서드 참조의 차이, 메서드 참조의 4가지 유형, 그리고 실무에서 자주 사용하는 패턴들을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. 메서드 참조란?

**메서드 참조(Method Reference)**는 람다 표현식을 더 간결하고 명확하게 표현하기 위한 문법입니다. `::` 기호를 사용하여 메서드나 생성자를 직접 참조합니다.

**기존 람다:**
```java
list.stream().map(item -> ItemDto.from(item));
```

**메서드 참조:**
```java
list.stream().map(ItemDto::from);
```

> [!tip] 핵심 포인트
> 메서드 참조는 람다 표현식이 단순히 메서드를 호출하는 경우에만 사용할 수 있으며, 코드를 더 간결하고 읽기 쉽게 만들어줍니다.

---

#### 2. 왜 메서드 참조를 사용해야 할까?

| 구분 | 람다 표현식 | 메서드 참조 |
| --- | --- | --- |
| **코드 길이** | 길어짐 | 짧고 간결함 |
| **가독성** | 복잡해질 수 있음 | 명확하고 직관적임 |
| **사용 용도** | 단순히 메서드만 호출할 때 | 메서드 호출만 필요할 때 적합 |

**예시 비교:**

```java
// 람다 표현식
List<MemberDto.Response> members = memberRepository.findAll().stream()
    .map(member -> MemberDto.Response.from(member))
    .collect(Collectors.toList());

// 메서드 참조 (권장)
List<MemberDto.Response> members = memberRepository.findAll().stream()
    .map(MemberDto.Response::from)  // 가장 많이 사용하는 경우
    .collect(Collectors.toList());
```

**결과는 동일하지만, 메서드 참조가 더 깔끔하고 가독성이 높습니다.**

---

#### 3. 메서드 참조 유형별 사용법

메서드 참조는 4가지 유형이 있습니다.

##### 3.1 정적 메서드 참조

**형식:** `ClassName::staticMethod`

**설명:** 클래스의 정적 메서드를 호출합니다.

**예제:**

```java
public class Utils {
    public static String toUpper(String str) {
        return str.toUpperCase();
    }
}

// 사용
List<String> result = list.stream()
    .map(Utils::toUpper)
    .collect(Collectors.toList());

// 람다로 표현하면
List<String> result = list.stream()
    .map(str -> Utils.toUpper(str))
    .collect(Collectors.toList());
```

**실무 예시:**

```java
// DTO 변환
List<MemberDto.Response> members = memberRepository.findAll().stream()
    .map(MemberDto.Response::from)
    .collect(Collectors.toList());
```

##### 3.2 인스턴스 메서드 참조 (특정 객체)

**형식:** `object::instanceMethod`

**설명:** 특정 인스턴스의 메서드를 호출합니다.

**예제:**

```java
public class Printer {
    public void print(String str) {
        System.out.println(str);
    }
}

// 사용
Printer printer = new Printer();
list.forEach(printer::print);

// 람다로 표현하면
list.forEach(str -> printer.print(str));
```

**실무 예시:**

```java
// 로거 사용
Logger logger = LoggerFactory.getLogger(MyClass.class);
errors.forEach(logger::error);
```

##### 3.3 인스턴스 메서드 참조 (임의 객체)

**형식:** `ClassName::instanceMethod`

**설명:** 스트림 요소가 메서드 호출 대상일 때 사용합니다.

**예제:**

```java
List<String> result = list.stream()
    .map(String::toLowerCase)  // 각 요소의 toLowerCase() 호출
    .collect(Collectors.toList());

// 람다로 표현하면
List<String> result = list.stream()
    .map(str -> str.toLowerCase())
    .collect(Collectors.toList());
```

**실무 예시:**

```java
// 문자열 변환
List<String> upper = words.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

// 객체 메서드 호출
List<Integer> lengths = words.stream()
    .map(String::length)
    .collect(Collectors.toList());
```

##### 3.4 생성자 참조

**형식:** `ClassName::new`

**설명:** 생성자를 호출하여 객체를 생성합니다.

**예제:**

```java
// Member 클래스
public class Member {
    private String id;
    
    public Member(String id) {
        this.id = id;
    }
}

// 사용
List<Member> members = ids.stream()
    .map(Member::new)  // new Member(id) 호출
    .collect(Collectors.toList());

// 람다로 표현하면
List<Member> members = ids.stream()
    .map(id -> new Member(id))
    .collect(Collectors.toList());
```

**실무 예시:**

```java
// 컬렉션 생성
List<String> list = Arrays.stream(array)
    .collect(Collectors.toCollection(ArrayList::new));

// Optional 생성
Optional<String> optional = Optional.of("value");
```

---

#### 4. 실무 활용 예시

##### 4.1 DTO 변환 (가장 많이 사용)

```java
// Entity → DTO 변환
List<MemberDto.Response> members = memberRepository.findAll().stream()
    .map(MemberDto.Response::from)  // 정적 메서드 참조
    .collect(Collectors.toList());

// MemberDto.Response 클래스
public class Response {
    public static Response from(Member member) {
        return new Response(
            member.getId(),
            member.getName(),
            member.getEmail()
        );
    }
}
```

##### 4.2 컬렉션 출력

```java
List<String> list = List.of("Java", "Spring", "JPA");

// System.out.println 사용
list.forEach(System.out::println);

// 람다로 표현하면
list.forEach(s -> System.out.println(s));
```

##### 4.3 정렬

```java
List<String> list = new ArrayList<>(List.of("Java", "Spring", "JPA"));

// String의 compareTo 메서드 사용
list.sort(String::compareTo);

// 람다로 표현하면
list.sort((a, b) -> a.compareTo(b));
```

##### 4.4 필터링과 변환 조합

```java
List<String> result = words.stream()
    .filter(String::isEmpty)  // 인스턴스 메서드 참조 (임의 객체)
    .map(String::toUpperCase)  // 인스턴스 메서드 참조 (임의 객체)
    .collect(Collectors.toList());
```

---

#### 장점/단점

**장점:**
- 코드 간결성과 가독성 향상
- 의도 명확화 (메서드 호출임을 명확히 표현)
- 람다보다 더 간결한 문법
- 스트림 API와 함께 사용 시 강력한 효과

**단점:**
- 메서드 호출만 가능 (복잡한 로직은 람다 사용)
- 초보자에게는 문법이 생소할 수 있음
- 디버깅 시 스택 트레이스가 복잡할 수 있음

---

#### 필요 조건

- **Java 8 이상**: 메서드 참조는 Java 8에서 도입
- **람다 표현식 이해**: 메서드 참조는 람다의 특수한 형태
- **함수형 인터페이스**: 메서드 참조도 함수형 인터페이스에서만 사용 가능

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 메서드 참조 사용 시 확인사항

- [ ] 람다가 단순히 메서드를 호출하는 경우인가?
- [ ] 메서드 시그니처가 함수형 인터페이스와 일치하는가?
- [ ] 코드 가독성이 향상되는가?
- [ ] 복잡한 로직이 포함되어 있지 않은가? (있으면 람다 사용)

#### 실무 활용 예시

**1. DTO 변환 (가장 많이 사용)**

```java
// Entity → DTO
List<BoardDto> boards = boardRepository.findAll().stream()
    .map(BoardDto::from)
    .collect(Collectors.toList());

// DTO → Entity
List<Board> entities = dtos.stream()
    .map(Board::fromDto)
    .collect(Collectors.toList());
```

**2. 컬렉션 출력**

```java
// 로그 출력
errors.forEach(logger::error);
warnings.forEach(logger::warn);

// 콘솔 출력
list.forEach(System.out::println);
```

**3. 정렬**

```java
// 기본 정렬
list.sort(String::compareTo);

// 역순 정렬
list.sort(Collections.reverseOrder());
```

**4. 필터링**

```java
// null 제거
List<String> nonNull = list.stream()
    .filter(Objects::nonNull)
    .collect(Collectors.toList());

// 빈 문자열 제거
List<String> nonEmpty = list.stream()
    .filter(s -> !s.isEmpty())
    .collect(Collectors.toList());
```

#### 주의사항

**1. 복잡한 로직은 람다 사용**

```java
// 나쁜 예: 메서드 참조로는 불가능
list.stream()
    .map(item -> {
        // 복잡한 로직
        if (item.getStatus() == Status.ACTIVE) {
            return processActive(item);
        } else {
            return processInactive(item);
        }
    });

// 좋은 예: 람다 사용
list.stream()
    .map(item -> processItem(item));
```

**2. 메서드 시그니처 확인**

```java
// 메서드 시그니처가 일치해야 함
interface MyFunction {
    String apply(Integer i);
}

// String.valueOf(int)는 int를 받지만, Integer도 가능
List<String> result = numbers.stream()
    .map(String::valueOf)  // OK: Integer → String
    .collect(Collectors.toList());
```

---

### 설정 시 반드시 고려해야 할 파라미터

- **메서드 시그니처 일치**: 함수형 인터페이스의 메서드 시그니처와 참조할 메서드의 시그니처가 일치해야 함
- **가독성 우선**: 메서드 참조가 오히려 가독성을 해치는 경우 람다 사용
- **복잡도**: 복잡한 로직이 포함된 경우 메서드로 분리 후 메서드 참조 사용

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "메서드 참조를 항상 사용해야 한다"는 생각은 잘못되었습니다. 메서드 참조는 단순히 메서드를 호출하는 경우에만 적합하며, 복잡한 로직이 포함된 경우 람다를 사용하는 것이 좋습니다. 또한 "메서드 참조가 항상 람다보다 빠르다"는 생각도 잘못되었습니다. 성능 차이는 거의 없으며, 가독성과 간결성이 주요 목적입니다.

#### 메서드 참조 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **코드 중복**: `item -> 클래스명.메서드(item)` 같은 패턴 반복
> - **가독성 저하**: 불필요하게 긴 람다 표현식
> - **의도 불명확**: 단순 메서드 호출임을 명확히 표현하지 못함
> - **유지보수 어려움**: 반복적인 패턴으로 인한 유지보수 어려움

---

## 요약

- **메서드 참조의 정의**: 람다 표현식을 더 간결하고 명확하게 표현하기 위한 문법으로, `::` 기호를 사용하여 메서드나 생성자를 직접 참조합니다.

- **4가지 유형**: 정적 메서드 참조(`ClassName::staticMethod`), 인스턴스 메서드 참조 특정 객체(`object::instanceMethod`), 인스턴스 메서드 참조 임의 객체(`ClassName::instanceMethod`), 생성자 참조(`ClassName::new`)

- **사용 시기**: 람다 표현식이 단순히 메서드를 호출하는 경우에 사용하며, 특히 DTO 변환, 컬렉션 출력, 정렬 등에서 자주 사용됩니다.

- **장점**: 코드 간결성과 가독성 향상, 의도 명확화, 스트림 API와 함께 사용 시 강력한 효과

- 메서드 참조는 람다 표현식의 특수한 형태로, 단순 메서드 호출을 더 간결하게 표현할 수 있게 해줍니다.
- 실무에서 DTO 변환, 컬렉션 출력, 정렬 등에서 가장 많이 사용되며, 코드 가독성을 크게 향상시킵니다.
- 복잡한 로직이 포함된 경우에는 메서드 참조 대신 람다를 사용하는 것이 좋습니다.

---

### 예상 꼬리질문정리

#### 1. 메서드 참조와 람다 표현식의 차이는?

- **메서드 참조**: 단순히 메서드를 호출하는 경우에 사용, `::` 기호 사용
- **람다 표현식**: 복잡한 로직이 포함된 경우에 사용, `->` 기호 사용
- **선택 기준**: 메서드 호출만 필요하면 메서드 참조, 추가 로직이 있으면 람다

#### 2. 메서드 참조의 4가지 유형은?

- **정적 메서드 참조**: `ClassName::staticMethod`
- **인스턴스 메서드 참조 (특정 객체)**: `object::instanceMethod`
- **인스턴스 메서드 참조 (임의 객체)**: `ClassName::instanceMethod`
- **생성자 참조**: `ClassName::new`

#### 3. 메서드 참조를 사용할 수 없는 경우는?

- **복잡한 로직**: 조건문, 반복문 등이 포함된 경우
- **여러 메서드 호출**: 하나의 람다에서 여러 메서드를 호출하는 경우
- **매개변수 변환**: 매개변수를 변환한 후 메서드를 호출하는 경우

#### 4. 생성자 참조는 어떻게 사용하나요?

- **형식**: `ClassName::new`
- **사용 예**: `ids.stream().map(Member::new).collect(Collectors.toList())`
- **조건**: 생성자 시그니처가 함수형 인터페이스와 일치해야 함

#### 5. 메서드 참조의 성능은?

- **성능 차이**: 람다와 메서드 참조의 성능 차이는 거의 없음
- **목적**: 성능 향상이 아닌 가독성과 간결성 향상
- **컴파일**: 둘 다 invokedynamic을 사용하여 동일한 방식으로 컴파일

---

## 참고 자료

- [Oracle Java Tutorials - Method References](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html) - Oracle 공식 메서드 참조 튜토리얼
- [Baeldung - Method References in Java](https://www.baeldung.com/java-method-references) - 메서드 참조 가이드

---

## 관련 노트

- **[[람다 표현식 (Lambda Expression)|람다 표현식]]** - 메서드 참조의 기반이 되는 람다 표현식
- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - 메서드와 객체의 관계

---

**태그:** #java #method-reference #람다 #함수형프로그래밍 #면접 #개념정리
