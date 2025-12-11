# Collections Framework (컬렉션 프레임워크)

#java #collections #list #set #map #queue #면접 #개념정리

**관련 개념:** [[자바 다형성 (Polymorphism)|다형성]] [[제네릭 (Generics)|제네릭]] [[인터페이스]]

> [!note] 이어서 읽기
> Collections Framework를 이해하려면 **[[제네릭 (Generics)|제네릭]]**과 **[[인터페이스]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

Java Collections Framework의 구조, 주요 컬렉션 타입(List, Set, Map, Queue)의 특징과 사용법, 그리고 각 컬렉션의 선택 기준을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- Collections Framework가 없으면 배열만으로 데이터를 관리해야 하며, 동적 크기 조절, 정렬, 검색 등의 기능을 직접 구현해야 합니다. 배열은 크기가 고정되어 있어 삽입/삭제가 비효율적이고, 중복 제거, 정렬, 검색 등의 기능을 매번 구현해야 하므로 코드 중복이 발생하고 유지보수가 어려워집니다.

- Collections Framework는 다양한 데이터 구조를 표준화된 인터페이스로 제공하여 개발 생산성을 크게 향상시킵니다. List, Set, Map, Queue 등 상황에 맞는 컬렉션을 선택하여 사용할 수 있으며, 제네릭을 통해 타입 안전성을 보장하고, 다형성을 활용하여 유연한 코드 작성이 가능합니다. 또한 표준화된 API로 학습 곡선을 낮추고 코드 재사용성을 높입니다.

- 컬렉션 타입별 특징과 사용 시나리오, 시간 복잡도에 대한 이해, 그리고 실무에서 적절한 컬렉션 선택 능력을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**Collections Framework**는 Java에서 데이터를 저장하고 관리하기 위한 표준화된 인터페이스와 클래스들의 집합입니다.

**핵심 인터페이스**:
- **Collection**: List, Set, Queue의 상위 인터페이스
- **List**: 순서가 있고 중복을 허용하는 컬렉션
- **Set**: 순서가 없고 중복을 허용하지 않는 컬렉션
- **Map**: 키-값 쌍으로 데이터를 저장하는 컬렉션 (Collection 인터페이스 상속 안 함)
- **Queue**: FIFO(First In First Out) 구조의 컬렉션

---

#### 1. List 인터페이스

##### 개념 정의

**List**는 순서가 있고 중복을 허용하는 컬렉션입니다. 인덱스를 통해 요소에 접근할 수 있으며, 동일한 값을 여러 번 저장할 수 있습니다.

##### 주요 구현 클래스

**1. ArrayList**
- **내부 구조**: 동적 배열 (배열 기반)
- **특징**: 
  - 인덱스 기반 접근이 빠름 (O(1))
  - 끝에 추가/삭제는 빠름 (O(1))
  - 중간 삽입/삭제는 느림 (O(n))
  - 동기화되지 않음 (Thread-safe하지 않음)
- **사용 시나리오**: 
  - 빈번한 조회 작업
  - 끝에 추가/삭제가 많은 경우
  - 크기를 미리 알 수 없는 동적 배열이 필요한 경우

**2. LinkedList**
- **내부 구조**: 이중 연결 리스트 (노드 기반)
- **특징**:
  - 중간 삽입/삭제가 빠름 (O(1))
  - 인덱스 기반 접근이 느림 (O(n))
  - 메모리 오버헤드가 큼 (각 노드가 이전/다음 노드 참조 저장)
- **사용 시나리오**:
  - 빈번한 중간 삽입/삭제
  - 큐나 스택처럼 양 끝에서만 작업하는 경우

##### 주요 메서드

```java
List<String> list = new ArrayList<>();

// 추가
list.add("element");           // 끝에 추가
list.add(0, "first");          // 특정 인덱스에 삽입
list.addAll(otherList);        // 다른 컬렉션의 모든 요소 추가

// 조회
String element = list.get(0);  // 인덱스로 조회
int index = list.indexOf("element");  // 요소의 인덱스 찾기
boolean contains = list.contains("element");  // 포함 여부 확인

// 수정
list.set(0, "newElement");     // 특정 인덱스의 요소 교체

// 삭제
list.remove(0);                // 인덱스로 삭제
list.remove("element");        // 요소로 삭제
list.clear();                  // 모든 요소 삭제

// 기타
int size = list.size();        // 크기
boolean isEmpty = list.isEmpty();  // 비어있는지 확인
```

##### ArrayList vs LinkedList 비교

| 구분 | ArrayList | LinkedList |
|------|-----------|------------|
| **내부 구조** | 동적 배열 | 이중 연결 리스트 |
| **인덱스 접근** | O(1) - 빠름 | O(n) - 느림 |
| **끝에 추가** | O(1) - 빠름 | O(1) - 빠름 |
| **중간 삽입** | O(n) - 느림 | O(1) - 빠름 |
| **중간 삭제** | O(n) - 느림 | O(1) - 빠름 |
| **메모리** | 적음 | 많음 (노드 참조) |
| **사용 시나리오** | 조회가 많은 경우 | 삽입/삭제가 많은 경우 |

---

#### 2. Set 인터페이스

##### 개념 정의

**Set**은 순서가 없고 중복을 허용하지 않는 컬렉션입니다. 수학의 집합 개념과 동일하며, 동일한 요소를 여러 번 저장할 수 없습니다.

##### 주요 구현 클래스

**1. HashSet**
- **내부 구조**: 해시 테이블 (HashMap 기반)
- **특징**:
  - 추가/삭제/조회가 모두 O(1) 평균 시간 복잡도
  - 순서 보장 안 함
  - null 값 허용
  - 동기화되지 않음
- **사용 시나리오**:
  - 중복 제거가 필요한 경우
  - 빠른 조회가 필요한 경우
  - 순서가 중요하지 않은 경우

**2. TreeSet**
- **내부 구조**: Red-Black Tree (이진 탐색 트리)
- **특징**:
  - 자동 정렬 (오름차순)
  - 추가/삭제/조회가 O(log n) 시간 복잡도
  - null 값 불가능
  - 정렬된 순서로 순회 가능
- **사용 시나리오**:
  - 정렬된 데이터가 필요한 경우
  - 범위 검색이 필요한 경우

**3. LinkedHashSet**
- **내부 구조**: 해시 테이블 + 연결 리스트
- **특징**:
  - 삽입 순서 보장
  - HashSet과 유사한 성능
  - 순서가 중요하지만 중복 제거도 필요한 경우
- **사용 시나리오**:
  - 삽입 순서를 유지하면서 중복을 제거해야 하는 경우

##### 주요 메서드

```java
Set<String> set = new HashSet<>();

// 추가
set.add("element");            // 추가 (중복 시 false 반환)
set.addAll(otherSet);         // 다른 컬렉션의 모든 요소 추가

// 조회
boolean contains = set.contains("element");  // 포함 여부 확인
int size = set.size();        // 크기

// 삭제
set.remove("element");        // 요소 삭제
set.clear();                  // 모든 요소 삭제

// 순회
for (String element : set) {
    System.out.println(element);
}
```

##### HashSet vs TreeSet 비교

| 구분 | HashSet | TreeSet |
|------|---------|---------|
| **내부 구조** | 해시 테이블 | Red-Black Tree |
| **순서** | 보장 안 함 | 자동 정렬 |
| **성능** | O(1) 평균 | O(log n) |
| **null 허용** | 가능 | 불가능 |
| **사용 시나리오** | 빠른 조회, 중복 제거 | 정렬된 데이터 필요 |

---

#### 3. Map 인터페이스

##### 개념 정의

**Map**은 키(Key)와 값(Value)의 쌍으로 데이터를 저장하는 컬렉션입니다. 키는 중복될 수 없으며, 각 키는 하나의 값과 매핑됩니다.

##### 주요 구현 클래스

**1. HashMap**
- **내부 구조**: 해시 테이블
- **특징**:
  - 추가/삭제/조회가 O(1) 평균 시간 복잡도
  - 순서 보장 안 함
  - null 키와 null 값 허용
  - 동기화되지 않음
- **사용 시나리오**:
  - 가장 많이 사용되는 Map 구현체
  - 빠른 조회가 필요한 경우
  - 순서가 중요하지 않은 경우

**2. TreeMap**
- **내부 구조**: Red-Black Tree
- **특징**:
  - 키 기준 자동 정렬
  - 추가/삭제/조회가 O(log n) 시간 복잡도
  - null 키 불가능
  - 범위 검색 가능
- **사용 시나리오**:
  - 정렬된 키-값 쌍이 필요한 경우
  - 범위 검색이 필요한 경우

**3. LinkedHashMap**
- **내부 구조**: 해시 테이블 + 연결 리스트
- **특징**:
  - 삽입 순서 또는 접근 순서 보장
  - HashMap과 유사한 성능
- **사용 시나리오**:
  - 삽입 순서를 유지하면서 빠른 조회가 필요한 경우

##### 주요 메서드

```java
Map<String, Integer> map = new HashMap<>();

// 추가/수정
map.put("key1", 100);         // 키-값 추가 (기존 키면 값 교체)
map.putIfAbsent("key1", 200); // 키가 없을 때만 추가
map.putAll(otherMap);         // 다른 Map의 모든 요소 추가

// 조회
Integer value = map.get("key1");           // 키로 값 조회
Integer value2 = map.getOrDefault("key2", 0);  // 키가 없으면 기본값 반환
boolean containsKey = map.containsKey("key1");  // 키 존재 여부
boolean containsValue = map.containsValue(100); // 값 존재 여부

// 삭제
map.remove("key1");           // 키로 삭제
map.remove("key1", 100);     // 키-값 쌍으로 삭제
map.clear();                  // 모든 요소 삭제

// 기타
int size = map.size();        // 크기
Set<String> keys = map.keySet();      // 모든 키
Collection<Integer> values = map.values();  // 모든 값
Set<Map.Entry<String, Integer>> entries = map.entrySet();  // 모든 엔트리

// 순회
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    String key = entry.getKey();
    Integer value = entry.getValue();
}
```

##### HashMap vs TreeMap 비교

| 구분 | HashMap | TreeMap |
|------|---------|---------|
| **내부 구조** | 해시 테이블 | Red-Black Tree |
| **순서** | 보장 안 함 | 키 기준 자동 정렬 |
| **성능** | O(1) 평균 | O(log n) |
| **null 키** | 가능 | 불가능 |
| **사용 시나리오** | 일반적인 Map 사용 | 정렬된 Map 필요 |

---

#### 4. Queue 인터페이스

##### 개념 정의

**Queue**는 FIFO(First In First Out) 구조의 컬렉션입니다. 먼저 추가된 요소가 먼저 제거되는 구조입니다.

##### 주요 구현 클래스

**1. LinkedList (Queue 구현)**
- **특징**:
  - 양 끝에서 추가/삭제 가능
  - Queue와 Deque 인터페이스 모두 구현
- **사용 시나리오**: 일반적인 큐 구현

**2. PriorityQueue**
- **내부 구조**: 힙(Heap) 기반
- **특징**:
  - 우선순위에 따라 정렬
  - 최소 힙 또는 최대 힙 구현 가능
  - O(log n) 시간 복잡도
- **사용 시나리오**:
  - 우선순위가 있는 작업 처리
  - 최소/최대값을 빠르게 찾아야 하는 경우

##### 주요 메서드

```java
Queue<String> queue = new LinkedList<>();

// 추가
queue.add("element");         // 추가 (용량 초과 시 예외)
queue.offer("element");       // 추가 (용량 초과 시 false 반환)

// 조회
String head = queue.peek();   // 맨 앞 요소 조회 (없으면 null)
String head2 = queue.element(); // 맨 앞 요소 조회 (없으면 예외)

// 삭제
String removed = queue.poll(); // 맨 앞 요소 제거 및 반환 (없으면 null)
String removed2 = queue.remove(); // 맨 앞 요소 제거 및 반환 (없으면 예외)

// 기타
int size = queue.size();
boolean isEmpty = queue.isEmpty();
```

##### PriorityQueue 사용 예시

```java
// 최소 힙 (기본)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
minHeap.offer(5);
minHeap.offer(2);
minHeap.offer(8);
minHeap.poll();  // 2 반환 (가장 작은 값)

// 최대 힙
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
maxHeap.offer(5);
maxHeap.offer(2);
maxHeap.offer(8);
maxHeap.poll();  // 8 반환 (가장 큰 값)
```

---

#### 5. 레거시 컬렉션 (간단 설명)

**Vector**: ArrayList의 동기화된 버전 (사용 비권장)
- 모든 메서드가 동기화되어 있어 성능이 느림
- 대신 `Collections.synchronizedList()` 사용 권장

**Stack**: LIFO 구조 (사용 비권장)
- Vector를 상속받아 구현됨
- 대신 `Deque` 인터페이스 사용 권장: `new ArrayDeque<>()`

**Hashtable**: HashMap의 동기화된 버전 (사용 비권장)
- 모든 메서드가 동기화되어 있어 성능이 느림
- 대신 `ConcurrentHashMap` 사용 권장

---

### **Interview Answer Version (면접 답변식 요약)**

Java Collections Framework는 데이터를 저장하고 관리하기 위한 표준화된 인터페이스와 클래스들의 집합입니다.

**List**는 순서가 있고 중복을 허용하는 컬렉션으로, ArrayList는 배열 기반으로 인덱스 접근이 빠르고, LinkedList는 연결 리스트 기반으로 중간 삽입/삭제가 빠릅니다.

**Set**은 중복을 허용하지 않는 컬렉션으로, HashSet은 해시 테이블 기반으로 O(1) 평균 시간 복잡도를 가지며, TreeSet은 Red-Black Tree 기반으로 자동 정렬됩니다.

**Map**은 키-값 쌍으로 데이터를 저장하며, HashMap은 해시 테이블 기반으로 가장 많이 사용되고, TreeMap은 키 기준 자동 정렬을 제공합니다.

**Queue**는 FIFO 구조로, LinkedList로 구현하거나 PriorityQueue로 우선순위 큐를 구현할 수 있습니다.

각 컬렉션은 상황에 맞게 선택하여 사용하며, 시간 복잡도와 사용 시나리오를 고려하여 선택하는 것이 중요합니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 컬렉션 선택 가이드

**1. 조회가 많은 경우**
- `ArrayList` (List)
- `HashSet` (Set)
- `HashMap` (Map)

**2. 삽입/삭제가 많은 경우**
- `LinkedList` (List, Queue)
- `HashSet` (Set)
- `HashMap` (Map)

**3. 정렬이 필요한 경우**
- `TreeSet` (Set)
- `TreeMap` (Map)

**4. 순서 보장이 필요한 경우**
- `ArrayList`, `LinkedList` (List)
- `LinkedHashSet` (Set)
- `LinkedHashMap` (Map)

#### 주의사항

**1. 동기화 이슈**
```java
// ❌ Thread-safe하지 않음
List<String> list = new ArrayList<>();

// ✅ 동기화가 필요한 경우
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
// 또는
List<String> concurrentList = new CopyOnWriteArrayList<>();
```

**2. null 처리**
```java
// HashSet, HashMap: null 허용
Set<String> set = new HashSet<>();
set.add(null);  // 가능

// TreeSet, TreeMap: null 불가능
Set<String> treeSet = new TreeSet<>();
treeSet.add(null);  // NullPointerException 발생
```

**3. equals()와 hashCode()**
```java
// Set과 Map의 키는 equals()와 hashCode()를 올바르게 구현해야 함
class Person {
    String name;
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return Objects.equals(name, person.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name);
    }
}
```

**4. Iterator 사용 시 수정**
```java
// ❌ ConcurrentModificationException 발생
for (String item : list) {
    list.remove(item);  // 예외 발생
}

// ✅ Iterator 사용
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    iterator.remove();  // 안전하게 삭제
}
```

#### 실무 활용 예시

**1. 중복 제거**
```java
List<String> list = Arrays.asList("a", "b", "a", "c");
Set<String> uniqueSet = new HashSet<>(list);  // 중복 제거
```

**2. 빈도수 계산**
```java
Map<String, Integer> frequency = new HashMap<>();
for (String word : words) {
    frequency.put(word, frequency.getOrDefault(word, 0) + 1);
}
```

**3. 정렬된 데이터**
```java
Set<Integer> sortedSet = new TreeSet<>();
sortedSet.add(5);
sortedSet.add(2);
sortedSet.add(8);
// 자동으로 [2, 5, 8] 순서로 정렬됨
```

---

### 예상 꼬리질문정리

1. **ArrayList와 LinkedList의 차이점과 각각의 사용 시나리오는?**
   - ArrayList는 배열 기반으로 인덱스 접근이 O(1)이고, LinkedList는 연결 리스트 기반으로 중간 삽입/삭제가 O(1)입니다. 조회가 많으면 ArrayList, 삽입/삭제가 많으면 LinkedList를 사용합니다.

2. **HashMap의 내부 동작 원리는?**
   - HashMap은 해시 테이블을 사용하며, 키의 hashCode()를 사용하여 버킷에 저장합니다. 해시 충돌 시 체이닝이나 Open Addressing 방식으로 해결합니다. Java 8부터는 버킷의 요소가 8개 이상이면 연결 리스트를 Red-Black Tree로 변환합니다.

3. **HashSet과 TreeSet의 차이점은?**
   - HashSet은 해시 테이블 기반으로 O(1) 평균 시간 복잡도를 가지며 순서를 보장하지 않습니다. TreeSet은 Red-Black Tree 기반으로 O(log n) 시간 복잡도를 가지며 자동 정렬됩니다.

4. **HashMap에서 키로 사용할 수 있는 타입의 조건은?**
   - equals()와 hashCode()를 올바르게 구현해야 합니다. 불변 객체(immutable)를 사용하는 것이 권장되며, 키가 변경되면 해시값이 변경되어 조회가 불가능해질 수 있습니다.

5. **ConcurrentModificationException이 발생하는 이유는?**
   - 컬렉션을 순회하는 중에 컬렉션을 직접 수정하면 발생합니다. Iterator의 remove() 메서드를 사용하거나, 순회가 끝난 후 수정해야 합니다.

6. **PriorityQueue의 내부 구조는?**
   - 힙(Heap) 자료구조를 사용하며, 최소 힙 또는 최대 힙으로 구현할 수 있습니다. 우선순위에 따라 정렬되며, 최소/최대값을 O(log n) 시간에 찾을 수 있습니다.

7. **LinkedHashMap이 삽입 순서를 보장하는 원리는?**
   - 해시 테이블과 연결 리스트를 결합하여 구현합니다. 각 엔트리가 이전/다음 엔트리를 참조하여 삽입 순서를 유지합니다.

8. **Collections.synchronizedList()와 CopyOnWriteArrayList의 차이는?**
   - Collections.synchronizedList()는 모든 메서드를 동기화하여 성능이 느립니다. CopyOnWriteArrayList는 읽기 작업 시 복사본을 사용하여 읽기 성능이 좋지만, 쓰기 작업 시 전체 배열을 복사하므로 쓰기 성능이 느립니다.

9. **TreeMap에서 범위 검색은 어떻게 하나요?**
   - subMap(), headMap(), tailMap() 메서드를 사용하여 범위 검색이 가능합니다. 이 메서드들은 SortedMap 인터페이스에서 제공합니다.

10. **컬렉션의 크기를 동적으로 조절하는 방법은?**
    - ArrayList는 내부적으로 배열 크기를 초과하면 더 큰 배열을 생성하여 복사합니다. 초기 용량을 설정하여 재할당 횟수를 줄일 수 있습니다: `new ArrayList<>(initialCapacity)`.

---

## 관련 노트

- **[[제네릭 (Generics)|제네릭]]** - Collections Framework에서 타입 안전성을 제공하는 제네릭
- **[[인터페이스]]** - Collections Framework의 인터페이스 기반 설계
- **[[자바 다형성 (Polymorphism)|다형성]]** - Collections Framework에서 활용되는 다형성

---

**태그:** #java #collections #list #set #map #queue #면접 #개념정리

