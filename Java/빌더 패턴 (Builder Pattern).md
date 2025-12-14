#java #design-pattern #builder #dto #immutable #면접 #개념정리

**관련 개념:** [[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]] [[SOLID 원칙 (객체지향 설계 원칙)|SOLID 원칙]]

> [!note] 이어서 읽기
> 빌더 패턴을 이해하려면 **[[객체지향 프로그래밍 (OOP)|객체지향 프로그래밍]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**빌더 패턴(Builder Pattern)** 은 복잡한 객체의 생성 과정과 표현 방법을 분리하여 다양한 구성의 인스턴스를 만드는 생성 패턴이다. 생성자에 들어갈 매개 변수를 메서드로 하나하나 받아들이고 마지막에 통합 빌드해서 객체를 생성하는 방식이다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 생성자 호출 시, 많은 매개변수가 있을 때, 각 인자가 어떤 필드에 해당하는지 파악하기 어렵고, 매개변수 순서를 따라야 하며, 선택적 매개변수를 생략할 수 없어 가독성과 유연성이 떨어집니다.

- 빌더 패턴은 객체 생성 과정을 일관된 프로세스로 표현하고, 필수 매개변수와 선택적 매개변수를 분리하여 가독성을 향상시킵니다. 또한 불변 객체를 만들 수 있어 스레드 안전성과 안정성을 보장합니다.

- DTO(Data Transfer Object)를 설계할 때, 많은 필드를 가진 객체를 안전하고 유연하게 생성할 수 있으며, 코드의 가독성과 유지보수성을 크게 향상시킵니다.

---

## 1. 빌더 패턴 탄생 배경

### **점층적 생성자 패턴 (Telescoping Constructor Pattern)**

점층적 생성자 패턴은 필수 매개변수와 함께 선택 매개변수를 0개, 1개, 2개... 받는 형태로, 다양한 매개변수를 입력받아 인스턴스를 생성하고 싶을 때 사용하던 생성자를 오버로딩 하는 방식입니다.

```java
class Post {
    // 필수 매개변수
    private String title;
    private String content;
    private String author;

    // 선택 매개변수
    private String category;
    private String tags;
    private boolean isPublic;
    private int viewCount;

    public Post(String title, String content, String author, 
                String category, String tags, boolean isPublic, int viewCount) {
        this.title = title;
        this.content = content;
        this.author = author;
        this.category = category;
        this.tags = tags;
        this.isPublic = isPublic;
        this.viewCount = viewCount;
    }

    public Post(String title, String content, String author, 
                String category, String tags, boolean isPublic) {
        this(title, content, author, category, tags, isPublic, 0);
    }

    public Post(String title, String content, String author, String category) {
        this(title, content, author, category, null, true, 0);
    }

    public Post(String title, String content, String author) {
        this(title, content, author, null, null, true, 0);
    }
}
```

```java
public static void main(String[] args) {
    // 모든 필드가 있는 게시글
    Post post1 = new Post("제목", "내용", "작성자", "공지사항", "태그1,태그2", true, 0);

    // 제목, 내용, 작성자만 있는 게시글
    Post post2 = new Post("제목", "내용", "작성자");

    // 카테고리만 있는 게시글 (나머지는 null이나 0으로 채워야 함)
    Post post3 = new Post("제목", "내용", "작성자", "공지사항", null, true, 0);
}
```

**문제점**:

1. **가독성 문제**: 몇 번째 인자가 어떤 필드인지 파악하기 어려움
2. **유연성 부족**: 선택적 필드를 생략하려면 null이나 0을 전달해야 함
3. **유지보수 어려움**: 생성자 메서드 수가 기하급수적으로 늘어남

### **자바 빈(Java Beans) 패턴**

Setter 메소드를 사용한 자바 빈 패턴은 매개변수가 없는 생성자로 객체 생성 후 Setter 메소드를 이용해 클래스 필드의 초깃값을 설정하는 방식입니다.

```java
class Post {
    private String title;
    private String content;
    private String author;
    private String category;
    private String tags;
    private boolean isPublic;
    private int viewCount;
    
    public Post() {}

    public void setTitle(String title) { this.title = title; }
    public void setContent(String content) { this.content = content; }
    public void setAuthor(String author) { this.author = author; }
    public void setCategory(String category) { this.category = category; }
    public void setTags(String tags) { this.tags = tags; }
    public void setIsPublic(boolean isPublic) { this.isPublic = isPublic; }
    public void setViewCount(int viewCount) { this.viewCount = viewCount; }
}
```

```java
public static void main(String[] args) {
    Post post = new Post();
    post.setTitle("제목");
    post.setContent("내용");
    post.setAuthor("작성자");
    post.setCategory("공지사항");
    // 문제점: 필수 필드(title, content)를 설정하지 않아도 객체가 생성됨
}
```

**문제점**:

1. **일관성 문제**: 필수 필드를 설정하지 않아도 객체가 생성되어 유효하지 않은 상태가 됨
2. **불변성 문제**: Setter 메소드가 노출되어 언제든지 객체를 조작할 수 있음

---

## 2. 빌더 패턴이란?

빌더 패턴은 이러한 문제들을 해결하기 위해 별도의 Builder 클래스를 만들어 메소드를 통해 step-by-step으로 값을 입력받은 후에 최종적으로 `build()` 메소드로 하나의 인스턴스를 생성하여 리턴하는 패턴입니다.

```java
public static void main(String[] args) {
    // 생성자 방식
    Post post = new Post("제목", "내용", "작성자", "공지사항", null, true, 0);

    // 빌더 방식
    Post post = new Post.Builder("제목", "내용", "작성자")
        .category("공지사항")
        .tags("태그1,태그2")
        .isPublic(true)
        .build();
}
```

빌더 패턴을 이용하면 더 이상 생성자 오버로딩 열거를 하지 않아도 되며, 데이터의 순서에 상관없이 객체를 만들어내 생성자 인자 순서를 파악할 필요도 없고 잘못된 값을 넣는 실수도 하지 않게 됩니다.

---

## 3. 빌더 패턴 구조

### **빌더 클래스 구현하기**

게시글(Post) 클래스에 대한 빌더를 구현해보겠습니다.

```java
class Post {
    private final String title;
    private final String content;
    private final String author;
    private final String category;
    private final String tags;
    private final boolean isPublic;
    private final int viewCount;

    private Post(Builder builder) {
        this.title = builder.title;
        this.content = builder.content;
        this.author = builder.author;
        this.category = builder.category;
        this.tags = builder.tags;
        this.isPublic = builder.isPublic;
        this.viewCount = builder.viewCount;
    }

    public static class Builder {
        // 필수 매개변수
        private final String title;
        private final String content;
        private final String author;

        // 선택 매개변수
        private String category;
        private String tags;
        private boolean isPublic = true; // 디폴트 값
        private int viewCount = 0; // 디폴트 값

        // 필수 매개변수는 빌더 생성자로 받음
        public Builder(String title, String content, String author) {
            this.title = title;
            this.content = content;
            this.author = author;
        }

        // 선택 매개변수는 메서드로 설정
        public Builder category(String category) {
            this.category = category;
            return this; // 체이닝을 위해 this 반환
        }

        public Builder tags(String tags) {
            this.tags = tags;
            return this;
        }

        public Builder isPublic(boolean isPublic) {
            this.isPublic = isPublic;
            return this;
        }

        public Builder viewCount(int viewCount) {
            this.viewCount = viewCount;
            return this;
        }

        // 최종 객체 생성
        public Post build() {
            return new Post(this);
        }
    }
}
```

### **빌더 클래스 사용하기**

```java
public static void main(String[] args) {
    // 필수 필드만 있는 게시글
    Post post1 = new Post.Builder("제목", "내용", "작성자")
            .build();

    // 카테고리와 태그가 있는 게시글
    Post post2 = new Post.Builder("제목", "내용", "작성자")
            .category("공지사항")
            .tags("태그1,태그2")
            .build();

    // 모든 필드가 있는 게시글
    Post post3 = new Post.Builder("제목", "내용", "작성자")
            .category("공지사항")
            .tags("태그1,태그2")
            .isPublic(false)
            .viewCount(100)
            .build();
}
```

> [!note] return this의 의미
> 각 메서드에서 `return this`를 반환함으로써 메서드 체이닝(Chaining)이 가능해집니다. 이를 통해 연속적으로 빌더 메서드들을 호출할 수 있습니다.

---

## 4. 빌더 패턴 장점

### **1. 가독성 향상**

생성자 방식은 매개변수가 많아질수록 가독성이 급격하게 떨어집니다. 빌더 패턴을 적용하면 어떤 필드에 어떤 값이 설정되는지 한눈에 파악할 수 있습니다.

```java
// 생성자 방식 - 각 인자가 무엇인지 파악 어려움
Post post1 = new Post("제목", "내용", "작성자", "공지사항", "태그", true, 0);

// 빌더 방식 - 명확하게 어떤 필드에 값이 설정되는지 알 수 있음
Post post2 = new Post.Builder("제목", "내용", "작성자")
        .category("공지사항")
        .tags("태그")
        .isPublic(true)
        .viewCount(0)
        .build();
```

### **2. 필수/선택 매개변수 분리**

필수 매개변수는 빌더 생성자로 받고, 선택적 매개변수는 메서드로 받아 명확하게 구분할 수 있습니다.

```java
// 필수 필드만 설정
Post post1 = new Post.Builder("제목", "내용", "작성자").build();

// 선택 필드 추가
Post post2 = new Post.Builder("제목", "내용", "작성자")
        .category("공지사항")
        .build();
```

### **3. 디폴트 값 설정**

선택적 필드에 디폴트 값을 설정할 수 있어, 해당 메서드를 호출하지 않아도 기본값이 적용됩니다.

```java
class Builder {
    private boolean isPublic = true; // 디폴트 값
    private int viewCount = 0; // 디폴트 값
    
    // isPublic()을 호출하지 않으면 기본적으로 true
}
```

### **4. 유효성 검증 분리**

각 필드별로 유효성 검증을 분리하여 작성할 수 있습니다.

```java
public Builder title(String title) {
    if (title == null || title.isEmpty()) {
        throw new IllegalArgumentException("제목은 필수입니다.");
    }
    if (title.length() > 100) {
        throw new IllegalArgumentException("제목은 100자 이하여야 합니다.");
    }
    this.title = title;
    return this;
}
```

### **5. 불변성 보장**

빌더 패턴을 사용하면 `final` 키워드를 사용하여 불변 객체를 만들 수 있어 스레드 안전성과 안정성을 보장합니다.

```java
class Post {
    private final String title; // final로 불변 보장
    private final String content;
    // Setter 메서드 없음 → 객체 생성 후 변경 불가
}
```

---

## 5. 빌더 패턴 단점

### **1. 코드 복잡성 증가**

빌더 패턴을 적용하려면 N개의 클래스에 대해 N개의 새로운 빌더 클래스를 만들어야 해서, 클래스 수가 늘어나고 구조가 복잡해질 수 있습니다.

다만 **Lombok의 `@Builder` 어노테이션**을 사용하면 빌더 클래스를 직접 작성할 필요가 없어 코드 복잡성이 크게 상쇄됩니다. 50줄 이상의 빌더 코드가 어노테이션 1줄로 대체되며, 필드 추가 시에도 자동으로 빌더 메서드가 생성되어 유지보수 부담이 최소화됩니다.

하지만 Lombok 사용 시에도 완전히 사라지지 않는 부분이 있습니다:
- **외부 라이브러리 의존성**: Lombok 라이브러리를 프로젝트에 추가해야 함
- **IDE 설정 필요**: IntelliJ, Eclipse 등에서 Lombok 플러그인 설치 및 의존성 설정 필요
- **컴파일 타임 의존**: Lombok이 컴파일 시 코드를 생성하므로, Lombok 없이는 컴파일이 불가능함
- **디버깅 복잡도**: 생성된 코드를 직접 볼 수 없어 디버깅 시 약간의 어려움이 있을 수 있음

### **2. 성능 저하**

매번 메서드를 호출하여 빌더를 거쳐 인스턴스화 하기 때문에 생성자보다는 약간의 성능 저하가 있습니다. 다만 일반적인 상황에서는 무시할 수 있는 수준입니다.

### **3. 지나친 남용 금지**

클래스의 필드가 4개보다 적고, 필드의 변경 가능성이 없는 경우라면 차라리 생성자나 정적 팩토리 메소드를 이용하는 것이 더 좋을 수 있습니다.

---

## 6. DTO 관점에서의 Builder 패턴

### **DTO에서 Builder 패턴을 사용하는 이유**

1. **많은 필드 관리**: DTO는 보통 많은 필드를 가지며, 각 필드가 선택적일 수 있습니다
2. **가독성 향상**: API 요청/응답 시 어떤 필드에 어떤 값이 들어가는지 명확하게 알 수 있습니다
3. **불변성 보장**: DTO는 데이터 전송 후 변경되면 안 되므로 불변 객체로 만드는 것이 안전합니다
4. **유효성 검증**: 빌더에서 각 필드별로 유효성 검증을 수행할 수 있습니다

### **DTO Builder 패턴 예시**

```java
public class PostCreateRequest {
    private final String title;
    private final String content;
    private final String author;
    private final String category;
    private final String tags;
    private final boolean isPublic;

    private PostCreateRequest(Builder builder) {
        this.title = builder.title;
        this.content = builder.content;
        this.author = builder.author;
        this.category = builder.category;
        this.tags = builder.tags;
        this.isPublic = builder.isPublic;
    }

    public static class Builder {
        private final String title; // 필수
        private final String content; // 필수
        private final String author; // 필수
        private String category;
        private String tags;
        private boolean isPublic = true;

        public Builder(String title, String content, String author) {
            if (title == null || title.isEmpty()) {
                throw new IllegalArgumentException("제목은 필수입니다.");
            }
            if (content == null || content.isEmpty()) {
                throw new IllegalArgumentException("내용은 필수입니다.");
            }
            this.title = title;
            this.content = content;
            this.author = author;
        }

        public Builder category(String category) {
            this.category = category;
            return this;
        }

        public Builder tags(String tags) {
            this.tags = tags;
            return this;
        }

        public Builder isPublic(boolean isPublic) {
            this.isPublic = isPublic;
            return this;
        }

        public PostCreateRequest build() {
            return new PostCreateRequest(this);
        }
    }

    // Getters
    public String getTitle() { return title; }
    public String getContent() { return content; }
    public String getAuthor() { return author; }
    public String getCategory() { return category; }
    public String getTags() { return tags; }
    public boolean isPublic() { return isPublic; }
}
```

**사용 예시**:

```java
// Controller에서 사용
@PostMapping("/posts")
public ResponseEntity<Post> createPost(@RequestBody PostCreateRequest request) {
    Post post = postService.createPost(request);
    return ResponseEntity.ok(post);
}

// 클라이언트에서 요청 생성
PostCreateRequest request = new PostCreateRequest.Builder("제목", "내용", "작성자")
    .category("공지사항")
    .tags("태그1,태그2")
    .isPublic(true)
    .build();
```

---

## 7. 기본 객체 사용과 Builder 사용의 차이점

### **비교 예시**

#### **생성자 방식**

```java
// 생성자 방식
Post post = new Post("제목", "내용", "작성자", "공지사항", "태그", true, 0);
// 문제점: 각 인자가 무엇인지 파악하기 어려움
```

#### **Setter 방식**

```java
// Setter 방식
Post post = new Post();
post.setTitle("제목");
post.setContent("내용");
post.setAuthor("작성자");
// 문제점: 일관성 문제, 불변성 문제
```

#### **Builder 방식**

```java
// Builder 방식
Post post = new Post.Builder("제목", "내용", "작성자")
    .category("공지사항")
    .tags("태그")
    .isPublic(true)
    .viewCount(0)
    .build();
// 장점: 가독성, 불변성, 유연성 모두 확보
```

### **주요 차이점 요약**

| 항목 | 생성자 방식 | Setter 방식 | Builder 방식 |
|------|------------|------------|-------------|
| **가독성** | 낮음 | 중간 | 높음 |
| **불변성** | 가능 | 불가능 | 가능 |
| **일관성** | 보장됨 | 보장 안 됨 | 보장됨 |
| **유연성** | 낮음 | 높음 | 높음 |
| **코드 복잡도** | 낮음 | 낮음 | 높음 |
| **성능** | 높음 | 높음 | 약간 낮음 |

---

## 8. Lombok의 @Builder

개발자가 좀 더 편하게 빌더 패턴을 이용하기 위해 Lombok에서는 `@Builder` 어노테이션을 지원합니다. 클래스에 어노테이션만 붙여주면 컴파일 시 자동으로 빌더 API가 생성됩니다.

### **기본 사용법**

```java
import lombok.Builder;
import lombok.AllArgsConstructor;
import lombok.AccessLevel;

@Builder
@AllArgsConstructor(access = AccessLevel.PRIVATE)
public class Post {
    private final String title;
    private final String content;
    private final String author;
    private final String category;
    private final String tags;
    private final boolean isPublic;
    private final int viewCount;
}
```

```java
public static void main(String[] args) {
    Post post = Post.builder()
            .title("제목")
            .content("내용")
            .author("작성자")
            .category("공지사항")
            .tags("태그1,태그2")
            .isPublic(true)
            .viewCount(0)
            .build();
}
```

### **필수 파라미터 설정**

`@Builder` 어노테이션으로는 필수 파라미터를 지정할 수 없으므로, 별도의 `builder()` 정적 메서드를 구현하여 필수 파라미터를 설정하도록 유도할 수 있습니다.

```java
@Builder
@AllArgsConstructor(access = AccessLevel.PRIVATE)
public class Post {
    private final String title;
    private final String content;
    private final String author;
    // ... 기타 필드들

    // 필수 파라미터 빌더 메서드 구현
    public static PostBuilder builder(String title, String content, String author) {
        if (title == null || content == null || author == null) {
            throw new IllegalArgumentException("필수 파라미터 누락");
        }
        return new PostBuilder().title(title).content(content).author(author);
    }
}
```

---

## 요약

- **빌더 패턴**은 복잡한 객체의 생성 과정과 표현 방법을 분리하여 다양한 구성의 인스턴스를 만드는 생성 패턴입니다.
- **점층적 생성자 패턴**과 **자바 빈 패턴**의 문제점을 해결하기 위해 등장했습니다.
- **장점**: 가독성 향상, 불변성 보장, 필수/선택 매개변수 분리, 유효성 검증 분리
- **단점**: 코드 복잡성 증가, 약간의 성능 저하, 지나친 남용 금지
- **DTO 관점**에서 빌더 패턴은 많은 필드를 가진 데이터 전송 객체를 안전하고 유연하게 생성할 수 있게 해줍니다.
- **Lombok의 @Builder**를 사용하면 보일러플레이트 코드를 줄일 수 있습니다.

---

## 참고 자료

- [Effective Java - Item 2: Builder Pattern](https://www.oracle.com/technical-resources/articles/java/josh-bloch.html)
- [GoF Design Patterns - Builder Pattern](https://refactoring.guru/design-patterns/builder)
- [Lombok @Builder Documentation](https://projectlombok.org/features/Builder)
