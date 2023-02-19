---
layout: post
read_time: true
show_date: true
title:  "use strict란"
date:   2023-02-19
description: 깃 블로그 만드는거 왜이렇게 어려워요.
img: my_keyboard.jpeg
tags: [TIL, JavaScript]
author: hyobbeee
mathjax: yes
---
## 📍개념

일부 기능을 제한한다는 의미, 오류 방지 목적을 가지며 단점보다는 장점이 많다.

### 🔹장점

- 의도하지 않은 전역변수가 선언되지 못하도록 한다.
- 삭제할 수 없는 속성을 삭제하려고 시도하면 오류를 발생시킨다.
- 함수의 파라미터 이름에 중복을 허용하지 않아 혼란을 줄여준다.
- 전역에서 this는 undefinded이다.
- 일반적인 코딩 실수를 잡아 예외처리 한다.

### 🔸단점

- 못쓰게 되는 기능들이 필요한 기능일 가능성도 있다.
- function.caller와 function.arguments에 접근 할 수 있다.
- 서로 다른 strict mode로 작성된 코드를 연결 했을 때 오류가 발생 할 수 있다.