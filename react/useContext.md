# useContext - 전역처럼 데이터 공유하기

#react #hooks #usecontext #context #props-drilling #면접 #개념정리

**관련 개념:** [[컴포넌트]] [[Props와 State]] [[React Props]] [[단방향 데이터 흐름]]

---

## 핵심 개념

`useContext`는 React의 Context 시스템에서 데이터를 꺼내 쓰기 위한 Hook입니다. 복잡한 컴포넌트 구조에서 props를 여러 단계에 걸쳐 전달하는 대신, 원하는 컴포넌트에서 바로 데이터를 사용할 수 있게 해줍니다.

---

## 기본 사용법

### 1. Context 생성

```jsx
import { createContext } from 'react';

const MyContext = createContext();
// React가 사용할 Context 객체 생성
```

### 2. Context Provider로 값 공급

```jsx
<MyContext.Provider value={공유할_값}>
  {/* 이 내부의 컴포넌트들은 모두 useContext로 값 접근 가능 */}
  <ChildComponent />
</MyContext.Provider>
```

### 3. 하위 컴포넌트에서 값 소비

```jsx
import { useContext } from 'react';

function ChildComponent() {
  const value = useContext(MyContext);
  // MyContext: createContext()로 생성한 컨텍스트 객체
  // value: Provider에서 전달한 value를 가져옴
  
  return <div>{value}</div>;
}
```

---

## 작동 원리

```
1. Context 생성
   createContext() → Context 객체 생성
      ↓
2. Provider로 값 전달
   <Context.Provider value={...}> → 최상위에서 값 공급
      ↓
3. useContext로 값 소비
   useContext(Context) → 하위 컴포넌트에서 값 가져오기
```

---

## Context를 사용하는 이유

### Props Drilling 문제 해결

**문제**: Props를 여러 단계에 걸쳐 전달해야 함

```jsx
// ❌ Props Drilling 예시
function App() {
  const user = { name: "Jeameon" };
  return <Parent user={user} />;
}

function Parent({ user }) {
  return <Child user={user} />;
}

function Child({ user }) {
  return <GrandChild user={user} />;
}

function GrandChild({ user }) {
  return <div>{user.name}</div>; // 실제로 사용하는 곳
}
```

**해결**: Context로 직접 접근

```jsx
// ✅ Context 사용
const UserContext = createContext();

function App() {
  const user = { name: "Jeameon" };
  return (
    <UserContext.Provider value={user}>
      <Parent />
    </UserContext.Provider>
  );
}

function GrandChild() {
  const user = useContext(UserContext);
  return <div>{user.name}</div>; // 바로 접근 가능
}
```

### 장점

- **Props Drilling 제거**: 상위에서 하위로 props를 일일이 전달하지 않아도 됨
- **데이터 접근 단순화**: 원하는 컴포넌트에서 바로 데이터 접근 가능
- **글로벌 설정 관리 용이**: 로그인 정보, 테마, 다국어 설정 등 전역 상태 관리에 유용

---

## 전달되는 값의 변경에 따른 영향

- Context의 `value`가 변경되면, 해당 Context를 사용 중인 **모든 하위 컴포넌트가 자동으로 리렌더링**됩니다.
- 따라서, 자주 바뀌는 값이라면 불필요한 렌더링을 피하기 위한 최적화(useMemo, 분리 렌더링 등)가 필요할 수 있습니다.

---

## 주의사항

> [!warning] 주의
> 
> - **모든 상태를 Context에 담는 것은 비효율적일 수 있습니다.**
> - 값이 자주 바뀌거나 컴포넌트 수가 많아지면 성능 문제가 생길 수 있음
> - Context는 **자주 바뀌지 않는 전역적인 값**에 사용하는 것이 좋습니다
>   - ✅ 좋은 예: 테마, 사용자 정보, 다국어 설정
>   - ❌ 나쁜 예: 입력값, 실시간 카운터, 애니메이션 상태

---

## 실전 예시

### 테마 관리

```jsx
// ThemeContext.js
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  return useContext(ThemeContext);
}

// 사용
function App() {
  return (
    <ThemeProvider>
      <Header />
    </ThemeProvider>
  );
}

function Header() {
  const { theme, setTheme } = useTheme();
  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      현재 테마: {theme}
    </button>
  );
}
```

---

## 관련 노트

- [[컴포넌트]] - Context를 사용하는 컴포넌트
- [[Props와 State]] - Props Drilling 문제와 해결
- [[React Props]] - Props 전달 방식
- [[단방향 데이터 흐름]] - React의 데이터 흐름

---

**태그:** #react #hooks #usecontext #context #props-drilling #면접 #개념정리

