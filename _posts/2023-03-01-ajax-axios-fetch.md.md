---
layout: post
title:  "Ajax, axios, fetch 비교, 장단점 정리"
date:   2023-03-01
author: hyobbeee
categories: WEB
tags: TIL WEB
cover:  "/assets/instacode.png"
---
# Ajax

### 📍개념

Asynchronous Javascript And XML의 약자로, 

비동기적으로 JS를 사용해서 데이터를 받아와 동적으로 DOM을 갱신 및 조작하는 웹 개발 기법

여기서 XML이 있는 이유는 과거 데이터 포맷으로 XML을 많이 사용했기 때문

### 📍동작원리

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9af90128-231d-43b4-a4a2-10ba4df3ad04/Untitled.png)

사용자가 AJAX가 적용된 UI와 상호작용하면, 서버에 AJAX 요청을 보내게 된다. 

서버는 DB에서 데이터를 가져와서 JS파일에 정의되어 있는대로 HTML/CSS와 데이터를 융합하여 만든 DOM객체를 UI에 업데이트 시킨다.

비동기로 이뤄지며, 기존의 페이지를 전부 로딩하는 방식이 아닌 일부만 업데이트 하는 방식

### 📍사용법

일반적으로 `XMLHttpRequest` 객체를 사용하여 인스턴스를 만들어 인스턴스의 `open()`, `send()` 등의 매서드를 이용한다.

```jsx
let ourRequest = new XMLHttpRequest();
ourRequest.open( // 메서드 주소 설정
	"GET", "https://learnwebcode.github.io/json-example/animals-1.json"
);
ourRequest.onload = () => {
	let ourData = JSON.parse(ourRequest.responseText);
	console.log(ourData[0]);
};
ourRequest.send(); // 요청 전송
```

`open()` 으로 요청할 메서드와 URL을 설정한 뒤, 모두 로드되었을 경우 콜백함수를 초기화 한다.

# axios

axios는 Node.js와 브라우저를 위한 Promis API를 활용하는 HTTP 통신 라이브러리이다.

비동기로 HTTP 통신을 할 수 있으며 return을 promise 객체로 해주기 떄문에 response 데이터를 다루기 쉽다.

### 🔹장점

- response timeout(fetch에는 없는 기능) 처리 방법이 존재
- Promise 기반으로 만들어졌기 때문에 데이터 다루기 편리하다.
- 브라우저 호환성이 뛰어나다.

### 🔸단점

- 사용을 위해 모듈 설치 필요(`npm install axios`)

### 📍사용법

```jsx
axios({
	method: 'post',
	url: 'https://localhost:3000',
	data: {
		username: '김코딩',
		userId: 1,
	}
}).then((response) => console.log(response));
```

# [Fetch API](https://www.notion.so/fetch-0d4882b30dd24b019bb9a03d8d2585fd)

ES6부터 들어온 JavaScript 내장 라이브러리이다.

Promise 기반으로 만들어졌기 때문에 axios와 마찬가지로 데이터 다루기가 쉽고,

내장 라이브러리여서 쉽게 CORS 이슈를 처리 가능

### 🔹장점

- 페이지를 전환하지 않고 빠르게 화면 일부분을 업데이트 가능
- 수신하는 데이터 양을 줄일 수 있고 클라이언트에게 처리를 양도 가능
- 서버 처리를 기다리지 않고 비동기 요청이 가능

### 🔸단점

- 지원하지 않는 브라우저 있음(IE11..)
- 네트워크 에러 발생시 response timeout 없이 기다려야 함
- JSON으로 변환해주는 과정 필요
- 상대적으로 axios에 비해 기능 부족

### 📍사용법

```jsx
fetch("https://localhost:3000/user/post", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    id: "asd123",
    description: "hello world",
  }),
}).then((response) => console.log(response));
```

## 💡summary

- 간단한 통신의 경우 : fetch
- 기능이 좀 더 필요한 경우 : axios

하지만 React-Native와 같이 업데이트가 잦은 경우 fetch가 내장 라이브러리 이기 때문에 좀 더 안정적일 수 있다고 함