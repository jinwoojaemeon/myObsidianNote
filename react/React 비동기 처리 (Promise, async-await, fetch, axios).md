# React 비동기 처리 (Promise, async-await, fetch, axios)

#react #비동기 #promise #async-await #fetch #axios #useEffect #면접 #개념정리

**관련 개념:** [[컴포넌트|컴포넌트]] [[Props와 State|Props와 State]] [[Custom Hook|Custom Hook]]

> [!note] 이어서 읽기
> React 비동기 처리를 이해하려면 **[[컴포넌트|컴포넌트]]**와 **[[Props와 State|Props와 State]]**의 핵심 개념을 함께 확인하세요.

---

## Topic (오늘의 주제)

JavaScript의 비동기 처리 개념과 Promise, async/await를 이해하고, React에서 fetch와 axios를 사용하여 서버와 통신하는 방법을 학습한다.

---

### **Why (왜 사용하는가? 왜 중요한가?)**

- JavaScript는 싱글 스레드 언어이지만, 서버 통신, 타이머, 파일 읽기 등은 비동기로 처리해야 하므로 Promise와 async/await를 통해 비동기 코드를 효율적으로 관리할 수 있습니다.

- React 컴포넌트에서 서버 데이터를 가져오거나 전송할 때는 useEffect와 비동기 패턴을 사용하여 컴포넌트 생명주기에 맞춰 데이터를 로드할 수 있습니다.

- fetch는 브라우저 내장 함수로 간단하게 사용할 수 있고, axios는 더 편리한 기능과 에러 처리를 제공하여 실무에서 널리 사용됩니다.

---

### **Core Concept (핵심 개념 정리)**

**비동기(Asynchronous)**는 작업이 완료될 때까지 기다리지 않고 다음 코드를 실행하며, 결과는 나중에 처리하는 방식입니다. 서버 통신, 타이머 등에 사용됩니다.

**Promise**는 비동기 작업을 객체로 관리하는 표준으로, 콜백 지옥을 해결하고 비동기 로직을 읽기 쉽게 만듭니다. pending(대기) → fulfilled(성공) 또는 rejected(실패) 상태를 가집니다.

**async/await**는 Promise를 기반으로 하며, 비동기 코드를 동기 코드처럼 순차적으로 작성할 수 있게 해줍니다.

**fetch**는 브라우저에 내장된 HTTP 요청 함수이고, **axios**는 더 편리한 기능을 제공하는 외부 HTTP 클라이언트 라이브러리입니다.

> [!tip] 핵심 포인트
> React에서 비동기 데이터 로딩은 useEffect 내부에서 async 함수를 선언하고 호출하는 패턴을 사용하며, 로딩/에러 상태까지 관리하여 사용자 경험을 향상시킵니다.

---

## 1. 비동기 개념 & Promise

### **자바스크립트의 비동기란?**

- **JS는 싱글 스레드, 동기적 언어이다.**
    - 명령어가 한 줄씩 "순차적으로" 실행됨
- 하지만, **서버에서 데이터 받아오기, 타이머, 파일 읽기** 등은 "기다리지 않고, 나중에 결과를 받아 처리"해야 함 → **비동기(Asynchronous)**
- **동기(Synchronous)**: 끝날 때까지 다음 코드 실행 되지 않음
    
    **비동기(Asynchronous)**: 일단 넘어가고, 결과는 나중에 처리함

### **JS에서 비동기를 처리하는 기본 방식: 콜백(callback)**

- 함수에 "나중에 실행할 일"을 전달 (ex: setTimeout, 이벤트 리스너 등)
- **콜백 지옥이 발생함**
    
    콜백 안에 또 콜백이 중첩 → 코드 읽기 어려움
    

```jsx
// step1: 사용자 정보 요청
getUser(function(user) {
  // step2: 사용자로부터 게시글 요청
  getPosts(user.id, function(posts) {
    // step3: 첫 게시글의 댓글 요청
    getComments(posts[0].id, function(comments) {
      // step4: 댓글의 첫 작성자 정보 요청
      getUser(comments[0].userId, function(commentUser) {
        console.log("댓글 작성자 이름:", commentUser.name);
        // ...계속 중첩될 수 있음
      }, function(error) {
        console.error("댓글 작성자 조회 실패!", error);
      });
    }, function(error) {
      console.error("댓글 조회 실패!", error);
    });
  }, function(error) {
    console.error("게시글 조회 실패!", error);
  });
}, function(error) {
  console.error("사용자 정보 조회 실패!", error);
});
```

### **콜백지옥을 탈출하기 위한 Promise의 등장**

- 콜백 지옥을 해결하기 위해 나온 **비동기 작업을 객체로 관리하는 표준**
- **"미래에 결과를 약속"하는 객체**
- 3가지 상태: **pending(대기) → fulfilled(성공) or rejected(실패)**
- **then, catch, finally**로 결과/에러/후처리 분리

```jsx
const promise = new Promise((resolve, reject) => {
  // 비동기 작업
  setTimeout(() => {
    const success = true;
    if (success) {
      resolve("성공!");
    } else {
      reject("실패!");
    }
  }, 2000);
});

promise
  .then(result => console.log(result))     // 성공시 실행
  .catch(error => console.error(error))    // 실패시 실행
  .finally(() => console.log("끝!"));      // 무조건 실행
```

### **Promise를 사용하는 이유**

- **콜백 지옥 방지**: then/catch로 단계별 관리
- **비동기 로직을 읽기 쉽게**
- **에러와 성공을 한눈에 처리**

---

## 2. async/await : 비동기 코드를 동기처럼 쓰는 마법

### **async/await 기본 문법**

- **`async` 함수**: 항상 "Promise를 반환"하는 함수로 이 함수가 **"비동기 함수"임을 표시**, 내부에서 **await를 쓸 수 있게 해줌**
    (명시적으로 Promise를 리턴하지 않아도 내부에서 감싸짐)
- **`await` 키워드**: **async 함수 내부에서만** 사용 가능한 키워드로 Promise가 "resolve(성공)"될 때까지 **기다렸다가**, 결과값을 변수에 담는다
    
    (동기 코드처럼 값을 쓸 수 있음)
    

```jsx
// async -> fetchData는 promise객체가 된다.
async function fetchData() {
  const result1 = await someAsyncFunc();
  const result2 = await someAsyncFunc(result1);
  // 이 아래는 결과가 올 때까지 기다렸다가 실행
}
```

### **왜 async/await이 필요할까?**

- Promise로 콜백지옥은 해결됐지만, `.then().then().catch()` 체인이 계속 이어지면 **여전히 코드가 "계단식"으로 복잡**해질 수 있음
- **비동기 코드를 "동기 코드처럼"** 순서대로, 깔끔하게 읽고 쓸 수 있다면 가독성이 증가한다.

### **Promise then/catch 체인**

```jsx
getUser()
  .then(user => getPosts(user.id))
  .then(posts => getComments(posts[0].id))
  .then(comments => getUser(comments[0].userId))
  .then(commentUser => {
    console.log("댓글 작성자 이름:", commentUser.name);
  })
  .catch(error => {
    console.error("에러 발생!", error);
  });
```

### **async/await 코드로 변경**

```jsx
async function showCommentUser() {
  try {
    const user = await getUser();
    const posts = await getPosts(user.id);
    const comments = await getComments(posts[0].id);
    const commentUser = await getUser(comments[0].userId);
    console.log("댓글 작성자 이름:", commentUser.name);
  } catch (error) {
    console.error("에러 발생!", error);
  }
}

showCommentUser();
```

- **마치 동기 코드처럼 위에서 아래로 쭉 읽힌다!**
- try/catch로 에러도 한 번에 처리

---

## 3. JS의 네트워크 요청, 가장 쉬운 시작 : fetch

### **fetch란?**

- **fetch**는 자바스크립트에서 서버와 통신(HTTP 요청)을 할 때 사용하는 **내장 함수**
- API, 데이터베이스, 외부 서비스 등 **웹에서 데이터를 가져오거나 보낼 때** 사용함
- 최신 브라우저에 기본 내장되어 있음(따로 설치 X)
- **Promise 기반** → 비동기적으로 동작

### **기본 사용법**

```jsx
fetch(요청할URL, {
  method: "POST" 또는 "PUT" 또는 "GET"...,
  headers: {...},
  body: JSON.stringify({
    key1: value1,
    key2: value2,
  })
})
  .then(response => response.json()) // 성공시 -> json객체로 변환
  .then(data => console.log("받아온 데이터:", data)) // 변환완료시 데이터출력
  .catch(error => console.error("에러:", error));
```

**fetch(url)**

- 서버로부터 데이터를 받아옴 → 이때 반환값이 바로 **Promise**

**.then(response => response.json())**

- 서버에서 온 "응답(response)"을 JSON 객체로 변환
    (json도 역시 비동기라 Promise 반환)

**.then(data => ...)**

- 변환된 데이터를 받아서 실제로 활용

**.catch(error => ...)**

- 네트워크 에러 등 실패 처리

### **fetch의 특징과 장점**

- **간단하고 직관적**: REST API 실습, 외부 데이터 활용에 최적화
- **GET, POST, PUT, DELETE** 등 모든 HTTP 메서드 지원
- **기본적으로 Promise 반환** → then, async/await 모두 사용 가능
- 별도 설치 없이 바로 사용 가능

### **fetch 예제**

```jsx
// GET 요청
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error("에러:", error));

// POST 요청
fetch("https://jsonplaceholder.typicode.com/posts", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    title: "새 글",
    body: "내용입니다",
    userId: 1,
  }),
})
  .then(response => response.json())
  .then(data => console.log("등록된 데이터:", data))
  .catch(error => console.error("에러:", error));
```

---

## 4. axios : 더 편리한 HTTP 클라이언트

### **axios란?**

- **axios**는 자바스크립트에서 HTTP 통신(서버 데이터 요청/전송 등)을 쉽고 강력하게 도와주는 **외부 라이브러리**
- 브라우저 & Node.js 모두 지원
- **Promise 기반** → then/catch/async/await로 사용

### **왜 fetch 대신 axios를 쓸까?**

**fetch의 아쉬운 점**

- 응답을 `.json()`으로 한 번 더 파싱해야 함
- 에러 처리가 복잡함 (HTTP 에러도 `.catch`가 아니라 then에서 분기)
- POST/PUT 등에서 body, header 세팅이 번거로움
- timeout, 요청 취소, 인터셉터 등 고급 기능 부족

**axios의 장점**

- 요청/응답이 자동으로 JSON 변환
- 에러 처리가 단순
    - HTTP 상태 에러도 `.catch`로 한 번에 잡힘
- POST/PUT body/헤더 작성이 간단
- timeout, 요청 취소, 인터셉터, 응답 가공 등 다양한 고급 기능 지원
- 구조 분해로 바로 데이터만 받아서 사용 가능
    - fetch: `res.json()` → axios: `res.data`

### **라이브러리 설치**

```bash
npm install axios
```

### **GET 요청**

```jsx
import axios from "axios";

axios.get("https://jsonplaceholder.typicode.com/posts/1")
  .then(response => {
    console.log("받은 데이터:", response.data); // 바로 data!
  })
  .catch(error => {
    console.error("에러 발생:", error);
  });
```

### **async/await로 사용**

```jsx
import axios from "axios";

async function getPost() {
  try {
    const response = await axios.get("https://jsonplaceholder.typicode.com/posts/2");
    console.log("받은 데이터:", response.data);
  } catch (error) {
    console.error("에러 발생:", error);
  }
}

getPost();
```

### **POST 요청**

```jsx
import axios from "axios";

axios.post("https://jsonplaceholder.typicode.com/posts", {
  title: "새 글",
  body: "내용입니다",
  userId: 1,
})
  .then(res => console.log("등록된 데이터:", res.data))
  .catch(err => console.error("에러:", err));
```

### **fetch vs axios 비교**

|  | fetch | axios |
| --- | --- | --- |
| 기본 지원 | 내장 함수 | 외부 설치 필요 |
| 반환값 | Promise (response 객체) | Promise (응답 자체에 data 포함) |
| 데이터 추출 | `.json()` 메서드로 한 번 더 파싱 필요 | `.data` 속성에 바로 결과 |
| 에러처리 | 네트워크 에러만 .catch, HTTP 에러는 별도 | 네트워크/HTTP 에러 모두 .catch로 |
| 옵션 설정 | headers/body 등 직접 객체로 명시 | 더 직관적, 자동화된 옵션 |
| 고급기능 | 부족(요청 취소, 인터셉터 등 불편) | 요청 취소, 인터셉터, timeout 등 지원 |

---

## 5. useEffect와 비동기 패턴

### **왜 useEffect에서 비동기 요청을 할까?**

- React 컴포넌트는 "화면(UI)"만 만드는 게 아니라 **외부 서버에서 데이터도 불러와야** 할 때가 많음
- 예)
    - "앱이 시작되면 최신 게시글을 가져오기"
    - "사용자가 이동한 페이지의 상세 정보 요청하기"
- 이런 외부 데이터 요청(비동기 작업)은 컴포넌트가 렌더링(=화면에 나타남)된 이후에 실행되어야 함
- 그래서 **useEffect**를 사용!

### **useEffect에서 비동기 요청 패턴**

#### **기본형**

```jsx
import { useEffect } from "react";

useEffect(() => {
  // 여기에서 비동기 요청(API, 서버 데이터 불러오기 등)
}, []);
```

- 빈 의존성 배열(`[]`)은 처음 한 번만 실행(컴포넌트 마운트 시)

#### **fetch/axios로 데이터 불러오기 (Promise 방식)**

```jsx
import { useEffect, useState } from "react";
import axios from "axios";

function MyComponent() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    axios.get("https://jsonplaceholder.typicode.com/posts")
      .then(response => setPosts(response.data))
      .catch(error => console.error("에러:", error));
  }, []);

  return (
    <ul>
      {posts.map(post => <li key={post.id}>{post.title}</li>)}
    </ul>
  );
}
```

#### **async/await로 패턴 바꾸기**

> [!warning] useEffect와 async
> useEffect 콜백 자체에는 async를 붙일 수 없음
> 
> (그래서 내부에서 async 함수를 "선언 → 호출")
> 

```jsx
import { useEffect, useState } from "react";

function MyComponent() {
  const [todo, setTodo] = useState(null);

  useEffect(() => {
    async function fetchTodo() {
      try {
        const res = await fetch("https://jsonplaceholder.typicode.com/todos/1");
        const data = await res.json();
        setTodo(data);
      } catch (err) {
        console.error("에러:", err);
      }
    }
    fetchTodo();
  }, []);

  return (
    <div>
      {todo ? <p>{todo.title}</p> : <p>로딩 중...</p>}
    </div>
  );
}
```

- **중요:**
    - useEffect 콜백에는 async 직접 사용 ❌
    - 내부에 async 함수를 만들어서 즉시 호출하는 패턴

### **로딩/에러 상태까지 관리하기**

- **사용자 경험(UX)을 위해 "로딩 중/에러" 상태도 처리를 해줘야함**

```jsx
import { useEffect, useState } from "react";
import axios from "axios";

function MyComponent() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        const res = await axios.get("https://jsonplaceholder.typicode.com/posts/1");
        setData(res.data);
      } catch (e) {
        setError(e);
      } finally {
        setLoading(false);
      }
    }
    fetchData();
  }, []);

  if (loading) return <p>로딩 중...</p>;
  if (error) return <p>에러 발생: {error.message}</p>;
  return <div>{data && data.title}</div>;
}
```

- 이 구조를 **실전에서 가장 많이 사용!**

### **의존성 배열 활용**

```jsx
import { useEffect, useState } from "react";
import axios from "axios";

function PostDetail({ postId }) {
  const [post, setPost] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchPost() {
      try {
        setLoading(true);
        const res = await axios.get(`https://jsonplaceholder.typicode.com/posts/${postId}`);
        setPost(res.data);
      } catch (err) {
        console.error("에러:", err);
      } finally {
        setLoading(false);
      }
    }
    fetchPost();
  }, [postId]); // postId가 변경될 때마다 재실행

  if (loading) return <p>로딩 중...</p>;
  return <div>{post && <h1>{post.title}</h1>}</div>;
}
```

### **컴포넌트 언마운트 시 요청 취소**

```jsx
import { useEffect, useState } from "react";
import axios from "axios";

function MyComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    
    async function fetchData() {
      try {
        const res = await axios.get("https://jsonplaceholder.typicode.com/posts/1", {
          signal: controller.signal
        });
        setData(res.data);
      } catch (err) {
        if (err.name !== 'CanceledError') {
          console.error("에러:", err);
        }
      }
    }
    
    fetchData();
    
    // 컴포넌트 언마운트 시 요청 취소
    return () => {
      controller.abort();
    };
  }, []);

  return <div>{data && data.title}</div>;
}
```

---

## 요약

- **비동기**는 작업 완료를 기다리지 않고 다음 코드를 실행하며, 결과는 나중에 처리하는 방식입니다.
- **Promise**는 비동기 작업을 객체로 관리하여 콜백 지옥을 해결하고 코드 가독성을 향상시킵니다.
- **async/await**는 Promise를 기반으로 비동기 코드를 동기 코드처럼 순차적으로 작성할 수 있게 해줍니다.
- **fetch**는 브라우저 내장 HTTP 요청 함수이고, **axios**는 더 편리한 기능을 제공하는 외부 라이브러리입니다.
- **useEffect**에서 비동기 요청을 할 때는 내부에 async 함수를 선언하고 호출하는 패턴을 사용하며, 로딩/에러 상태까지 관리하여 사용자 경험을 향상시킵니다.

---

## 참고 자료

- [MDN - Promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [MDN - async/await](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/async_function)
- [MDN - fetch](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)
- [axios 공식 문서](https://axios-http.com/)

