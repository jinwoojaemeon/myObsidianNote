# npm run build (빌드 명령어)

#npm #빌드 #package.json #프론트엔드 #개발도구 #면접 #개념정리

**관련 개념:** [[package.json]] [[번들링]] [[배포]] [[프로덕션 빌드]] [[스크립트]]

---

## Why (왜 사용하는가? 왜 중요한가?)

- **실무**: `npm run build` 없이는 개발 환경 코드를 그대로 배포해야 하므로, 파일 크기가 크고 최적화되지 않아 로딩 속도가 느립니다. 또한 소스맵이나 디버깅 정보가 노출되어 보안 문제가 발생할 수 있습니다.

- **구조적 의미**: 개발 코드를 프로덕션용으로 최적화하여 번들 크기를 줄이고, 코드를 압축/난독화하며, 불필요한 파일을 제거합니다. 이를 통해 배포 파일 크기를 최소화하고 런타임 성능을 극대화합니다.

- **면접 의도**: 빌드 프로세스에 대한 이해, 개발 환경과 프로덕션 환경의 차이, 그리고 배포 전 최적화 과정을 아는지 확인하려 합니다. 현대 프론트엔드 개발 워크플로우에 대한 지식을 평가합니다.

---

## Core Concept (핵심 개념 정리)

### 개념 정의

**`npm run build`**는 `package.json`의 `scripts` 섹션에 정의된 `build` 스크립트를 실행하는 명령어입니다. 이 명령어는 개발 환경의 소스 코드를 프로덕션 배포용으로 변환, 최적화, 번들링하는 과정을 수행합니다.

> [!tip] 중요한 특징
> `npm run build`는 실제로 `package.json`의 `scripts.build` 필드를 찾아서 실행합니다. 각 프로젝트마다 빌드 설정이 다를 수 있으며, React는 `react-scripts build`, Vue는 `vue-cli-service build` 등을 사용합니다.

---

### 동작 방식

```
npm run build 실행 흐름:

1. 사용자가 터미널에서 "npm run build" 입력
      ↓
2. npm이 package.json의 scripts 섹션 확인
   {
     "scripts": {
       "build": "react-scripts build"
     }
   }
      ↓
3. scripts.build에 정의된 명령어 실행
   (예: react-scripts build, vue-cli-service build)
      ↓
4. 빌드 도구가 소스 코드 분석
   - Entry point 찾기
   - 의존성 그래프 생성
      ↓
5. 번들링 및 최적화
   - 코드 트랜스파일 (Babel, TypeScript 등)
   - 코드 압축 (Minification)
   - 코드 난독화 (Uglification)
   - 트리 쉐이킹 (사용하지 않는 코드 제거)
      ↓
6. 정적 자산 처리
   - 이미지 최적화
   - CSS 처리 및 최적화
   - 폰트 파일 복사
      ↓
7. build/ 또는 dist/ 폴더에 결과물 생성
   - 최적화된 JavaScript 파일
   - 최적화된 CSS 파일
   - 정적 자산들
   - index.html
```

**관련 개념:** [[package.json]] [[번들링]] [[트랜스파일링]] [[최적화]]

---

### 장점과 단점

> [!success] 장점
> 
> - ✅ **성능 최적화**: 코드 압축, 난독화로 파일 크기 감소
> - ✅ **프로덕션 준비**: 개발용 코드를 배포용으로 변환
> - ✅ **보안 강화**: 소스맵 제거, 디버깅 정보 제거
> - ✅ **로딩 속도 향상**: 최적화된 번들로 빠른 로딩
> - ✅ **호환성 보장**: 최신 문법을 구형 브라우저용으로 변환

> [!warning] 단점
> 
> - ❌ **빌드 시간**: 대규모 프로젝트는 빌드에 시간이 걸림
> - ❌ **디버깅 어려움**: 압축/난독화로 인한 디버깅 복잡도 증가
> - ❌ **설정 복잡도**: 빌드 도구 설정이 복잡할 수 있음

---

> [!info] 필요 조건
> 
> `npm run build`를 실행하려면:
> - **package.json 파일**: 프로젝트 루트에 존재해야 함
> - **scripts.build 필드**: package.json의 scripts 섹션에 build 명령어 정의
> - **빌드 도구**: react-scripts, vue-cli-service, webpack, vite 등
> - **의존성 설치**: `npm install`로 node_modules 설치 완료

---

### package.json scripts 예시

```json
{
  "scripts": {
    "build": "react-scripts build",
    "build:prod": "NODE_ENV=production react-scripts build",
    "build:analyze": "npm run build && npx webpack-bundle-analyzer build/static/js/*.js"
  }
}
```

| 스크립트 | 설명 |
|:---:|:---:|
| **build** | 기본 빌드 명령어 |
| **build:prod** | 프로덕션 환경 변수와 함께 빌드 |
| **build:analyze** | 빌드 후 번들 크기 분석 |

---

### 개발 빌드 vs 프로덕션 빌드 비교

| 구분 | 개발 빌드 (npm start) | 프로덕션 빌드 (npm run build) |
|:---:|:---:|:---:|
| **목적** | 개발 중 빠른 피드백 | 배포용 최적화 |
| **파일 크기** | 큼 (소스맵 포함) | 작음 (압축됨) |
| **코드 가독성** | 높음 (포맷팅 유지) | 낮음 (압축/난독화) |
| **빌드 속도** | 빠름 (HMR 지원) | 느림 (최적화 과정) |
| **디버깅** | 쉬움 (소스맵) | 어려움 (압축됨) |
| **에러 메시지** | 상세함 | 간소화됨 |
| **사용 시점** | 개발 중 | 배포 전 |

---

## Interview Answer Version (면접 답변식 요약)

`npm run build`는 package.json의 scripts 섹션에 정의된 build 명령어를 실행하는 명령어입니다.

사용하는 이유는 개발 환경의 코드를 프로덕션 배포용으로 최적화하기 위해서입니다. 소스 코드를 압축하고 난독화하여 파일 크기를 줄이고, 사용하지 않는 코드를 제거하며, 정적 자산을 최적화합니다.

핵심 특징은 각 프로젝트의 package.json에 정의된 빌드 스크립트를 찾아서 실행한다는 점입니다. React는 react-scripts build, Vue는 vue-cli-service build를 사용하며, 이 과정을 통해 build나 dist 폴더에 최적화된 배포 파일이 생성됩니다.

---

## Practical Tip (사용시 주의할 점 or 활용 예)

### 설정 시 반드시 고려해야 할 파라미터

- **환경 변수**: `NODE_ENV=production` 설정 확인
- **빌드 출력 경로**: build/, dist/ 등 출력 폴더 확인
- **소스맵 생성**: 프로덕션에서는 소스맵 제거 권장 (보안)
- **번들 크기 제한**: 큰 번들 경고 설정

### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "npm run build는 항상 같은 결과를 만든다"는 생각은 잘못되었습니다. package.json의 scripts.build에 정의된 명령어에 따라 빌드 도구와 설정이 달라지며, 환경 변수나 설정 파일에 따라 결과가 달라질 수 있습니다.

### 적용 사례

- **React**: `"build": "react-scripts build"` → build/ 폴더 생성
- **Vue**: `"build": "vue-cli-service build"` → dist/ 폴더 생성
- **Next.js**: `"build": "next build"` → .next/ 폴더 생성
- **Vite**: `"build": "vite build"` → dist/ 폴더 생성

### 이걸 모르고 사용하면 생기는 문제

> [!error] 이걸 모르고 사용하면
> 
> - **빌드 실패**: package.json에 build 스크립트가 없으면 에러 발생
> - **환경 변수 누락**: 프로덕션 API URL 등 환경 변수 설정 안 하면 개발 환경 연결
> - **번들 크기 폭증**: 최적화 설정 없이 빌드하면 파일 크기가 커짐
> - **소스맵 노출**: 프로덕션에서 소스맵 포함 시 보안 문제
> - **캐싱 문제**: 파일명 해시 없이 빌드하면 브라우저 캐싱 문제 발생

---

## 예상 꼬리질문 정리

### 1. package.json에 build 스크립트가 없으면 어떻게 되나?

- **에러 발생**: `npm ERR! missing script: build` 에러
- **해결 방법**: package.json의 scripts 섹션에 build 명령어 추가
- **확인 방법**: `npm run` 명령어로 사용 가능한 스크립트 목록 확인

### 2. 빌드 결과물은 어디에 생성되나?

- **프로젝트마다 다름**: 
  - React: `build/` 폴더
  - Vue: `dist/` 폴더
  - Next.js: `.next/` 폴더
- **설정 변경 가능**: webpack.config.js, vite.config.js 등에서 출력 경로 설정

### 3. 빌드 전에 해야 할 작업은?

- **의존성 설치**: `npm install` 완료 확인
- **환경 변수 설정**: `.env.production` 파일 확인
- **테스트 실행**: `npm test` 또는 `npm run test` 실행
- **린트/포맷팅**: `npm run lint` 실행

### 4. 빌드 최적화 방법은?

- **코드 스플리팅**: 라우트별, 컴포넌트별 분할
- **트리 쉐이킹**: 사용하지 않는 코드 제거
- **압축**: Gzip, Brotli 압축
- **이미지 최적화**: WebP 변환, 압축
- **번들 분석**: webpack-bundle-analyzer로 크기 확인

### 5. npm run build와 npm build의 차이는?

- **npm run build**: scripts 섹션의 build 명령어 실행 (권장)
- **npm build**: npm의 내장 명령어 (거의 사용 안 함)
- **차이점**: `npm run`을 사용하면 커스텀 스크립트를 실행할 수 있음

---

## 관련 노트

- [[package.json]] - package.json 상세 설명
- [[번들링]] - 번들링과 최적화
- [[배포]] - 배포 프로세스
- [[프로덕션 빌드]] - 프로덕션 환경 빌드
- [[스크립트]] - npm scripts 활용법
- [[환경 변수]] - 환경 변수 관리

---

**태그:** #npm #빌드 #package.json #프론트엔드 #개발도구 #면접 #개념정리

