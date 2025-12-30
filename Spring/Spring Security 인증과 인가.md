#spring #spring-security #authentication #authorization #인증 #인가 #보안 #면접 #개념정리

**관련 개념:** [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]] [[스프링 부트 (Spring Boot)|스프링 부트]] [[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]

> [!note] 이어서 읽기
> Spring Security를 이해하려면 **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

**Spring Security**는 Spring 기반 애플리케이션을 위한 보안 프레임워크로, **인증(Authentication)**과 **인가(Authorization)**를 제공한다. 인증은 "누구인가"를 확인하는 과정이고, 인가는 "무엇을 할 수 있는가"를 확인하는 과정이다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 보안이 없는 애플리케이션은 누구나 접근할 수 있어 데이터 유출, 무단 접근, 시스템 파괴 등의 심각한 보안 위험이 발생합니다. 인증 없이는 사용자 신원을 확인할 수 없고, 인가 없이는 권한에 따른 접근 제어가 불가능합니다.

- Spring Security는 인증과 인가를 체계적으로 관리하여 애플리케이션의 보안을 강화합니다. 다양한 인증 방식(폼 로그인, JWT, OAuth 등)을 지원하고, 역할 기반 접근 제어(RBAC)를 통해 세밀한 권한 관리를 가능하게 합니다. 개발자가 보안 로직을 직접 구현하지 않아도 프레임워크가 자동으로 처리하여 개발 생산성을 높입니다.

- 인증과 인가의 차이, Spring Security의 인증 과정, 인가 과정, 주요 컴포넌트들, 그리고 실제 구현 방법을 이해해야 합니다.

---

## 1. 인증(Authentication)과 인가(Authorization)의 차이

### **인증(Authentication)이란?**

**인증(Authentication)**은 "누구인가?"를 확인하는 과정입니다.

**예시:**
- 로그인: 사용자가 자신의 신원을 증명 (ID, 비밀번호)
- JWT 토큰 검증: 토큰이 유효한지 확인
- OAuth 인증: 소셜 로그인을 통한 신원 확인

**핵심 질문:**
- "당신은 누구입니까?"
- "이 사용자가 정말 이 사람이 맞습니까?"

### **인가(Authorization)이란?**

**인가(Authorization)**는 "무엇을 할 수 있는가?"를 확인하는 과정입니다.

**예시:**
- 관리자만 사용자 삭제 가능
- 본인만 자신의 정보 수정 가능
- 특정 역할(ROLE)만 접근 가능한 페이지

**핵심 질문:**
- "이 사용자가 이 작업을 수행할 권한이 있습니까?"
- "이 사용자가 이 리소스에 접근할 수 있습니까?"

### **인증 vs 인가 비교**

| 구분 | 인증 (Authentication) | 인가 (Authorization) |
|------|---------------------|---------------------|
| **질문** | "누구인가?" | "무엇을 할 수 있는가?" |
| **목적** | 사용자 신원 확인 | 권한 확인 |
| **시점** | 로그인 시 | 리소스 접근 시 |
| **예시** | 로그인, 토큰 검증 | 역할 확인, 권한 체크 |
| **결과** | 인증된 사용자 정보 | 권한 부여 여부 |

### **실제 시나리오**

```
1. 사용자가 로그인 시도
   → 인증(Authentication): "당신은 누구입니까?"
   → ID/비밀번호 확인 → 인증 성공

2. 사용자가 관리자 페이지 접근 시도
   → 인가(Authorization): "당신은 관리자 권한이 있습니까?"
   → 역할 확인 → 인가 성공/실패
```

**코드 예시:**

```java
// 인증: 사용자 신원 확인
@PostMapping("/login")
public ResponseEntity<?> login(@RequestBody LoginRequest request) {
    // 인증 과정: 사용자가 맞는지 확인
    Authentication authentication = authenticationManager.authenticate(
        new UsernamePasswordAuthenticationToken(
            request.getUsername(), 
            request.getPassword()
        )
    );
    return ResponseEntity.ok("인증 성공");
}

// 인가: 권한 확인
@DeleteMapping("/users/{id}")
@PreAuthorize("hasRole('ADMIN')")  // 인가: 관리자만 접근 가능
public ResponseEntity<?> deleteUser(@PathVariable Long id) {
    // 관리자 권한이 있는 사용자만 실행 가능
    userService.deleteUser(id);
    return ResponseEntity.ok("삭제 완료");
}
```

---

## 2. Spring Security의 인증 과정

### **인증 과정 전체 흐름**

```
1️⃣ 사용자가 로그인 요청
   (ID, 비밀번호 전송)
      ↓
2️⃣ AuthenticationFilter가 요청 가로채기
      ↓
3️⃣ AuthenticationManager가 인증 처리 위임
      ↓
4️⃣ UserDetailsService가 사용자 정보 조회
      ↓
5️⃣ PasswordEncoder가 비밀번호 검증
      ↓
6️⃣ 인증 성공 → SecurityContext에 Authentication 저장
      ↓
7️⃣ 인증된 사용자 정보 반환
```

### **주요 컴포넌트**

#### **1. AuthenticationFilter**

**역할:**
- HTTP 요청을 가로채서 인증 정보 추출
- 인증 성공/실패 처리

**예시:**
```java
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                    HttpServletResponse response, 
                                    FilterChain filterChain) {
        // JWT 토큰 추출
        String token = extractToken(request);
        
        if (token != null && validateToken(token)) {
            // 인증 정보 설정
            Authentication authentication = getAuthentication(token);
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }
        
        filterChain.doFilter(request, response);
    }
}
```

#### **2. AuthenticationManager**

**역할:**
- 인증 처리를 담당하는 인터페이스
- 실제 인증은 `ProviderManager`가 수행

**예시:**
```java
@Configuration
public class SecurityConfig {
    
    @Bean
    public AuthenticationManager authenticationManager(
            AuthenticationConfiguration authConfig) throws Exception {
        return authConfig.getAuthenticationManager();
    }
}
```

#### **3. UserDetailsService**

**역할:**
- 사용자 정보를 데이터베이스에서 조회
- `UserDetails` 객체 반환

**예시:**
```java
@Service
public class CustomUserDetailsService implements UserDetailsService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Override
    public UserDetails loadUserByUsername(String username) 
            throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("사용자를 찾을 수 없습니다."));
        
        return User.builder()
                .username(user.getUsername())
                .password(user.getPassword())
                .roles(user.getRole())
                .build();
    }
}
```

#### **4. PasswordEncoder**

**역할:**
- 비밀번호 암호화 및 검증
- BCrypt, Argon2 등 다양한 알고리즘 지원

**예시:**
```java
@Configuration
public class SecurityConfig {
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}

// 사용 예시
@Service
public class UserService {
    
    @Autowired
    private PasswordEncoder passwordEncoder;
    
    public void createUser(String username, String password) {
        // 비밀번호 암호화
        String encodedPassword = passwordEncoder.encode(password);
        User user = new User(username, encodedPassword);
        userRepository.save(user);
    }
    
    public boolean validatePassword(String rawPassword, String encodedPassword) {
        // 비밀번호 검증
        return passwordEncoder.matches(rawPassword, encodedPassword);
    }
}
```

### **인증 성공 후 처리**

인증이 성공하면 `SecurityContext`에 `Authentication` 객체가 저장됩니다.

```java
// SecurityContext에 저장되는 정보
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

// 인증된 사용자 정보
String username = authentication.getName();
Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
```

---

## 3. Spring Security의 인가 과정

### **인가 과정 전체 흐름**

```
1️⃣ 인증된 사용자가 리소스 접근 요청
      ↓
2️⃣ FilterSecurityInterceptor가 요청 가로채기
      ↓
3️⃣ AccessDecisionManager가 권한 확인
      ↓
4️⃣ 사용자의 권한과 요청된 리소스의 필요 권한 비교
      ↓
5️⃣ 권한 있음 → 접근 허용
   권한 없음 → 접근 거부 (403 Forbidden)
```

### **주요 컴포넌트**

#### **1. FilterSecurityInterceptor**

**역할:**
- 인증된 사용자의 요청을 가로채서 권한 확인
- 마지막 필터로 동작

#### **2. AccessDecisionManager**

**역할:**
- 권한 확인을 담당하는 인터페이스
- `AffirmativeBased`, `ConsensusBased`, `UnanimousBased` 구현체 제공

**예시:**
```java
@Configuration
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/admin/**").hasRole("ADMIN")  // 인가: 관리자만
                .requestMatchers("/user/**").hasAnyRole("USER", "ADMIN")  // 인가: 사용자 또는 관리자
                .requestMatchers("/public/**").permitAll()  // 인가: 모두 허용
                .anyRequest().authenticated()  // 인가: 인증된 사용자만
            );
        return http.build();
    }
}
```

### **인가 방식**

#### **1. 역할 기반 인가 (Role-Based)**

```java
@Configuration
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .requestMatchers("/user/**").hasRole("USER")
            );
        return http.build();
    }
}
```

#### **2. 메서드 레벨 인가**

```java
@RestController
public class UserController {
    
    @GetMapping("/users")
    @PreAuthorize("hasRole('USER')")  // 인가: USER 역할만
    public ResponseEntity<List<User>> getUsers() {
        return ResponseEntity.ok(userService.findAll());
    }
    
    @DeleteMapping("/users/{id}")
    @PreAuthorize("hasRole('ADMIN')")  // 인가: ADMIN 역할만
    public ResponseEntity<?> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.ok("삭제 완료");
    }
    
    @PutMapping("/users/{id}")
    @PreAuthorize("hasRole('USER') and #id == authentication.principal.id")  // 인가: 본인만
    public ResponseEntity<?> updateUser(@PathVariable Long id, @RequestBody User user) {
        userService.updateUser(id, user);
        return ResponseEntity.ok("수정 완료");
    }
}
```

#### **3. 표현식 기반 인가**

```java
@Configuration
@EnableMethodSecurity(prePostEnabled = true)
public class SecurityConfig {
    // 메서드 레벨 인가 활성화
}

@RestController
public class UserController {
    
    @GetMapping("/users/{id}")
    @PreAuthorize("hasPermission(#id, 'User', 'READ')")  // 커스텀 권한 확인
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        return ResponseEntity.ok(userService.findById(id));
    }
}
```

---

## 4. SecurityContext와 Authentication

### **SecurityContext란?**

**SecurityContext**는 현재 인증된 사용자 정보를 담고 있는 컨텍스트입니다.

**특징:**
- `ThreadLocal`을 사용하여 스레드별로 독립적
- 요청이 끝나면 자동으로 정리됨

**예시:**
```java
// SecurityContext에서 인증 정보 가져오기
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

if (authentication != null && authentication.isAuthenticated()) {
    String username = authentication.getName();
    Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
}
```

### **Authentication 객체**

**Authentication**은 인증 정보를 담고 있는 객체입니다.

**주요 속성:**
- `principal`: 사용자 정보 (UserDetails)
- `credentials`: 인증 정보 (비밀번호 등)
- `authorities`: 권한 목록

**예시:**
```java
@RestController
public class UserController {
    
    @GetMapping("/me")
    public ResponseEntity<UserInfo> getCurrentUser(
            @AuthenticationPrincipal UserDetails userDetails) {
        // @AuthenticationPrincipal로 현재 사용자 정보 주입
        UserInfo userInfo = new UserInfo(
            userDetails.getUsername(),
            userDetails.getAuthorities()
        );
        return ResponseEntity.ok(userInfo);
    }
}
```

---

## 5. 실제 구현 예시

### **기본 설정**

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())  // 개발 환경에서만
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/public/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/user/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()
            )
            .formLogin(form -> form
                .loginPage("/login")
                .defaultSuccessUrl("/")
            )
            .logout(logout -> logout
                .logoutUrl("/logout")
                .logoutSuccessUrl("/login")
            );
        return http.build();
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### **UserDetailsService 구현**

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Override
    public UserDetails loadUserByUsername(String username) 
            throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("사용자를 찾을 수 없습니다."));
        
        return User.builder()
                .username(user.getUsername())
                .password(user.getPassword())
                .roles(user.getRole().name())
                .build();
    }
}
```

### **컨트롤러에서 인증/인가 사용**

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    // 인증: 로그인
    @PostMapping("/login")
    public ResponseEntity<?> login(@RequestBody LoginRequest request) {
        // 인증 로직
        return ResponseEntity.ok("로그인 성공");
    }
    
    // 인가: 본인만 접근 가능
    @GetMapping("/{id}")
    @PreAuthorize("hasRole('USER') and #id == authentication.principal.id")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        return ResponseEntity.ok(userService.findById(id));
    }
    
    // 인가: 관리자만 접근 가능
    @DeleteMapping("/{id}")
    @PreAuthorize("hasRole('ADMIN')")
    public ResponseEntity<?> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.ok("삭제 완료");
    }
    
    // 현재 사용자 정보 가져오기
    @GetMapping("/me")
    public ResponseEntity<User> getCurrentUser(
            @AuthenticationPrincipal UserDetails userDetails) {
        User user = userService.findByUsername(userDetails.getUsername());
        return ResponseEntity.ok(user);
    }
}
```

---

## 6. JWT 기반 인증

### **JWT 인증 흐름**

```
1️⃣ 사용자가 로그인 (ID, 비밀번호)
      ↓
2️⃣ 서버가 JWT 토큰 발급
      ↓
3️⃣ 클라이언트가 토큰을 저장 (로컬 스토리지, 쿠키 등)
      ↓
4️⃣ 이후 요청에 토큰 포함 (Authorization 헤더)
      ↓
5️⃣ 서버가 토큰 검증 및 사용자 인증
```

### **JWT 필터 구현**

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    @Autowired
    private JwtTokenProvider jwtTokenProvider;
    
    @Autowired
    private UserDetailsService userDetailsService;
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                    HttpServletResponse response, 
                                    FilterChain filterChain) throws ServletException, IOException {
        String token = extractToken(request);
        
        if (token != null && jwtTokenProvider.validateToken(token)) {
            String username = jwtTokenProvider.getUsernameFromToken(token);
            UserDetails userDetails = userDetailsService.loadUserByUsername(username);
            
            Authentication authentication = new UsernamePasswordAuthenticationToken(
                userDetails, null, userDetails.getAuthorities()
            );
            
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }
        
        filterChain.doFilter(request, response);
    }
    
    private String extractToken(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        if (bearerToken != null && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

### **SecurityConfig에 JWT 필터 추가**

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Autowired
    private JwtAuthenticationFilter jwtAuthenticationFilter;
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)  // JWT는 무상태
            )
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .anyRequest().authenticated()
            )
            .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
        
        return http.build();
    }
}
```

---

## 7. 인증과 인가의 관계

### **인증이 선행되어야 인가가 가능**

```
인증 (Authentication)
  ↓
사용자 신원 확인 완료
  ↓
인가 (Authorization)
  ↓
권한 확인 및 접근 제어
```

**예시:**
```java
// 1. 인증 없이는 인가 불가능
@GetMapping("/users/{id}")
@PreAuthorize("hasRole('USER')")  // 인증된 사용자만 접근 가능
public ResponseEntity<User> getUser(@PathVariable Long id) {
    // 인증되지 않은 사용자는 여기 도달할 수 없음
    return ResponseEntity.ok(userService.findById(id));
}

// 2. 인증 후 인가 확인
@DeleteMapping("/users/{id}")
@PreAuthorize("hasRole('ADMIN')")  // 인증 + 관리자 권한 필요
public ResponseEntity<?> deleteUser(@PathVariable Long id) {
    // 1단계: 인증 확인 (로그인 여부)
    // 2단계: 인가 확인 (관리자 권한 여부)
    userService.deleteUser(id);
    return ResponseEntity.ok("삭제 완료");
}
```

---

## 요약

- **인증(Authentication)**은 "누구인가?"를 확인하는 과정으로, 사용자 신원을 증명합니다.
- **인가(Authorization)**는 "무엇을 할 수 있는가?"를 확인하는 과정으로, 권한에 따른 접근을 제어합니다.
- **Spring Security 인증 과정**: AuthenticationFilter → AuthenticationManager → UserDetailsService → PasswordEncoder → SecurityContext 저장
- **Spring Security 인가 과정**: FilterSecurityInterceptor → AccessDecisionManager → 권한 확인 → 접근 허용/거부
- **주요 컴포넌트**: AuthenticationManager, UserDetailsService, PasswordEncoder, AccessDecisionManager
- **SecurityContext**: 현재 인증된 사용자 정보를 담고 있는 컨텍스트 (ThreadLocal 사용)
- **인증 방식**: 폼 로그인, JWT, OAuth 등 다양한 방식 지원
- **인가 방식**: 역할 기반(Role-Based), 메서드 레벨, 표현식 기반 인가
- **인증이 선행되어야 인가가 가능**: 인증된 사용자만 권한 확인이 가능합니다.

---

## 참고 자료

- [Spring Security Reference](https://docs.spring.io/spring-security/reference/index.html)
- [Spring Security Authentication](https://docs.spring.io/spring-security/reference/servlet/authentication/index.html)
- [Spring Security Authorization](https://docs.spring.io/spring-security/reference/servlet/authorization/index.html)

---

### 예상 꼬리질문 정리

#### 1. 인증과 인가의 차이를 간단히 설명해주세요.

**답변:**
- **인증(Authentication)**: "누구인가?"를 확인하는 과정입니다. 사용자가 자신의 신원을 증명하는 것으로, 로그인, 토큰 검증 등이 해당합니다.
- **인가(Authorization)**: "무엇을 할 수 있는가?"를 확인하는 과정입니다. 인증된 사용자가 특정 리소스에 접근할 권한이 있는지 확인하는 것으로, 역할 기반 접근 제어 등이 해당합니다.

**예시:**
```java
// 인증: 로그인
@PostMapping("/login")
public ResponseEntity<?> login(@RequestBody LoginRequest request) {
    // 사용자 신원 확인
}

// 인가: 권한 확인
@DeleteMapping("/users/{id}")
@PreAuthorize("hasRole('ADMIN')")  // 관리자 권한 확인
public ResponseEntity<?> deleteUser(@PathVariable Long id) {
    // 권한이 있는 사용자만 실행
}
```

#### 2. Spring Security의 인증 과정을 설명해주세요.

**답변:**
1. 사용자가 로그인 요청 (ID, 비밀번호)
2. `AuthenticationFilter`가 요청을 가로채서 인증 정보 추출
3. `AuthenticationManager`가 인증 처리 위임
4. `UserDetailsService`가 사용자 정보를 데이터베이스에서 조회
5. `PasswordEncoder`가 비밀번호 검증
6. 인증 성공 시 `SecurityContext`에 `Authentication` 객체 저장
7. 인증된 사용자 정보 반환

#### 3. UserDetailsService의 역할은 무엇인가요?

**답변:**
- `UserDetailsService`는 사용자 정보를 데이터베이스에서 조회하는 인터페이스입니다.
- `loadUserByUsername(String username)` 메서드를 구현하여 사용자 정보를 조회하고 `UserDetails` 객체를 반환합니다.
- Spring Security가 인증 과정에서 이 서비스를 사용하여 사용자 정보를 가져옵니다.

**예시:**
```java
@Service
public class CustomUserDetailsService implements UserDetailsService {
    
    @Override
    public UserDetails loadUserByUsername(String username) {
        User user = userRepository.findByUsername(username);
        return User.builder()
                .username(user.getUsername())
                .password(user.getPassword())
                .roles(user.getRole())
                .build();
    }
}
```

#### 4. PasswordEncoder는 왜 필요한가요?

**답변:**
- `PasswordEncoder`는 비밀번호를 암호화하고 검증하는 역할을 합니다.
- 평문 비밀번호를 그대로 저장하면 보안 위험이 있으므로, BCrypt, Argon2 등의 알고리즘으로 암호화하여 저장합니다.
- 로그인 시 입력한 비밀번호와 저장된 암호화된 비밀번호를 비교하여 인증합니다.

**예시:**
```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}

// 비밀번호 암호화
String encodedPassword = passwordEncoder.encode("rawPassword");

// 비밀번호 검증
boolean matches = passwordEncoder.matches("rawPassword", encodedPassword);
```

#### 5. SecurityContext란 무엇인가요?

**답변:**
- `SecurityContext`는 현재 인증된 사용자 정보를 담고 있는 컨텍스트입니다.
- `ThreadLocal`을 사용하여 스레드별로 독립적으로 관리됩니다.
- 요청이 끝나면 자동으로 정리되며, `SecurityContextHolder`를 통해 접근할 수 있습니다.

**예시:**
```java
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
String username = authentication.getName();
Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
```

#### 6. @PreAuthorize와 @Secured의 차이는?

**답변:**
- **@PreAuthorize**: SpEL(Spring Expression Language) 표현식을 사용하여 더 복잡한 권한 확인이 가능합니다.
- **@Secured**: 단순한 역할 기반 인가에 사용되며, 표현식 사용이 제한적입니다.

**예시:**
```java
// @PreAuthorize: 표현식 사용 가능
@PreAuthorize("hasRole('USER') and #id == authentication.principal.id")
public User getUser(Long id) { }

// @Secured: 단순 역할 확인
@Secured("ROLE_ADMIN")
public void deleteUser(Long id) { }
```

#### 7. JWT 기반 인증의 장단점은?

**답변:**
- **장점**:
  - 무상태(Stateless): 서버에 세션을 저장하지 않아 확장성이 좋음
  - 분산 시스템에 적합: 여러 서버에서 동일한 토큰으로 인증 가능
  - 클라이언트에 저장: 서버 부하 감소
- **단점**:
  - 토큰 탈취 시 보안 위험: 토큰이 만료될 때까지 유효
  - 토큰 크기: 많은 정보를 담으면 토큰이 커짐
  - 토큰 무효화 어려움: 만료 전까지 무효화 불가능

#### 8. 인증과 인가 중 어느 것이 먼저 실행되나요?

**답변:**
- **인증이 먼저 실행**되고, 그 다음에 인가가 실행됩니다.
- 인증되지 않은 사용자는 인가 과정에 도달할 수 없습니다.
- 인증이 성공하면 `SecurityContext`에 사용자 정보가 저장되고, 이후 요청에서 인가 과정에서 이 정보를 사용합니다.

**흐름:**
```
1. 인증 (Authentication): 사용자 신원 확인
2. SecurityContext에 인증 정보 저장
3. 인가 (Authorization): 권한 확인
4. 접근 허용/거부
```

#### 9. Spring Security의 필터 체인은 어떻게 동작하나요?

**답변:**
- Spring Security는 여러 필터로 구성된 필터 체인을 사용합니다.
- 각 필터는 특정 역할을 담당하며, 순차적으로 실행됩니다.
- 주요 필터: `SecurityContextPersistenceFilter`, `UsernamePasswordAuthenticationFilter`, `FilterSecurityInterceptor` 등

**예시:**
```
요청 → SecurityContextPersistenceFilter → UsernamePasswordAuthenticationFilter → 
FilterSecurityInterceptor → 컨트롤러
```

#### 10. 역할(Role)과 권한(Authority)의 차이는?

**답변:**
- **역할(Role)**: 사용자의 일반적인 역할 (예: USER, ADMIN)
- **권한(Authority)**: 구체적인 권한 (예: USER_READ, USER_WRITE)
- 역할은 여러 권한을 포함할 수 있으며, Spring Security에서는 `ROLE_` 접두사를 사용합니다.

**예시:**
```java
// 역할 기반
@PreAuthorize("hasRole('ADMIN')")

// 권한 기반
@PreAuthorize("hasAuthority('USER_DELETE')")
```

---

## 관련 노트

- **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]** - Spring Security가 동작하는 Spring 환경
- **[[스프링 부트 (Spring Boot)|스프링 부트]]** - Spring Boot에서 Spring Security 사용
- **[[IoC와 DI (제어의 역전과 의존성 주입)|IoC와 DI]]** - Spring Security의 의존성 주입

---

**태그:** #spring #spring-security #authentication #authorization #인증 #인가 #보안 #면접 #개념정리

