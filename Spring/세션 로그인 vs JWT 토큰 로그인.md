 #spring #spring-security #session #jwt #token #authentication #로그인 #면접 #개념정리

**관련 개념:** [[Spring Security 인증과 인가|Spring Security 인증과 인가]] [[스프링 부트 (Spring Boot)|스프링 부트]] [[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]

> [!note] 이어서 읽기
> 세션과 JWT 로그인 방식을 이해하려면 **[[Spring Security 인증과 인가|Spring Security 인증과 인가]]**의 핵심 개념을 함께 확인하세요.

---

## 세션 VS JWT 로그인 

**세션 로그인**과 **JWT 토큰 로그인**은 사용자 인증을 처리하는 두 가지 주요 방식이다. 세션 로그인은 서버가 사용자 상태를 관리하는 Stateful 방식이고, JWT 로그인은 서버가 상태를 관리하지 않는 Stateless 방식이다. 각각의 장단점을 이해하고 프로젝트 특성에 맞는 방식을 선택해야 한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- 로그인 방식을 선택하지 않으면 보안 취약점이 발생하거나 확장성 문제가 생길 수 있습니다. 세션 로그인은 서버 메모리 부하와 확장성 제약이 있고, JWT 로그인은 토큰 탈취 시 보안 위험이 있습니다. 각 방식의 특징을 이해하지 못하면 잘못된 선택으로 인해 성능 저하나 보안 문제가 발생할 수 있습니다.

- 세션과 JWT 로그인 방식을 이해하면 프로젝트 특성에 맞는 인증 방식을 선택할 수 있습니다. 전통적인 웹 애플리케이션(서버 사이드 렌더링)에는 세션 로그인이 적합하고, 프론트엔드와 백엔드가 분리된 SPA, 마이크로서비스 아키텍처, 그리고 확장성이 중요한 엔터프라이즈급 서비스에는 JWT 로그인이 적합합니다. 각 방식의 동작 원리와 구현 방법을 이해하면 효과적인 보안 시스템을 구축할 수 있습니다.

- 세션과 JWT의 차이, 각 방식의 동작 원리, 장단점, 그리고 Spring Boot에서의 구현 방법을 이해해야 합니다.

---

## 1. 세션 로그인과 JWT 로그인의 기본 개념

### **세션 로그인 (Session-Based Authentication)**

**세션 로그인**은 서버가 사용자 인증 정보를 **서버 메모리나 데이터베이스에 저장**하고, 클라이언트에게는 **세션 ID를 쿠키로 전달**하는 방식입니다.

**특징:**
- 서버가 사용자 상태를 직접 관리 (Stateful)
- 세션 ID를 쿠키로 전달
- 서버 메모리나 DB에 세션 정보 저장

### **JWT 토큰 로그인 (Token-Based Authentication)**

**JWT 로그인**은 서버가 사용자 인증 정보를 **토큰에 담아 클라이언트에 전달**하고, 클라이언트는 이후 요청에 이 토큰을 포함시키는 방식입니다.

**특징:**
- 서버가 사용자 상태를 관리하지 않음 (Stateless)
- 토큰을 Authorization 헤더로 전달
- 클라이언트가 토큰을 보관

---

## 2. 세션 로그인 방식

### **세션 로그인의 동작 원리**

```
1️⃣ 사용자가 로그인 요청 (ID, 비밀번호)
      ↓
2️⃣ 서버가 사용자 정보 확인
      ↓
3️⃣ 서버가 세션 생성 및 저장 (메모리/DB)
      ↓
4️⃣ 서버가 세션 ID를 쿠키로 클라이언트에 전달
      ↓
5️⃣ 클라이언트가 이후 요청에 쿠키 자동 포함
      ↓
6️⃣ 서버가 세션 ID로 세션 조회하여 인증 확인
```

### **세션 로그인의 특징**

**Stateful (상태 유지) 방식:**
- 서버가 사용자 인증 정보를 세션에 저장
- 서버 메모리나 데이터베이스에 세션 정보 보관
- 세션 ID로 사용자 정보 조회

**쿠키 기반 인증:**
- 세션 ID를 쿠키에 담아 전달
- 브라우저가 자동으로 쿠키를 포함하여 전송
- HttpOnly, Secure 옵션으로 보안 강화 가능

### **세션 로그인 구현 예시**

```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    
    @Autowired
    private AuthService authService;
    
    @PostMapping("/login")
    public ResponseEntity<?> login(@RequestBody LoginRequest request, 
                                   HttpSession session) {
        // 1. 사용자 인증
        User user = authService.authenticate(request.getUsername(), request.getPassword());
        
        // 2. 세션에 사용자 정보 저장
        session.setAttribute("user", user);
        
        return ResponseEntity.ok("로그인 성공");
    }
    
    @GetMapping("/logout")
    public ResponseEntity<?> logout(HttpSession session) {
        // 세션 무효화
        session.invalidate();
        return ResponseEntity.ok("로그아웃 완료");
    }
}
```

### **Spring Security 세션 설정**

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED)  // 세션 사용
                .maximumSessions(1)  // 동시 세션 1개만 허용
                .maxSessionsPreventsLogin(false)  // 기존 세션 만료
            )
            .formLogin(form -> form
                .loginPage("/login")
                .defaultSuccessUrl("/")
            )
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/login", "/signup").permitAll()
                .anyRequest().authenticated()
            );
        
        return http.build();
    }
}
```

### **세션 로그인의 장점**

- ✅ **보안성**: 서버가 세션을 관리하므로 토큰 탈취 위험이 낮음
- ✅ **즉시 무효화**: 로그아웃 시 세션을 즉시 삭제 가능
- ✅ **서버 제어**: 서버가 세션을 완전히 제어 가능
- ✅ **전통적인 방식**: 검증된 안정적인 방식

### **세션 로그인의 단점**

- ❌ **확장성 제약**: 서버 메모리에 세션 저장 시 서버 확장 어려움
- ❌ **서버 부하**: 세션 조회를 위한 DB 접근으로 인한 부하
- ❌ **쿠키 의존**: 쿠키를 사용하지 못하는 환경에서 제한적
- ❌ **CORS 복잡**: 도메인이 다른 경우 쿠키 전송 복잡

---

## 3. JWT 토큰 로그인 방식

### **JWT란?**

**JWT (JSON Web Token)**는 인증 정보를 JSON 형식으로 저장하고, Base64로 인코딩하고 서명 정보를 더하여 생성한 토큰입니다.

**JWT 구조:**

```
헤더(Header).페이로드(Payload).서명(Signature)
```

**예시:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

### **JWT 구성 요소**

#### **1. Header (헤더)**

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**의미:**
- `alg`: 서명 알고리즘 (HS256, RS256 등)
- `typ`: 토큰 타입 (JWT)

**헤더의 의의:**
- **토큰 처리 방법 명시**: 서버가 어떤 알고리즘으로 검증해야 하는지 알려줌
- **표준화된 형식**: JWT임을 명확히 표시하여 파서가 올바르게 처리하도록 함
- **암시하는 것**: "이 토큰은 HS256 알고리즘으로 서명되었고, JWT 형식이다"

**예시:**
```json
{
  "alg": "HS256",  // HMAC-SHA256 알고리즘 사용
  "typ": "JWT"     // JWT 타입임을 명시
}
```

#### **2. Payload (페이로드)**

```json
{
  "sub": "user123",
  "role": "USER",
  "iat": 1516239022,
  "exp": 1516242622
}
```

**의미:**
- `sub`: 사용자 식별자 (Subject)
- `role`: 사용자 권한
- `iat`: 토큰 발급 시간 (Issued At)
- `exp`: 토큰 만료 시간 (Expiration)

**페이로드의 의의:**
- **인증 정보 저장**: 사용자 식별자, 권한 등 인증에 필요한 정보를 담음
- **서버 상태 불필요**: 사용자 정보가 토큰에 포함되어 서버가 별도로 조회할 필요 없음
- **Stateless 인증**: 서버가 상태를 저장하지 않아도 인증 가능
- **암시하는 것**: "이 토큰의 주인은 user123이고, USER 권한을 가지며, 2024-01-01에 발급되어 2024-01-02까지 유효하다"

**예시:**
```json
{
  "sub": "user123",           // 누구인가? (사용자 ID)
  "role": "USER",             // 어떤 권한을 가지는가?
  "iat": 1516239022,         // 언제 발급되었는가?
  "exp": 1516242622          // 언제 만료되는가?
}
```

**주의사항:**
- 페이로드는 Base64로 인코딩되지만 **암호화되지 않음**
- 민감한 정보(비밀번호 등)를 담으면 안 됨
- 누구나 디코딩하여 내용을 볼 수 있음

#### **3. Signature (서명)**

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

**의미:**
- 헤더와 페이로드를 비밀키로 서명
- 토큰 변조 방지

**서명의 의의:**
- **무결성 보장**: 토큰이 변조되지 않았음을 증명
- **인증 검증**: 서버가 발급한 토큰임을 확인
- **보안 강화**: 비밀키를 모르면 위조 불가능
- **암시하는 것**: "이 토큰은 서버가 발급했고, 내용이 변조되지 않았다"

**동작 원리:**
```
1. 헤더와 페이로드를 Base64로 인코딩
2. 두 값을 "."으로 연결
3. 비밀키(secret)로 HMAC-SHA256 서명 생성
4. 서명을 Base64로 인코딩하여 토큰에 추가
```

**검증 과정:**
```
1. 서버가 받은 토큰의 헤더와 페이로드 추출
2. 동일한 비밀키로 서명 재생성
3. 토큰의 서명과 재생성한 서명 비교
4. 일치하면 유효한 토큰, 불일치하면 변조된 토큰
```

**예시:**
```java
// 서명 생성
String signature = HMACSHA256(
    base64UrlEncode(header) + "." + base64UrlEncode(payload),
    "mySecretKey"
);

// 검증
String receivedSignature = extractSignature(token);
String calculatedSignature = HMACSHA256(
    base64UrlEncode(header) + "." + base64UrlEncode(payload),
    "mySecretKey"
);

if (receivedSignature.equals(calculatedSignature)) {
    // 토큰이 유효함
} else {
    // 토큰이 변조됨
}
```

### **세 부분의 관계**

**전체 구조:**
```
Header.Payload.Signature
  ↓      ↓        ↓
형식   정보    무결성
```

**각 부분의 역할:**
- **Header**: "어떻게 처리할 것인가?" (알고리즘, 타입)
- **Payload**: "무엇을 담고 있는가?" (사용자 정보, 권한)
- **Signature**: "진짜인가?" (변조 여부 확인)

**왜 세 부분으로 나누었나?**
1. **표준화**: 모든 JWT가 동일한 구조를 가져 파싱이 쉬움
2. **검증 용이**: 서명만 확인하면 토큰의 무결성 검증 가능
3. **확장성**: 페이로드에 필요한 정보를 자유롭게 추가 가능
4. **보안**: 서명으로 변조를 방지하면서도 페이로드는 읽을 수 있음

### **JWT 로그인의 동작 원리**

```
1️⃣ 사용자가 로그인 요청 (ID, 비밀번호)
      ↓
2️⃣ 서버가 사용자 정보 확인
      ↓
3️⃣ 서버가 JWT 토큰 생성 (사용자 정보 포함)
      ↓
4️⃣ 서버가 JWT 토큰을 클라이언트에 전달
      ↓
5️⃣ 클라이언트가 토큰을 저장 (localStorage, sessionStorage)
      ↓
6️⃣ 클라이언트가 이후 요청에 Authorization 헤더로 토큰 포함
      ↓
7️⃣ 서버가 토큰 검증 및 사용자 인증 확인
```

### **JWT 로그인 구현 예시**

#### **1. JWT 토큰 생성 및 검증 클래스**

```java
@Component
public class JwtTokenProvider {
    
    private final SecretKey secretKey;
    private final long expiration;
    
    public JwtTokenProvider(
            @Value("${jwt.secret}") String secret,
            @Value("${jwt.expiration}") long expiration) {
        this.secretKey = Keys.hmacShaKeyFor(secret.getBytes(StandardCharsets.UTF_8));
        this.expiration = expiration;
    }
    
    // JWT 토큰 생성
    public String generateToken(String userId, String role) {
        Date now = new Date();
        Date expiryDate = new Date(now.getTime() + expiration);
        
        return Jwts.builder()
                .subject(userId)
                .claim("role", role)
                .issuedAt(now)
                .expiration(expiryDate)
                .signWith(secretKey)
                .compact();
    }
    
    // 토큰에서 사용자 ID 추출
    public String getUserIdFromToken(String token) {
        Claims claims = getClaims(token);
        return claims.getSubject();
    }
    
    // 토큰에서 권한 추출
    public String getRoleFromToken(String token) {
        Claims claims = getClaims(token);
        return claims.get("role", String.class);
    }
    
    // 토큰 유효성 검증
    public boolean validateToken(String token) {
        try {
            Jwts.parser()
                .verifyWith(secretKey)
                .build()
                .parseSignedClaims(token);
            return true;
        } catch (Exception e) {
            return false;
        }
    }
    
    private Claims getClaims(String token) {
        return Jwts.parser()
                .verifyWith(secretKey)
                .build()
                .parseSignedClaims(token)
                .getPayload();
    }
}
```

#### **2. 로그인 서비스**

```java
@Service
@RequiredArgsConstructor
public class AuthService {
    
    private final UserRepository userRepository;
    private final JwtTokenProvider jwtTokenProvider;
    private final PasswordEncoder passwordEncoder;
    
    public LoginResponse login(LoginRequest request) {
        // 1. 사용자 조회
        User user = userRepository.findByUsername(request.getUsername())
                .orElseThrow(() -> new IllegalArgumentException("사용자를 찾을 수 없습니다."));
        
        // 2. 비밀번호 확인
        if (!passwordEncoder.matches(request.getPassword(), user.getPassword())) {
            throw new IllegalArgumentException("비밀번호가 일치하지 않습니다.");
        }
        
        // 3. JWT 토큰 생성
        String token = jwtTokenProvider.generateToken(user.getId(), user.getRole());
        
        // 4. 응답 생성
        return LoginResponse.builder()
                .token(token)
                .userId(user.getId())
                .username(user.getUsername())
                .role(user.getRole())
                .build();
    }
}
```

#### **3. JWT 인증 필터**

```java
@Component
@RequiredArgsConstructor
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    private final JwtTokenProvider jwtTokenProvider;
    private final UserDetailsService userDetailsService;
    
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                    HttpServletResponse response, 
                                    FilterChain filterChain) throws ServletException, IOException {
        
        try {
            // 1. Request Header에서 JWT 토큰 추출
            String token = getJwtFromRequest(request);
            
            // 2. 토큰 검증
            if (StringUtils.hasText(token) && jwtTokenProvider.validateToken(token)) {
                // 3. 토큰에서 사용자 ID 추출
                String userId = jwtTokenProvider.getUserIdFromToken(token);
                
                // 4. 사용자 정보 로드
                UserDetails userDetails = userDetailsService.loadUserByUsername(userId);
                
                // 5. SecurityContext에 인증 정보 저장
                Authentication authentication = new UsernamePasswordAuthenticationToken(
                    userDetails, null, userDetails.getAuthorities()
                );
                SecurityContextHolder.getContext().setAuthentication(authentication);
            }
        } catch (Exception e) {
            logger.error("JWT 인증 실패", e);
        }
        
        filterChain.doFilter(request, response);
    }
    
    private String getJwtFromRequest(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

#### **4. Spring Security 설정**

```java
@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig {
    
    private final JwtAuthenticationFilter jwtAuthenticationFilter;
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // CSRF 비활성화 (JWT 사용 시)
            .csrf(csrf -> csrf.disable())
            
            // 세션 미사용 (Stateless)
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            
            // 권한 설정
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .anyRequest().authenticated()
            )
            
            // JWT 필터 추가
            .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
        
        return http.build();
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### **JWT 로그인의 장점**

- ✅ **확장성**: 서버가 상태를 관리하지 않아 수평 확장 용이
- ✅ **분산 시스템**: 여러 서버에서 동일한 토큰으로 인증 가능
- ✅ **모바일 친화적**: 쿠키 없이도 동작
- ✅ **CORS 대응**: 도메인이 다른 경우에도 사용 가능
- ✅ **서버 부하 감소**: 세션 조회 불필요

### **JWT 로그인의 단점**

- ❌ **토큰 탈취 위험**: 토큰이 탈취되면 만료 전까지 유효
- ❌ **토큰 무효화 어려움**: 로그아웃해도 토큰이 유효
- ❌ **토큰 크기**: 많은 정보를 담으면 토큰이 커짐
- ❌ **서버 부하**: 매 요청마다 토큰 검증 필요

---

## 4. 세션 로그인 vs JWT 로그인 비교

### **전체 비교표**

| 항목 | 세션 로그인 | JWT 로그인 |
|------|------------|-----------|
| **인증 정보 저장** | 서버 세션에 저장 | 클라이언트가 토큰 보관 |
| **상태 관리** | Stateful (서버가 상태 유지) | Stateless (서버는 상태 없음) |
| **인증 방식** | 쿠키 기반 (세션 ID) | Authorization 헤더 (Bearer 토큰) |
| **확장성** | 낮음 (서버 메모리/DB 의존) | 높음 (서버 확장 용이) |
| **보안성** | 높음 (서버 제어 가능) | 중간 (토큰 탈취 위험) |
| **즉시 무효화** | 가능 (세션 삭제) | 어려움 (토큰 만료까지 유효) |
| **서버 부하** | 세션 조회 필요 | 토큰 검증 필요 |
| **모바일 대응** | 제한적 (쿠키 의존) | 우수 (헤더 기반) |
| **CORS** | 복잡 (쿠키 전송) | 간단 (헤더 전송) |
| **사용 시나리오** | 전통적인 웹 애플리케이션 | SPA, 마이크로서비스 |

### **시각적 비교**

#### **세션 로그인 흐름**

```
클라이언트                    서버
    │                         │
    ├─ 로그인 요청 ────────────>│
    │                         │ 세션 생성 및 저장
    │<── 세션 ID (쿠키) ───────┤
    │                         │
    ├─ 요청 + 쿠키 ────────────>│
    │                         │ 세션 조회
    │<── 응답 ────────────────┤
```

#### **JWT 로그인 흐름**

```
클라이언트                    서버
    │                         │
    ├─ 로그인 요청 ────────────>│
    │                         │ JWT 토큰 생성
    │<── JWT 토큰 ─────────────┤
    │                         │
    ├─ 요청 + JWT 토큰 ────────>│
    │                         │ 토큰 검증
    │<── 응답 ────────────────┤
```

---

## 5. 언제 어떤 방식을 사용해야 할까?

### **세션 로그인을 사용해야 하는 경우**

- ✅ 전통적인 웹 애플리케이션 (서버 사이드 렌더링)
- ✅ 보안이 매우 중요한 시스템
- ✅ 즉시 로그아웃 처리가 필요한 경우
- ✅ 서버 확장이 필요하지 않은 경우
- ✅ 쿠키를 사용할 수 있는 환경

### **JWT 로그인을 사용해야 하는 경우**

- ✅ 프론트엔드와 백엔드가 분리된 SPA (Single Page Application)
- ✅ 마이크로서비스 아키텍처
- ✅ 엔터프라이즈급 서비스 (대규모 트래픽, 확장성 요구)
- ✅ 모바일 앱과의 통신
- ✅ 서버 확장이 필요한 경우
- ✅ 여러 도메인 간 인증이 필요한 경우
- ✅ 분산 시스템 (여러 서버로 구성된 환경)

---

## 6. 하이브리드 방식

### **Refresh Token 패턴**

JWT의 단점을 보완하기 위해 **Access Token**과 **Refresh Token**을 함께 사용하는 방식입니다.

**동작 원리:**
- **Access Token**: 짧은 유효 기간 (예: 15분) - 실제 인증에 사용
- **Refresh Token**: 긴 유효 기간 (예: 7일) - Access Token 갱신에 사용

**장점:**
- Access Token이 탈취되어도 짧은 시간만 유효
- Refresh Token으로 토큰 갱신 가능
- 로그아웃 시 Refresh Token 무효화 가능

**구현 예시:**

```java
@Service
public class AuthService {
    
    public LoginResponse login(LoginRequest request) {
        // 사용자 인증
        User user = authenticate(request);
        
        // Access Token 생성 (15분)
        String accessToken = jwtTokenProvider.generateToken(user.getId(), 15 * 60 * 1000);
        
        // Refresh Token 생성 (7일)
        String refreshToken = jwtTokenProvider.generateToken(user.getId(), 7 * 24 * 60 * 60 * 1000);
        
        // Refresh Token을 DB에 저장 (로그아웃 시 무효화 가능)
        saveRefreshToken(user.getId(), refreshToken);
        
        return LoginResponse.builder()
                .accessToken(accessToken)
                .refreshToken(refreshToken)
                .build();
    }
    
    public TokenResponse refreshToken(String refreshToken) {
        // Refresh Token 검증
        if (!jwtTokenProvider.validateToken(refreshToken)) {
            throw new IllegalArgumentException("유효하지 않은 토큰입니다.");
        }
        
        // DB에서 Refresh Token 확인
        if (!isValidRefreshToken(refreshToken)) {
            throw new IllegalArgumentException("유효하지 않은 토큰입니다.");
        }
        
        // 새로운 Access Token 발급
        String userId = jwtTokenProvider.getUserIdFromToken(refreshToken);
        String newAccessToken = jwtTokenProvider.generateToken(userId, 15 * 60 * 1000);
        
        return TokenResponse.builder()
                .accessToken(newAccessToken)
                .build();
    }
}
```

---

## 요약

- **세션 로그인**은 서버가 사용자 인증 정보를 세션에 저장하고, 세션 ID를 쿠키로 전달하는 Stateful 방식입니다.
- **JWT 로그인**은 서버가 사용자 인증 정보를 토큰에 담아 클라이언트에 전달하고, 클라이언트가 이후 요청에 토큰을 포함시키는 Stateless 방식입니다.
- **세션 로그인의 장점**: 보안성 높음, 즉시 무효화 가능, 서버 제어 가능
- **세션 로그인의 단점**: 확장성 제약, 서버 부하, 쿠키 의존
- **JWT 로그인의 장점**: 확장성 우수, 분산 시스템 대응, 모바일 친화적, CORS 대응
- **JWT 로그인의 단점**: 토큰 탈취 위험, 토큰 무효화 어려움, 토큰 크기 문제
- **선택 기준**: 전통적인 웹 애플리케이션은 세션, SPA나 마이크로서비스는 JWT
- **하이브리드 방식**: Refresh Token 패턴으로 JWT의 단점을 보완 가능

---

## 참고 자료

- [Spring Security Reference](https://docs.spring.io/spring-security/reference/index.html)
- [JWT.io](https://jwt.io/) - JWT 토큰 디버깅 및 정보
- [JJWT 라이브러리](https://github.com/jwtk/jjwt) - Java JWT 라이브러리

---

### 예상 꼬리질문 정리

#### 1. 세션 로그인과 JWT 로그인의 가장 큰 차이는 무엇인가요?

**답변:**
- **세션 로그인**: 서버가 사용자 상태를 관리하는 Stateful 방식입니다. 서버 메모리나 DB에 세션 정보를 저장하고, 세션 ID를 쿠키로 전달합니다.
- **JWT 로그인**: 서버가 사용자 상태를 관리하지 않는 Stateless 방식입니다. 사용자 정보를 토큰에 담아 클라이언트에 전달하고, 클라이언트가 토큰을 보관합니다.

**핵심 차이:**
- 세션: 서버가 상태를 기억함
- JWT: 서버가 상태를 기억하지 않음

#### 2. JWT 토큰의 구조를 설명해주세요.

**답변:**
- JWT는 `Header.Payload.Signature` 세 부분으로 구성됩니다.
- **Header**: 토큰 타입과 서명 알고리즘 정보
- **Payload**: 사용자 정보 (ID, 권한, 만료 시간 등)
- **Signature**: 헤더와 페이로드를 비밀키로 서명한 값 (변조 방지)

**예시:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

#### 3. JWT 로그인에서 토큰 탈취 위험을 어떻게 해결하나요?

**답변:**
- **HTTPS 사용**: 토큰 전송 시 암호화
- **짧은 유효 기간**: Access Token을 짧게 설정 (예: 15분)
- **Refresh Token 패턴**: Access Token과 Refresh Token 분리
- **토큰 저장 위치**: localStorage 대신 httpOnly 쿠키 사용 고려
- **토큰 무효화**: Refresh Token을 DB에 저장하여 로그아웃 시 무효화

**예시:**
```java
// Access Token: 짧은 유효 기간
String accessToken = jwtTokenProvider.generateToken(userId, 15 * 60 * 1000);

// Refresh Token: 긴 유효 기간, DB에 저장
String refreshToken = jwtTokenProvider.generateToken(userId, 7 * 24 * 60 * 60 * 1000);
saveRefreshToken(userId, refreshToken);  // 로그아웃 시 삭제 가능
```

#### 4. 세션 로그인에서 서버 확장 시 문제점은 무엇인가요?

**답변:**
- **세션 저장 위치**: 서버 메모리에 저장하면 서버 확장 시 세션 공유 문제 발생
- **해결 방법**:
  - 세션을 Redis 같은 외부 저장소에 저장
  - Sticky Session 사용 (로드 밸런서에서 같은 서버로 라우팅)
  - 세션 클러스터링

**예시:**
```java
// Redis에 세션 저장
@Configuration
public class SessionConfig {
    
    @Bean
    public RedisConnectionFactory connectionFactory() {
        return new LettuceConnectionFactory();
    }
    
    @Bean
    public RedisSessionRepository sessionRepository() {
        return new RedisSessionRepository(connectionFactory());
    }
}
```

#### 5. JWT 토큰을 어디에 저장하는 것이 안전한가요?

**답변:**
- **localStorage**: XSS 공격에 취약하지만 구현이 간단
- **httpOnly 쿠키**: XSS 공격에 상대적으로 안전하지만 CSRF 공격에 취약
- **메모리**: 가장 안전하지만 새로고침 시 사라짐

**권장:**
- 일반적으로 localStorage 사용 (XSS 방지에 집중)
- 매우 중요한 시스템은 httpOnly 쿠키 + CSRF 토큰 사용

#### 6. 세션 로그인과 JWT 로그인 중 어떤 것을 선택해야 하나요?

**답변:**
- **세션 로그인 선택 시**:
  - 전통적인 웹 애플리케이션
  - 보안이 매우 중요한 시스템
  - 즉시 로그아웃 처리가 필요한 경우
  
- **JWT 로그인 선택 시**:
  - 프론트엔드와 백엔드가 분리된 SPA
  - 마이크로서비스 아키텍처
  - 서버 확장이 필요한 경우
  - 모바일 앱과의 통신

#### 7. JWT 토큰의 만료 시간은 어떻게 설정하나요?

**답변:**
- **Access Token**: 짧게 설정 (15분 ~ 1시간)
  - 탈취되어도 짧은 시간만 유효
  - Refresh Token으로 갱신 가능
  
- **Refresh Token**: 길게 설정 (7일 ~ 30일)
  - Access Token 갱신에 사용
  - DB에 저장하여 로그아웃 시 무효화 가능

**예시:**
```java
// Access Token: 15분
String accessToken = jwtTokenProvider.generateToken(userId, 15 * 60 * 1000);

// Refresh Token: 7일
String refreshToken = jwtTokenProvider.generateToken(userId, 7 * 24 * 60 * 60 * 1000);
```

#### 8. Spring Security에서 JWT 필터는 어디에 추가하나요?

**답변:**
- `UsernamePasswordAuthenticationFilter` 이전에 추가합니다.
- 필터 체인에서 인증 필터보다 먼저 실행되어 JWT 토큰을 검증하고 SecurityContext에 인증 정보를 설정합니다.

**예시:**
```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
    return http.build();
}
```

#### 9. JWT 토큰 검증은 어떻게 하나요?

**답변:**
1. Authorization 헤더에서 토큰 추출
2. 토큰 형식 확인 (Bearer 토큰)
3. 서명 검증 (비밀키로 서명 확인)
4. 만료 시간 확인
5. 사용자 정보 추출 및 SecurityContext에 저장

**예시:**
```java
public boolean validateToken(String token) {
    try {
        Claims claims = Jwts.parser()
            .verifyWith(secretKey)
            .build()
            .parseSignedClaims(token)
            .getPayload();
        
        // 만료 시간 확인
        Date expiration = claims.getExpiration();
        return expiration.after(new Date());
    } catch (Exception e) {
        return false;
    }
}
```

#### 10. 세션 로그인에서 쿠키의 HttpOnly 옵션은 왜 중요한가요?

**답변:**
- **HttpOnly 옵션**: JavaScript에서 쿠키 접근 불가
- **XSS 공격 방지**: 악성 스크립트가 쿠키를 탈취하는 것을 방지
- **보안 강화**: 세션 ID가 탈취되는 것을 방지

**설정:**
```java
@Bean
public CookieSerializer cookieSerializer() {
    DefaultCookieSerializer serializer = new DefaultCookieSerializer();
    serializer.setHttpOnly(true);
    serializer.setSecure(true);  // HTTPS에서만 전송
    return serializer;
}
```

---

## 관련 노트

- **[[Spring Security 인증과 인가|Spring Security 인증과 인가]]** - 인증과 인가의 기본 개념
- **[[스프링 부트 (Spring Boot)|스프링 부트]]** - Spring Boot에서의 보안 설정
- **[[스프링 프레임워크 (Spring Framework)|스프링 프레임워크]]** - Spring Security가 동작하는 환경

---

**태그:** #spring #spring-security #session #jwt #token #authentication #로그인 #면접 #개념정리

