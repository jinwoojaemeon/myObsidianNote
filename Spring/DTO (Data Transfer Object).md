# DTO (Data Transfer Object)

#spring #dto #data-transfer-object #빌더패턴 #lombok #면접 #개념정리

**관련 개념:** [[Entity]] [[VO (Value Object)|VO]] [[빌더 패턴]] [[Lombok]]

> [!note] 이어서 읽기
> DTO를 이해하려면 **[[Entity]]**와 **[[VO (Value Object)|VO]]**의 차이, 그리고 **[[빌더 패턴]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

DTO의 정의, 목적, 사용 방법, 그리고 빌더 패턴과 Lombok의 @Builder 어노테이션을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- DTO가 없으면 Entity나 VO를 계층 간에 직접 전달하게 되어, DB 스키마가 변경되면 모든 계층에 영향을 주고, 불필요한 정보가 노출될 수 있습니다. 또한 비즈니스 로직과 데이터 전달 로직이 혼재되어 유지보수가 어려워지고, 클라이언트에 민감한 정보나 불필요한 필드가 노출될 위험이 있습니다.

- DTO는 계층 간 데이터 교환을 위한 순수한 데이터 전달 객체로, 비즈니스 로직 없이 데이터만 보관합니다. Controller, Service, Repository 계층 사이에서 안전하게 데이터를 전달할 수 있으며, 필요한 데이터만 선택적으로 전달하여 보안성과 유연성을 높입니다. 또한 각 계층의 독립성을 유지하여 변경 영향 범위를 최소화합니다.

- DTO 설계 원칙, Entity와의 변환 방법, 빌더 패턴 활용, 그리고 실무에서의 DTO 사용 패턴을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

**DTO(Data Transfer Object)**는 계층 간 데이터 교환을 위한 순수 데이터를 담는 용도의 객체입니다. 비즈니스 로직 없이 데이터만 보관하며, 주로 Controller ↔ Service ↔ Repository 계층 사이에서 사용됩니다.

**핵심 특징**:
- 순수 데이터 전달 객체
- 비즈니스 로직 없음
- 계층 간 데이터 교환 전용
- 필요한 데이터만 포함

---

#### 1. DTO의 필요성

##### 1.1 계층 간 데이터 전달의 문제점

**Entity를 직접 사용할 때의 문제**:
- DB 스키마 변경 시 모든 계층에 영향
- 불필요한 정보 노출 (예: 비밀번호, 내부 ID 등)
- 순환 참조 문제
- 계층 간 결합도 증가

**예시**:
```java
// ❌ 나쁜 예: Entity를 직접 사용
@RestController
public class UserController {
    @PostMapping("/users")
    public User createUser(@RequestBody User user) {
        // User Entity에 비밀번호, 내부 ID 등이 포함되어 노출됨
        return userService.save(user);
    }
}

// ✅ 좋은 예: DTO 사용
@RestController
public class UserController {
    @PostMapping("/users")
    public UserResponseDto createUser(@RequestBody UserCreateDto dto) {
        // 필요한 데이터만 전달
        return userService.create(dto);
    }
}
```

##### 1.2 DTO를 사용하는 이유

**1. 보안성 향상**
- 민감한 정보(비밀번호, 내부 ID 등) 노출 방지
- 클라이언트에 필요한 정보만 전달

**2. 계층 간 독립성**
- 각 계층의 변경이 다른 계층에 영향을 주지 않음
- DB 스키마 변경 시 DTO만 수정

**3. 유연성**
- 계층별로 필요한 데이터만 포함
- 다양한 요구사항에 대응 가능

**4. 명확성**
- 데이터 전달 목적이 명확함
- 코드 가독성 향상

---

#### 2. DTO 사용 예시

##### 2.1 기본 DTO 구조

```java
// 요청 DTO
public class UserLoginDto {
    private String id;
    private String password;
    
    // 기본 생성자
    public UserLoginDto() {}
    
    // Getter, Setter
    public String getId() { return id; }
    public void setId(String id) { this.id = id; }
    
    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }
}

// 응답 DTO
public class UserResponseDto {
    private Long id;
    private String name;
    private String email;
    
    // Getter, Setter
    // ...
}
```

##### 2.2 DTO와 Entity 변환

```java
// 게시글 생성 DTO
public class CreateDTO {
    private String userId;
    private String title;
    private String contents;
    private String fileName;
    
    // DTO → Entity 변환 메서드
    public Board toEntity() {
        return Board.builder()
                .memberEmail(userId)
                .title(title)
                .contents(contents)
                .fileName(fileName)
                .build();
    }
}

// 사용 예시
@PostMapping("/boards")
public ResponseEntity<BoardResponseDto> createBoard(
        @RequestBody CreateDTO dto) {
    Board board = dto.toEntity();  // DTO → Entity 변환
    Board saved = boardService.save(board);
    return ResponseEntity.ok(BoardResponseDto.from(saved));
}
```

##### 2.3 JSON 요청 예시

```json
// 클라이언트 요청
POST /boards
Content-Type: application/json
{
  "title": "게시글 제목",
  "contents": "본문 내용",
  "userId": "abc@example.com"
}
```

---

#### 3. Inner Class를 활용한 DTO 구성

##### 3.1 Inner Class란?

**Inner Class(중첩 클래스)**는 클래스 안에 클래스를 정의하는 것입니다. DTO를 기능이나 목적별로 그룹화하여 관리할 수 있습니다.

##### 3.2 Inner Class를 사용하는 이유

**1. 기능별 그룹화**
- 연관된 DTO들을 하나로 묶기
- 예: `BoardRequest.CreateDTO`, `BoardRequest.UpdateDTO`

**2. 네임스페이스 관리**
- DTO 클래스가 많아질 때 도메인별로 정리
- 클래스 이름 충돌 방지

**3. 명확한 목적 표현**
- `BoardRequest`는 "게시글에 대한 요청 객체들"을 모아둔 클래스

##### 3.3 Inner Class DTO 예시

```java
// 게시글 요청 DTO 그룹
public class BoardRequest {
    
    // 게시글 생성 DTO
    public static class CreateDTO {
        private String userId;
        private String title;
        private String contents;
        private String fileName;
        
        public Board toEntity() {
            return Board.builder()
                    .memberEmail(userId)
                    .title(title)
                    .contents(contents)
                    .fileName(fileName)
                    .build();
        }
    }
    
    // 게시글 수정 DTO
    public static class UpdateDTO {
        private String title;
        private String contents;
        private String fileName;
        
        // 수정 로직
    }
}

// 사용 예시
@PostMapping("/boards")
public ResponseEntity<BoardResponseDto> createBoard(
        @RequestBody BoardRequest.CreateDTO dto) {
    // ...
}

@PutMapping("/boards/{id}")
public ResponseEntity<BoardResponseDto> updateBoard(
        @PathVariable Long id,
        @RequestBody BoardRequest.UpdateDTO dto) {
    // ...
}
```

**장점**:
- 관련 DTO들을 논리적으로 그룹화
- 클래스 이름이 명확함 (`BoardRequest.CreateDTO`)
- 도메인별로 관리 용이

---

#### 4. 빌더 패턴 (Builder Pattern)

##### 4.1 빌더 패턴이란?

**빌더 패턴**은 객체 생성 시 생성자 대신 가독성 좋고 유연하게 생성할 수 있는 방식입니다.

##### 4.2 기존 방식의 문제점

```java
// ❌ 기존 생성자 방식
User user = new User("abc@example.com", "최지원", 30);
// 문제점:
// 1. 파라미터 순서를 외워야 함
// 2. 어떤 값이 어떤 필드인지 불명확
// 3. 선택적 파라미터 처리 어려움
// 4. 확장에 취약
```

##### 4.3 빌더 패턴 구현

```java
public class User {
    private String name;
    private String email;
    private int age;
    
    // private 생성자: 외부에서 new 못하게 막음
    private User(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.age = builder.age;
    }
    
    // 내부 static Builder 클래스
    public static class Builder {
        private String name;
        private String email;
        private int age;
        
        public Builder name(String name) {
            this.name = name;
            return this;  // 체이닝을 위한 자기 자신 반환
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
    
    // Builder 인스턴스 생성 메서드
    public static Builder builder() {
        return new Builder();
    }
}

// 사용 예시
User user = User.builder()
    .name("최지원")
    .email("abc@example.com")
    .age(22)
    .build();
```

##### 4.4 빌더 패턴의 장점

| 구분 | 기존 생성자 방식 | 빌더 패턴 |
|------|-----------------|-----------|
| **가독성** | 파라미터 순서 외우기 힘듦 | 어떤 값이 어떤 필드인지 명확 |
| **유연성** | 선택적 파라미터 어려움 | 필요한 값만 설정 가능 |
| **확장성** | 확장에 취약 | 유연하고 읽기 쉬움 |
| **안전성** | 파라미터 순서 실수 가능 | 메서드 이름으로 명확히 구분 |

**추가 장점**:
- 선택적 파라미터 처리 용이
- 불변 객체 생성 가능
- 복잡한 객체 생성 시 유용

---

#### 5. Lombok의 @Builder 어노테이션

##### 5.1 @Builder란?

**@Builder**는 Lombok이 제공하는 기능으로, 클래스에 붙이면 빌더 메서드를 자동 생성해줍니다. 수동으로 빌더 패턴을 구현할 필요 없이 어노테이션만으로 사용할 수 있습니다.

##### 5.2 @Builder 사용 예시

```java
import lombok.Builder;
import lombok.Getter;
import lombok.Setter;

@Builder
@Getter
@Setter
public class User {
    private String name;
    private String email;
    private int age;
}

// 사용 예시
User user = User.builder()
    .name("최지원")
    .email("abc@example.com")
    .age(22)
    .build();
```

##### 5.3 @Builder의 장점

**1. 코드 간결성**
- 수동으로 빌더 클래스를 작성할 필요 없음
- 보일러플레이트 코드 제거

**2. 자동 생성**
- 컴파일 시 빌더 클래스 자동 생성
- 유지보수 불필요

**3. 유연한 사용**
- 필요한 필드만 설정 가능
- 메서드 체이닝 지원

##### 5.4 @Builder 고급 사용법

```java
// 1. 특정 필드만 빌더에 포함
@Builder
public class User {
    @Builder.Default
    private String status = "ACTIVE";  // 기본값 설정
    
    private String name;
    private String email;
    
    @Builder.Exclude
    private Long id;  // 빌더에서 제외
}

// 2. toBuilder 옵션
@Builder(toBuilder = true)
public class User {
    private String name;
    private String email;
}

// 사용: 기존 객체를 복사하여 새 객체 생성
User user1 = User.builder()
    .name("홍길동")
    .email("hong@example.com")
    .build();

User user2 = user1.toBuilder()
    .name("김철수")  // name만 변경
    .build();

// 3. 클래스 레벨이 아닌 메서드 레벨 사용
public class User {
    private String name;
    private String email;
    
    @Builder
    public static User create(String name, String email) {
        User user = new User();
        user.name = name;
        user.email = email;
        return user;
    }
}
```

##### 5.5 @Builder vs 수동 빌더

| 구분 | @Builder | 수동 빌더 |
|------|----------|-----------|
| **코드 양** | 적음 (어노테이션만) | 많음 (빌더 클래스 구현) |
| **커스터마이징** | 제한적 | 자유롭게 가능 |
| **유지보수** | 자동 생성 | 수동 관리 |
| **학습 곡선** | 낮음 | 높음 |

---

#### 6. DTO에서 빌더 패턴 활용

##### 6.1 DTO에 @Builder 적용

```java
@Builder
@Getter
@Setter
public class UserCreateDto {
    private String name;
    private String email;
    private String password;
    
    public User toEntity() {
        return User.builder()
                .name(this.name)
                .email(this.email)
                .password(this.password)
                .build();
    }
}

// 사용 예시
@PostMapping("/users")
public ResponseEntity<UserResponseDto> createUser(
        @RequestBody UserCreateDto dto) {
    User user = dto.toEntity();
    User saved = userService.save(user);
    return ResponseEntity.ok(UserResponseDto.from(saved));
}
```

##### 6.2 Entity에서 DTO로 변환

```java
// 응답 DTO
@Builder
@Getter
public class UserResponseDto {
    private Long id;
    private String name;
    private String email;
    
    // Entity → DTO 변환 메서드
    public static UserResponseDto from(User user) {
        return UserResponseDto.builder()
                .id(user.getId())
                .name(user.getName())
                .email(user.getEmail())
                .build();
    }
}
```

---

### **Interview Answer Version (면접 답변식 요약)**

DTO(Data Transfer Object)는 계층 간 데이터 교환을 위한 순수 데이터를 담는 용도의 객체입니다. 비즈니스 로직 없이 데이터만 보관하며, 주로 Controller, Service, Repository 계층 사이에서 사용됩니다.

DTO가 필요한 이유는 Entity를 직접 사용하면 DB 스키마 변경 시 모든 계층에 영향을 주고, 불필요한 정보가 노출될 수 있기 때문입니다. DTO를 사용하면 각 계층의 독립성을 유지하고, 필요한 데이터만 선택적으로 전달하여 보안성과 유연성을 높일 수 있습니다.

Inner Class를 활용하면 연관된 DTO들을 기능별로 그룹화하여 관리할 수 있으며, 예를 들어 `BoardRequest.CreateDTO`, `BoardRequest.UpdateDTO`처럼 구성할 수 있습니다.

빌더 패턴은 객체 생성 시 생성자 대신 가독성 좋고 유연하게 생성할 수 있는 방식으로, Lombok의 @Builder 어노테이션을 사용하면 빌더 메서드를 자동 생성할 수 있습니다. 이를 통해 코드를 간결하게 유지하면서도 객체 생성을 유연하게 할 수 있습니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### DTO 설계 시 주의사항

**1. 불필요한 필드 포함 지양**
```java
// ❌ 나쁜 예: 불필요한 필드 포함
public class UserCreateDto {
    private String name;
    private String email;
    private String password;
    private Long id;  // 생성 시점에는 불필요
    private LocalDateTime createdAt;  // 서버에서 생성
}

// ✅ 좋은 예: 필요한 필드만
public class UserCreateDto {
    private String name;
    private String email;
    private String password;
}
```

**2. Entity와의 변환 메서드 제공**
```java
// DTO → Entity
public class UserCreateDto {
    public User toEntity() {
        return User.builder()
                .name(this.name)
                .email(this.email)
                .password(this.password)
                .build();
    }
}

// Entity → DTO
public class UserResponseDto {
    public static UserResponseDto from(User user) {
        return UserResponseDto.builder()
                .id(user.getId())
                .name(user.getName())
                .email(user.getEmail())
                .build();
    }
}
```

**3. 검증 로직 분리**
```java
// ❌ 나쁜 예: DTO에 비즈니스 로직 포함
public class UserCreateDto {
    public boolean isValid() {
        // 비즈니스 로직...
        return true;
    }
}

// ✅ 좋은 예: 별도 Validator 사용
@Valid
public class UserCreateDto {
    @NotBlank
    private String name;
    
    @Email
    private String email;
}
```

#### 실무 활용 예시

**1. 계층별 DTO 분리**
```java
// Controller → Service
public class UserCreateRequest {
    private String name;
    private String email;
    private String password;
}

// Service → Repository (Entity 사용)
// Entity는 Repository 계층에서만 사용

// Service → Controller
public class UserResponse {
    private Long id;
    private String name;
    private String email;
}
```

**2. MapStruct를 활용한 변환**
```java
@Mapper
public interface UserMapper {
    UserMapper INSTANCE = Mappers.getMapper(UserMapper.class);
    
    User toEntity(UserCreateDto dto);
    UserResponseDto toDto(User user);
}

// 사용
User user = UserMapper.INSTANCE.toEntity(dto);
```

**3. Inner Class로 그룹화**
```java
public class BoardRequest {
    @Builder
    @Getter
    public static class Create {
        private String title;
        private String contents;
    }
    
    @Builder
    @Getter
    public static class Update {
        private String title;
        private String contents;
    }
}
```

#### Lombok @Builder 주의사항

**1. 상속 관계에서의 주의**
```java
// ❌ 문제: 상속 시 빌더가 제대로 작동하지 않음
@Builder
public class Parent {
    private String parentField;
}

@Builder
public class Child extends Parent {
    private String childField;
}

// ✅ 해결: @SuperBuilder 사용
@SuperBuilder
public class Parent {
    private String parentField;
}

@SuperBuilder
public class Child extends Parent {
    private String childField;
}
```

**2. 불변 객체 생성**
```java
@Builder
@Getter
public class User {
    private final String name;  // final 필드 사용
    private final String email;
    
    // 불변 객체로 생성 가능
}
```

---

### 예상 꼬리질문정리

1. **DTO와 Entity의 차이는 무엇인가요?**
   - Entity는 JPA와 매핑되는 데이터베이스 테이블과 대응되는 객체이며, 비즈니스 로직을 포함할 수 있습니다. DTO는 계층 간 데이터 전달만을 위한 순수 데이터 객체로, 비즈니스 로직을 포함하지 않습니다.

2. **DTO와 VO(Value Object)의 차이는 무엇인가요?**
   - DTO는 데이터 전달 목적이며 가변(mutable)할 수 있습니다. VO는 값 자체를 표현하는 불변(immutable) 객체로, equals()와 hashCode()를 오버라이딩하여 값 비교를 합니다.

3. **왜 Entity를 직접 사용하지 않고 DTO를 사용하나요?**
   - Entity는 DB 스키마와 밀접하게 연결되어 있어 변경 시 모든 계층에 영향을 줍니다. 또한 민감한 정보나 불필요한 필드가 노출될 수 있으며, 순환 참조 문제가 발생할 수 있습니다. DTO는 이러한 문제를 해결합니다.

4. **빌더 패턴의 장점은 무엇인가요?**
   - 가독성이 좋고, 선택적 파라미터 처리가 용이하며, 확장에 유연합니다. 또한 메서드 체이닝을 통해 코드가 읽기 쉬워집니다.

5. **@Builder와 수동 빌더 구현의 차이는 무엇인가요?**
   - @Builder는 Lombok이 자동으로 빌더 클래스를 생성하여 코드가 간결하지만 커스터마이징이 제한적입니다. 수동 빌더는 자유롭게 커스터마이징할 수 있지만 코드 양이 많고 유지보수가 필요합니다.

6. **Inner Class를 사용한 DTO 구성의 장단점은 무엇인가요?**
   - 장점: 관련 DTO들을 논리적으로 그룹화하고, 클래스 이름이 명확하며, 도메인별로 관리가 용이합니다. 단점: 클래스 구조가 복잡해질 수 있고, 접근이 다소 번거로울 수 있습니다.

7. **DTO 변환을 자동화하는 방법은 무엇인가요?**
   - MapStruct, ModelMapper, Dozer 등의 라이브러리를 사용할 수 있습니다. MapStruct는 컴파일 타임에 변환 코드를 생성하여 성능이 좋고, ModelMapper는 런타임에 리플렉션을 사용합니다.

8. **@Builder에서 @Builder.Default의 역할은 무엇인가요?**
   - 필드에 기본값을 설정할 수 있게 해주는 어노테이션입니다. 빌더로 객체를 생성할 때 해당 필드를 설정하지 않으면 기본값이 사용됩니다.

9. **DTO에 검증 로직을 넣어도 되나요?**
   - 기본적인 입력 검증(Bean Validation)은 가능하지만, 비즈니스 로직은 포함하지 않는 것이 좋습니다. 비즈니스 로직은 Service 계층에서 처리해야 합니다.

10. **DTO를 사용할 때 성능 이슈가 있나요?**
    - Entity와 DTO 간 변환 과정에서 약간의 오버헤드가 발생할 수 있지만, 대부분의 경우 무시할 수 있는 수준입니다. MapStruct 같은 컴파일 타임 변환 라이브러리를 사용하면 성능 이슈를 최소화할 수 있습니다.

---

## 관련 노트

- **[[Entity]]** - DTO와 구분되는 데이터베이스 매핑 객체
- **[[VO (Value Object)|VO]]** - DTO와 유사하지만 불변 객체인 Value Object
- **[[빌더 패턴]]** - 객체 생성을 위한 디자인 패턴
- **[[Lombok]]** - @Builder를 제공하는 라이브러리

---

**태그:** #spring #dto #data-transfer-object #빌더패턴 #lombok #면접 #개념정리

