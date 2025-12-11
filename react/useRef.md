# useRef - 참조 관리

#react #hooks #useref #ref #dom #면접 #개념정리

**관련 개념:** [[컴포넌트]] [[Props와 State]] [[가상 DOM]] [[리렌더링]]

---

## 핵심 개념

`useRef`는 React에서 DOM 요소나 값을 참조하기 위한 Hook입니다. `useState`와 달리 값이 변경되어도 컴포넌트가 리렌더링되지 않으며, 컴포넌트가 리렌더링되어도 값을 유지합니다.

---

## 기본 사용법

### 1. DOM 요소 참조

```jsx
import { useRef } from 'react';

function MyComponent() {
  const inputRef = useRef(null);
  
  const handleClick = () => {
    inputRef.current.focus();  // DOM 요소에 직접 접근
  };
  
  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleClick}>포커스</button>
    </div>
  );
}
```

### 2. 값 저장 (리렌더링 없이)

```jsx
import { useRef } from 'react';

function MyComponent() {
  const countRef = useRef(0);
  
  const handleClick = () => {
    countRef.current += 1;  // 리렌더링 없이 값 변경
    console.log(countRef.current);  // 1, 2, 3...
  };
  
  return <button onClick={handleClick}>클릭</button>;
}
```

---

## useRef의 특징

### 1. 리렌더링을 발생시키지 않음

```jsx
function MyComponent() {
  const [count, setCount] = useState(0);  // 리렌더링 발생
  const countRef = useRef(0);              // 리렌더링 발생 안 함
  
  const incrementState = () => {
    setCount(count + 1);  // 컴포넌트 리렌더링
  };
  
  const incrementRef = () => {
    countRef.current += 1;  // 리렌더링 없음
  };
  
  return (
    <div>
      <p>State: {count}</p>  {/* 화면에 표시됨 */}
      <p>Ref: {countRef.current}</p>  {/* 화면에 표시 안 됨 (리렌더링 없으므로) */}
      <button onClick={incrementState}>State 증가</button>
      <button onClick={incrementRef}>Ref 증가</button>
    </div>
  );
}
```

### 2. 컴포넌트 리렌더링 후에도 값 유지

```jsx
function MyComponent() {
  const renderCount = useRef(0);
  
  renderCount.current += 1;  // 리렌더링될 때마다 증가
  
  return <div>렌더링 횟수: {renderCount.current}</div>;
}
```

### 3. 이전 값 저장

```jsx
function MyComponent({ value }) {
  const prevValue = useRef();
  
  useEffect(() => {
    prevValue.current = value;  // 이전 값 저장
  });
  
  return (
    <div>
      <p>현재: {value}</p>
      <p>이전: {prevValue.current}</p>
    </div>
  );
}
```

---

## useRef vs useState 비교

| 구분 | useState | useRef |
|:---:|:---:|:---:|
| **리렌더링** | 값 변경 시 리렌더링 발생 | 값 변경 시 리렌더링 없음 |
| **값 접근** | 직접 접근 (변수명) | `.current`로 접근 |
| **용도** | 화면에 표시되는 상태 | DOM 참조, 이전 값 저장, 렌더링 불필요한 값 |
| **초기값** | useState(초기값) | useRef(초기값) |
| **값 변경** | setState(새값) | ref.current = 새값 |

---

## 주요 사용 사례

### 1. DOM 요소에 직접 접근

```jsx
function ScrollToTop() {
  const topRef = useRef(null);
  
  const scrollToTop = () => {
    topRef.current.scrollIntoView({ behavior: 'smooth' });
  };
  
  return (
    <div>
      <div ref={topRef}>맨 위</div>
      <button onClick={scrollToTop}>위로 이동</button>
    </div>
  );
}
```

### 2. 이전 props/state 값 저장

```jsx
function MyComponent({ userId }) {
  const prevUserId = useRef();
  
  useEffect(() => {
    if (prevUserId.current !== userId) {
      console.log('사용자 변경:', prevUserId.current, '→', userId);
    }
    prevUserId.current = userId;
  }, [userId]);
  
  return <div>User ID: {userId}</div>;
}
```

### 3. 타이머 ID 저장

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const timerRef = useRef(null);
  
  const start = () => {
    timerRef.current = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);
  };
  
  const stop = () => {
    clearInterval(timerRef.current);
  };
  
  useEffect(() => {
    return () => {
      clearInterval(timerRef.current);  // cleanup
    };
  }, []);
  
  return (
    <div>
      <p>{seconds}초</p>
      <button onClick={start}>시작</button>
      <button onClick={stop}>정지</button>
    </div>
  );
}
```

### 4. 렌더링 횟수 추적

```jsx
function MyComponent() {
  const renderCount = useRef(0);
  renderCount.current += 1;
  
  return <div>렌더링 횟수: {renderCount.current}</div>;
}
```

---

## 주의사항

### 1. `.current`를 통해 접근

```jsx
// ✅ 올바른 사용
const ref = useRef(0);
ref.current = 10;      // 값 변경
console.log(ref.current);  // 값 읽기

// ❌ 잘못된 사용
ref = 10;  // 에러 발생
```

### 2. 초기 렌더링에서 이전 값은 undefined

```jsx
function MyComponent({ value }) {
  const prevValue = useRef();
  
  // 첫 렌더링: prevValue.current는 undefined
  // 두 번째 렌더링부터: 이전 값 저장됨
  
  useEffect(() => {
    prevValue.current = value;
  });
}
```

### 3. DOM 조작은 최소화

React는 가상 DOM을 통해 DOM을 관리하므로, ref로 직접 DOM을 조작하는 것은 최소화해야 합니다.

```jsx
// ❌ 과도한 DOM 조작
function MyComponent() {
  const divRef = useRef();
  
  useEffect(() => {
    divRef.current.style.color = 'red';
    divRef.current.style.fontSize = '20px';
    divRef.current.classList.add('active');
    // ...
  });
}

// ✅ CSS 클래스나 상태로 관리
function MyComponent() {
  const [isActive, setIsActive] = useState(false);
  return <div className={isActive ? 'active' : ''}>...</div>;
}
```

---

## useRef와 forwardRef

커스텀 컴포넌트에 ref를 전달하려면 `forwardRef`를 사용해야 합니다.

```jsx
import { forwardRef, useRef } from 'react';

const CustomInput = forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});

function MyComponent() {
  const inputRef = useRef(null);
  
  return (
    <CustomInput 
      ref={inputRef} 
      onFocus={() => inputRef.current.focus()} 
    />
  );
}
```

---

## 관련 노트

- [[컴포넌트]] - useRef를 사용하는 컴포넌트
- [[Props와 State]] - useState와 useRef의 차이
- [[가상 DOM]] - DOM 직접 조작과 가상 DOM
- [[리렌더링]] - useRef가 리렌더링을 발생시키지 않는 이유

---

**태그:** #react #hooks #useref #ref #dom #면접 #개념정리

