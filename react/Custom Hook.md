# Custom Hook - 나만의 훅 만들기

#react #hooks #custom-hook #재사용성 #코드-분리 #면접 #개념정리

**관련 개념:** [[컴포넌트]] [[Props와 State]] [[useState]] [[useEffect]]

---

## 핵심 개념

Custom Hook은 React의 기본 훅(useState, useEffect 등)을 조합하여 **재사용 가능한 로직을 추출**한 함수입니다. 컴포넌트 간에 반복되는 로직(예: 입력 상태 관리, API 호출, 이벤트 등록 등)을 커스텀 훅으로 분리하면 코드를 더 깔끔하고 유지보수하기 쉽게 만들 수 있습니다.

---

## 기본 사용법

### Custom Hook 만들기

```jsx
// 파일: useInput.js
import { useState } from "react";

function useInput(initialValue) {
  const [value, setValue] = useState(initialValue);
  
  const onChange = (e) => {
    setValue(e.target.value);
  };
  
  return { value, onChange };
}

export default useInput;
```

### Custom Hook 사용하기

```jsx
import useInput from './useInput';

function MyComponent() {
  const name = useInput("");
  const email = useInput("");
  
  return (
    <div>
      <input value={name.value} onChange={name.onChange} />
      <input value={email.value} onChange={email.onChange} />
    </div>
  );
}
```

---

## Custom Hook의 규칙

### 1. 이름은 반드시 `use`로 시작

```jsx
// ✅ 올바른 예
function useInput() { }
function useFetch() { }
function useLocalStorage() { }

// ❌ 잘못된 예
function input() { }  // React가 훅으로 인식하지 못함
function getInput() { }  // use로 시작하지 않음
```

**이유**: React가 이 함수를 훅으로 인식하기 위해 필요

### 2. 훅의 규칙을 그대로 따라야 함

- 조건문, 반복문, 중첩 함수 안에서 호출 ❌
- 반드시 컴포넌트 또는 다른 커스텀 훅의 **최상단에서만 호출**

```jsx
// ❌ 잘못된 예
function MyComponent() {
  if (condition) {
    const value = useInput("");  // 조건문 안에서 호출 불가
  }
  
  for (let i = 0; i < 10; i++) {
    const value = useInput("");  // 반복문 안에서 호출 불가
  }
}

// ✅ 올바른 예
function MyComponent() {
  const value = useInput("");  // 최상단에서 호출
  
  if (condition) {
    // value 사용 가능
  }
}
```

---

## Custom Hook의 장점

### 1. 코드 재사용

반복되는 훅 로직을 추출해서 여러 컴포넌트에서 재사용 가능

```jsx
// 여러 컴포넌트에서 같은 로직 사용
function LoginForm() {
  const email = useInput("");
  const password = useInput("");
  // ...
}

function SignupForm() {
  const email = useInput("");  // 같은 훅 재사용
  const password = useInput("");
  // ...
}
```

### 2. 관심사 분리 (Separation of Concerns)

- UI와 로직을 깔끔하게 나눌 수 있음
- 컴포넌트는 **UI에 집중**, 훅은 **동작과 상태 처리에 집중**

```jsx
// ❌ 로직과 UI가 섞인 예
function MyComponent() {
  const [value, setValue] = useState("");
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    // 복잡한 로직...
  }, []);
  
  // UI 코드...
}

// ✅ Custom Hook으로 분리
function useData() {
  const [value, setValue] = useState("");
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    // 복잡한 로직...
  }, []);
  
  return { value, loading, error };
}

function MyComponent() {
  const { value, loading, error } = useData();
  // UI 코드만 집중
}
```

### 3. 가독성 향상

여러 훅이 섞인 로직을 하나의 함수로 캡슐화하여 컴포넌트가 더 간결해짐

### 4. 독립적인 상태 관리

커스텀 훅은 호출될 때마다 **자체적인 state와 effect**를 가짐

```jsx
function Component1() {
  const data = useData();  // 독립적인 상태
}

function Component2() {
  const data = useData();  // Component1과 별개의 상태
}
```

---

## 주의사항

### 1. 상태는 공유되지 않음

커스텀 훅은 **로직을 공유**하는 것이며, 내부 상태는 각각 독립적임

```jsx
function useCounter() {
  const [count, setCount] = useState(0);
  return { count, setCount };
}

function Component1() {
  const { count } = useCounter();  // count = 0
}

function Component2() {
  const { count } = useCounter();  // count = 0 (별개)
  // Component1의 count와 공유되지 않음
}
```

### 2. 남발은 피해야 함

단순하거나 짧은 로직까지 전부 커스텀 훅으로 만들면 코드가 지나치게 분리되어 오히려 가독성 저하

```jsx
// ❌ 과도한 분리
function useSingleState() {
  return useState(0);
}

// ✅ 적절한 분리
function useFormValidation() {
  // 복잡한 검증 로직이 있는 경우
}
```

---

## 실전 예시

### 1. API 호출 훅

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => {
        setData(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err);
        setLoading(false);
      });
  }, [url]);
  
  return { data, loading, error };
}

// 사용
function UserProfile({ userId }) {
  const { data, loading, error } = useFetch(`/api/users/${userId}`);
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  return <div>{data.name}</div>;
}
```

### 2. 로컬 스토리지 훅

```jsx
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });
  
  const setValue = (value) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };
  
  return [storedValue, setValue];
}

// 사용
function MyComponent() {
  const [name, setName] = useLocalStorage('name', '');
  return <input value={name} onChange={(e) => setName(e.target.value)} />;
}
```

---

## 관련 노트

- [[컴포넌트]] - Custom Hook을 사용하는 컴포넌트
- [[Props와 State]] - 상태 관리와 Custom Hook
- [[useState]] - Custom Hook에서 사용하는 기본 훅
- [[useEffect]] - Custom Hook에서 사용하는 기본 훅

---

**태그:** #react #hooks #custom-hook #재사용성 #코드-분리 #면접 #개념정리

