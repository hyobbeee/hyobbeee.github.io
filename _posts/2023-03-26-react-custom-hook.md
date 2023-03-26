---
layout: post
title:  "리액트 커스텀 훅"
date:   2023-03-26
tags: TIL React
author: hyobbeee
categories: React
cover:  "/assets/instacode.png"
---

# React Custom Hook

REST API 서버를 이용하여 json 데이터를 직접 받아와 실제 블로그 처럼 되도록 코드 작성한다.

<aside>
💡 ***Custom Hook**
간단하게 복습하자면, 계속 반복 사용하는 로직 커스텀해서 사용하는 훅
**e.g.** 유효성 검사하는 로직(id, pw), 상태 로직(사용자가 로그인했는지 확인), API 호출 로직 등 ..*

</aside>

 **

<aside>
💡 ***json-server 란 :** 직접 DB를 만들고 서버를 구축할 필요 없이 json파일을 이용하여 REST API 서버를 구축해주는 라이브러리*

</aside>

```jsx
$ npm i -g json-server
$ cd data
$ json-server --watch data.json --port 3001
```

json 파일이 있는 data 폴더에서 서버를 실행 시키고 json파일을 이용하여 

get, post 요청시 그에 맞게 응답해주는 형식이다.

App.js에서 Route 설정한 컴포넌트를 lazy로 바꾼뒤 Suspense 태그로 감싼다. 

<aside>
✅ 바뀌는 동안 보여줄 컴포넌트를 fallback 옵션에 넣는다.

</aside>

```jsx
const Home = React.lazy(() => import('./Home'));
const CreateBlog = React.lazy(() => import('./blogComponent/CreateBlog'));
const BlogDetails = React.lazy(() => import('./blogComponent/BlogDetail'));
const NotFound = React.lazy(() => import('./component/NotFound'));
const Loading = React.lazy(() => import('./component/Loading'));

<Suspense fallback={<div>로딩...</div>}>
   <Routes>
			...
   </Routes>
 </Suspense>
```

### 작성된 내용 클릭시 세부 내용 화면 띄우기

먼저 리액트 라우터의 `useParams`를 통해 URL의 파라미터 값에서 id 값을 추출하여 해당 id의 내용만 화면에 띄운다.

<aside>
💡 ***useParams**
: URL의 파라미터 값을 추출하여 사용할 수 있게 해주는 훅, 값을 객체 형태로 가져온다.*

</aside>

```jsx
// BlogDetail.js

const { id } = useParams()
// 이때 request 요청 메서드와 headers를 지정할 수 있다.
const request = {
  method: 'GET',
  headers: {'Content-Type': 'application/json'}
}

fetch(`http://localhost:3001/blogs/${id}`, request)
  .then(res => res.data)
  .cath(err => console.log(err))
```

### 게시글 작성

```jsx
// CreateBlog.js
const CreateBlog = () => {
  const [title, setTitle] = useState('');
  const [body, setBody] = useState('');
  const [author, setAuthor] = useState('김코딩');
  const navigate = useNavigate();

  const handleSubmit = (e) => {
      e.preventDefault();
      /* 등록 버튼을 누르면 게시물이 등록이 되며 home으로 리다이렉트 되어야 합니다. */
      /* 작성한 내용과 useNavigate를 이용하여 handleSubmit의 로직을 작성해보세요. */
      const bodys = {
        // 원래 id 입력해야하는데, json-server가 알아서 id를 auto-create 해줌
        title,
        body,
        author,
        "likes": 0
      }

      fetch('http://localhost:3001/blogs/', {
        method: "POST",
        headers: {
            "Content-Type" : "application/json",
            Accept: "application/json"
        },
        body: JSON.stringify(bodys)
      })
      .then(() => {
          window.location.href = 'http://localhost:3000/';
      })
      .catch((error) => {
          console.error('Error', error);
      })
  }

  return (
		...
```

### 삭제버튼

```jsx
...
const handleDeleteClick = () => {
    /* delete 버튼을 누르면 다시 home으로 리다이렉트 되어야 합니다. */
    /* useNavigate()를 이용하여 handleDeleteClick 로직을 작성해주세요. */
    console.log('delete!');
    fetch(`http://localhost:3001/blogs/${id}`, {
      method: 'DELETE',
      headers: {'Content-Type': 'application/json'},
    })
    .then(() => {
			// navigate('/');
      // window.location.reload();
      window.location.href = 'http://localhost:3000/'
    })
		.catch(err => console.log(err));
  }
```

페이지 이동시 기존에 useNavigate를 이용해 루트 경로로 리다이렉트 시켜줬지만 페이지가 리로드 되지 않기 때문에 따로 함수를 작성해줘야 했다.

`window.location.href` 사용시 리로드와 리다이렉트를 동시에 진행하기 때문에 좀 더 편리한 것 같다.

<aside>
🚨 ***TypeError: window.location.href is not a function***
window.location류 함수들을 사용했었기 떄문에 당연히 함수로 호출했는데 함수가 아니라 속성타입으로 지정해줘야 했다…ㅎ

</aside>

### 하트버튼

```jsx
// blogDetail.js

const handleLikeClick = () => {
    /* 하트를 누르면 home에서 새로고침을 했을 때 숫자가 올라가야 합니다. */
    /* isLike와 blog.likes를 이용하여 handleLikeClick의 로직을 작성해주세요. */
    console.log('like!');
    setIsLike(!isLike);
    let updateLike = blog.likes + 1;

    const patchData = {  // 기존 데이터를 덮어씌우기 때문에 데이터 전부다 보내줘야함
      // 'id' : blog.id, // 근데 json-server에서 id는 자동으로 생성해줌
      "title" : blog.title,
      "body" : blog.body,
      "author" : blog.author,
      "likes" : updateLike,
    }

    fetch(`http://localhost:3001/blogs/${id}`,{
      method: "PATCH",
      body : JSON.stringify(patchData),
      headers: {
        'Content-Type': 'application/json',
        Accept: 'application/json'
      },
    })
    .then( () => {
      window.location.reload() // 리로드해줘야 BlogList에서 업데이트됨
    })
    .catch(err => console.log(err))
  }
```

하트버튼의 카운트가 반영되려면 페이지가 리로드되야하는데 

리로드시 isLike값이 false가 되어서 하트의 불이 꺼져버린다.

이부분은 추후에 보완해야겠다.

### Custom Hook

useEffect hook은 컴포넌트마다 존재하고 있는데

이부분은 간단하게 커스텀 훅을 만들어 사용가능하다.

```jsx
// useFetch.js 

import { useState, useEffect } from 'react';

const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [isPending, setIsPending] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setTimeout(() => {
      fetch(url, {
        headers: {
          "Content-Type" : "application/json",
          Accept: "application/json"
        }
      })
      .then(res => {
        if (!res.ok) {
          throw Error('could not fetch the data for that resource');
        } 
        return res.json();
      })
      .then(data => {
        setIsPending(false);
        setData(data);
        setError(null);
      })
      .catch(err => {
        setIsPending(false);
        setError(err.message);
      })
    }, 1000) // 1초
  })
  
  return [data, isPending, error];
}

export default useFetch;
```

이제 이걸 사용하고 싶은 곳에서 구조분해하여 할당해주기만 하면 된다.

```jsx
const {blog, isPending, error} = useFetch('http://localhost:3001/blogs');

<h2>{ blog.title }</h2>
<p>Written by { blog.author }</p>
<div>{ blog.body }</div>
{!isLike ? '❤️' : '🤍' }
```

- 0326 추가
    
    ```jsx
    
    // blogs라는 배열에서 author가 중복되지 않은 요소들만을 추출하여 authorUnique라는 새로운 배열에 저장
    
    const authorUnique = blogs && (blogs.fliter((character, idx, arr) => {
    	return arr.findIndex(item => item.author === character.author) === idx
    }))
    ```
    
    author 중복이 될 수 있음, 중복은 제거 하고 유니크한 이름만 모아서 새로운 배열로 저장