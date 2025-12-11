# Zustand (상태관리 라이브러리)

#react #zustand #상태관리 #전역상태 #redux #context-api #면접 #개념정리

**관련 개념:** [[Props와 State|Props와 State]] [[useContext|useContext]] [[컴포넌트|컴포넌트]]

> [!note] 이어서 읽기
> Zustand를 이해하려면 **[[Props와 State|Props와 State]]**와 **[[useContext|useContext]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

Zustand를 사용하여 React 프로젝트에서 전역 상태를 간단하고 효율적으로 관리하는 방법을 학습하고, Context API와의 차이점을 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- Zustand는 Redux나 MobX보다 훨씬 가볍고 간단한 API로 전역 상태를 관리할 수 있어, 복잡한 보일러플레이트 코드 없이 빠르게 상태 관리를 구현할 수 있습니다.

- 필요한 값만 구독하여 불필요한 리렌더링을 최소화하고, Provider 없이도 어디서든 상태를 사용할 수 있어 개발 생산성을 크게 향상시킵니다.

- 비동기 작업, 미들웨어 확장 등 고급 기능을 지원하면서도 코드가 짧고 직관적이어서, 중/대규모 프로젝트에서도 효율적으로 사용할 수 있습니다.

---

### **Core Concept (핵심 개념 정리)**

**Zustand**는 리액트 프로젝트에서 전역 상태(전역 스토어)를 아주 간단하게 관리할 수 있는 경량 상태관리 라이브러리입니다. Redux나 MobX보다 훨씬 가볍고, 코드가 짧고 직관적입니다.

**Store(스토어)**는 Zustand에서 "전역 상태"와 "상태 변경 함수(액션)"를 한 번에 담아두는 공간입니다. `create` 함수를 사용하여 스토어를 생성합니다.

**상태(state)**는 실제로 저장/관리할 값(예: count, user, 리스트 등)이고, **액션(action)**은 상태를 바꾸는 함수(예: increase, addUser 등)입니다.

> [!tip] 핵심 포인트
> Zustand는 "useState만큼 쉽고, useContext보다 강력한 전역 상태 관리"가 필요할 때 딱 적합합니다. 중/대규모 프로젝트에서 Redux보다 가볍게 전역 상태 관리가 필요할 때 사용합니다.

---

## 1. Zustand란?

- **리액트 프로젝트에서 전역 상태(전역 스토어)를 아주 간단하게 관리할 수 있는 경량 상태관리 라이브러리**
- Redux나 MobX보다 훨씬 가볍고, 코드가 짧고 직관적임
- 함수 하나로 전역 상태를 선언하고, 어디서든 꺼내 쓸 수 있음
- 상태가 바뀌면 자동으로 연결된 컴포넌트가 리렌더링

---

## 2. 기본 사용법

### **라이브러리 설치**

```bash
npm install zustand
```

### **스토어(Store) 생성**

- **Store**란?
    
    Zustand에서 "전역 상태"와 "상태 변경 함수(액션)"를 한 번에 담아두는 공간
    
- **상태(state)**: 실제로 저장/관리할 값 (예: count, user, 리스트 등)
- **액션(action)**: 상태를 바꾸는 함수 (예: increase, addUser 등)
- `create` 함수를 사용해 스토어(전역상태관리 훅)를 만든다

```jsx
import { create } from "zustand";

const useCounterStore = create((set, get) => ({
  count: 0,
  // set의 콜백으로 상태를 바로 업데이트 (불변성 보장)
  increase: () => set((state) => ({ count: state.count + 1 })),
  // get()으로 현재 상태값을 직접 읽어서 업데이트
  decrease: () => set({ count: get().count - 1 }),
}));

export default useCounterStore;
```

### **스토어 값 사용**

```jsx
function Counter() {
  // 필요한 상태와 함수만 골라서 사용
  const { count, increase, decrease } = useCounterStore();

  return (
    <div>
      <h2>카운트: {count}</h2>
      <button onClick={increase}>증가</button>
      <button onClick={decrease}>감소</button>
    </div>
  );
}
```

### **여러 컴포넌트에서 상태 공유**

```jsx
function Display() {
  const count = useCounterStore((state) => state.count);
  return <p>현재 카운트: {count}</p>;
}
```

- `useCounterStore` 훅을 여러 컴포넌트에서 불러와도 상태는 **공유**되고, 변경되면 해당 컴포넌트만 리렌더링

### **비동기 업데이트 예시**

```jsx
const useStore = create((set) => ({
  items: [],
  fetchItems: async () => {
    const response = await fetch('/api/items');
    const data = await response.json();
    set({ items: data });
  },
}));
```

---

## 3. Zustand를 사용하는 이유

### **1. 아주 간단한 API로 전역 상태를 관리할 수 있다**

- 복잡한 보일러플레이트(액션/리듀서/Provider 등) 필요 없이, **단 한 줄의 코드**로 상태와 상태 변경 함수를 만들 수 있음
- 가장 많이 사용하는 상태관리 라이브러리인 Redux와 같이 복잡한 구조 없이 useState처럼 쉽게 전역 상태를 선언/변경/구독 가능

### **2. 작고 빠르며, 사용법이 직관적이다**

- 라이브러리 크기가 매우 작아 프로젝트가 가벼워짐
- 불필요한 리렌더링이 적음 (필요한 컴포넌트만 리렌더)
- typescript, immer 등과도 쉽게 연동 가능

### **3. 비동기/동기 업데이트, 비리액트 환경(plain JS)에서도 사용 가능**

- 비동기 작업, 외부 API, 서버 상태 동기화 등도 지원
- React에 종속적이지 않고, JS 어디서나 쓸 수 있음

---

## 4. 주요 특징 요약

- **코드가 짧고, 쉽고, 빠르다**
- **Provider, 액션 타입 등 복잡한 구조 불필요**
- **필요한 값만 구독 가능 (최소 리렌더링)**
- **JS 어디서든 사용 가능 (비리액트 환경도 지원)**

---

## 5. Zustand vs Context API 비교

| 항목 | **Zustand** | **Context API** |
| --- | --- | --- |
| **개념/역할** | 전역 상태관리 "전용 라이브러리" | 리액트 내장 "값 전달 메커니즘" (실질적인 전역 상태 X) |
| **설정 난이도** | 매우 간단 (스토어 함수 1줄) | 비교적 간단하지만, Provider/Consumer 필요 |
| **리렌더링 성능** | 필요한 값만 구독, 리렌더 최소화 (최적화 O) | 값이 바뀌면 **Provider 하위 전체 리렌더** (완전한 최적화 X) |
| **코드 복잡도** | 스토어 함수 + 훅 호출만으로 끝! | 여러 Context 만들면 코드가 금방 복잡해짐 |
| **비동기/미들웨어** | 내장 지원 (fetch, immer, 미들웨어 등 쉽게 확장) | 직접 구현(복잡) |
| **Provider 필요** | **필요 없음!** 어디서든 훅만 호출 | 반드시 Provider로 감싸야 함 |
| **실무 확장성** | 중/대규모 앱, 많은 상태, 도메인별 스토어에 강함 | 소규모, 간단한 전역값(테마, 언어 등)에 적합 |
| **주요 사용처** | 실무형 상태관리, 복잡한 앱, 다양한 상태, 비동기 | 테마, 로그인정보, 언어설정 등 전역 "읽기 값" |

---

## 6. 상황별 선택 가이드

### **Zustand가 더 적합할 때**

- "전역적으로 복잡한 상태"가 많다 (리스트, 유저, 장바구니 등)
- 상태가 자주/크게 바뀐다
- 상태관리 로직이 점점 늘어나고, 확장성이 필요하다
- 불필요한 리렌더링이 부담스럽다
- 비동기/미들웨어 등 고급 기능이 필요하다

### **Context API가 더 적합할 때**

- "딱 몇 개의 간단한 전역 값"만 필요하다
    
    (예: 테마, 언어, 로그인 유저, 환경설정 등)
    
- 값 변경이 자주 일어나지 않고, 소규모 앱/프로토타입이다
- 외부 라이브러리 없이 완전한 내장 기능으로 충분하다

---

## 7. 고급 사용법

### **선택적 구독 (Selector)**

```jsx
// 전체 상태를 구독하지 않고, 필요한 값만 구독
function Display() {
  // count만 변경될 때만 리렌더링
  const count = useCounterStore((state) => state.count);
  return <p>현재 카운트: {count}</p>;
}
```

### **여러 값 구독**

```jsx
function Component() {
  // 여러 값을 한 번에 구독
  const { count, name } = useCounterStore((state) => ({
    count: state.count,
    name: state.name
  }));
  
  return <div>{name}: {count}</div>;
}
```

### **미들웨어 사용 (예: Immer)**

```jsx
import { create } from "zustand";
import { immer } from "zustand/middleware/immer";

const useStore = create(
  immer((set) => ({
    items: [],
    addItem: (item) => set((state) => {
      // Immer를 사용하면 직접 수정 가능 (불변성 자동 처리)
      state.items.push(item);
    }),
  }))
);
```

### **TypeScript 지원**

```typescript
import { create } from "zustand";

interface CounterState {
  count: number;
  increase: () => void;
  decrease: () => void;
}

const useCounterStore = create<CounterState>((set) => ({
  count: 0,
  increase: () => set((state) => ({ count: state.count + 1 })),
  decrease: () => set((state) => ({ count: state.count - 1 })),
}));
```

### **로컬 스토리지 연동 (Persist)**

```jsx
import { create } from "zustand";
import { persist } from "zustand/middleware";

const useStore = create(
  persist(
    (set) => ({
      count: 0,
      increase: () => set((state) => ({ count: state.count + 1 })),
    }),
    {
      name: "counter-storage", // localStorage 키 이름
    }
  )
);
```

---

## 요약

- **Zustand**는 가장 심플하고 빠르게 전역 상태를 관리할 수 있는 리액트 라이브러리입니다.
- "useState만큼 쉽고, useContext보다 강력한 전역 상태 관리"가 필요할 때 딱 적합합니다.
- **중/대규모 프로젝트에서 Redux보다 가볍게 전역 상태 관리가 필요할 때 사용**합니다.
- Provider 없이도 사용 가능하고, 필요한 값만 구독하여 불필요한 리렌더링을 최소화합니다.
- Context API는 간단한 전역 값에 적합하고, Zustand는 복잡한 상태 관리에 적합합니다.

---

## 참고 자료

- [Zustand 공식 문서](https://zustand-demo.pmnd.rs/)
- [Zustand GitHub](https://github.com/pmndrs/zustand)

