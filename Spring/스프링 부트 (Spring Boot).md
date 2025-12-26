#spring #spring-boot #java #auto-configuration #embedded-server #면접 #개념정리

**관련 개념:** [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]] [[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]

> [!note] 이어서 읽기
> 스프링 부트를 이해하려면 **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**스프링 부트(Spring Boot)** 는 스프링 프레임워크 기반의 애플리케이션을 더욱 쉽고 빠르게 개발할 수 있도록 도와주는 서브 프로젝트이다. 복잡한 설정을 자동화하고 내장 서버를 제공하여 개발자가 비즈니스 로직에 집중할 수 있게 해준다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 스프링 프레임워크만 사용하면 설정해야 할 라이브러리와 구성이 많아져, 개발자들이 설정에 많은 시간을 쓰게 됩니다. WAS 설치, WAR 파일 빌드, 의존성 버전 관리, Bean 설정 등 복잡한 초기 설정이 필요합니다.

- 스프링 부트는 이러한 복잡한 설정을 자동화하여 즉시 애플리케이션 개발을 시작할 수 있도록 돕습니다. 내장 서버를 제공하여 별도의 WAS 설치 없이 실행 가능하고, Starter 패키지로 의존성을 간편하게 관리하며, Auto Configuration으로 자주 사용하는 Bean을 자동으로 등록합니다.

- 스프링 부트의 등장 배경, 내장 서버의 장점, Starter 패키지와 버전 관리, Auto Configuration의 동작 원리를 이해해야 합니다.

---

## 1. 스프링 부트의 등장 배경

### **스프링 프레임워크만 사용했을 때의 문제점**

스프링 생태계가 커지면서 설정해야 할 라이브러리와 구성이 너무 많아져, 개발자들이 설정을 하는 데 너무 많은 시간을 쓰게 되었습니다.

**문제점:**

1. **복잡한 의존성 관리**
   - 각 라이브러리의 버전을 직접 관리해야 함
   - 라이브러리 간 호환성 문제 발생 가능
   - 의존성 충돌 해결이 어려움

2. **많은 XML/Java 설정**
   - DispatcherServlet, ViewResolver, DataSource 등 많은 Bean 설정 필요
   - 설정 파일이 복잡하고 길어짐

3. **WAS 설치 및 배포 복잡**
   - 별도의 톰캣 등 WAS를 설치해야 함
   - WAR 파일을 빌드하여 배포해야 함
   - 개발 환경과 운영 환경 설정이 다름

### **스프링 부트의 해결책**

스프링 부트는 이러한 복잡한 설정을 자동화하여 즉시 애플리케이션 개발을 시작할 수 있도록 돕는 서브 프로젝트입니다.

**핵심 목표:**
- **설정 최소화**: "Convention over Configuration" 원칙
- **즉시 실행 가능**: 메인 메서드만으로 웹 서버 구동
- **개발 생산성 향상**: 비즈니스 로직에 집중

---

## 2. 스프링 부트가 제공하는 3가지 핵심 편리함

### **2.1 내장 서버 (Embedded Server)**

#### **스프링 프레임워크 방식**

**과거 방식:**
- 별도의 WAS(톰캣 등)를 설치해야 함
- WAR 파일을 빌드하여 배포해야 함
- 개발 환경과 운영 환경 설정이 다름

**예시:**

```xml
<!-- pom.xml -->
<project>
    <packaging>war</packaging>
    <dependencies>
        <!-- 스프링 의존성 -->
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

```java
// web.xml 설정 필요
<web-app>
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>
    <!-- ... -->
</web-app>
```

**배포 과정:**
1. 톰캣 서버 설치
2. WAR 파일 빌드
3. 톰캣에 WAR 파일 배포
4. 서버 시작

#### **스프링 부트 방식**

**현재 방식:**
- 톰캣을 라이브러리로 내장
- 메인 메서드 실행만으로 웹 서버 구동
- JAR 파일로 실행 가능

**예시:**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
        // 메인 메서드 실행만으로 웹 서버 구동!
    }
}
```

**실행:**
```bash
java -jar application.jar
# 또는 IDE에서 main 메서드 실행
```

**장점:**
- ✅ 별도의 WAS 설치 불필요
- ✅ JAR 파일 하나로 실행 가능
- ✅ 개발 환경과 운영 환경이 동일
- ✅ 빠른 개발 시작

### **2.2 편리한 의존성 및 버전 관리**

#### **스프링 프레임워크 방식**

**과거 방식:**
- 각 라이브러리를 개별적으로 추가해야 함
- 버전을 직접 관리해야 함
- 라이브러리 간 호환성 문제 발생 가능

**예시:**

```xml
<!-- pom.xml -->
<dependencies>
    <!-- 스프링 MVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.21</version>
    </dependency>
    
    <!-- 스프링 JDBC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.3.21</version>
    </dependency>
    
    <!-- Hibernate -->
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-core</artifactId>
        <version>5.6.9</version>
    </dependency>
    
    <!-- Jackson -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.13.3</version>
    </dependency>
    
    <!-- 톰캣 -->
    <dependency>
        <groupId>org.apache.tomcat.embed</groupId>
        <artifactId>tomcat-embed-core</artifactId>
        <version>9.0.65</version>
    </dependency>
    
    <!-- ... 수많은 의존성을 하나씩 추가 -->
</dependencies>
```

**문제점:**
- 의존성을 하나씩 추가해야 함
- 버전 호환성을 직접 확인해야 함
- 버전 충돌 발생 시 해결이 어려움

#### **스프링 부트 방식**

**현재 방식:**
- **Starter 패키지**: 한 줄의 설정으로 관련된 수많은 라이브러리를 한꺼번에 가져옴
- **버전 자동 관리**: 라이브러리 간의 호환성을 고려하여 최적의 버전을 스프링 부트가 알아서 관리

**예시:**

```xml
<!-- pom.xml -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.7.0</version>
</parent>

<dependencies>
    <!-- 웹 개발에 필요한 모든 라이브러리를 한 번에 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- JPA 개발에 필요한 모든 라이브러리를 한 번에 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
</dependencies>
```

**Starter 패키지가 포함하는 것들:**

`spring-boot-starter-web` 하나로 다음이 모두 포함됨:
- Spring MVC
- Tomcat (내장 서버)
- Jackson (JSON 처리)
- Validation
- 기타 웹 개발에 필요한 라이브러리들

**장점:**
- ✅ 한 줄로 필요한 모든 라이브러리 추가
- ✅ 버전 호환성 자동 관리
- ✅ 버전 충돌 문제 해결
- ✅ 의존성 관리 간소화

### **2.3 자동 구성 (Auto Configuration)**

#### **스프링 프레임워크 방식**

**과거 방식:**
- 자주 사용하는 Bean을 직접 설정해야 함
- 설정 파일이 길고 복잡해짐

**예시:**

```java
@Configuration
public class AppConfig {
    
    // DataSource 설정
    @Bean
    public DataSource dataSource() {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setJdbcUrl("jdbc:h2:mem:testdb");
        dataSource.setUsername("sa");
        dataSource.setPassword("");
        return dataSource;
    }
    
    // JdbcTemplate 설정
    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }
    
    // DispatcherServlet 설정
    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }
    
    // ViewResolver 설정
    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
    
    // ... 수많은 Bean 설정
}
```

**문제점:**
- 반복적인 Bean 설정 코드
- 설정 파일이 길고 복잡
- 비즈니스 로직에 집중하기 어려움

#### **스프링 부트 방식**

**현재 방식:**
- 일반적으로 자주 사용하는 Bean들을 스프링 부트가 자동으로 등록
- `@SpringBootApplication` 어노테이션 내의 `@EnableAutoConfiguration`을 통해 이 기능이 활성화

**예시:**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

**@SpringBootApplication의 구성:**

```java
@SpringBootApplication
= @Configuration
+ @EnableAutoConfiguration  // 자동 구성 활성화
+ @ComponentScan            // 컴포넌트 스캔
```

**Auto Configuration이 자동으로 등록하는 Bean들:**

- `DataSource`: 데이터베이스 연결
- `JdbcTemplate`: JDBC 작업
- `EntityManager`: JPA 작업
- `DispatcherServlet`: 스프링 MVC
- `ViewResolver`: 뷰 리졸버
- 기타 자주 사용하는 Bean들

**조건부 자동 구성:**

스프링 부트는 클래스패스에 특정 클래스가 있을 때만 자동 구성을 활성화합니다.

```java
@Configuration
@ConditionalOnClass(DataSource.class)  // DataSource 클래스가 있을 때만
public class DataSourceAutoConfiguration {
    
    @Bean
    @ConditionalOnMissingBean  // 이미 Bean이 없을 때만
    public DataSource dataSource() {
        // 자동으로 DataSource 생성
    }
}
```

**장점:**
- ✅ 반복적인 Bean 설정 코드 제거
- ✅ 설정 파일 간소화
- ✅ 비즈니스 로직에 집중 가능
- ✅ 필요 시 커스터마이징 가능

---

## 3. 스프링 vs 스프링 부트 비교

### **전체 비교표**

| 항목 | 스프링 프레임워크 | 스프링 부트 |
|------|------------------|------------|
| **서버** | 별도 WAS 설치 필요 | 내장 서버 (톰캣) |
| **배포** | WAR 파일 빌드 필요 | JAR 파일로 실행 |
| **의존성 관리** | 각 라이브러리 개별 추가 | Starter 패키지로 한 번에 |
| **버전 관리** | 직접 관리 | 자동 관리 |
| **Bean 설정** | 직접 설정 필요 | Auto Configuration |
| **설정 파일** | XML 또는 Java Config | application.properties/yml |
| **개발 속도** | 느림 (설정 시간 소요) | 빠름 (즉시 시작) |
| **학습 곡선** | 높음 | 낮음 |

### **설정 비교**

#### **스프링 프레임워크**

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
    
    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
    
    @Bean
    public DataSource dataSource() {
        // DataSource 설정
    }
    
    // ... 많은 설정
}
```

#### **스프링 부트**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

// application.properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=
```

### **의존성 비교**

#### **스프링 프레임워크**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.21</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.3.21</version>
    </dependency>
    <!-- ... 수많은 의존성 -->
</dependencies>
```

#### **스프링 부트**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- 끝! -->
</dependencies>
```

---

## 4. 스프링 부트의 핵심 철학

### **Convention over Configuration**

**설정보다 관례를 우선시**하는 원칙입니다.

- 개발자가 명시적으로 설정하지 않아도, 스프링 부트가 일반적인 관례를 따라 자동으로 설정
- 필요할 때만 커스터마이징

### **Opinionated Defaults**

**의견이 있는 기본값**을 제공합니다.

- 스프링 부트가 "이렇게 하는 게 좋다"고 제안하는 기본 설정
- 대부분의 경우 기본값으로 충분
- 필요 시 변경 가능

---

## 5. 스프링 부트 사용 시나리오

### **스프링 부트를 사용하면 좋은 경우**

- ✅ 빠른 프로토타이핑이 필요한 경우
- ✅ 마이크로서비스 아키텍처
- ✅ RESTful API 개발
- ✅ 최신 스프링 기능을 빠르게 사용하고 싶은 경우

### **스프링 프레임워크를 직접 사용하는 경우**

- ✅ 매우 세밀한 설정이 필요한 경우
- ✅ 레거시 시스템과의 통합
- ✅ 특정 버전의 라이브러리를 사용해야 하는 경우

---

## 요약

- **스프링 부트**는 스프링 프레임워크 기반의 애플리케이션을 더욱 쉽고 빠르게 개발할 수 있도록 도와주는 서브 프로젝트입니다.
- **내장 서버**: 톰캣을 라이브러리로 내장하여 메인 메서드 실행만으로 웹 서버 구동 가능
- **Starter 패키지**: 한 줄의 설정으로 관련된 수많은 라이브러리를 한꺼번에 가져오고 버전을 자동 관리
- **Auto Configuration**: 자주 사용하는 Bean을 자동으로 등록하여 설정 코드 최소화
- **스프링 부트의 철학**: Convention over Configuration, Opinionated Defaults
- **장점**: 설정 최소화, 빠른 개발 시작, 비즈니스 로직에 집중 가능

---

## 참고 자료

- [Spring Boot 공식 문서](https://spring.io/projects/spring-boot)
- [Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)
- [Spring Boot Starters](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters)
