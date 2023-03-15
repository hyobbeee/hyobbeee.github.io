---
layout: post
title:  "async & await"
date:   2023-03-14
tags: TIL JavaScript
author: hyobbeee
categories: JavaScript
cover:  "/assets/instacode.png"
---
## 📍개념

가성비 좋게 비동기를 동기처럼 동작하도록 구현한다.

## 📍특징

1. 프로미스를 기반으로 동작
2. then/catch/finally 후속 처리 메서드 사용 가능

## ☑️ async 함수

**언제나 프로미스를 반환**한다. 

함수 선언문, 함수 표현식, 화살표 함수, 메서드에 사용 가능하다.

단, 클래스의 constructor 메서드는 async 메서드가 될 수 없다. (인스턴스를 반환해야되므로)

## ☑️ await 키워드

반드시 async 함수 내부에서 사용한다. 

**프로미스가 settled 상태가 될 때까지 대기**하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환한다.

## 📍특징

**앞선 비동기 처리의 결과를 가지고 다음 비동기 처리를 수행할 때 사용한다.**

반드시 프로미스 앞에서 사용해야한다.