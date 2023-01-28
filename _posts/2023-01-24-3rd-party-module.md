---
layout: post
title:  "node.js / 3rd-party 모듈"
date:   2023-01-24
categories: TIL JavaScript
---

- 학습목표
    - Node.js 내장 모듈을 사용할 수 있다.
    - 서드 파티 모듈 (3rd-party module)을 활용할 수 있다.
    - Node.js 공식 문서를 활용하여 `fs.readFile`을 이용할 수 있다.

<br>
# Node.js

## 📍개념

Node.js는 Chrome V8 JavaScript 엔진으로 빌드 된 JavaScript 런타임이다.
즉, 노드를 통해 다양한 자바스크립트 애플리케이션을 실행할 수 있으며, 서버를 실행하는데 많이 이용됨.
<aside>
  🎃 자신이 이해하는 범위만큼 모듈을 쓸 수 있음..
</aside>

## 📍사용법

파일을 읽을 때 : `readFile` 이라는 메서드

파일을 저장할 때 : `writeFile` 이라는 메서드(`saveFile` 메서드를 써야할 것 같지만 없음)

모든 모듈은 ‘모듈을 사용하기 위해 불러오는 과정’이 필요

```js
<script src="불러오고싶은_스크립트.js"></script>
```

Node.js 에서는 javascript 코드 가장 상단에 `require` 구문을 이용해 다른 파일을 불러옴

```js
const fs = require('fs'); // 파일 시스템 모듈 호출
const dns = require('dns'); // DNS 모듈 호출

// 호출 후 fs.readFile 메서드 등을 사용 가능
```
<br>
# 3rd-party 모듈을 사용하는 법

## 📍개념

해당 프로그래밍 언어에서 공식적으로 제공하는 빌트인 모듈이 아닌 모든 외부 모듈을 일컫는다.

## 📍사용법

예시로 `underscore`이 있는데 Node.js 공식문서에 없는 모듈이어서 서드파티 모듈임 → 다운을 위해선 npm 사용해야함

```js
npm install underscore
```

이후 Node.js 내장 모듈을 사용하듯 `require` 구문을 통해 호출 가능

```js
const _ = require('underscore');
```
<br>
# 공식 문서 읽는법

## fs.readFile(path[, options], callback)

메서드 `fs.readFile`은 **비동기적**으로 파일 내용 전체를 읽는다.

### -  path : \<string> | \<Buffer> | \<URL> | \<integer>

path에는 파일이름을 전달인자로 받는데, 네 가지 종류의 타입을 넘길 수 있지만 일반적으로 문자열 타입 사용

```js
fs.readFile('/etc/passwd', ..., ...)
```

### - options : \<Object> | \<string>

대괄호로 감싼 두번째 전달인자는 선택적 전달인자를 의미함

options은 문자열 또는 객체 형태로 받을 수 있다. 

문자열로 전달할 경우 인코딩을 받음.

```js
// /etc/passwd 파일을 'utf8'을 사용하여 읽습니다.
fs.readFile('/etc/passwd', 'utf8', ...);
```

```js
let options = {
  encoding: 'utf8', // utf8 인코딩 방식으로 엽니다
  flag: 'r' // 읽기 위해 엽니다
}

// /etc/passwd 파일을 options를 사용하여 읽습니다.
fs.readFile('/etc/passwd', options, ...)
```

### - callback \<Function>

마지막인자는 콜백함수를 전달함. 파일을 읽고 난 후 비동기적으로 실행되는 함수

- `err` \<Error> | \<AggregateError>
- `data` \<string> | \<Buffer>

콜백함수에는 두가지 매개변수 있는데, 에러 발생하지 않으면 `err`은 `null`이 되며, `data`에 문자열이나 `Buffer` 라는 객체가 전달됨.

```js
fs.readFile('test.txt', 'utf8', (err, data) => {
  if (err) {
    throw err; // 에러를 던집니다.
  }
  console.log(data);
});
```