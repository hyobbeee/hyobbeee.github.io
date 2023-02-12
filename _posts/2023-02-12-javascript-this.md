---
layout: post
read_time: true
show_date: true
title:  "자바스크립트 this"
date:   2023-02-12
description: 깃 블로그 만드는거 왜이렇게 어려워요.
img: my_keyboard.jpeg
tags: [TIL, JavaScript]
author: hyobbeee
mathjax: yes
---

## 📍개념

자바스크립트 내에서 this는 ‘누가 나를 불렀느냐’ 를 뜻한다고 한다.

→ 선언이 아닌 호출에 따라 달라진다.

## 📍종류

### 1. 단독 this

아묻따 this를 호출하는 경우 global object를 가리킨다.

브라우저에서 호출하는 경우 : `[object Window]` 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dc654a79-6777-49ab-84bf-e9ba3dadc953/Untitled.png)

```jsx
'use strict';

let x = this;
console.log(x); // Window
```

### 2. 함수 안 this

함수 안에서 this는 함수의 주인에게 바인딩 된다.

함수의 주인은 window 객체이다. (단독 this와 같음)

```jsx
function myFunction() {
	return this;
}
console.log(myFunction()); // Window
```

```jsx
let num = 0;
function addNum() {
	this.num = 100;
	num++;

	console.log(num); // 101
	console.log(window.num) // 101
	console.log(num === window.num); // true
}

addNum();
```

위 코드에서 this.num의 this는 window 객체를 가리킨다.

따라서 num은 전역 변수를 가리키게 된다.

다만 strict mode에서는 조금 다르다.

함수 내의 this에 디폴트 바인딩이 없기 때문에 undefined가 된다.

```jsx
"use strict";
 
function myFunction() {
  return this;
}
console.log(myFunction()); //undefined
```

```jsx
"use strict";
 
var num = 0;
function addNum() {
  this.num = 100; //ERROR! Cannot set property 'num' of undefined
  num++;
}
 
addNum();
```

따라서 this.num을 호출하면 undefined.num을 호출하는 것과 같기 때문에 에러가 난다.

### 3. 매서드 안 this

일반 함수가 아닌 매서드에서는

매서드 호출 시 매서드 내부 코드에서 사용된 this 해당 매서드를 호출한 객체로 바인딩 된다.

```jsx
let person = {
	firstName: 'coding',
	lastName: 'Kim',
	fullName: function() {
		return this.firstName + ' ' + this.lastName;
	},
};

person.fullName(); // 여기서는 person이 호출 객체이다.
```

```jsx
var num = 0;

function showNum() {
	console.log(this.num);
}

showNum(); // 0

let obj = {
	num: 200,
	func: showNum,
};

obj.func(); // 200
```

### 4. 이벤트 핸들러 안 this

이벤트 핸들러에서 this는 이벤트를 받는 HTML 요소를 가리킨다.

```jsx
let btn = document.querySelector('#btn');
btn.addEventListener('click', function () {
	console.log(this); // #btn
});
```

### 5. 생성자 안 this

생성자 함수가 생성하는 객체로 this가 바인딩 된다.

```jsx
function Person(name) {
	this.name = name;
}

let kim = new Person('Lee');
let lee = new Person('Kim');

console.log(kim.name); // Lee
console.log(lee.name); // Kim
```

### 6. 명시적 바인딩을 한 this

명시적 바인딩은 짝을 지어주는 것이다. 

`apply()` 와 `call()` 메서드는 Function Object에 기본적으로 정의된 매서드인데, 인자를 this로 만들어 주는 기능을 한다.

```jsx
function whoisThis() {
	console.log(this);
}

whoisThis(); // Window

let obj = {
	x: 123,
};

whoisThis.call(obj); // {x: 123}
```

`apply()` 에서 매개변수로 받은 첫번째 값은 함수 내부에서 사용되는 this에 바인딩되고, 

두번째 값인 배열은 자신을 호출한 함수의 인자로 사용한다.

```jsx
function Character(name, level) {
	this.name = name;
	this.level = level;
}

function Player(name, level, job) {
	this.name = name;
	this.level = level;
	this.job = job;
}
```

이렇게 두 생성자 함수가 있을 때 this.name과 this.level을 받아오는 부분이 같은 경우

`apply()` 를 사용할 수 있다.

```jsx
function Character(name, level) {
  this.name = name;
  this.level = level;
}
 
function Player(name, level, job) {
  Character.apply(this, [name, level]);
  this.job = job;
}
 
let me = new Player('Kim', 27, 'developer');

console.log(me) // Player {name: 'Kim', level: 27, job: 'developer'}
```

`call()` 도 `apply()`와 거의 같다.

차이점이 있다면 `call()` 은 인수 목록을 받고 `apply()` 는 인수 배열을 받는다는 차이가 있다.

```jsx
function Character(name, level) {
	this.name = name;
	this.level = level;
}

function Player(name, level, job) {
	Character.call(this, name, level);
	this.job = job;
}

let me = {};
Player.call(me, 'Kim', 27, 'developer');
```

`apply()`  나 `call()` 은 보통 유사배열 객체에게 배열 매서드를 쓰고자 할 때 사용한다.

예를 들어 arguments 객체는 함수에 전달된 인수를 Array 형태로 보여주지만 배열 매서드를 쓸 수가 없다.

이 경우에 SSG 사용 가능하다.

```jsx
function func(a, b, c) {
	console.log(arguments);

	arguments.push('hi!'); // TypeError (arguments.push is not a function)
}
```

```jsx
function func(a, b, c) {
	let args = Array.prototype.slice.apply(arguments);
	args.push('hi!');
	console.dir(args);
}

func(1, 2, 3); // [ 1, 2, 3, 'hi!' ]
```

```jsx
let list = {
	0: 'Kim',
	1: 'Lee',
	2: 'Yun',
	length: 3,
};

Array.prototype.push.call(list, 'Park');
console.log(list); // {0: 'Kim', 1: 'Lee', 2: 'Yun', 3: 'Park', length: 4}
```

+ 

ES6부터  `Array.from()` 이라는 매서드 사용가능하다.

유사배열객체를 얕게 복사해 새 Array 객체로 만든다.

```jsx
let children = document.body.children; // HTMLCollection

children.forEach(function (el) { // forEach: 함수를 주어진 배열요소 각각에 실행함
	el.classList.add('on'); // ERROR (children.forEach is not a function)
```

```jsx
let children = document.body.children; // HTMLCollection

Array.from(children).forEach(function (el) {
	el.classList.add('on');
});
```

### 7. 화살표 함수로 쓴 this

함수 안에서 this가 왜 전역 객체가 되는지 모르겠으면 화살표 함수를 쓰면 된다.

화살표 함수는 전역 컨텍스트에서 실행되더라도 this를 새로 정의하지 않고, 바로 바깥 함수나 클래스의 this를 쓰기 때문이다.

```jsx
let Person = function (name, age) {
	this.name = name;
  this.age = age;
  this.say = function () {
    console.log(this); // Person {name: "Nana", age: 28}
 
    setTimeout(function () {
      console.log(this); // Window
      console.log(this.name + ' is ' + this.age + ' years old');
    }, 100);
  };
};
let me = new Person('Nana', 28);
 
me.say(); //global is undefined years old
```

위 코드를 보면 내부 함수에서 this가 전역 객체를 가리키는 바람에 의도와는 다른 결과가 나왔다.

```jsx
let Person = function (name, age) {
  this.name = name;
  this.age = age;
  this.say = function () {
    console.log(this); // Person {name: "Nana", age: 28}
 
    setTimeout(() => {
      console.log(this); // Person {name: "Nana", age: 28}
      console.log(this.name + ' is ' + this.age + ' years old'); 
    }, 100);
  };
};
let me = new Person('Nana', 28); //Nana is 28 years old
```

화살표 함수로 바꾸면 결과 제대로 나온다.