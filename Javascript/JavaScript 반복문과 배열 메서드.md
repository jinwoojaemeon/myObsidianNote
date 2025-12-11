# JavaScript 반복문과 배열 메서드

#javascript #반복문 #배열 #메서드 #면접 #개념정리

**관련 개념:** [[JavaScript 함수]] [[배열]] [[고차함수]] [[불변성]]

---

## 핵심 개념

JavaScript는 다양한 반복문과 배열 메서드를 제공하여 데이터를 효율적으로 처리할 수 있습니다.

---

## 기본 반복문

### 1. for문

```javascript
for(let i = 0; i < 5; i++) {
    console.log(i);
}
```

### 2. while문

```javascript
let j = 0;
while(j < 5) {
    console.log(j);
    j++;
}
```

### 3. do-while문

```javascript
let j = 0;
do {
    console.log(j);
    j++;
} while(j < 5);
```

---

## 객체/배열 순회

### for...of (배열, 이터러블)

```javascript
const fruits = ["사과", "배", "딸기"];

for(const fruit of fruits) {
    console.log(fruit); // 값 직접 접근
}
```

### for...in (객체)

```javascript
const apple = {
    id: 4,
    name: "사과",
    price: 3000
}

for(let key in apple) {
    console.log(key + " : " + apple[key]); // 키로 접근
}
```

---

## 배열 메서드 (고차 함수)

### forEach - 순회 전용

```javascript
fruits.forEach((obj, index) => {
    console.log(`${index} -> ${obj.name}`);
});
```

### map() - 변형된 새 배열 반환

```javascript
const numbers = [1, 3, 5, 7, 9];
const squared = numbers.map((num) => num * num);
// [1, 9, 25, 49, 81]
```

**용도**: 서버 데이터를 UI에 맞게 변형할 때

### filter() - 조건에 맞는 요소만 추출

```javascript
const filtered = numbers.filter((num) => num % 3 === 0);
// [3, 9]
```

**용도**: 서버 데이터 삭제 후 UI 상태 반영할 때

### find() - 조건에 맞는 첫 번째 요소

```javascript
const found = numbers.find((num) => num % 3 === 0);
// 3
```

### some() - 하나라도 조건 만족하면 true

```javascript
const hasEven = numbers.some((num) => num % 2 === 0);
// false
```

### every() - 모두 조건 만족하면 true

```javascript
const allPositive = numbers.every((num) => num > 0);
// true
```

### reduce() - 누적하여 하나의 결과값 도출

```javascript
// 배열을 객체로 변환
const stdList = [
    { name: "최지원", score: 80 },
    { name: "김지원", score: 20 }
];

const scoreMap = stdList.reduce((scoreMap, std) => {
    scoreMap[std.name] = std.score;
    return scoreMap;
}, {});

// { "최지원": 80, "김지원": 20 }
```

**구조**: `배열.reduce((누적값, 배열요소) => { return 누적값; }, 초기값)`

---

## 관련 노트

- [[JavaScript 함수]] - 고차 함수 개념
- [[배열]] - 배열 메서드 상세
- [[고차함수]] - map, filter, reduce 원리
- [[불변성]] - 배열 메서드와 불변성

---

**태그:** #javascript #반복문 #배열 #메서드 #면접 #개념정리

