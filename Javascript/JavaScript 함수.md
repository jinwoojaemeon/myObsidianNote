# JavaScript 함수

#javascript #함수 #화살표함수 #콜백 #면접 #개념정리

**관련 개념:** [[JavaScript 변수 선언 (var, let, const)|변수 선언]] [[호이스팅]] [[this 바인딩]] [[클로저]]

---

## 핵심 개념

### 함수는 값(변수)으로 취급됨

JavaScript에서 함수는 **일급 객체(First-class citizen)**로, 변수처럼 사용할 수 있습니다.

---

## 함수 선언 방식

### 1. 기본 함수 선언

```javascript
function hello() {
    console.log("Hello, react");
}

// 호이스팅됨 (선언 전 호출 가능)
tmp(); 
function tmp() {
    console.log("나는함함함수다");
}
```

### 2. 함수 표현식 (익명 함수)

```javascript
const printMsg = function() {
    console.log("this is a function");
}

// 호이스팅 안됨 (선언 전 호출 불가)
// tmp2(); // ❌ 에러
let tmp2 = function() {
    console.log("나도 익명함수다");
}
```

### 3. 화살표 함수

```javascript
// 기본 형태
const add = (a, b) => { 
    return a + b 
};

// return만 있으면 생략 가능
const add2 = (a, b) => a + b;

// 매개변수 하나면 괄호 생략 가능
const print = msg => console.log(msg);
```

---

## 기본 매개변수

```javascript
function greet(name = "방문자", msg = "안녕하세요") {
    console.log(`${name}님, ${msg}`);
}

greet(); // "방문자님, 안녕하세요"
greet("Jeameon", "Hello"); // "Jeameon님, Hello"
```

**주의사항**:
- `null`: 개발자가 명시적으로 전달한 값 → 기본값 사용 안 함
- `undefined`: 시스템이 정해준 빈값 → 기본값 사용

---

## 화살표 함수의 this 바인딩

### 일반 함수의 this

```javascript
const human1 = {
    name: "Jeameon",
    info: function() {
        setTimeout(function() {
            console.log(this.name); // ❌ undefined (this가 window)
        }, 1000);
    }
}
```

### 화살표 함수의 this (Lexical Scope)

```javascript
const human2 = {
    name: "Jeoeon",
    info: function() {
        setTimeout(() => {
            console.log(this.name); // ✅ "Jeoeon" (상위 스코프 this 상속)
        }, 1000);
    }
}
```

**핵심**: 화살표 함수는 자신만의 `this`를 바인딩하지 않고, **선언된 위치의 스코프를 계승**합니다.

---

## 콜백 함수

특정 함수 실행 후 실행하고 싶은 코드를 정의하는 용도

```javascript
const run = (callback) => {
    // 특정 기능 수행
    callback();
}

run(() => {
    console.log("콜백 함수 실행");
});
```

---

## 관련 노트

- [[JavaScript 변수 선언 (var, let, const)|변수 선언]] - 변수와 함수의 호이스팅 차이
- [[호이스팅]] - 함수 호이스팅 상세
- [[this 바인딩]] - this의 동작 원리
- [[클로저]] - 함수와 스코프

---

**태그:** #javascript #함수 #화살표함수 #콜백 #면접 #개념정리

