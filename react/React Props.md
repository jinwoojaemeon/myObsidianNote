# React Props (Properties)

#react #props #컴포넌트 #프론트엔드 #면접 #개념정리

**관련 개념:** [[컴포넌트]] [[State]] [[이벤트 핸들링]] [[단방향 데이터 흐름]] [[컴포넌트 통신]]

> [!note] 관련 개념
> Props는 **[[컴포넌트]]** 간 데이터를 전달하는 핵심 메커니즘입니다. 컴포넌트의 재사용성과 독립성을 실현하는 기반이 됩니다.

---

## Core Concept (핵심 개념 정리)

## Why (왜 사용하는가? 왜 중요한가?)

- **실무**: Props 없이는 컴포넌트가 고정된 데이터만 표시할 수 있어 재사용성이 떨어집니다. 같은 컴포넌트를 다른 데이터로 사용하려면 컴포넌트를 복사하거나 수정해야 하며, 데이터 변경 시 여러 곳을 동시에 수정해야 하는 문제가 발생합니다.

- **구조적 의미**: 부모 컴포넌트에서 자식 컴포넌트로 데이터를 전달하는 단방향 데이터 흐름을 제공합니다. 이를 통해 데이터의 출처를 명확히 하고, 예측 가능한 컴포넌트 동작을 보장하며, 디버깅과 유지보수를 용이하게 합니다.

- **면접 의도**: React의 단방향 데이터 흐름에 대한 이해, Props와 State의 차이, Props Drilling 문제와 해결 방법, 그리고 컴포넌트 설계 능력을 확인하려 합니다.

---

### 개념 정의

**Props(Properties)는 React 컴포넌트에 전달되는 읽기 전용 데이터입니다. 부모 컴포넌트에서 자식 컴포넌트로 데이터를 전달하는 메커니즘으로, 함수의 매개변수와 유사한 역할을 합니다. Props는 **불변(Immutable)**하며, 컴포넌트 내에서 직접 수정할 수 없습니다.

> [!tip] 중요한 특징
> Props는 "Properties"의 줄임말이며, 컴포넌트의 속성이나 설정값을 의미합니다. React의 단방향 데이터 흐름(Unidirectional Data Flow)의 핵심입니다.

---

### 동작 방식

```
Props 전달 및 사용 흐름:

1. 부모 컴포넌트에서 Props 정의
   <ChildComponent name="John" age={25} />
      ↓
2. React가 Props 객체로 변환
   { name: "John", age: 25 }
      ↓
3. 자식 컴포넌트에서 Props 받기
   function ChildComponent(props) {
     return <div>{props.name} is {props.age}</div>;
   }
      ↓
4. Props 사용 (읽기 전용)
   - 렌더링에 사용 가능
   - 직접 수정 불가 (읽기 전용)
      ↓
5. Props 변경 시
   - 부모 컴포넌트에서만 변경 가능
   - 자식 컴포넌트는 자동으로 리렌더링
```

**관련 개념:** [[컴포넌트]] [[단방향 데이터 흐름]] [[리렌더링]] [[불변성]]

---

### Props 사용 방법

#### 1. 함수형 컴포넌트에서 Props 받기

```javascript
// 방법 1: props 객체로 받기
function Greeting(props) {
  return <h1>Hello, {props.name}!</h1>;
}

// 방법 2: 구조 분해 할당 (권장)
function Greeting({ name, age }) {
  return <h1>Hello, {name}! You are {age} years old.</h1>;
}

// 방법 3: 기본값 설정
function Greeting({ name = "Guest", age = 0 }) {
  return <h1>Hello, {name}!</h1>;
}
```

#### 2. 클래스 컴포넌트에서 Props 받기

```javascript
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

#### 3. Props 전달하기

```javascript
// 부모 컴포넌트
function App() {
  return (
    <div>
      <Greeting name="John" age={25} />
      <Greeting name="Jane" age={30} />
    </div>
  );
}
```

---

### 장점과 단점

> [!success] 장점
> 
> - ✅ **재사용성**: 같은 컴포넌트를 다른 데이터로 여러 번 사용 가능
> - ✅ **단방향 데이터 흐름**: 데이터 흐름이 명확하고 예측 가능
> - ✅ **불변성**: Props는 읽기 전용이어서 실수로 변경하는 것을 방지
> - ✅ **테스트 용이성**: Props를 통해 다양한 시나리오 테스트 가능
> - ✅ **디버깅 편의성**: 데이터의 출처가 명확함

> [!warning] 단점
> 
> - ❌ **Props Drilling**: 깊은 계층 구조에서 Props를 계속 전달해야 함
> - ❌ **복잡한 데이터 전달**: 여러 단계를 거쳐야 할 때 번거로움
> - ❌ **동적 업데이트 제한**: 부모 컴포넌트에서만 변경 가능
> - ❌ **타입 안정성**: JavaScript에서는 타입 체크가 런타임에만 가능 (TypeScript 사용 권장)

---

> [!info] 필요 조건
> 
> Props를 사용하려면:
> - **React 컴포넌트**: 함수형 또는 클래스 컴포넌트
> - **부모-자식 관계**: Props는 부모에서 자식으로만 전달 가능
> - **JSX 문법**: 컴포넌트에 속성으로 전달
> - **선택사항**: TypeScript 또는 PropTypes로 타입 검증

---

### Props vs State 비교

| 구분 | Props | State |
|:---:|:---:|:---:|
| **소유권** | 부모 컴포넌트 | 컴포넌트 자체 |
| **변경 가능성** | 읽기 전용 (Immutable) | 변경 가능 (Mutable) |
| **데이터 흐름** | 부모 → 자식 (단방향) | 컴포넌트 내부 |
| **초기값 설정** | 부모 컴포넌트에서 전달 | 컴포넌트 내부에서 초기화 |
| **변경 방법** | 부모 컴포넌트에서만 변경 | setState 또는 useState |
| **용도** | 외부에서 받는 데이터 | 컴포넌트 내부 상태 |
| **예시** | 사용자 이름, 제품 정보 | 입력값, 체크박스 상태 |

---

### Props 타입 검증

#### PropTypes 사용 (선택사항)

```javascript
import PropTypes from 'prop-types';

function Greeting({ name, age }) {
  return <h1>Hello, {name}!</h1>;
}

Greeting.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
};

Greeting.defaultProps = {
  age: 0,
};
```

#### TypeScript 사용 (권장)

```typescript
interface GreetingProps {
  name: string;
  age?: number; // 선택적 prop
}

function Greeting({ name, age = 0 }: GreetingProps) {
  return <h1>Hello, {name}!</h1>;
}
```

---

## Interview Answer Version (면접 답변식 요약)

React Props는 컴포넌트에 전달되는 읽기 전용 데이터입니다.

사용하는 이유는 컴포넌트의 재사용성을 높이고, 부모에서 자식으로 데이터를 전달하기 위해서입니다. Props는 불변(Immutable)이므로 컴포넌트 내에서 직접 수정할 수 없고, 부모 컴포넌트에서만 변경할 수 있습니다.

핵심 특징은 단방향 데이터 흐름을 제공한다는 점입니다. 데이터는 항상 부모에서 자식으로 흐르며, 이를 통해 데이터의 출처를 명확히 하고 예측 가능한 컴포넌트 동작을 보장합니다. Props를 통해 함수처럼 매개변수를 받아 다양한 데이터로 컴포넌트를 재사용할 수 있습니다.

---

## Practical Tip (사용시 주의할 점 or 활용 예)

### 설정 시 반드시 고려해야 할 파라미터

- **타입 검증**: PropTypes 또는 TypeScript로 Props 타입 정의
- **기본값 설정**: defaultProps 또는 기본 매개변수로 기본값 제공
- **필수 Props**: isRequired로 필수 Props 명시
- **구조 분해 할당**: 가독성을 위해 구조 분해 할당 사용

### 흔히 발생하는 문제/오해

> [!warning] 흔한 오해
> 
> "Props를 직접 수정해도 된다"는 생각은 잘못되었습니다. Props는 읽기 전용이므로, `props.name = "New Name"`과 같이 직접 수정하면 React가 경고를 표시합니다. Props를 변경하려면 부모 컴포넌트에서 새로운 Props를 전달해야 합니다.

### 적용 사례

- **재사용 가능한 컴포넌트**: Button, Card, Modal 등
- **리스트 렌더링**: map 함수와 함께 사용
  ```javascript
  {users.map(user => <UserCard key={user.id} user={user} />)}
  ```
- **조건부 렌더링**: Props 값에 따라 다른 UI 표시
- **이벤트 핸들러 전달**: 함수를 Props로 전달하여 자식에서 호출

### 이걸 모르고 사용하면 생기는 문제

> [!error] 이걸 모르고 사용하면
> 
> - **Props Drilling**: 깊은 계층에서 Props를 계속 전달해야 하는 문제
> - **타입 오류**: 타입 검증 없이 사용하면 런타임 에러 발생 가능
> - **불필요한 리렌더링**: 객체나 함수를 인라인으로 전달하면 매번 새로 생성
> - **Props 직접 수정**: Props를 수정하려고 시도하면 React 경고 발생
> - **undefined 처리 누락**: 필수 Props를 전달하지 않으면 에러 발생

---

## 예상 꼬리질문 정리

### 1. Props와 State의 차이는 무엇인가?

- **Props**: 
  - 부모 컴포넌트에서 전달받는 읽기 전용 데이터
  - 컴포넌트 외부에서 제어
  - 함수의 매개변수와 유사
  
- **State**: 
  - 컴포넌트 내부에서 관리하는 변경 가능한 데이터
  - 컴포넌트 자체가 제어
  - 함수의 지역 변수와 유사

### 2. Props를 자식에서 부모로 전달할 수 있나?

- **직접 전달 불가**: Props는 단방향 데이터 흐름 (부모 → 자식)
- **해결 방법**: 
  - 콜백 함수를 Props로 전달하여 자식에서 부모로 데이터 전달
  - "Props Down, Events Up" 패턴 사용
  ```javascript
  // 부모
  function Parent() {
    const handleChildData = (data) => {
      console.log(data); // 자식에서 받은 데이터
    };
    return <Child onData={handleChildData} />;
  }
  
  // 자식
  function Child({ onData }) {
    const sendData = () => {
      onData("Hello from child");
    };
    return <button onClick={sendData}>Send</button>;
  }
  ```

### 3. Props Drilling 문제는 어떻게 해결하나?

- **문제**: 깊은 계층 구조에서 Props를 여러 단계 전달
- **해결 방법**:
  - **Context API**: 전역 상태 관리
  - **상태 관리 라이브러리**: Redux, Zustand 등
  - **컴포넌트 구조 재설계**: 불필요한 중간 컴포넌트 제거
  - **Composition**: children prop 활용

### 4. Props에 함수를 전달할 때 주의할 점은?

- **인라인 함수 주의**: 매 렌더링마다 새 함수 생성
  ```javascript
  // ❌ 나쁜 예: 매번 새 함수 생성
  <Child onClick={() => handleClick(id)} />
  
  // ✅ 좋은 예: useCallback 사용
  const handleClick = useCallback(() => {
    // ...
  }, [dependencies]);
  <Child onClick={handleClick} />
  ```
- **메모이제이션**: useCallback으로 함수 메모이제이션
- **의존성 배열**: useCallback의 의존성 배열 관리

### 5. Props의 기본값을 설정하는 방법은?

- **방법 1: defaultProps (클래스 컴포넌트)**
  ```javascript
  Greeting.defaultProps = {
    age: 0,
  };
  ```
  
- **방법 2: 기본 매개변수 (함수형 컴포넌트, 권장)**
  ```javascript
  function Greeting({ name, age = 0 }) {
    return <h1>Hello, {name}!</h1>;
  }
  ```

### 6. Props를 조건부로 전달하는 방법은?

- **스프레드 연산자 사용**
  ```javascript
  const props = condition ? { name: "John" } : {};
  <Child {...props} />
  ```
  
- **단축 평가 사용**
  ```javascript
  <Child 
    name="John"
    {...(condition && { age: 25 })}
  />
  ```

### 7. children prop은 무엇인가?

- **children**: 특별한 prop으로, 컴포넌트 태그 사이의 내용을 받음
  ```javascript
  function Card({ children }) {
    return <div className="card">{children}</div>;
  }
  
  // 사용
  <Card>
    <h1>Title</h1>
    <p>Content</p>
  </Card>
  ```
- **용도**: 컴포넌트를 래퍼로 사용할 때 유용
- **타입**: ReactNode (문자열, 숫자, 요소, 배열 등)

---

## 관련 노트

- **[[컴포넌트]]** - Props를 사용하는 컴포넌트 개념
- [[State]] - Props와 함께 사용하는 내부 상태
- [[이벤트 핸들링]] - Props로 함수 전달하여 이벤트 처리
- [[단방향 데이터 흐름]] - React의 데이터 흐름 원칙
- [[컴포넌트 통신]] - 부모-자식 컴포넌트 간 통신
- [[Context API]] - Props Drilling 해결 방법
- [[TypeScript]] - Props 타입 안정성

---

> [!tip] 다음 단계
> Props를 이해했다면 **[[컴포넌트]]**와 **[[State]]**를 함께 학습하여 React 컴포넌트의 전체 동작 방식을 파악하세요. 또한 Props Drilling 문제를 해결하기 위한 **[[Context API]]**도 알아보세요.

---

**태그:** #react #props #컴포넌트 #프론트엔드 #면접 #개념정리

