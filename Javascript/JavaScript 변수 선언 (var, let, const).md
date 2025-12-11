# JavaScript 변수 선언 (var, let, const)

#javascript #변수 #호이스팅 #tdz #면접 #개념정리

**관련 개념:** [[호이스팅]] [[스코프]] [[TDZ]] [[JavaScript 함수]]

---

## 핵심 개념

### var의 문제점

```javascript
console.log(tmp); // undefined (에러가 아님!)
var tmp = "여기서부터 생성됨";
```

- **호이스팅**: 변수 선언이 스코프 최상단으로 끌어올려짐
- **문제**: 선언 전 접근 시 `undefined` 반환 (에러가 아님)
- **현재**: 더 이상 사용하지 않음

### let과 const의 해결책

- **TDZ (Temporal Dead Zone)**: 변수 선언 전 접근 불가
- **호이스팅은 되지만**: 선언 시점의 코드가 실행되기 전까지는 TDZ에 등록되어 사용 불가
- **let**: 변수 (재할당 가능, 중복 선언 불가)
- **const**: 상수 (재할당 불가, 중복 선언 불가)

---

## 변수명 작성 규칙

1. `$`, `_` 외 특수문자 사용 불가
2. 숫자로 시작 불가
3. 예약어 사용 불가 (let, const 등)

### 올바른 변수명 예시

```javascript
let $price = 10000;      // 두 가지 타입 구분용
let userName = "Jeameon"; // 카멜 케이스
let _status = "active";  // 복사한 값 표현용
```

---

## 사용 예시

```javascript
// let: 재할당 가능
let name = "Jeameon";
name = "Jeoeon"; // ✅ 가능

// const: 재할당 불가
const age = 50;
age = 15; // ❌ 에러 발생
```

---

## 관련 노트

- [[호이스팅]] - 변수/함수 호이스팅 상세
- [[스코프]] - 변수 스코프 개념
- [[TDZ]] - Temporal Dead Zone
- [[JavaScript 함수]] - 함수 선언과 호이스팅

---

**태그:** #javascript #변수 #호이스팅 #tdz #면접 #개념정리

