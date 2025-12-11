# ES6 문법: 템플릿 리터럴, 구조 분해 할당, 스프레드 연산자

#javascript #es6 #템플릿리터럴 #구조분해 #스프레드 #면접 #개념정리

**관련 개념:** [[JavaScript 변수 선언 (var, let, const)|변수 선언]] [[JavaScript 클래스와 객체|객체]] [[불변성]] [[React Props]]

---

## 핵심 개념

**ES6 (ECMAScript 2015)**에서 도입된 문법들로 코드를 더 간결하고 읽기 쉽게 만들어줍니다. 템플릿 리터럴, 구조 분해 할당, 스프레드 연산자는 현대 JavaScript 개발에서 필수적인 기능입니다.

---

## 1. 템플릿 리터럴

문자열과 변수를 쉽게 결합하고, 여러 줄 문자열 처리 가능

```javascript
const userName = "Jeameon";
console.log(`안녕하세요. ${userName}님!`);

const multiStr = `이 변수는
여러줄로 작성된
문자열을 가지고 있습니다.`;
```

---

## 2. 구조 분해 할당 (Destructuring)

객체나 배열에서 필요한 값을 바로 변수로 추출

### 객체 구조 분해

```javascript
const userInfo = {
    name: "Jeameon",
    age: 22,
    email: "jeameon@gmail.com",
    job: "developer"
}

// 기본 사용
const { name, age } = userInfo;

// 변수명 변경
const { name, job: userJob } = userInfo;

// 함수 매개변수에서 사용
function myInfo({ name, age }) {
    console.log(`이름 : ${name}, 나이 : ${age}`);
}
```

### 배열 구조 분해

```javascript
const numbers = [10, 20, 30];

// 기본 사용
const [num1, num2] = numbers;

// 필요 없는 값 생략
const [,, num] = numbers; // num = 30
```

### React에서의 활용

```javascript
// useState의 반환값 구조 분해
const [count, setCount] = useState(0);
// useState() return [값, setter 함수]
```

---

## 3. 스프레드 연산자 (...)

배열/객체를 복사, 병합, 수정할 때 사용

### 객체 복사 및 수정

```javascript
let user = {
    name: "Jeameon",
    age: 22,
    job: "developer"
}

// ❌ 나쁜 예: 직접 수정 (불변성 위반)
// user.job = "student"; 
// React에서 값 변경을 감지하지 못함

// ✅ 좋은 예: 스프레드로 새 객체 생성
user = {
    ...user,        // 기존 속성 복사
    job: "student"  // 덮어쓰기
}
```

**React에서 중요한 이유**: 불변성을 지켜야 리렌더링이 정상 동작

---

## 관련 노트

- [[JavaScript 변수 선언 (var, let, const)|변수 선언]] - let, const와 구조 분해
- [[JavaScript 클래스와 객체|객체]] - 객체와 스프레드 연산자
- [[불변성]] - React에서 불변성의 중요성
- [[React Props]] - 구조 분해를 활용한 Props 사용

---

**태그:** #javascript #es6 #템플릿리터럴 #구조분해 #스프레드 #면접 #개념정리

