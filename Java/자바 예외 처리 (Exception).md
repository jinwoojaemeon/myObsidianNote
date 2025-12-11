# 자바 예외 처리 (Exception)

#java #예외처리 #exception #try-catch #throwable #면접 #개념정리

**관련 개념:** [[에러]] [[런타임 오류]] [[체크 예외]] [[언체크 예외]] [[try-catch-finally]]

> [!note] 이어서 읽기
> 예외 처리를 이해하려면 **[[런타임 오류]]**와 **[[에러]]**의 차이를 함께 확인하세요.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 예외 처리를 제대로 하지 않으면 프로그램이 예기치 않게 종료되어 사용자 경험이 나빠지고, 시스템 안정성이 떨어집니다. 네트워크 오류, 파일 I/O 오류, 데이터베이스 연결 실패 등 다양한 예외 상황을 처리하지 못하면 프로덕션 환경에서 심각한 문제가 발생할 수 있습니다. 또한 예외를 적절히 처리하고 로깅하지 않으면 디버깅이 매우 어려워집니다.

- 예외 처리는 프로그램의 안정성과 신뢰성을 보장하는 핵심 메커니즘입니다. 정상적인 실행 흐름과 예외 상황을 분리하여 코드의 가독성과 유지보수성을 높이고, 예외 정보를 통해 문제의 원인을 파악하고 적절한 복구 전략을 수립할 수 있게 합니다. 체크 예외와 언체크 예외의 구분을 통해 컴파일 타임에 예외 처리를 강제하거나 선택적으로 처리할 수 있도록 설계되었습니다.

- 지원자가 예외 처리에 대한 깊은 이해를 가지고 있는지, 체크 예외와 언체크 예외의 차이를 알고 있는지, 예외 처리의 best practice를 알고 있는지 확인하려 합니다. 또한 예외 처리의 구조적 설계와 예외 전파, 예외 래핑 등 고급 개념을 이해하는지 평가합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 1. 도입: 예외 처리의 정의와 역할

**예외(Exception)**는 프로그램 실행 중 발생하는 비정상적인 상황이나 오류를 나타내는 객체입니다. Java는 예외를 객체로 표현하여 예외 정보를 전달하고, 예외 처리 메커니즘을 통해 프로그램의 안정성을 보장합니다.

**C 언어와의 차이**:
- **C 언어**: 예외 처리 메커니즘이 없음, 에러 코드나 null 체크로 처리
- **Java**: 예외 객체를 통한 체계적인 예외 처리 메커니즘 제공

**예외 처리의 역할**:
1. 프로그램 실행 중 발생하는 비정상적인 상황을 감지하고 처리
2. 예외 정보를 통해 문제의 원인을 파악
3. 프로그램의 비정상 종료를 방지하고 안정성 보장
4. 정상적인 실행 흐름과 예외 상황을 분리

> [!tip] 핵심 포인트
> Java의 예외 처리는 "예외가 발생하면 예외 객체가 생성되고, 이를 처리하는 코드가 실행된다"는 구조입니다. 예외를 처리하지 않으면 프로그램이 비정상 종료됩니다.

---

#### 2. 예외 처리 구조: Exception Hierarchy

**예외 계층 구조**:

```
Throwable (최상위 클래스)
    │
    ├── Error (시스템 레벨 오류)
    │   ├── OutOfMemoryError
    │   ├── StackOverflowError
    │   └── VirtualMachineError
    │
    └── Exception (예외)
        │
        ├── RuntimeException (언체크 예외)
        │   ├── NullPointerException
        │   ├── ArrayIndexOutOfBoundsException
        │   ├── IllegalArgumentException
        │   ├── ClassCastException
        │   └── ArithmeticException
        │
        └── Checked Exception (체크 예외)
            ├── IOException
            │   ├── FileNotFoundException
            │   └── EOFException
            ├── SQLException
            ├── ClassNotFoundException
            └── InterruptedException
```

#### 3. 예외 분류: 체크 예외 vs 언체크 예외

#### 체크 예외 (Checked Exception)

- **정의**: `Exception` 클래스를 상속받지만 `RuntimeException`을 상속받지 않은 예외
- **특징**: 컴파일 타임에 예외 처리를 강제함
- **처리 방법**: 
  - `try-catch`로 처리하거나
  - 메서드 시그니처에 `throws` 선언
- **예시**: `IOException`, `SQLException`, `ClassNotFoundException`

```java
// 컴파일 오류 발생! 예외 처리가 필요함
public void readFile() {
    FileReader file = new FileReader("file.txt"); // FileNotFoundException 발생 가능
}

// 올바른 처리 방법 1: try-catch
public void readFile() {
    try {
        FileReader file = new FileReader("file.txt");
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    }
}

// 올바른 처리 방법 2: throws 선언
public void readFile() throws FileNotFoundException {
    FileReader file = new FileReader("file.txt");
}
```

#### 언체크 예외 (Unchecked Exception)

- **정의**: `RuntimeException`을 상속받은 예외
- **특징**: 컴파일 타임에 예외 처리를 강제하지 않음
- **처리 방법**: 선택적으로 처리 가능
- **예시**: `NullPointerException`, `ArrayIndexOutOfBoundsException`, `IllegalArgumentException`

```java
// 컴파일 오류 없음! 예외 처리가 선택적
public void divide(int a, int b) {
    int result = a / b; // ArithmeticException 발생 가능하지만 강제하지 않음
}

// 선택적으로 처리 가능
public void divide(int a, int b) {
    try {
        int result = a / b;
    } catch (ArithmeticException e) {
        System.out.println("0으로 나눌 수 없습니다.");
    }
}
```

#### 4. 예외 처리 메커니즘: 키워드와 동작 방식

**예외 처리 흐름**:

```
1. 정상 실행
   메서드 실행 중...
      ↓
2. 예외 발생
   예외 상황 발생 (예: NullPointerException)
      ↓
3. 예외 객체 생성
   new NullPointerException() 생성
      ↓
4. 예외 전파
   현재 메서드에서 처리하지 않으면
   호출한 메서드로 예외 전파
      ↓
5. 예외 처리
   try-catch 블록에서 예외 처리
   또는 메서드 시그니처에 throws 선언
      ↓
6. 처리 결과
   - catch 블록 실행
   - finally 블록 실행 (있을 경우)
   - 프로그램 계속 실행 또는 종료
```

**관련 개념:** [[try-catch-finally]] [[throws]] [[throw]] [[예외 전파]]

##### 4.1 예외 처리 키워드

#### 1. try-catch-finally

```java
try {
    // 예외가 발생할 수 있는 코드
    int result = 10 / 0;
} catch (ArithmeticException e) {
    // 예외 처리 코드
    System.out.println("산술 예외 발생: " + e.getMessage());
} catch (Exception e) {
    // 여러 예외 처리 (상위 예외는 아래에)
    System.out.println("일반 예외 발생: " + e.getMessage());
} finally {
    // 항상 실행되는 코드 (리소스 정리 등)
    System.out.println("finally 블록 실행");
}
```

#### 2. throws

```java
// 메서드에서 예외를 호출자에게 전파
public void readFile() throws IOException {
    FileReader file = new FileReader("file.txt");
    // IOException이 발생하면 호출한 메서드로 전파
}
```

#### 3. throw

```java
// 명시적으로 예외를 발생시킴
public void validateAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("나이는 0 이상이어야 합니다.");
    }
}
```

#### 4. try-with-resources (Java 7+)

```java
// 자동으로 리소스를 닫아줌 (AutoCloseable 구현 필요)
try (FileReader file = new FileReader("file.txt");
     BufferedReader reader = new BufferedReader(file)) {
    String line = reader.readLine();
} catch (IOException e) {
    e.printStackTrace();
} // finally 없이도 자동으로 close() 호출
```

---

### 장점과 단점

> [!success] 장점
> 
> - ✅ **프로그램 안정성**: 예외 상황을 처리하여 비정상 종료 방지
> - ✅ **오류 정보 제공**: 예외 객체를 통해 상세한 오류 정보 전달
> - ✅ **코드 가독성**: 정상 흐름과 예외 처리 분리
> - ✅ **컴파일 타임 검증**: 체크 예외를 통한 강제적 예외 처리
> - ✅ **예외 전파**: 호출 체인을 통해 예외를 상위로 전달 가능
> - ✅ **리소스 관리**: try-with-resources를 통한 자동 리소스 정리

> [!warning] 단점
> 
> - ❌ **코드 복잡성**: 예외 처리 코드로 인한 코드 증가
> - ❌ **성능 오버헤드**: 예외 객체 생성 및 스택 트레이스 생성 비용
> - ❌ **과도한 체크 예외**: 불필요한 예외 처리로 인한 코드 복잡도 증가
> - ❌ **예외 전파 오용**: 예외를 무분별하게 전파하면 호출자가 부담
> - ❌ **예외 무시**: catch 블록에서 예외를 무시하면 디버깅 어려움

---

> [!info] 필요 조건
> 
> 예외 처리를 사용하려면:
> - **예외 클래스**: Java에서 제공하는 예외 클래스 또는 사용자 정의 예외
> - **예외 처리 구문**: try-catch-finally 또는 throws 선언
> - **예외 발생 상황**: 실제로 예외가 발생할 수 있는 코드
> - **예외 처리 전략**: 예외를 어떻게 처리할지 결정 (로깅, 복구, 전파 등)

---

### 주요 예외 클래스

#### RuntimeException (언체크 예외)

| 예외 | 발생 상황 |
|:---:|:---|
| `NullPointerException` | null 참조에 접근할 때 |
| `ArrayIndexOutOfBoundsException` | 배열 인덱스 범위 초과 |
| `IllegalArgumentException` | 잘못된 인자 전달 |
| `ClassCastException` | 잘못된 타입 캐스팅 |
| `ArithmeticException` | 산술 연산 오류 (0으로 나누기 등) |
| `NumberFormatException` | 숫자 형식 변환 실패 |

#### Checked Exception

| 예외 | 발생 상황 |
|:---:|:---|
| `IOException` | 입출력 오류 |
| `FileNotFoundException` | 파일을 찾을 수 없을 때 |
| `SQLException` | 데이터베이스 오류 |
| `ClassNotFoundException` | 클래스를 찾을 수 없을 때 |
| `InterruptedException` | 스레드가 중단되었을 때 |

---

### 예외 처리 Best Practice

#### 1. 구체적인 예외 처리

```java
// 나쁜 예: 모든 예외를 Exception으로 처리
try {
    // 코드
} catch (Exception e) {
    e.printStackTrace();
}

// 좋은 예: 구체적인 예외 처리
try {
    FileReader file = new FileReader("file.txt");
} catch (FileNotFoundException e) {
    System.out.println("파일을 찾을 수 없습니다: " + e.getMessage());
} catch (IOException e) {
    System.out.println("입출력 오류: " + e.getMessage());
}
```

#### 2. 예외 무시 금지

```java
// 나쁜 예: 예외를 무시
try {
    // 코드
} catch (Exception e) {
    // 아무것도 하지 않음
}

// 좋은 예: 로깅 또는 적절한 처리
try {
    // 코드
} catch (Exception e) {
    logger.error("예외 발생", e);
    // 또는 복구 로직
}
```

#### 3. 예외 전파 vs 처리

```java
// 낮은 레벨: 예외를 전파
public void readFile() throws IOException {
    FileReader file = new FileReader("file.txt");
}

// 높은 레벨: 예외를 처리
public void processFile() {
    try {
        readFile();
    } catch (IOException e) {
        logger.error("파일 처리 실패", e);
        // 사용자에게 알림 또는 기본값 설정
    }
}
```

#### 4. 사용자 정의 예외

```java
// 사용자 정의 예외 클래스
public class CustomException extends Exception {
    public CustomException(String message) {
        super(message);
    }
    
    public CustomException(String message, Throwable cause) {
        super(message, cause);
    }
}

// 사용
public void validate(int value) throws CustomException {
    if (value < 0) {
        throw new CustomException("값은 0 이상이어야 합니다.");
    }
}
```

---

#### 5. 정리 및 결론

**핵심 요약**:

1. **예외 처리의 정의와 역할**:
   - 예외는 프로그램 실행 중 발생하는 비정상적인 상황을 나타내는 객체입니다.
   - C 언어와 달리 Java는 예외 객체를 통한 체계적인 예외 처리 메커니즘을 제공합니다.
   - 예외 처리를 통해 프로그램의 비정상 종료를 방지하고 안정성을 보장합니다.

2. **예외 처리 구조: Exception Hierarchy**:
   - `Throwable`을 최상위로 하여 `Error`와 `Exception`으로 구분됩니다.
   - `Exception`은 다시 체크 예외와 언체크 예외로 나뉩니다.
   - 각 예외는 계층 구조를 통해 분류되고 관리됩니다.

3. **예외 분류: 체크 예외 vs 언체크 예외**:
   - **체크 예외**: 컴파일 타임에 예외 처리를 강제하는 예외 (`try-catch` 또는 `throws` 필요)
   - **언체크 예외**: `RuntimeException`을 상속받은 예외로, 예외 처리가 선택적

4. **예외 처리 메커니즘**:
   - `try-catch-finally`: 예외를 처리하고 리소스를 정리
   - `throws`: 예외를 호출자에게 전파
   - `throw`: 명시적으로 예외를 발생
   - `try-with-resources`: Java 7+ 자동 리소스 관리

**개인적 인사이트**:
- 예외 처리는 단순한 오류 처리 도구가 아니라 프로그램의 안정성과 신뢰성을 보장하는 핵심 메커니즘입니다.
- 체크 예외와 언체크 예외의 구분을 이해하면 적절한 예외 처리 전략을 수립할 수 있습니다.
- 예외 처리 Best Practice를 따르면 코드의 가독성과 유지보수성을 높일 수 있습니다.

**추가 학습 제안**:
- 예외 전파와 예외 래핑
- 사용자 정의 예외 클래스 설계
- 예외 처리 성능 최적화
- 실무 예외 처리 전략

---

### 설정 시 반드시 고려해야 할 파라미터

- **예외 처리 전략**: 
  - 예외를 처리할지 전파할지 결정
  - 로깅 레벨 및 형식 결정
  - 사용자에게 보여줄 메시지 결정

- **예외 계층 구조**: 
  - 사용자 정의 예외 클래스 설계
  - 예외 래핑 전략

- **리소스 관리**: 
  - `try-with-resources` 사용
  - `AutoCloseable` 인터페이스 구현

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> 1. **"모든 예외를 Exception으로 처리하면 된다"**: 
>    - 구체적인 예외를 처리해야 정확한 오류 정보를 얻을 수 있습니다.
> 
> 2. **"예외를 무시해도 된다"**: 
>    - 예외를 무시하면 디버깅이 매우 어려워지고, 문제가 숨겨질 수 있습니다.
> 
> 3. **"체크 예외는 항상 처리해야 한다"**: 
>    - 적절한 레벨에서 예외를 처리하는 것이 중요하며, 모든 레벨에서 처리할 필요는 없습니다.
> 
> 4. **"finally는 항상 실행된다"**: 
>    - `System.exit()` 호출 시 finally는 실행되지 않습니다.

#### 예외 처리 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **프로그램 비정상 종료**: 예외를 처리하지 않아 프로그램이 갑자기 종료
> - **디버깅 어려움**: 예외를 무시하거나 제대로 로깅하지 않아 문제 원인 파악 불가
> - **리소스 누수**: finally 블록이나 try-with-resources를 사용하지 않아 리소스가 해제되지 않음
> - **예외 전파 오용**: 모든 예외를 전파하여 호출자가 부담을 지게 됨
> - **성능 저하**: 예외를 과도하게 사용하거나 스택 트레이스를 자주 생성하여 성능 저하
> - **보안 문제**: 예외 메시지에 민감한 정보가 포함되어 보안 취약점 발생

---

## 예상 꼬리질문 정리

### 1. Error와 Exception의 차이는?

- **Error**: 
  - 시스템 레벨의 심각한 오류
  - 복구 불가능한 상황
  - 예: `OutOfMemoryError`, `StackOverflowError`
  - 일반적으로 처리하지 않음
  
- **Exception**: 
  - 애플리케이션 레벨의 예외
  - 복구 가능한 상황
  - 예: `IOException`, `SQLException`
  - 처리 가능

### 2. 체크 예외와 언체크 예외를 언제 사용하나요?

- **체크 예외**: 
  - 복구 가능한 예외
  - 호출자가 반드시 처리해야 하는 예외
  - 예: 파일 I/O, 데이터베이스 연결
  
- **언체크 예외**: 
  - 프로그래머의 실수로 발생하는 예외
  - 복구가 어려운 예외
  - 예: `NullPointerException`, `IllegalArgumentException`

### 3. 예외를 전파하는 것과 처리하는 것 중 어떤 것이 좋나요?

- **전파 (throws)**: 
  - 현재 레벨에서 처리할 수 없을 때
  - 호출자가 더 적절한 처리를 할 수 있을 때
  - 낮은 레벨의 메서드에서 사용
  
- **처리 (try-catch)**: 
  - 현재 레벨에서 처리할 수 있을 때
  - 사용자에게 적절한 메시지를 보여줄 수 있을 때
  - 높은 레벨의 메서드에서 사용

### 4. finally 블록은 언제 사용하나요?

- **리소스 정리**: 파일, 데이터베이스 연결, 네트워크 연결 등
- **정리 작업**: 임시 파일 삭제, 로그 기록 등
- **항상 실행되어야 하는 코드**: 예외 발생 여부와 관계없이 실행

### 5. try-with-resources는 어떻게 동작하나요?

- **AutoCloseable 인터페이스**: `close()` 메서드를 가진 인터페이스
- **자동 리소스 관리**: try 블록 종료 시 자동으로 `close()` 호출
- **여러 리소스**: 세미콜론으로 구분하여 여러 리소스 선언 가능
- **예외 처리**: close() 중 발생한 예외도 처리 가능

### 6. 예외 래핑(Exception Wrapping)이란?

- **정의**: 하나의 예외를 다른 예외로 감싸는 것
- **목적**: 
  - 예외 계층 구조 단순화
  - 구현 세부사항 숨기기
  - 더 의미 있는 예외로 변환
- **예시**: 
  ```java
  try {
      // 코드
  } catch (SQLException e) {
      throw new DataAccessException("데이터 접근 실패", e);
  }
  ```

### 7. 예외 처리의 성능 영향은?

- **예외 객체 생성**: 스택 트레이스 생성 비용
- **예외 처리 오버헤드**: try-catch 블록 자체는 비용이 낮음
- **Best Practice**: 
  - 예외를 제어 흐름으로 사용하지 않기
  - 자주 발생하는 경우 예외 대신 조건문 사용
  - 예외는 진짜 예외 상황에만 사용

---

## 관련 노트

- **[[에러]]** - Error와 Exception의 차이
- **[[런타임 오류]]** - 런타임에 발생하는 오류
- **[[체크 예외]]** - Checked Exception 상세 설명
- **[[언체크 예외]]** - Unchecked Exception 상세 설명
- **[[try-catch-finally]]** - 예외 처리 구문
- **[[throws]]** - 예외 전파
- **[[throw]]** - 예외 발생
- **[[try-with-resources]]** - 자동 리소스 관리

---

> [!tip] 다음 단계
> 예외 처리를 이해했다면, **[[런타임 오류]]**와 **[[에러]]**의 차이를 확인하세요. 또한 실제 프로젝트에서 예외 처리 전략을 수립하는 방법을 학습하세요.

---

**태그:** #java #예외처리 #exception #try-catch #throwable #면접 #개념정리

