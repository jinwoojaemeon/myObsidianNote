# React 개발 도구와 라이브러리

#react #라이브러리 #도구 #개발환경 #면접 #개념정리

**관련 개념:** [[컴포넌트|컴포넌트]] [[React 비동기 처리 (Promise, async-await, fetch, axios)|React 비동기 처리]]

> [!note] 이어서 읽기
> React 개발 도구를 이해하려면 **[[컴포넌트|컴포넌트]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

React 개발에 유용한 도구와 라이브러리들을 이해하고, 각각의 설치 방법과 기본 사용법을 학습한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- React 개발 도구와 라이브러리는 개발 생산성을 크게 향상시키고, 코드 품질을 유지하며, 사용자 경험을 개선합니다.

- ESLint와 Prettier는 코드 일관성을 유지하고 문법 오류를 사전에 방지하여 팀 협업을 원활하게 합니다.

- react-icons, styled-components, react-toastify 등은 UI 개발을 빠르고 효율적으로 만들어주며, react-hook-form과 yup은 폼 관리와 유효성 검사를 간편하게 처리합니다.

---

### **Core Concept (핵심 개념 정리)**

**react-icons**는 무료 아이콘을 컴포넌트처럼 사용할 수 있는 라이브러리입니다.

**styled-components**는 CSS-in-JS 방식으로 컴포넌트 스타일을 관리하고, 전역 테마 설정을 제공합니다.

**ESLint**는 코드 문법 오류를 검사하고, **Prettier**는 코드 포맷팅을 자동화합니다.

**dotenv**는 환경 변수를 관리하여 민감한 정보를 코드에서 분리합니다.

**react-toastify**는 간편한 알림 UI를 제공하고, **react-hook-form + yup**은 폼 관리와 유효성 검사를 쉽게 처리합니다.

**react-spinners**는 로딩 애니메이션을 제공하고, **json-server**는 백엔드 없이 REST API 서버를 빠르게 구축할 수 있게 해줍니다.

> [!tip] 핵심 포인트
> 이러한 도구와 라이브러리들을 적절히 조합하면 React 개발 생산성과 코드 품질을 크게 향상시킬 수 있습니다.

---

## 1. react-icons

- https://react-icons.github.io/react-icons/의 사용법 참고
- 무료 아이콘을 가져와서 컴포넌트처럼 사용할 수 있다
- 다양한 브랜드 아이콘, 시스템 아이콘 지원

### **설치**

```bash
npm install react-icons
```

### **사용법**

```jsx
import { FaBeer } from "react-icons/fa";

function Example() {
  return <h3>리엑트 화이팅 <FaBeer /></h3>;
}
```

### **다양한 아이콘 사용**

```jsx
import { FaBeer, FaHome, FaUser } from "react-icons/fa";
import { MdEmail, MdSettings } from "react-icons/md";
import { AiOutlineHeart, AiFillHeart } from "react-icons/ai";

function IconExample() {
  return (
    <div>
      <FaBeer />
      <FaHome />
      <FaUser />
      <MdEmail />
      <MdSettings />
      <AiOutlineHeart />
      <AiFillHeart />
    </div>
  );
}
```

---

## 2. styled-components (테마 & 글로벌 스타일)

- https://styled-components.com/의 사용법 참고
- 전역 테마(다크모드/라이트모드 등)를 쉽게 설정할 수 있다
- ThemeProvider로 테마를 공급하고, 컴포넌트에서 사용
- `styled-components`에서 제공하는 **전역 스타일 설정 도구는 GlobalStyle**
- `CSS 초기화(Reset)`나, `body 배경색`, `글꼴`, `전체 마진 제거` 등에 주로 사용

### **설치**

```bash
npm install styled-components
```

### **GlobalStyle 설정**

```jsx
import { createGlobalStyle } from "styled-components";

const GlobalStyle = createGlobalStyle`
  *, *::before, *::after {
    box-sizing: border-box;
  }

  body {
    margin: 0;
    padding: 0;
    font-family: 'Pretendard', 'Segoe UI', sans-serif;
    background-color: ${({ theme }) => theme.background};
    color: ${({ theme }) => theme.text};
    transition: all 0.3s;
  }

  a {
    text-decoration: none;
    color: inherit;
  }

  button {
    font-family: inherit;
    cursor: pointer;
  }
`;

export default GlobalStyle;
```

### **테마 설정**

```jsx
import { ThemeProvider } from "styled-components";
import GlobalStyle from "./GlobalStyle";

const theme = {
  colors: {
    primary: "#0070f3",
    secondary: "#1db954",
  },
  background: "#ffffff",
  text: "#000000",
};

function App() {
  return (
    <ThemeProvider theme={theme}>
      <GlobalStyle />
      <YourComponent />
    </ThemeProvider>
  );
}
```

### **컴포넌트에서 테마 사용**

```jsx
import styled from "styled-components";

const Button = styled.button`
  background: ${({ theme }) => theme.colors.primary};
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  
  &:hover {
    background: ${({ theme }) => theme.colors.secondary};
  }
`;
```

### **다크모드 예시**

```jsx
import { useState } from "react";
import { ThemeProvider } from "styled-components";

const lightTheme = {
  background: "#ffffff",
  text: "#000000",
  colors: {
    primary: "#0070f3",
  },
};

const darkTheme = {
  background: "#1a1a1a",
  text: "#ffffff",
  colors: {
    primary: "#0070f3",
  },
};

function App() {
  const [isDark, setIsDark] = useState(false);

  return (
    <ThemeProvider theme={isDark ? darkTheme : lightTheme}>
      <GlobalStyle />
      <button onClick={() => setIsDark(!isDark)}>
        {isDark ? "라이트 모드" : "다크 모드"}
      </button>
      <YourComponent />
    </ThemeProvider>
  );
}
```

---

## 3. ESLint + Prettier

- https://prettier.io/playground/를 참고
- 코드를 **일관되게 유지**하고 **문법 오류**를 사전에 막아준다
- Prettier는 **자동 포맷팅** 담당

### **설치**

```bash
npm install --save-dev eslint prettier eslint-config-prettier eslint-plugin-prettier eslint-plugin-react eslint-plugin-react-hooks
```

### **확장 프로그램 설치 (필수)**

VSCode 확장 탭에서 아래 2개 설치:

- ✅ **ESLint**
- ✅ **Prettier - Code formatter**

### **설정 파일**

#### **.eslintrc.js**

```js
module.exports = {
  env: {
    // 브라우저 전역 객체(window 등)를 사용할 수 있게 설정
    browser: true,
    // 최신 ES2021 문법을 허용
    es2021: true,
    // Node.js 환경에서도 돌아가도록 설정 (예: process 사용)
    node: true,
  },
  extends: [
    // ESLint 기본 권장 규칙 사용
    'eslint:recommended',
    // React 관련 규칙 기본 제공
    'plugin:react/recommended',
    // Prettier와 충돌하는 ESLint 규칙 제거 + Prettier 오류를 ESLint 오류로 처리
    'plugin:prettier/recommended',
  ],
  parserOptions: {
    ecmaFeatures: {
      // JSX 문법을 파싱할 수 있게 설정
      jsx: true,
    },
    // 최신 ECMAScript 버전 지원 (예: ES2022+)
    ecmaVersion: 'latest',
    // import/export를 사용할 수 있게 모듈 소스 타입 지정
    sourceType: 'module',
  },
  plugins: [
    'react',        // React 관련 규칙 활성화
    'react-hooks',  // React Hooks 관련 규칙 활성화
  ],
  rules: {
    // React 17+에서는 자동 import가 필요 없기 때문에 꺼줌
    'react/react-in-jsx-scope': 'off',

    // PropTypes 사용을 강제하지 않음
    'react/prop-types': 'off',

    // Hooks 규칙 위반은 에러 처리 (useEffect, useState 등 사용 규칙)
    'react-hooks/rules-of-hooks': 'error',

    // useEffect 등에서 의존성 배열 누락 시 경고
    'react-hooks/exhaustive-deps': 'warn',
  },
  settings: {
    react: {
      // 설치된 React 버전을 자동 감지 (버전에 따라 적절한 규칙 적용됨)
      version: 'detect',
    },
  },
  ignores: [
    'node_modules',
    'dist',
    'build',
    '.eslintrc.js',
    'vite.config.js',
    'webpack.config.js',
  ],
};
```

#### **.prettierrc**

```json
{
  "semi": true,
  "tabWidth": 2,
  "useTabs": false,
  "printWidth": 120,
  "singleQuote": true,
  "trailingComma": "es5",
  "bracketSpacing": true,
  "arrowParens": "always"
}
```

설정 옵션 설명:

- `semi`: 명령문의 끝에 항상 세미콜론(;)을 붙임
- `tabWidth`: 들여쓰기 너비를 2칸으로 설정
- `useTabs`: 들여쓰기 시 탭(tab) 대신 공백(space)을 사용
- `printWidth`: 한 줄의 최대 길이를 120자로 제한
- `singleQuote`: 문자열에 큰따옴표(") 대신 작은따옴표(') 사용
- `trailingComma`: 객체나 배열 등에서 마지막 요소 뒤에 쉼표 추가
- `bracketSpacing`: 객체 리터럴에서 중괄호 사이에 공백 추가
- `arrowParens`: 화살표 함수의 매개변수가 하나여도 괄호를 항상 붙임

#### **package.json 스크립트 추가**

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint . --ext .js,.jsx",
    "lint:fix": "eslint . --ext .js,.jsx --fix",
    "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
    "preview": "vite preview"
  }
}
```

### **VS Code 자동 설정**

**프로젝트 루트에 `.vscode` 폴더가 없다면 직접 만들고 그 안에 `settings.json` 추가**

```bash
mkdir .vscode
echo {} > .vscode\settings.json
```

**`.vscode/settings.json` 내용**

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll": true,
    "source.fixAll.eslint": true
  },
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

### **사용법**

**ESLint 사용법**:

- 코드 검사: `npm run lint`
- 자동 수정: `npm run lint:fix`

**Prettier 사용법**:

- 코드 포맷팅: `npm run format`

---

## 4. dotenv (.env 파일)

- 민감한 정보를 `.env` 파일로 분리해서 관리
- React에서는 기본 지원 (.env 파일 만들면 됨)

### **사용 예시**

#### **.env 파일**

```
# Vite 환경에서는 무조건 접두사 VITE_로 시작
VITE_API_URL=https://yourapi.com
VITE_API_KEY=your-api-key-here
```

#### **코드에서 사용**

```jsx
// Vite 번들러는 환경 변수를 가져올 때 import.meta를 통해서 가져옴
const apiUrl = import.meta.env.VITE_API_URL;
const apiKey = import.meta.env.VITE_API_KEY;

// 기존 Create React App 환경에서 사용 시
// const apiUrl = process.env.REACT_APP_API_URL;
```

### **주의사항**

- `.env` 파일은 절대 Git에 커밋하지 않기 (`.gitignore`에 추가)
- `.env.example` 파일을 만들어서 필요한 환경 변수 목록만 공유

---

## 5. react-toastify

- https://fkhadra.github.io/react-toastify/introduction/을 참고
- 요청 성공/실패 등 알림을 간편하게 보여준다

### **설치**

```bash
npm install react-toastify
```

### **기본 사용법**

```jsx
import { ToastContainer, toast } from "react-toastify";
import "react-toastify/dist/ReactToastify.css";

function App() {
  const handleSuccess = () => {
    toast.success("요청이 성공했습니다!");
  };

  const handleError = () => {
    toast.error("에러가 발생했습니다!");
  };

  return (
    <div>
      <button onClick={handleSuccess}>성공 알림</button>
      <button onClick={handleError}>에러 알림</button>
      <ToastContainer />
    </div>
  );
}
```

### **커스텀 설정**

```jsx
import { nanoid } from 'nanoid';
import { toast } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';

const defaultOption = {
  position: 'top-right',
  autoClose: 2500,
  hideProgressBar: false,
  closeOnClick: true,
  pauseOnHover: false,
  draggable: false,
  progress: undefined,
  theme: 'light',
};

let activeToastId = null;

export const performToast = ({ msg, type }) => {
  if (activeToastId && toast.isActive(activeToastId)) return;

  activeToastId = nanoid();

  const options = {
    ...defaultOption,
    toastId: activeToastId,
  };

  switch (type) {
    case 'error':
      return toast.error(msg, options);
    case 'success':
      return toast.success(msg, options);
    case 'warning':
      return toast.warn(msg, options);
    default:
      return;
  }
};
```

### **사용 예시**

```jsx
import { performToast } from './utils/toast';

function MyComponent() {
  const handleSubmit = async () => {
    try {
      await submitData();
      performToast({ msg: '저장되었습니다!', type: 'success' });
    } catch (error) {
      performToast({ msg: '저장에 실패했습니다.', type: 'error' });
    }
  };

  return <button onClick={handleSubmit}>제출</button>;
}
```

---

## 6. react-hook-form + yup

- https://react-hook-form.com/를 참고
- **폼 입력 상태 관리 + 검증**을 아주 쉽게
- yup은 스키마 기반 검증 도구

### **설치**

```bash
npm install react-hook-form @hookform/resolvers yup
```

### **기본 사용법**

```jsx
import { useForm } from "react-hook-form";
import { yupResolver } from "@hookform/resolvers/yup";
import * as yup from "yup";

const schema = yup.object({
  name: yup.string().required("이름은 필수입니다."),
  email: yup.string().email("올바른 이메일을 입력하세요.").required("이메일은 필수입니다."),
  age: yup.number().positive("나이는 양수여야 합니다.").required("나이는 필수입니다."),
});

function MyForm() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: yupResolver(schema),
  });

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("name")} placeholder="이름" />
      <p>{errors.name?.message}</p>
      
      <input {...register("email")} type="email" placeholder="이메일" />
      <p>{errors.email?.message}</p>
      
      <input {...register("age")} type="number" placeholder="나이" />
      <p>{errors.age?.message}</p>
      
      <button type="submit">제출</button>
    </form>
  );
}
```

### **주요 개념**

- `yup.object().shape({...})`: 객체 형태로 유효성 검사를 정의함 (폼 전체 구조를 의미)
- `name: yup.string().required(...)`: name 필드는 문자열이고 필수 입력이라는 뜻
- `register()`: 입력 요소에 연결해서 값 추적 + 유효성 검사 적용
- `handleSubmit()`: 폼 제출 이벤트를 핸들링 (검사 통과 시 onSubmit 호출)
- `errors`: 각 필드의 유효성 검사 결과(에러 객체)

### **고급 사용법**

```jsx
import { useForm } from "react-hook-form";
import { yupResolver } from "@hookform/resolvers/yup";
import * as yup from "yup";

const schema = yup.object({
  password: yup.string().min(8, "비밀번호는 8자 이상이어야 합니다.").required(),
  confirmPassword: yup.string()
    .oneOf([yup.ref("password")], "비밀번호가 일치하지 않습니다.")
    .required(),
});

function PasswordForm() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: yupResolver(schema),
  });

  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("password")} type="password" />
      <p>{errors.password?.message}</p>
      
      <input {...register("confirmPassword")} type="password" />
      <p>{errors.confirmPassword?.message}</p>
      
      <button type="submit">제출</button>
    </form>
  );
}
```

---

## 7. react-spinners (로딩 컴포넌트)

- https://www.davidhu.io/react-spinners/를 참고
- 네트워크 요청 중 깔끔한 로딩 애니메이션 보여주기

### **설치**

```bash
npm install react-spinners
```

### **기본 사용법**

```jsx
import { ClipLoader } from "react-spinners";

function Loader() {
  return <ClipLoader color="#36d7b7" />;
}
```

### **다양한 스피너 사용**

```jsx
import { ClipLoader, BeatLoader, PulseLoader, RingLoader } from "react-spinners";

function LoadingExample() {
  return (
    <div>
      <ClipLoader color="#36d7b7" size={50} />
      <BeatLoader color="#36d7b7" size={15} />
      <PulseLoader color="#36d7b7" size={10} />
      <RingLoader color="#36d7b7" size={60} />
    </div>
  );
}
```

### **조건부 렌더링 예시**

```jsx
import { useState, useEffect } from "react";
import { ClipLoader } from "react-spinners";
import axios from "axios";

function DataComponent() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchData() {
      try {
        const res = await axios.get("https://jsonplaceholder.typicode.com/posts/1");
        setData(res.data);
      } catch (error) {
        console.error(error);
      } finally {
        setLoading(false);
      }
    }
    fetchData();
  }, []);

  if (loading) {
    return (
      <div style={{ display: 'flex', justifyContent: 'center', alignItems: 'center', height: '100vh' }}>
        <ClipLoader color="#36d7b7" size={50} />
      </div>
    );
  }

  return <div>{data && <h1>{data.title}</h1>}</div>;
}
```

---

## 8. json-server

- **json-server**는 **간단한 JSON 파일만 있으면, 바로 완전한 REST API 서버를 만들어주는 개발 도구**
- API 서버를 별도로 만들지 않아도, **GET, POST, PUT, PATCH, DELETE 요청**을 다 테스트할 수 있음
- 프론트엔드 개발자들이 **백엔드 없이 빠르게 CRUD 기능을 연습**할 때 매우 유용함

### **설치**

```bash
npm install -D json-server
```

### **데이터 파일 만들기**

**db.json (루트에 생성)**

```json
{
  "posts": [
    { "id": 1, "title": "첫 번째 글", "body": "내용입니다." },
    { "id": 2, "title": "두 번째 글", "body": "또 다른 내용입니다." }
  ],
  "users": [
    { "id": 1, "name": "홍길동" },
    { "id": 2, "name": "임꺽정" }
  ]
}
```

### **서버 실행**

```bash
npx json-server --watch db.json --port 3001
```

- `--watch`: 파일을 감시해서 수정되면 서버 자동 반영
- `--port`: 사용할 포트번호 지정 (기본 3000)

**실행 후 접근 가능한 엔드포인트:**

- `http://localhost:3001/posts` - GET, POST
- `http://localhost:3001/posts/1` - GET, PUT, PATCH, DELETE
- `http://localhost:3001/users` - GET, POST
- `http://localhost:3001/users/1` - GET, PUT, PATCH, DELETE

### **package.json 스크립트 추가**

```json
{
  "scripts": {
    "server": "json-server --watch db.json --port 3001"
  }
}
```

이후 `npm run server`로 실행 가능

### **사용 예시**

```jsx
import { useEffect, useState } from "react";
import axios from "axios";

function PostList() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    async function fetchPosts() {
      const res = await axios.get("http://localhost:3001/posts");
      setPosts(res.data);
    }
    fetchPosts();
  }, []);

  const handleCreate = async () => {
    const newPost = { title: "새 글", body: "내용" };
    await axios.post("http://localhost:3001/posts", newPost);
    // 목록 새로고침
  };

  const handleDelete = async (id) => {
    await axios.delete(`http://localhost:3001/posts/${id}`);
    // 목록 새로고침
  };

  return (
    <div>
      {posts.map(post => (
        <div key={post.id}>
          <h3>{post.title}</h3>
          <p>{post.body}</p>
          <button onClick={() => handleDelete(post.id)}>삭제</button>
        </div>
      ))}
    </div>
  );
}
```

### **고급 기능**

#### **필터링**

```
GET http://localhost:3001/posts?title=첫
```

#### **정렬**

```
GET http://localhost:3001/posts?_sort=id&_order=desc
```

#### **페이지네이션**

```
GET http://localhost:3001/posts?_page=1&_limit=10
```

---

## 요약

- **react-icons**: 무료 아이콘을 컴포넌트처럼 사용할 수 있는 라이브러리
- **styled-components**: CSS-in-JS 방식으로 스타일 관리 및 전역 테마 설정
- **ESLint + Prettier**: 코드 품질 유지 및 자동 포맷팅
- **dotenv**: 환경 변수 관리로 민감한 정보 분리
- **react-toastify**: 간편한 알림 UI 제공
- **react-hook-form + yup**: 폼 관리와 유효성 검사 간소화
- **react-spinners**: 로딩 애니메이션 제공
- **json-server**: 백엔드 없이 REST API 서버 구축

---

## 참고 자료

- [react-icons 공식 문서](https://react-icons.github.io/react-icons/)
- [styled-components 공식 문서](https://styled-components.com/)
- [ESLint 공식 문서](https://eslint.org/)
- [Prettier 공식 문서](https://prettier.io/)
- [react-toastify 공식 문서](https://fkhadra.github.io/react-toastify/)
- [react-hook-form 공식 문서](https://react-hook-form.com/)
- [react-spinners 공식 문서](https://www.davidhu.io/react-spinners/)
- [json-server GitHub](https://github.com/typicode/json-server)

