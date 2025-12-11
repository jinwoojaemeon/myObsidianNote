# JavaScript 모듈 시스템 (ES6 Modules)

#javascript #모듈 #es6 #import #export #면접 #개념정리

**관련 개념:** [[JavaScript 함수]] [[스코프]] [[코드 재사용성]]

---

## 핵심 개념

코드를 여러 파일로 나누어 재사용하고 관리할 수 있게 해주는 구조입니다. 각 파일은 독립된 스코프를 가지며, `import`/`export`로 기능을 공유합니다.

---

## 모듈 내보내기 (export)

### Named Export - 여러 개 내보내기

```javascript
// utils.js
export function add(a, b) {
    return a + b;
}

export const PI = 3.141592653589793;
```

### Default Export - 하나만 내보내기

```javascript
// utils.js
export default function hello(name = "방문자") {
    console.log(`안녕하세요. ${name}님!`);
}
```

**특징**: 이름 없이 내보내며, 가져올 때 이름을 자유롭게 지정 가능

---

## 모듈 가져오기 (import)

```javascript
// index.js

// Named import
import { add, PI } from "./utils.js";

// Default import (이름 자유롭게 지정 가능)
import helloFunc from "./utils.js";

console.log("2 + 3 = ", add(2, 3));
console.log("PI = ", PI);
helloFunc("Jeameon");
```

---

## 모듈의 독립된 스코프

```javascript
// 모듈 시스템에서는 각 파일에 독립된 scope를 제공한다.
// 다른 파일의 변수나 함수는 반드시 import를 통해 가져와서 사용한다.
```

- 각 파일은 독립된 스코프
- 다른 파일의 변수/함수는 `import`로만 접근 가능
- 전역 변수 오염 방지

---

## 브라우저에서 사용

```html
<!-- index.html -->
<script type="module" src="./index.js"></script>
```

**주의**: `type="module"`을 반드시 지정해야 합니다.

---

## 모듈 시스템의 장점

1. **코드 분리**: 기능별로 파일 분리 가능
2. **재사용성**: 여러 곳에서 같은 기능 사용
3. **독립성**: 각 파일의 스코프가 독립적
4. **유지보수**: 기능별로 관리 용이

---

## 관련 노트

- [[JavaScript 함수]] - 함수를 모듈로 내보내기
- [[스코프]] - 모듈의 독립된 스코프
- [[코드 재사용성]] - 모듈화의 목적

---

**태그:** #javascript #모듈 #es6 #import #export #면접 #개념정리

