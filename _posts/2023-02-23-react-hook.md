---
layout: post
read_time: true
show_date: true
title:  "CORS"
date:   2023-02-20
description: 깃 블로그 만드는거 왜이렇게 어려워요.
img: my_keyboard.jpeg
tags: [TIL, web]
author: hyobbeee
mathjax: yes
---

### 탄생배경

- 고차 컴포넌트와 render props로 인한 코드 추적의 어려움을 해결하기 위함

### 특징

- 컴포넌트로부터 상태 관련 로직을 추상화 ⇒ 테스트와 재사용이 가능
- **계층의 변화 없이 상태 관련 로직을 재사용할 수 있음**
- 서로 비슷한 것을 하는 작은 함수의 묶음으로 컴포넌트를 나눔
    
    클래스 컴포넌트: 생명주기 메소드를 기반으로 쪼갬 (ex. componentDidMount)
    
    함수형 컴포넌트: 성격이 비슷한 함수 묶음으로 쪼갬 (ex. 구독 설정 및 데이터를 불러오는 것과 같은 로직)
    

### 사용규칙

1. 최상위 (at the top level)에서만 Hook 호출
2. React 함수 컴포넌트 내에서만 Hook  호출

## **Hook과 함수 컴포넌트**

### 함수 컴포넌트

- state를 가지지 못함
- lifecycle 함수를 가지지 못함

### Hook

함수 컴포넌트에서 state와 lifecycle 함수를 이용할 수 있다.

## useState

### 컨셉

<aside>
💡 state 값 갱신하면 React가 새로운 state를 컴포넌트에 넘긴다.

</aside>

- 지역변수 단위이며 컴포넌트 개별적으로 갱신
- 클래스 컴포넌트에서 `this.setState()`가 동일한 역할을 한다.
- 얕은 비교를 한다.

1. event 발생! ⇒ state 갱신!
2. 리액트가 새 state를 컴포넌트에 넘겨줌
3. 컴포넌트 rerender!

### useState vs this.setState

**this.setState**

- state 업데이트는 병합된다.
- state는 다양한 독립적인 변수를 포함할 수 있는데,  별도의 `setState()`호출로 이러한 변수를 독립적으로 업데이트할 수 있습니다.

```jsx
constructor(props) {
  super(props);
  this.state = {
    posts: [],
    comments: []
  };
}

componentDidMount() {
  this.setState({
      posts: response.posts
    });

  this.setState({
    comments: response.comments
  });
}
```

### 문법

```jsx
import React, { useState } from 'react';

function Example() {
  // 새로운 state 변수를 선언하고, count라 부르겠습니다.
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

1. Import useState
2. 구조 분해 할당으로 변수 선언
    
    `[ 변수, set변수 ] = useState(초기값)`
    
3. set변수 함수로 state 갱신
4. 컴포넌트 rerender

## memo

### 컨셉

<aside>
💡 props로 얻은 결과를 메모이징

</aside>

- props가 변동되면 rerender

만약, memo를 설정한 컴포넌트에

- useState
- useReducer
- useContext

훅을 사용하면, state/context 변할 때마다 다시 렌더링된다.

## useRef

### 컨셉

<aside>
💡 요소를 선택/포커스 관리하기 위해  사용하는 함수

</aside>

### 사용

1. Import useRef
2. 변수에 useRef 선언
3. 원하는 요소에 ref={변수} 선언
4. 사용할 때, 변수.current 사용

### 예시

```jsx
import React, { useRef } from 'react';

function InputSample() {
  const nameInput = useRef();

  const onReset = () => {
    // Do something
    nameInput.current.focus();
  };

  return (
    <div>
      <input ref={nameInput} />
      <input />
      <button onClick={onReset}>초기화</button>
    </div>
  );
}

export default InputSample;
```

## useEffect

<aside>
💡 렌더링 이후에 발생하는 사이드 이펙트 설정

</aside>

- componentDidMount, componentDidUpdate, componentWillUnmount와 같은 목적으로 제공
- props와 state에 접근 가능
- 여러개의 effect 사용 가능
- 로직과 서로 관련 있는 코드들을 한군데에 모아서 작성 가능

### 특징

1. 첫번째 렌더링에서 수행
2. 이후의 모든 업데이트에서도 수행
3. 리렌더링하면, effect는 매번 새로 교체

### 문법

useEffect(콜백함수, 인자)

1. useEffect(콜백함수, []) ⇒ 첫번째 렌더링에서만 수행
2. useEffect(콜백함수, [변수]) ⇒ 변수가 업데이트 될 때마다 실행

### 실행순서

- React가 DOM을 업데이트한 뒤
- `render` ⇒ `React DOM update` ⇒ `side effect 발생`

![react hook flow diagram.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/797e2e8c-e950-4e5b-98c0-e44bcd4dac41/react_hook_flow_diagram.png)

### effect 해제

- useEffect에 return () ⇒ { 해제할 함수 표현식 } 추가

```jsx
import React, { useState, useEffect } from 'react';

function WidthPrinter() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(()=>{
    const onResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize',onResize); // 등록

    return ()=> {
        window.removeEventListener('resize',onResize);  // 해제
    }
  })

  return <div>{`width is ${width}`}</div>;
}

export default WidthPrinter;
```