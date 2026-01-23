#network #dns #domain-name-system #인터넷 #면접 #개념정리

**관련 개념:** [[인터넷 프로토콜]] [[웹 통신]]

> [!note] 이어서 읽기
> DNS를 이해하려면 인터넷의 기본 동작 원리를 함께 확인하세요.

---

## Topic (오늘의 주제)

**DNS(Domain Name System, 도메인 이름 시스템)**는 사람이 읽을 수 있는 도메인 이름(예: www.google.com)을 컴퓨터가 이해할 수 있는 IP 주소(예: 172.217.31.142)로 변환하는 인터넷의 전화번호부 역할을 하는 시스템이다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- DNS 없이 웹사이트에 접속하려면 숫자로 이루어진 IP 주소(예: 172.217.31.142)를 직접 입력해야 하며, 이는 기억하기 어렵고 사용자 경험이 나쁩니다. 또한 IP 주소가 변경되면 모든 사용자가 새로운 주소를 알아야 하는 문제가 있습니다.

- DNS를 사용하면 사람이 읽기 쉬운 도메인 이름(예: www.google.com)으로 웹사이트에 접속할 수 있어 사용자 경험이 크게 향상됩니다. 도메인 이름과 IP 주소를 분리하여 관리하므로 IP 주소가 변경되어도 도메인 이름은 그대로 유지할 수 있습니다.

- DNS의 동작 원리, DNS 서버의 계층 구조, DNS 쿼리 과정, 그리고 DNS 캐싱 메커니즘을 이해해야 합니다.

---

## 1. DNS란 무엇인가?

### **DNS의 정의**

**DNS(Domain Name System, 도메인 이름 시스템)**는 사람이 읽을 수 있는 도메인 이름을 컴퓨터가 이해할 수 있는 IP 주소로 변환하는 인터넷의 전화번호부 역할을 하는 시스템입니다.

**핵심 개념:**
- 도메인 이름 ↔ IP 주소 변환
- 분산 데이터베이스 시스템
- 인터넷의 핵심 인프라

### **DNS가 필요한 이유**

**DNS 없이:**
```
사용자 → IP 주소 직접 입력 (172.217.31.142)
```

**문제점:**
- 숫자로 이루어진 IP 주소는 기억하기 어려움
- IP 주소 변경 시 모든 사용자가 새 주소를 알아야 함
- 사용자 경험 저하

**DNS 사용:**
```
사용자 → 도메인 이름 입력 (www.google.com) → DNS → IP 주소 변환 → 접속
```

**장점:**
- 사람이 읽기 쉬운 도메인 이름 사용
- IP 주소 변경 시 DNS만 업데이트하면 됨
- 사용자 경험 향상

---

## 2. 도메인 이름 구조

### **도메인 이름의 계층 구조**

도메인 이름은 점(.)으로 구분된 계층 구조로 이루어져 있습니다.

```
www.google.com
│   │     │
│   │     └─ 최상위 도메인 (TLD: Top-Level Domain)
│   └───────── 2차 도메인 (Second-Level Domain)
└───────────── 서브도메인 (Subdomain)
```

### **도메인 이름 예시**

```
www.google.com
├─ com: 최상위 도메인 (TLD)
├─ google: 2차 도메인
└─ www: 서브도메인

mail.naver.com
├─ com: 최상위 도메인
├─ naver: 2차 도메인
└─ mail: 서브도메인
```

### **최상위 도메인 (TLD) 종류**

- **일반 최상위 도메인 (gTLD)**: `.com`, `.org`, `.net`, `.edu`, `.gov`
- **국가 코드 최상위 도메인 (ccTLD)**: `.kr` (한국), `.jp` (일본), `.us` (미국)

---

## 3. DNS 서버의 계층 구조

### **DNS 서버 계층**

DNS는 분산 데이터베이스 시스템으로, 여러 계층의 DNS 서버로 구성되어 있습니다.

```
Root DNS Server (.)
    ↓
TLD DNS Server (.com, .kr 등)
    ↓
Authoritative DNS Server (google.com 등)
    ↓
Local DNS Server (ISP 제공)
```

### **DNS 서버 종류**

#### 1. Root DNS Server (루트 DNS 서버)

- 최상위 계층의 DNS 서버
- 전 세계에 13개의 루트 서버 존재
- TLD DNS 서버의 주소를 알고 있음

#### 2. TLD DNS Server (최상위 도메인 DNS 서버)

- `.com`, `.kr` 등의 최상위 도메인을 관리
- 해당 도메인의 Authoritative DNS 서버 주소를 알고 있음

#### 3. Authoritative DNS Server (권한 있는 DNS 서버)

- 특정 도메인(예: google.com)의 IP 주소를 직접 관리
- 도메인 소유자가 설정한 DNS 서버

#### 4. Local DNS Server (로컬 DNS 서버, Recursive DNS Server)

- ISP(인터넷 서비스 제공자)가 제공하는 DNS 서버
- 사용자의 DNS 쿼리를 대신 처리
- 캐싱 기능 제공

---

## 4. DNS 쿼리 과정

### **DNS 쿼리 흐름**

```
1️⃣ 사용자가 브라우저에 www.google.com 입력
   ↓
2️⃣ Local DNS Server에 쿼리 전송
   ↓
3️⃣ Local DNS Server가 캐시 확인
   - 캐시에 있으면 → 즉시 반환
   - 캐시에 없으면 → 다음 단계 진행
   ↓
4️⃣ Root DNS Server에 쿼리
   - ".com"의 TLD DNS Server 주소 반환
   ↓
5️⃣ TLD DNS Server에 쿼리
   - "google.com"의 Authoritative DNS Server 주소 반환
   ↓
6️⃣ Authoritative DNS Server에 쿼리
   - "www.google.com"의 IP 주소 반환
   ↓
7️⃣ Local DNS Server가 IP 주소를 캐시에 저장
   ↓
8️⃣ 사용자에게 IP 주소 반환
   ↓
9️⃣ 브라우저가 IP 주소로 접속
```

### **구체적인 예시**

**사용자가 www.google.com에 접속하려고 할 때:**

1. **브라우저**: "www.google.com의 IP 주소가 뭐야?"
2. **Local DNS Server**: "캐시에 없네. Root DNS Server에 물어볼게."
3. **Root DNS Server**: ".com의 TLD DNS Server 주소는 이거야."
4. **Local DNS Server**: ".com TLD DNS Server에 물어볼게."
5. **TLD DNS Server**: "google.com의 Authoritative DNS Server 주소는 이거야."
6. **Local DNS Server**: "google.com Authoritative DNS Server에 물어볼게."
7. **Authoritative DNS Server**: "www.google.com의 IP 주소는 172.217.31.142야."
8. **Local DNS Server**: "캐시에 저장하고, 사용자에게 알려줄게."
9. **브라우저**: "172.217.31.142로 접속하자!"

---

## 5. DNS 캐싱

### **DNS 캐싱이란?**

**DNS 캐싱**은 한 번 조회한 도메인 이름과 IP 주소를 일정 시간 동안 저장하여, 같은 도메인을 다시 조회할 때 빠르게 응답할 수 있게 하는 기능입니다.

### **캐싱 계층**

1. **브라우저 캐시**: 브라우저가 DNS 조회 결과를 캐싱
2. **OS 캐시**: 운영체제가 DNS 조회 결과를 캐싱
3. **Local DNS Server 캐시**: ISP의 DNS 서버가 조회 결과를 캐싱

### **TTL (Time To Live)**

**TTL**은 DNS 레코드가 캐시에 저장될 수 있는 시간을 의미합니다.

```
www.google.com.    300    IN    A    172.217.31.142
                    ↑
                  TTL (초)
```

- TTL이 300초면, 300초 동안 캐시에 저장됨
- TTL이 지나면 캐시에서 삭제되고 다시 조회

### **캐싱의 장점**

- ✅ DNS 쿼리 횟수 감소
- ✅ 응답 시간 단축
- ✅ 네트워크 트래픽 감소
- ✅ DNS 서버 부하 감소

---

## 6. DNS 레코드 타입

### **주요 DNS 레코드 타입**

#### 1. A 레코드 (Address Record)

- 도메인 이름을 IPv4 주소로 매핑
- 가장 일반적으로 사용되는 레코드

```
www.google.com.    IN    A    172.217.31.142
```

#### 2. AAAA 레코드 (IPv6 Address Record)

- 도메인 이름을 IPv6 주소로 매핑

```
www.google.com.    IN    AAAA    2001:4860:4860::8888
```

#### 3. CNAME 레코드 (Canonical Name Record)

- 도메인 이름을 다른 도메인 이름으로 매핑 (별칭)

```
www.example.com.    IN    CNAME    example.com.
```

#### 4. MX 레코드 (Mail Exchange Record)

- 이메일 서버의 주소를 지정

```
example.com.    IN    MX    10    mail.example.com.
```

#### 5. NS 레코드 (Name Server Record)

- 도메인의 네임서버를 지정

```
example.com.    IN    NS    ns1.example.com.
```

#### 6. TXT 레코드 (Text Record)

- 텍스트 정보를 저장 (SPF, DKIM 등)

```
example.com.    IN    TXT    "v=spf1 include:_spf.google.com ~all"
```

---

## 7. DNS 보안

### **DNS 보안 위협**

#### 1. DNS 스푸핑 (DNS Spoofing)

- 악의적인 DNS 서버가 잘못된 IP 주소를 반환
- 사용자를 가짜 웹사이트로 유도

#### 2. DNS 캐시 포이즈닝 (DNS Cache Poisoning)

- DNS 캐시에 잘못된 정보를 주입
- 모든 사용자가 잘못된 IP 주소로 접속

### **DNS 보안 대책**

#### 1. DNSSEC (DNS Security Extensions)

- DNS 응답의 무결성을 검증
- 위조된 DNS 응답을 방지

#### 2. DNS over HTTPS (DoH)

- DNS 쿼리를 HTTPS로 암호화
- DNS 쿼리 내용을 제3자가 볼 수 없음

#### 3. DNS over TLS (DoT)

- DNS 쿼리를 TLS로 암호화
- DoH와 유사한 보안 기능 제공

---

## 요약

- **DNS**는 사람이 읽을 수 있는 도메인 이름을 IP 주소로 변환하는 인터넷의 전화번호부 역할을 하는 시스템입니다.
- **도메인 이름 구조**: 최상위 도메인(TLD) → 2차 도메인 → 서브도메인
- **DNS 서버 계층**: Root DNS Server → TLD DNS Server → Authoritative DNS Server → Local DNS Server
- **DNS 쿼리 과정**: 사용자 → Local DNS Server → Root → TLD → Authoritative → IP 주소 반환
- **DNS 캐싱**: 한 번 조회한 결과를 TTL 시간 동안 저장하여 성능 향상
- **DNS 레코드 타입**: A, AAAA, CNAME, MX, NS, TXT 등
- **DNS 보안**: DNSSEC, DoH, DoT 등을 통한 보안 강화

---

### 설정 시 반드시 고려해야 할 파라미터

- **DNS 서버 설정**: 
  - 공용 DNS 서버: Google DNS (8.8.8.8), Cloudflare DNS (1.1.1.1)
  - ISP 제공 DNS 서버 사용

- **TTL 설정**:
  - 짧은 TTL: IP 주소 변경 시 빠른 반영, 하지만 DNS 쿼리 증가
  - 긴 TTL: DNS 쿼리 감소, 하지만 IP 주소 변경 시 반영 지연

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "DNS는 단일 서버에서 모든 도메인을 관리한다"는 생각은 잘못되었습니다. DNS는 분산 데이터베이스 시스템으로, 전 세계에 수많은 DNS 서버가 계층적으로 구성되어 있습니다. 또한 "DNS 쿼리는 항상 Root DNS Server부터 시작한다"는 것도 정확하지 않습니다. Local DNS Server의 캐시에 정보가 있으면 Root DNS Server까지 가지 않고 즉시 응답합니다.

#### DNS 모르면 발생하는 문제점

> [!error] 이걸 모르고 사용하면
> 
> - **도메인 접속 실패**: DNS 서버 설정이 잘못되면 도메인 이름으로 접속할 수 없습니다.
> - **느린 접속 속도**: 잘못된 DNS 서버를 사용하면 DNS 쿼리 시간이 길어져 웹사이트 접속이 느려집니다.
> - **DNS 스푸핑 공격**: DNS 보안을 고려하지 않으면 악의적인 공격에 노출될 수 있습니다.
> - **IP 주소 변경 반영 지연**: TTL 설정이 길면 IP 주소 변경이 늦게 반영될 수 있습니다.
> - **DNS 쿼리 과부하**: 캐싱을 제대로 활용하지 않으면 불필요한 DNS 쿼리가 증가합니다.

---

### 출처

- [DNS - Wikipedia](https://en.wikipedia.org/wiki/Domain_Name_System)
- [How DNS Works - Cloudflare](https://www.cloudflare.com/learning/dns/what-is-dns/)

---

### 예상 꼬리질문정리

#### 1. DNS란 무엇인가요?

- DNS(Domain Name System)는 사람이 읽을 수 있는 도메인 이름(예: www.google.com)을 컴퓨터가 이해할 수 있는 IP 주소(예: 172.217.31.142)로 변환하는 인터넷의 전화번호부 역할을 하는 시스템입니다.

#### 2. DNS 쿼리 과정을 설명해주세요.

1. 사용자가 브라우저에 도메인 이름 입력
2. Local DNS Server에 쿼리 전송
3. Local DNS Server가 캐시 확인 (있으면 즉시 반환)
4. Root DNS Server에 쿼리 → TLD DNS Server 주소 반환
5. TLD DNS Server에 쿼리 → Authoritative DNS Server 주소 반환
6. Authoritative DNS Server에 쿼리 → IP 주소 반환
7. Local DNS Server가 IP 주소를 캐시에 저장
8. 사용자에게 IP 주소 반환

#### 3. DNS 서버의 계층 구조는 어떻게 되나요?

- **Root DNS Server**: 최상위 계층, TLD DNS Server 주소 관리
- **TLD DNS Server**: 최상위 도메인(.com, .kr 등) 관리, Authoritative DNS Server 주소 관리
- **Authoritative DNS Server**: 특정 도메인의 IP 주소를 직접 관리
- **Local DNS Server**: ISP가 제공하는 DNS 서버, 사용자의 쿼리를 대신 처리하고 캐싱

#### 4. DNS 캐싱이란 무엇인가요?

- DNS 캐싱은 한 번 조회한 도메인 이름과 IP 주소를 일정 시간(TTL) 동안 저장하여, 같은 도메인을 다시 조회할 때 빠르게 응답할 수 있게 하는 기능입니다. 브라우저, OS, Local DNS Server 등 여러 계층에서 캐싱이 이루어집니다.

#### 5. TTL이란 무엇인가요?

- TTL(Time To Live)은 DNS 레코드가 캐시에 저장될 수 있는 시간을 의미합니다. TTL이 짧으면 IP 주소 변경이 빠르게 반영되지만 DNS 쿼리가 증가하고, TTL이 길면 DNS 쿼리는 감소하지만 IP 주소 변경 반영이 지연됩니다.

#### 6. 주요 DNS 레코드 타입은 무엇인가요?

- **A 레코드**: 도메인 이름을 IPv4 주소로 매핑
- **AAAA 레코드**: 도메인 이름을 IPv6 주소로 매핑
- **CNAME 레코드**: 도메인 이름을 다른 도메인 이름으로 매핑 (별칭)
- **MX 레코드**: 이메일 서버의 주소를 지정
- **NS 레코드**: 도메인의 네임서버를 지정
- **TXT 레코드**: 텍스트 정보를 저장 (SPF, DKIM 등)

#### 7. DNS 보안 위협과 대책은 무엇인가요?

- **위협**: DNS 스푸핑, DNS 캐시 포이즈닝 등
- **대책**: 
  - **DNSSEC**: DNS 응답의 무결성을 검증
  - **DNS over HTTPS (DoH)**: DNS 쿼리를 HTTPS로 암호화
  - **DNS over TLS (DoT)**: DNS 쿼리를 TLS로 암호화

---

## 관련 노트

- **[[인터넷 프로토콜]]** - DNS가 작동하는 인터넷 프로토콜
- **[[웹 통신]]** - DNS를 활용한 웹 통신 과정

---

**태그:** #network #dns #domain-name-system #인터넷 #면접 #개념정리
