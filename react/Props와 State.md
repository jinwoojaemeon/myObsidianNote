# Props와 State

#react #props #state #컴포넌트 #면접 #개념정리

**관련 개념:** [[컴포넌트]] [[React Props]] [[단방향 데이터 흐름]] [[리렌더링]]

---

## Topic (오늘의 주제)

React 컴포넌트에서 데이터를 관리하는 두 가지 방식인 Props와 State의 차이점과 각각의 사용 시나리오를 이해한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- **실무**: Props와 State의 차이를 모르면 컴포넌트 설계가 어려워집니다. 언제 Props를 사용하고 언제 State를 사용해야 할지 판단하지 못하면, 불필요한 리렌더링이 발생하거나 데이터 흐름이 복잡해집니다. 잘못된 선택은 Props Drilling 문제나 상태 관리 복잡도를 증가시킵니다.

- **구조적 의미**: Props는 부모에서 자식으로의 단방향 데이터 흐름을 제공하여 예측 가능한 컴포넌트 동작을 보장합니다. State는 컴포넌트의 독립적인 내부 상태를 관리하여 사용자 인터랙션에 반응하는 동적 UI를 구현합니다. 이 두 가지를 적절히 구분하여 사용하면 컴포넌트의 재사용성과 유지보수성을 극대화할 수 있습니다.

- **면접 의도**: React의 단방향 데이터 흐름에 대한 이해, Props와 State의 차이점을 명확히 알고 있는지, 그리고 언제 어떤 것을 사용해야 하는지 판단할 수 있는 컴포넌트 설계 능력을 확인하려 합니다.

---

### **Core Concept (핵심 개념 정리)**

#### 개념 정의

**Props (Properties)**: 부모 컴포넌트에서 자식 컴포넌트로 전달되는 읽기 전용 데이터입니다. 함수의 매개변수와 유사하며, 컴포넌트의 외부에서 제어됩니다.

**State**: 컴포넌트 내부에서 관리하는 변경 가능한 데이터입니다. 컴포넌트의 동작에 따라 변경되며, 변경 시 컴포넌트가 리렌더링됩니다.

#### 동작 방식

```
Props 동작 흐름:
부모 컴포넌트 → Props 전달 → 자식 컴포넌트 (읽기 전용)
변경: 부모 컴포넌트에서만 가능

State 동작 흐름:
컴포넌트 내부 → useState 초기화 → State 값 변경 (setState)
변경: 컴포넌트 내부에서 setState로만 가능
```

#### Props vs State 비교

| 구분 | Props | State |
|:---:|:---:|:---:|
| **소유권** | 부모 컴포넌트 | 컴포넌트 자체 |
| **변경 가능성** | 읽기 전용 (Immutable) | 변경 가능 (Mutable) |
| **데이터 흐름** | 부모 → 자식 (단방향) | 컴포넌트 내부 |
| **초기값 설정** | 부모 컴포넌트에서 전달 | 컴포넌트 내부에서 초기화 |
| **변경 방법** | 부모 컴포넌트에서만 변경 | setState 또는 useState |
| **용도** | 외부에서 받는 데이터 | 컴포넌트 내부 상태 |
| **예시** | 사용자 이름, 제품 정보 | 입력값, 체크박스 상태 |

#### 장점/단점

**Props의 장점**:
- 단방향 데이터 흐름으로 예측 가능
- 컴포넌트 재사용성 향상
- 불변성 보장으로 실수 방지

**Props의 단점**:
- Props Drilling 문제 (깊은 계층 구조)
- 부모 컴포넌트에서만 변경 가능

**State의 장점**:
- 컴포넌트 독립적인 상태 관리
- 사용자 인터랙션에 즉각 반응
- 동적 UI 구현 가능

**State의 단점**:
- 컴포넌트가 복잡해질 수 있음
- 불필요한 리렌더링 발생 가능
- 상태 관리 복잡도 증가

#### 필요 조건

**Props 사용 조건**:
- 부모 컴포넌트가 존재해야 함
- 데이터를 외부에서 받아야 할 때
- 컴포넌트를 재사용해야 할 때

**State 사용 조건**:
- 컴포넌트 내부에서 관리해야 할 데이터
- 사용자 입력, UI 상태 등
- 시간에 따라 변경되는 데이터

#### 예시/비교

```javascript
// Props 예시: 부모에서 데이터 전달
function Parent() {
  const userName = "Jeameon";
  return <Child name={userName} />; // Props 전달
}

function Child({ name }) {
  return <h1>Hello, {name}!</h1>; // Props 사용 (읽기 전용)
}

// State 예시: 컴포넌트 내부 상태 관리
function Counter() {
  const [count, setCount] = useState(0); // State 초기화
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

---

### **Interview Answer Version (면접 답변식 요약)**

Props는 부모 컴포넌트에서 자식 컴포넌트로 전달되는 읽기 전용 데이터이고, State는 컴포넌트 내부에서 관리하는 변경 가능한 데이터입니다.

Props를 사용하는 이유는 컴포넌트의 재사용성을 높이고, 부모에서 자식으로의 단방향 데이터 흐름을 제공하기 위해서입니다. State를 사용하는 이유는 사용자 입력이나 UI 상태처럼 컴포넌트 내부에서 변경되어야 하는 데이터를 관리하기 위해서입니다.

핵심 차이점은 Props는 부모 컴포넌트에서만 변경 가능하고 읽기 전용이며, State는 컴포넌트 내부에서 setState로 변경할 수 있다는 점입니다. 일반적으로 외부에서 받는 데이터는 Props로, 컴포넌트 내부에서 관리하는 동적 데이터는 State로 사용합니다.

---

### **Practical Tip (사용시 주의할 점 or 활용 예)**

#### 설정 시 반드시 고려해야 할 파라미터

- **Props vs State 선택 기준**: 
  - 데이터가 부모에서 오는가? → Props
  - 컴포넌트 내부에서만 사용하는가? → State
  - 시간이 지나도 변하지 않는가? → Props
  - 사용자 입력이나 이벤트로 변경되는가? → State

- **State 최소화 원칙**: 
  - 다른 컴포넌트에서도 필요한 데이터는 상위로 올리기
  - 계산 가능한 값은 State로 저장하지 않기
  - Props로 받을 수 있는 데이터는 State로 만들지 않기

#### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "모든 데이터를 State로 관리하면 된다"는 생각은 잘못되었습니다. Props로 받을 수 있는 데이터를 State로 만들면 불필요한 상태 관리와 리렌더링이 발생합니다. 또한 "Props를 직접 수정해도 된다"는 생각도 위험합니다. Props는 읽기 전용이므로 직접 수정하면 React가 경고를 표시합니다.

#### 적용 사례

- **Props 사용 예시**:
  ```javascript
  // 제품 목록 컴포넌트
  function ProductList({ products }) {
    return products.map(product => 
      <ProductCard key={product.id} product={product} />
    );
  }
  ```

- **State 사용 예시**:
  ```javascript
  // 검색 입력 컴포넌트
  function SearchInput() {
    const [query, setQuery] = useState("");
    
    return (
      <input 
        value={query} 
        onChange={(e) => setQuery(e.target.value)} 
      />
    );
  }
  ```

- **Props와 State 함께 사용**:
  ```javascript
  // 부모에서 초기값을 Props로 받고, 내부에서 State로 관리
  function EditableText({ initialValue }) {
    const [value, setValue] = useState(initialValue);
    
    return (
      <input 
        value={value} 
        onChange={(e) => setValue(e.target.value)} 
      />
    );
  }
  ```

#### 이걸 모르고 사용하면 생기는 문제

> [!error] 이걸 모르고 사용하면
> 
> - **Props를 State로 변환하지 않기**: Props로 받은 값을 State로 저장하면 초기값만 반영되고, Props 변경이 반영되지 않음
> - **계산 가능한 값을 State로 저장**: 불필요한 State로 인한 복잡도 증가
> - **Props 직접 수정**: React 경고 발생 및 예측 불가능한 동작
> - **State를 Props로 전달**: Props Drilling 문제 발생
> - **불필요한 State**: 리렌더링 최적화 어려움

---

### 예상 꼬리질문정리

#### 1. Props로 받은 값을 State로 저장해도 되나?

- **일반적으로 권장하지 않음**: Props 변경이 State에 반영되지 않음
- **예외**: 초기값만 사용하고 이후 독립적으로 관리할 때
- **해결책**: `useEffect`로 Props 변경 감지하여 State 업데이트

#### 2. 언제 Props를 State로 변환해야 하나?

- **Derived State 패턴**: Props로 받은 값을 가공하여 State로 저장
- **사용자 입력과 결합**: Props 초기값 + 사용자 입력을 State로 관리
- **주의**: Props 변경 시 State도 업데이트해야 함

#### 3. State를 Props로 전달할 수 있나?

- **가능**: 부모의 State를 자식의 Props로 전달 가능
- **패턴**: "Lifting State Up" - 공통 부모로 State를 올려서 여러 자식에 전달
- **예시**: 여러 컴포넌트가 같은 데이터를 공유할 때

#### 4. Props와 State 중 어느 것을 사용해야 할지 판단하는 기준은?

- **3가지 질문**:
  1. 부모 컴포넌트에서 오는 데이터인가? → Props
  2. 시간이 지나도 변하지 않는가? → Props
  3. 다른 컴포넌트에서도 필요한가? → 상위 State로 올리기

#### 5. State를 최소화하는 이유는?

- **복잡도 감소**: State가 적을수록 컴포넌트가 단순해짐
- **디버깅 용이**: 상태 변경 추적이 쉬움
- **성능 최적화**: 불필요한 리렌더링 방지
- **테스트 용이**: 단순한 컴포넌트는 테스트하기 쉬움

---

## 관련 노트

- [[컴포넌트]] - Props와 State를 사용하는 컴포넌트
- [[React Props]] - Props 상세 설명
- [[단방향 데이터 흐름]] - React의 데이터 흐름 원칙
- [[리렌더링]] - State 변경과 리렌더링

---

**태그:** #react #props #state #컴포넌트 #면접 #개념정리

