#java #stream #함수형프로그래밍 #람다 #면접 #개념정리

**관련 개념:** [[람다 표현식 (Lambda Expression)|람다 표현식]] [[메서드 참조 (Method Reference)|메서드 참조]] [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]

> [!note] 이어서 읽기
> Stream API를 이해하려면 **[[람다 표현식 (Lambda Expression)|람다 표현식]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

Stream API가 무엇인지, 왜 사용하는지, 그리고 데이터 처리 방법을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 전통적인 반복문(for, while)을 사용하면 코드가 길고 가독성이 떨어지며, 필터링, 변환, 정렬, 집계 등의 작업을 여러 단계로 나누어 작성해야 합니다. 특히 중첩된 반복문과 복잡한 조건문은 코드를 이해하기 어렵게 만들고 버그 발생 가능성을 높입니다.

- Stream API는 자바 8부터 추가된 데이터 처리 전용 도구로, "데이터 흐름"처럼 생각하고 저장보다는 처리에 초점을 둡니다. 반복문 없이 깔끔한 코드로 필터, 변환, 정렬, 그룹화 등을 한 줄로 처리할 수 있으며, 람다 표현식과 함께 사용하여 함수형 프로그래밍 스타일로 데이터를 처리할 수 있습니다.

- Stream의 생성 방법, 중간 연산과 최종 연산의 차이, 주요 메서드들의 사용법, 그리고 실무에서 자주 사용하는 패턴들을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. Stream이란?

**Stream API**는 자바 8부터 추가된 **데이터 처리 전용 도구**입니다.

**Stream의 특징:**
- "데이터 흐름"처럼 생각하고, 저장보다는 **처리**에 초점
- JavaScript의 배열처럼 처리 가능
- 반복, 필터링, 변환, 정렬, 집계 등을 **간단하게 처리**

> [!tip] 핵심 포인트
> Stream은 데이터를 처리하는 파이프라인을 구성하여 선언적으로 데이터를 다룰 수 있게 해줍니다. 반복문 없이도 복잡한 데이터 처리를 간결하게 작성할 수 있습니다.

---

#### 2. 왜 써야 하나?

**전통적인 방식의 문제점:**

```java
List<String> result = new ArrayList<>();
for (String name : names) {
    if (name.startsWith("김")) {
        String upper = name.toUpperCase();
        result.add(upper);
    }
}
Collections.sort(result);
```

**Stream을 사용하면:**

```java
List<String> result = names.stream()
    .filter(name -> name.startsWith("김"))
    .map(String::toUpperCase)
    .sorted()
    .collect(Collectors.toList());
```

**장점:**
- ✅ 반복문 없이 깔끔한 코드
- ✅ 필터, 변환, 정렬, 그룹화 등을 한 줄로 처리
- ✅ 가독성 향상
- ✅ 병렬 처리 지원

---

#### 3. Stream 처리 순서

Stream은 3단계로 구성됩니다.

##### 3.1 데이터 생성 (스트림 생성)

**주요 방법:**

```java
// 컬렉션에서 생성
List<String> list = List.of("Java", "Spring", "JPA");
Stream<String> stream = list.stream();

// 배열에서 생성
String[] array = {"Java", "Spring", "JPA"};
Stream<String> stream = Arrays.stream(array);

// 직접 생성
Stream<String> stream = Stream.of("Java", "Spring", "JPA");

// 범위 생성
IntStream range = IntStream.range(1, 10);  // 1부터 9까지
```

##### 3.2 중간 연산 (데이터 변형)

중간 연산은 여러 개를 연결할 수 있으며, **지연 평가(Lazy Evaluation)**됩니다.

| 메서드 | 설명 | 예제 |
| --- | --- | --- |
| `filter()` | 조건에 맞는 요소만 선택 | `.filter(n -> n > 10)` |
| `map()` | 요소 변환 | `.map(n -> n * 2)` |
| `flatMap()` | 스트림 펼치기 | `.flatMap(list -> list.stream())` |
| `distinct()` | 중복 제거 | `.distinct()` |
| `sorted()` | 정렬 | `.sorted()` |
| `sorted(Comparator)` | 기준 정렬 | `.sorted((a,b)->b-a)` |
| `limit(n)` | 앞에서 n개만 | `.limit(5)` |
| `skip(n)` | 앞에서 n개 건너뜀 | `.skip(3)` |
| `peek()` | 중간 확인(디버깅) | `.peek(System.out::println)` |

**중간 연산 특징:**
- 여러 개를 연결 가능 (메서드 체이닝)
- 지연 평가: 최종 연산이 호출될 때까지 실행되지 않음
- 새로운 Stream을 반환

##### 3.3 최종 연산 (결과 수집)

최종 연산은 Stream을 소비하여 결과를 반환합니다.

| 메서드 | 설명 | 반환 |
| --- | --- | --- |
| `forEach()` | 요소 소비 | `void` |
| `collect()` | 컬렉션으로 변환 | `List`, `Set`, `Map` |
| `count()` | 개수 | `long` |
| `anyMatch()` | 하나라도 만족? | `boolean` |
| `allMatch()` | 모두 만족? | `boolean` |
| `noneMatch()` | 모두 불만족? | `boolean` |
| `findFirst()` | 첫 요소 | `Optional<T>` |
| `findAny()` | 아무 요소 | `Optional<T>` |
| `reduce()` | 누적 계산 | `Optional<T>` / `T` |
| `min()` | 최소값 | `Optional<T>` |
| `max()` | 최대값 | `Optional<T>` |

**최종 연산 특징:**
- Stream을 소비하여 결과 반환
- 최종 연산 후 Stream은 재사용 불가
- 최종 연산이 호출되어야 중간 연산이 실행됨 (지연 평가)

---

#### 4. Stream 예시 코드

##### 4.1 기본 예시

```java
List<String> names = List.of("김철수", "이영희", "김민수", "박지영");

List<String> result = names.stream()       // 1. 스트림 생성
    .filter(name -> name.startsWith("김"))  // 2. 필터링 (중간 연산)
    .map(String::toUpperCase)              // 2. 변환 (중간 연산)
    .sorted()                              // 2. 정렬 (중간 연산)
    .collect(Collectors.toList());         // 3. 결과 수집 (최종 연산)

// 결과: ["김민수", "김철수"]
```

##### 4.2 필터링

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// 짝수만 필터링
List<Integer> evens = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());

// 5보다 큰 수만 필터링
List<Integer> greaterThan5 = numbers.stream()
    .filter(n -> n > 5)
    .collect(Collectors.toList());
```

##### 4.3 변환 (map)

```java
List<String> words = List.of("Java", "Spring", "JPA");

// 대문자로 변환
List<String> upper = words.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

// 길이로 변환
List<Integer> lengths = words.stream()
    .map(String::length)
    .collect(Collectors.toList());
```

##### 4.4 정렬

```java
List<String> list = List.of("Java", "Spring", "JPA");

// 기본 정렬 (오름차순)
List<String> sorted = list.stream()
    .sorted()
    .collect(Collectors.toList());

// 역순 정렬
List<String> reversed = list.stream()
    .sorted(Collections.reverseOrder())
    .collect(Collectors.toList());

// 커스텀 정렬
List<String> custom = list.stream()
    .sorted((a, b) -> b.length() - a.length())  // 길이 역순
    .collect(Collectors.toList());
```

##### 4.5 집계

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

// 합계
int sum = numbers.stream()
    .reduce(0, (a, b) -> a + b);

// 최대값
Optional<Integer> max = numbers.stream()
    .max(Integer::compareTo);

// 최소값
Optional<Integer> min = numbers.stream()
    .min(Integer::compareTo);

// 개수
long count = numbers.stream()
    .count();
```

##### 4.6 조건 검사

```java
List<String> words = List.of("Java", "Spring", "JPA");

// 하나라도 "Java"를 포함하는지
boolean hasJava = words.stream()
    .anyMatch(s -> s.contains("Java"));

// 모두 비어있지 않은지
boolean allNonEmpty = words.stream()
    .allMatch(s -> !s.isEmpty());

// 모두 "Python"이 아닌지
boolean nonePython = words.stream()
    .noneMatch(s -> s.equals("Python"));
```

---

#### 5. 실무 활용 예시

##### 5.1 Entity → DTO 변환

```java
List<MemberDto.Response> members = memberRepository.findAll().stream()
    .map(MemberDto.Response::from)
    .collect(Collectors.toList());
```

##### 5.2 필터링과 변환 조합

```java
List<BoardDto> activeBoards = boardRepository.findAll().stream()
    .filter(board -> board.getStatus() == Status.ACTIVE)
    .filter(board -> board.getCreatedAt().isAfter(LocalDateTime.now().minusDays(7)))
    .map(BoardDto::from)
    .collect(Collectors.toList());
```

##### 5.3 그룹화

```java
// 상태별로 그룹화
Map<Status, List<Board>> grouped = boards.stream()
    .collect(Collectors.groupingBy(Board::getStatus));

// 카운트
Map<Status, Long> countByStatus = boards.stream()
    .collect(Collectors.groupingBy(Board::getStatus, Collectors.counting()));
```

##### 5.4 중복 제거

```java
List<String> unique = list.stream()
    .distinct()
    .collect(Collectors.toList());
```

---

#### 장점/단점

**장점:**
- 코드 간결성과 가독성 향상
- 선언적 프로그래밍 스타일
- 병렬 처리 지원 (`parallelStream()`)
- 함수형 프로그래밍 스타일
- 지연 평가로 성능 최적화 가능

**단점:**
- 학습 곡선 (새로운 개념)
- 디버깅이 어려울 수 있음
- 단순한 반복문보다 오버헤드가 있을 수 있음
- 과도한 사용 시 가독성 저하 가능

---

#### 필요 조건

- **Java 8 이상**: Stream API는 Java 8에서 도입
- **람다 표현식 이해**: Stream은 람다와 함께 사용
- **함수형 프로그래밍 개념**: 선언적 프로그래밍 스타일 이해

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### Stream 사용 시 확인사항

- [ ] Stream이 적절한 상황인가? (단순 반복은 전통적인 방식이 나을 수 있음)
- [ ] 중간 연산과 최종 연산을 올바르게 구분했는가?
- [ ] 최종 연산이 호출되었는가? (지연 평가)
- [ ] 병렬 처리가 필요한가? (`parallelStream()` 사용)

#### 실무 활용 예시

**1. 복잡한 필터링과 변환**

```java
List<OrderDto> recentOrders = orders.stream()
    .filter(order -> order.getStatus() == OrderStatus.COMPLETED)
    .filter(order -> order.getCreatedAt().isAfter(LocalDateTime.now().minusMonths(1)))
    .map(OrderDto::from)
    .sorted(Comparator.comparing(OrderDto::getCreatedAt).reversed())
    .limit(10)
    .collect(Collectors.toList());
```

**2. 통계 계산**

```java
IntSummaryStatistics stats = numbers.stream()
    .mapToInt(Integer::intValue)
    .summaryStatistics();

System.out.println("합계: " + stats.getSum());
System.out.println("평균: " + stats.getAverage());
System.out.println("최대값: " + stats.getMax());
System.out.println("최소값: " + stats.getMin());
```

**3. 병렬 처리**

```java
List<String> result = largeList.parallelStream()
    .filter(condition)
    .map(transformation)
    .collect(Collectors.toList());
```

#### 주의사항

**1. Stream은 재사용 불가**

```java
Stream<String> stream = list.stream();
stream.filter(s -> s.length() > 5);
stream.map(String::toUpperCase);  // 에러! Stream은 이미 소비됨

// 올바른 방법
List<String> result = list.stream()
    .filter(s -> s.length() > 5)
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

**2. 지연 평가 이해**

```java
// 중간 연산만으로는 실행되지 않음
Stream<String> stream = list.stream()
    .filter(s -> {
        System.out.println("필터링: " + s);  // 출력되지 않음
        return s.length() > 5;
    });

// 최종 연산이 호출되어야 실행됨
List<String> result = stream.collect(Collectors.toList());  // 이제 출력됨
```

**3. 단순한 경우는 전통적인 방식이 나을 수 있음**

```java
// 단순한 반복은 Stream보다 전통적인 방식이 더 명확할 수 있음
for (String item : list) {
    System.out.println(item);
}

// Stream 사용
list.forEach(System.out::println);
```

---

### 설정 시 반드시 고려해야 할 파라미터

- **성능 고려**: 대용량 데이터는 `parallelStream()` 고려
- **가독성 우선**: 복잡한 Stream은 메서드로 분리
- **최종 연산 확인**: Stream이 실제로 실행되도록 최종 연산 호출

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "Stream을 사용하면 항상 성능이 좋아진다"는 생각은 잘못되었습니다. Stream은 가독성과 유지보수성을 위한 것이며, 단순한 반복문보다 오버헤드가 있을 수 있습니다. 또한 "모든 반복문을 Stream으로 바꿔야 한다"는 생각도 잘못되었습니다. 상황에 따라 전통적인 반복문이 더 적합할 수 있습니다.

#### Stream API 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **코드 중복**: 반복적인 필터링, 변환 코드 작성
> - **가독성 저하**: 중첩된 반복문과 복잡한 조건문
> - **유지보수 어려움**: 복잡한 데이터 처리 로직
> - **현대적 Java 기능 미활용**: 함수형 프로그래밍 스타일 미활용
> - **병렬 처리 기회 상실**: 대용량 데이터 처리 시 성능 최적화 기회 상실

---

## 요약

- **Stream의 정의**: 자바 8부터 추가된 데이터 처리 전용 도구로, "데이터 흐름"처럼 생각하고 저장보다는 처리에 초점을 둡니다.

- **처리 순서**: 데이터 생성(스트림 생성) → 중간 연산(데이터 변형) → 최종 연산(결과 수집)

- **주요 메서드**: 
  - 중간 연산: `filter()`, `map()`, `sorted()`, `distinct()`, `limit()`, `skip()` 등
  - 최종 연산: `collect()`, `forEach()`, `count()`, `anyMatch()`, `allMatch()`, `reduce()` 등

- **장점**: 코드 간결성과 가독성 향상, 선언적 프로그래밍 스타일, 병렬 처리 지원, 함수형 프로그래밍 스타일

- Stream API는 현대적인 Java 개발에 필수적인 도구로, 데이터 처리를 선언적으로 작성할 수 있게 해줍니다.
- 람다 표현식과 메서드 참조와 함께 사용하면 강력한 데이터 처리 파이프라인을 구성할 수 있습니다.
- 지연 평가와 병렬 처리 기능을 이해하면 성능 최적화도 가능합니다.

---

### 예상 꼬리질문정리

#### 1. Stream과 컬렉션의 차이는?

- **컬렉션**: 데이터를 저장하는 자료 구조
- **Stream**: 데이터를 처리하는 도구
- **차이점**: Stream은 데이터를 저장하지 않고 처리만 하며, 한 번만 소비 가능

#### 2. 중간 연산과 최종 연산의 차이는?

- **중간 연산**: 여러 개를 연결 가능, 지연 평가, 새로운 Stream 반환
- **최종 연산**: Stream을 소비하여 결과 반환, 최종 연산 호출 시 중간 연산 실행
- **예시**: `filter()`, `map()`은 중간 연산, `collect()`, `forEach()`는 최종 연산

#### 3. 지연 평가(Lazy Evaluation)란?

- **정의**: 최종 연산이 호출될 때까지 중간 연산이 실행되지 않는 것
- **장점**: 불필요한 연산 방지, 성능 최적화
- **예시**: `filter()`와 `map()`만 호출해도 실행되지 않음, `collect()` 호출 시 실행

#### 4. parallelStream()은 언제 사용하나요?

- **사용 시기**: 대용량 데이터, CPU 집약적 작업
- **주의사항**: 스레드 안전성 고려, 상태 공유 시 문제 발생 가능
- **성능**: 작은 데이터는 오히려 느릴 수 있음

#### 5. Stream을 재사용할 수 있나요?

- **불가능**: Stream은 한 번만 소비 가능
- **이유**: 최종 연산 후 Stream은 닫힘
- **해결**: 매번 새로운 Stream 생성

---

## 참고 자료

- [Oracle Java Tutorials - Streams](https://docs.oracle.com/javase/tutorial/collections/streams/) - Oracle 공식 Stream 튜토리얼
- [Baeldung - Java 8 Stream](https://www.baeldung.com/java-8-streams) - Stream API 가이드

---

## 관련 노트

- **[[람다 표현식 (Lambda Expression)|람다 표현식]]** - Stream과 함께 사용하는 람다 표현식
- **[[메서드 참조 (Method Reference)|메서드 참조]]** - Stream에서 메서드 참조 활용
- **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]** - 함수형 프로그래밍과의 비교

---

**태그:** #java #stream #함수형프로그래밍 #람다 #면접 #개념정리
