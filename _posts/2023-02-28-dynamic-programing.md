---
layout: post
read_time: true
show_date: true
title:  "동적 계획법(Dynamic Programing)"
date:   2023-02-28
description: 재귀함수와의 비교
img: my_keyboard.jpeg
tags: [TIL, JavaScript, algorithm]
author: hyobbeee
mathjax: yes
---

## 📍개념

처음 진행되는 연산은 기록해두고, 이미 진행했던 연산이라면 다시 연산하는 것이 아니라 기록된 값을 가져오는 방식

→ **큰 문제를 작은 문제로 쪼개서 그 답을 저장해두고 재활용하는 것**

~~Richad Bellman이 1950년대에 그냥 멋있어보여서 그렇게 지은 거라고함;~~

## 📍배경

### 피보나치 수열

동적 계획법의 등장 배경은 피보나치 수열을 통해 알 수 있다.

피보나치 수열은 제 2항까지는 1, 제 3항부터는 바로 앞의 두항을 더한 수로 정의된다.

```jsx
(0), 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89...
```

프로그래밍에서 피보나치는 수열은 보통 재귀를 통해 표현한다.

아래는 피보나치 수열의 n번째 수를 구하는 함수이다.

```jsx
function fibonacci(n) {
	if (n <= 2)
		return 1;
	else
		return fibonacci(n - 1) + fibonacci(n - 2);
}
```

`fibonacci(6)` 의 실행 과정

![https://velog.velcdn.com/images/chelsea/post/627c053e-8a71-48e7-b0b2-7b8d327963a2/재귀피보.gif](https://velog.velcdn.com/images/chelsea/post/627c053e-8a71-48e7-b0b2-7b8d327963a2/재귀피보.gif)

이미 진행 됐던 연산임에도 불구하고 `fibo(4)` 의 연산이 2번, `fibo(3)` 의 연산이 3번 진행되는 것을 볼 수 있다. 

## 📍구현

```jsx
let fiboData = new Array(100).fill(0); // fiboData 라는 배열을 생성해 연산 값을 담아놓는다.

function fibonacci(n) {
	if (n <= 2) {
		return 1;
	}
	if (fiboData[n] === 0) { // 연산 값이 있는지 검사, 있는 경우 바로 fiboData[n] 반환
		fiboData[n] = fibonacci(n - 1) + fibonacci(n - 2); // 없는 경우 새로 연산해서 fiboData[n]에 값을 저장
	}
	return fiboData[n]; // 저장한 값 반환
}
```

위 함수를 보면 재귀와는 다르게 중복 연산이 사라진걸 확인 할 수 있다.

## 📍메모이제이션(Memoization)

위 코드에서는 하위 문제를 해결할 때 그 해결책을 저장해 두고, 똑같은 문제가 발생했을 때 저장되어 있던 해결책을 가지고 간단하게 해결했다.

이렇게 동일한 문제를 반복해야 할 경우, 한 번 계산된 결과를 저장해 두었다가 활용하는 방식으로 중복 계산을 줄이는 것을 말한다.

위 공식과 결과는 같지만 반대로 계산하는 식도 구현해 보았다.

```jsx
function fibonacci(n) {
  const fiboData = new Array(n + 1).fill(0);
  fiboData[0] = 0;
  fiboData[1] = 1;
  for (let i = 2; i <= n; i++) {
    fiboData[i] = fiboData[i - 1] + fiboData[i - 2];
  }
  return fiboData[n];
}
```

만약에 `fibonacci(6)` 을 호출한다면 

for문 내에서 fiboData[2] 부터 fiboData[6] 까지 점진적으로 계산해 나간다.

이렇게 처음 값부터 계산해 최종 값까지 계산해 내는 것을 **BOTTOM-UP** 방식이라고 한다.

## 장단점

동적 계획법은 모든 방법을 일일이 검토하여 최적의 해를 찾아내는 방식의 알고리즘이다. 

여기서 그리디 알고리즘(탐욕 알고리즘)과 대비된다. 

그리디 알고리즘은 모든 해를 구하지 않고 순간마다 그 순간에서의 최적의 해를 찾는 방식이다.

닥치는 순간만을 고려해서 해를 구하기 때문에 도출된 값이 항상 최적의 해라고 할 수는 없다.

하지만 동적 계획법은 모든 방법을 검토해보고 결과적으로 효율적인 값을 택한다. 

그런 면에서 동적 계획법은 그리디 알고리즘에 비해 시간이 오래 걸리지만, 결과적으로는 항상 최적의 해를 구할 수 있다는 이점을 가지고 있다.