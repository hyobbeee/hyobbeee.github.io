---
layout: post
title:  "리액트 심화"
date:   2023-03-22
tags: TIL React 
author: hyobbeee
categories: React
cover:  "/assets/instacode.png"
---

# Virtual DOM

---

## 📍Real DOM

DOM은 Document Object Model의 약자로, 뜻 그대로 문서 객체 모델이란 뜻

문서 객체란 브라우저가 javascript 같은 스크립팅 언어가 `<html>`, `<head>` , `<body>` 같은 태그들에 접근하고 조작할 수 있도록 문서를 트리 구조로 객체화 한 것

<aside>
✅ *DOM은 브라우저가 HTML문서를 조작할 수 있도록 트리를 구조화한 객체 모델이다.*

</aside>

![Untitled](/assets/react-advanced/Untitled.png)

DOM 객체는 트리 구조로 되어 있다. → `querySelector` , `addEventListener` 와 같은 DOM API 를 통해 문서의 요소들을 조작 가능

### DOM의 조작속도가 느려지는 이유

자료구조 중에서도 특히 트리는 데이터 저장 x → 저장된 데이터를 탐색하기 위해 사용 → 빠른 자료 탐색 성능이 장점인 자료구조라고 볼 수 있음

그렇기 때문에 그런 트리 구조로 된 **DOM**은 자바스크립트와 같은 스크립팅 언어가 접근하고 탐색하는 속도가 빠름 

→ **변경 및 업데이트 속도 또한 빠르다고 할 수 있음**

![브라우저 렌더링 과정](/assets/react-advanced/Untitled%201.png)

브라우저 렌더링 과정

브라우저는 렌더링 과정에서 DOM트리와 CSSOM 트리르 토대로 Render트리를 생성하고 각 요소가 배치될 공간을 계산(layout)한 뒤 이를 그려냄(paint)

만약 DOM이 변경된다면 업데이트된 요소와 그에 해당하는 자식 요소들에 의해 DOM트리를 재구축하게 됨

- Reflow : 레이아웃 재연산을 수행
- Repaint : 리플로우한 것을 화면에 그려냄

## 📍Virtual DOM

: 자바스크립트 객체로 이뤄진 가상의 DOM 트리를 사용하여 실제 DOM 조작을 최소화하고 성능을 최적화하는 기술

![Untitled](/assets/react-advanced/Untitled%202.png)

가상 DOM은 실제 DOM과 동기화되어, 상태가 변경될 때마다 가상 DOM을 새로 생성하여 이전상태와 비교한다.

그리고 변경이 필요한 부분만 실제 DOM에 반영하여 업데이트 하므로, 전체 UI를 다시 그리지 않아도됨

# Diffing Alogorithm

---

리액트가 기존 가상 DOM과 변경된 새로운 가상 DOM을 비교할 때,

리액트 입장에서는 변경된 새로운 가상 DOM 트리에 부합하도록 기존의 UI를 효율적으로 갱신하는 방법을 알아낼 필요가 있었다.

→ 하나의 트리를 다른 트리로 변형을 시키는 가장 작은 조작 방식을 알아내야만 했는데, 알아낸 조작 방식 알고리즘은 O(n^3)의 복잡도를 갖고 있었다.

만약 이 알고리즘을 그대로 React에 적용한다면 1000개의 엘리먼트를 실제 화면에 표시하기 위해 10억(1000^3)번의 비교 연산을 해야한다. 

이것은 너무 비싼 연산이기 때문에 리액트는 두가지 가정을 갖고 시간 복잡도 O(n) 의  새로운 휴리스틱 알고리즘(Heuristic Algorithm)을 구현해낸다.

### 두가지 가정

1. 각기 서로 다른 두 요소는 다른 트리를 구축할 것이다.
2. 개발자가 제공하는 `key` 프로퍼티를 가지고, 여러번 렌더링을 거쳐도 변경되지 말아야 하는 자식 요소가 무엇인지 알아낼 수 있을 것이다.

실제 이 두 가정은 거의 모든 실제 사용 사례에 들어맞게 되고, 여기서 리액트는 비교 알고리즘(Diffing Algorithm)을 사용한다.

## 📍리액트가 DOM트리를 탐색하는 방법

리액트는 기존의 가상 DOM 트리와 새롭게 변경된 가상 DOM트리를 비교할 때, 트리의 레벨 순서대로 순회하는 방식으로 탐색한다.

→ 같은 레벨(위치)끼리 비교한다는 뜻(BFS, 너비 우선 탐색의 일종)

![동일 선상에 있는 노드를 파악한 뒤 다음 자식 세대의 노드를 순차적으로 파악](/assets/react-advanced/Untitled%203.png)

동일 선상에 있는 노드를 파악한 뒤 다음 자식 세대의 노드를 순차적으로 파악

### 다른 타입의 DOM 엘리먼트인 경우

DOM 트리는 각 HTML태그마다 각각의 규칙이 있어 그 아래 들어가는 자식 태그가 한정적

- e.g.
    - `<ul>` 태그 밑에는 `<li>` 태그만 와야 함
    - `<p>` 태그 안에 `<p>` 태그를 또 쓰지 못함

자식 태그의 부모 태그 또한 정해져 있다는 특징이 있기 때문에, 부모 태그가 달라진다면 리액트는 이전 트리를 버리고 새로운 트리를 구축해 버린다.

```jsx
<div>
	<Counter />
</div>

//부모 태그가 div에서 span으로 바뀝니다.
<span>
	<Counter />
</span>
```

부모 노드였던 `<div>`가 `<span>`으로 바뀌어버리면 자식 노드인 `<Counter />`는 완전히 해제

이전 `<div>` 태그 속 `<Counter />`는 파괴되고, `<span>` 태그 속 새로운 `<Counter />`가 다시 실행

새로운 컴포넌트가 실행되면서 기존의 컴포넌트는 완전히 `해제(Unmount)`되어버리기 때문에 `<Counter />`가 갖고 있던 기존의 `state` 또한 파괴

### 같은 타입의 DOM 엘리먼트인 경우

반대로 타입이 바뀌지 않는다면 리액트는 최대한 렌더링을 하지 않는 방향으로 최소한의 변경 사항만 업데이트한다.

→ 리액트가 실제 DOM이 아닌 가상 DOM을 사용해 조작하기 때문에 가능

업데이트 할 내용이 생기면 virtual DOM 내부의 프로퍼티만 수정한 뒤, 모든 노드에 걸친 업데이트가 끝나면 그때 단 한번 실제 DOM으로의 렌더링을 시도한다.

```jsx
<div className="before" title="stuff" />

//기존의 엘리먼트가 태그는 바뀌지 않은 채 className만 바뀌었습니다.
<div className="after" title="stuff" />
```

리액트는 두 요소를 비교했을 때 `className` 만 수정되고 있다는 것을 알게 된다.

`className before` 와 `after` 는 아래와 같은 스타일을 갖고 있다고 해보자

```jsx
//className이 before인 컴포넌트
<div style={{color: 'red', fontWeight: 'bold"}} title="stuff" />

//className이 after인 컴포넌트
<div style={{color: 'green', fontWeight: 'bold"}} title="stuff" />
```

두 엘리먼트를 비교했을 때는 리액트는 `color` 스타일만 바뀌고 있음을 알아낸다. 

→ 리액트는 `color` 스타일만 수정하고 `fontWeight` 및 다른 요소는 수정하지 않음

이렇게 하나의 DOM 노드를 처리한 뒤 리액트는 뒤이어서 해당 노드들 밑의 자식들을 순차적으로 동시에 순회하면서 차이가 발견될 때마다 변경

→ 이를 재귀적으로 처리한다고 함

### 자식 엘리먼트의 재귀적 처리

예를 들면 이렇게 자식 엘리먼트가 변경이 된다고 가정해보자

```jsx
<ul>
  <li>first</li>
  <li>second</li>
</ul>

//자식 엘리먼트의 끝에 새로운 자식 엘리먼트를 추가했습니다.
<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```

리액트는 기존 `<ul>`과 새로운 `<ul>`을 비교할 때 자식 노드를 순차적으로 위에서부터 아래로 비교하면서 바뀐 점을 찾는다.

그렇기 때문에 예상대로 리액트는 첫번째 자식 노드들과 두번째 자식 노드들이 일치하는걸 확인한 뒤 세번째 자식 노드를 추가한다.

이렇게 리액트는 위에서 아래로 순차적으로 비교하기 때문에, 이 동작 방식에 대해 고민하지 않고 리스트의 처음에 엘리먼트를 삽입하게 되면 이전 코드에 비해 훨씬 나쁜 성능을 내게 된다.

```jsx
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

//자식 엘리먼트를 처음에 추가합니다.
<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```

이렇게 구현하게 되면 리액트는 우리의 기대대로 최소한으로 동작하지 못하게 된다.

리액트는 원래의 동작하던 방식대로 처음의 노드들을 비교하게 된다.

처음의 자식 노드를 비교할 때, `<li>Duke</li>`와 `<li>Connecticut</li>`로 자식 노드가 서로 다르다고 인지하게 된 리액트는 리스트 전체가 바뀌었다고 받아들임 

`<li>Duke</li>`와 `<li>Connecticut</li>`는 그대로기 때문에 두 자식 노드는 유지시켜도 된다는 것을 깨닫지 못하고 전부 버리고 새롭게 렌더링 해버린다.(🙅🏻‍♂️ 이는 굉장히 ~~비효율적인 동작 방식~~이다.)

🙆🏻‍♂️ ***리액트는 이문제를 해결하기 위해 `key`라는 속성을 지원***

이는 효율적으로 가상 DOM을 조작할 수 있도록 한다. 

<aside>
💡 개발할 당시 key라는 속성을 사용하지 않으면 리액트에서 `key`값을 달라고 경고문을 띄우는 것도 이것 때문이다. 
→ `key` 값이 없는 노드는 비효율적으로 동작할 수 있기 때문

</aside>

### 키(key)

만약 자식 노드들이 이 `key`를 갖고 있다면, React는 그 `key`를 이용해 기존 트리의 자식과 새로운 트리의 자식이 일치하는지 아닌지 확인 가능

```jsx
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

//key가 2014인 자식 엘리먼트를 처음에 추가합니다.
<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

리액트는 `key`속성을 통해 `'2014'`라는 자식 엘리먼트가 새롭게 생겼고, `'2015'`, `'2016'` 키를 가진 엘리먼트는 그저 위치만 이동했다는 걸 알게 된다.

→ 리액트는 기존의 동작 방식대로 다른 자식 엘리먼트는 변경하지 않고 추가된 엘리먼트만 변경한다.

이 `key`속성에는 보통 데이터 베이스 상의 유니크한 값(e.g. `Id`)을 부여해 주면 된다.

**키는 전역적으로 유일할 필요는 없고, 형제 엘리먼트 사이에서만 유일하면 됨**

만약 이런 유니크한 값이 없다면 최후의 수단으로 배열의 인덱스를 key로 사용할 수 있다. 

다만 배열이 다르게 정렬될 경우가 생긴다면 배열의 인덱스를 `key`로 선택했을 경우는 비효율적으로 동작할 것 → 왜냐하면 배열이 다르게 정렬되어도 인덱스는 그대로 유지되기 때문

인덱스는 그대로지만 그 요소가 바뀌어버린다면 리액트는 배열의 전체가 바뀌었다고 받아들일 것이고, 기존의 DOM트리를 버리고 새로운 DOM트리를 구축해버리기 떄문에 비효율적으로 동작하는 것

# Component와 Hook

---

리액트에는 다양한 Hook이 존재하고 있다.

그 전에 훅이 무엇인지, 가장 기본적인 훅이 무엇인지 사용 규칙 등에 대해 알아보자

## 📍Function Component와 Class Component

Hook은 함수 컴포넌트에서 사용하는 메서드이다.

함수 컴포넌트는 이전에는 클래스(class) 컴포넌트가 있었다.

많은 리액트 개발자들이 이클래스 컴포넌트를 사용하여 리액트 앱을 개발해왔었다.

### Class Component

여기 `<Counter />` 컴포넌트를 클래스로 작성한 간단한 코드를 보겠다

```jsx
class Counter extends Component {
	constructor(props) {
		super(props);
		this.state = {
			counter: 0
		}
		this.handleIncrease = this.handleIncrease.bind(this);
	}
	
	handleIncrease = () => {
		this.setState({
			counter: this.state.counter + 1
		})
	}
	
	render() {
		return (
			<div>
				<p>You clicked {this.state.counter} times</p>
				<button onClick={this.handleIncrease}>
					Click me
				</button>
			</div>
		)
	}
}
```

함수 컴포넌트 이전의 클래스 컴포넌트를 작성할 때에는(라떼는) 이 정도는 작성해야지 앱이 정상적으로 동작할 수 있었음

![https://user-images.githubusercontent.com/58800295/180726540-286c2747-422f-4daf-8e91-19d16384b720.gif](https://user-images.githubusercontent.com/58800295/180726540-286c2747-422f-4daf-8e91-19d16384b720.gif)

단순히 카운팅을 하는 기능만 구현한 것

이런 클래스 컴포넌트는 복잡해질수록 이해하기 어려워졌고, 컴포넌트 사이에서 상태로직을 재사용하기 어렵다는 단점이 었다.

또한 리액트 클래스 컴포넌트를 사용하기 위해서는 자바스크립트의 this 키워드가 어떤 방식으로 동작하는지 알아야 하는데, 이는 문법을 정확히 알지 못하면 동작 방식 자체를 정확히 이해하기 어렵게 만들곤 했다.

그래서 리액트는 점진적으로 클래스 컴포넌트에서 함수 컴포넌트로 넘어갔다.

다만 이전까지의 함수 컴포넌트는 클래스 컴포넌트와는 다르게 상태 값을 사용하거나 최적화할 수 있는 기능들이 조금 미진했는데, 그 부분들을 보완하기 위해 ***Hook이라는 개념***을 도입했다.

### Function Component

이번에는 `<Counter />` 컴포넌트를 함수형 컴포넌트로 작성해보자

```jsx
function Counter () {
	const [counter, setCounter] = useState(0);
	
	const handleIncrease = () => {
		setCounter(counter + 1)
	}
	
	return (
		<div>
			<p>당신은 {counter}번 클릭했다</p>
			<button onClick={handleIncrease}>
				Click me
			</button>
		</div>
	)
}
```

함수형 컴포넌트는 클래스형 컴포넌트에 비해 **훨씬 더 직관적**이고, **보기 쉽다**는 특징이 있다. 

이 `Counter`컴포넌트에서 숫자를 올리기 위해 상태값을 저장하고 사용할 수 있게 해주는 `useState()`가 있는데, 익히 들어 알고 있는 이 메서드가 바로 Hook이다.

***→ `Counter` 컴포넌트에서 `useState()` Hook을 호출해 함수 컴포넌트(function component)안에 state를 추가한 형태***

이 state는 컴포넌트가 리렌더링 되어도 그대로 유지될 것이고, 해당 컴포넌트에서 State Hook은 하나만 사용했지만 때에 따라서 여러개 사용할 수 도 있다.

## 📍Hook이란

> Hook은 React 16.8에 새로 추가된 기능입니다. Hook은 class를 작성하지 않고도 state와 다른 React의 기능들을 사용할 수 있게 해줍니다.
> 

리액트의 공식문서를 살펴보면 위와 같은 문구가 있는데, 다르게 말하면

**함수형 컴포넌트에서 상태 값 및 다른 여러 기능을 사용하기 편리하게 해주는 메서드를 의미**한다.

Hook은 class가 아닌 function으로만 리액트를 사용할 수 있게 해주는 것이기 때문에 $클래스형 컴포넌트에서는 동작하지 않는다.$

```jsx
...
render(){
    /* 클래스 컴포넌트는 render() 안에서 변수를 작성할 수 있습니다. */
    const [counter, setCounter] = useState(0);
...
}
```

![클래스 컴포넌트에서는 `useState()` Hook을 호출할 수 없다는 에러](/assets/react-advanced/Untitled%204.png)

클래스 컴포넌트에서는 `useState()` Hook을 호출할 수 없다는 에러

억지로 호출을 해보려고 해도 해당 방식은 리액트에서 허락하지 않는 호출 방식이기 때문에 위와 같은 에러를 브라우저 전면에 띄운다.

해당 에러를 삭제하면 컴포넌트 자체가 렌더링 되지 않는 것 확인 가능

## 📍Hook 사용 규칙

### 1. 리액트 함수의 최상위에서만 호출해야 한다.

반복문, 조건문, 중첩된 함수 내에서 Hook을 실행하면 예상한대로 동작하지 않을 우려가 있다.

```jsx
...
if (counter) {
	const [sample, setSample] = useState(0);
}
...
```

예를 들어 count가 있을 때 sample이라는 state를 사용하고 싶어서 조건문 안에 useState() hook을 불러왔다고 가정해보자

이런 식으로 호출하게 되면 리액트의 동작방식에 거스르기 떄문에 리액트는 에러를 출력한다. 

![useState를 조건문 안에서 호출하면 안된다는 에러](/assets/react-advanced/Untitled%205.png)

useState를 조건문 안에서 호출하면 안된다는 에러

컴포넌트 안에는 useState나 useEffect 같은 Hook들이 여러번 사용 될 수 있는데, 리액트는 이 Hook을 호출되는 순서대로 저장을 해놓는다.

그런데 조건문, 반복문 안에서 Hook을 호출하게 되면 호출되는 순서대로 저장을 하기 어려워지고, 결국 예기치 못한버그를 초래하게 될 수 있다.

### 2. 오직 리액트 함수 내에서만 사용되어야 한다.

이는 리액트 함수형 컴포넌트나 커스텀 Hook이 아닌 다른 일반 자바스크립트 함수 안에서 호출해서는 안된다는 의미

```jsx
...
window.onload = function () {
    useEffect(() => {
        // do something...
    }, [counter]);
}
...
```

예를 들어 window의 요소가 모두 준비 되면 `useEffect()` 가 호출되었으면 좋겠다고 생각해서 함수를 작성했다고 가정해보자.

이 또한 리액트의 동작 방식에 위배된다고 한다.

![window.onload라는 함수에서 useEffect를 호출하면 안된다는 에러](/assets/react-advanced/Untitled%206.png)

window.onload라는 함수에서 useEffect를 호출하면 안된다는 에러

애초에 Hook은 리액트의 함수 컴포넌트 내에서 사용되도록 만들어진 메서드이기 때문에 근본적으로 일반 자바스크립트 함수 내에서는 정상적으로 돌아가지 않는다.

# useMemo

---

컴포넌트는 기본적으로 상태가 변경되거나 부모 컴포넌트가 렌더링이 될 때마다 리렌더링을 하는 구조로 이뤄져있다. 그러나 너무 잦은 리렌더링은 좋지 않은 성능을 끼친다.

![너무 많은 렌더링이 앱에 안 좋은 성능을 미치는 극단적 예?](/assets/react-advanced/Untitled%207.png)

너무 많은 렌더링이 앱에 안 좋은 성능을 미치는 극단적 예?

리액트 훅은 함수 컴포넌트가 상태를 조작하고 및 최적화 기능을 사용할 수 있게끔 하는 메서드라고 했다. 그 중 렌더링 최적화를 위한 훅도 존재하는데, useCallback과 useMemo가 바로 그 역할을 하는 Hook이다.

## 📍useMemo란?

: 특정 값(value)를 재사용하고자 할 때 사용하는 Hook이다.

```jsx
function Calculator({value}) {
	const result = calculate(value);
	return <>
		<div>
			{result}
		</div>
	</>;
}
```

해당 컴포넌트는 props로 넘어온 `value`값을 `calculate`라는 함수에 인자로 넘겨서 `result`값을 구한 후, `<div>`엘리먼트로 출력을 하고 있다.

만약 여기서 `calculate`가 내부적으로 복잡한 연산을 해야하는 함수라 계산된 값을 반환하는 데에 시간이 몇 초 이상 걸린다고 가정해보자.

그렇다면 해당 컴포넌트는 렌더링을 할때마다 이 함수를 계속해서 호출할 것이고, 그때마다 시간이 몇 초이 상 소요가 될 것이다. 이 몇 초의 지연은 렌더링에도 영향을 미칠 것이고, 사용자는 ‘앱의 로딩 속도가 느리네?’라는 생각을 하게 될 것이다.

```jsx
// useMemo를 사용하기 전에 import 필요
import { useMemo } from 'react;

function Calculator({value}) {
	const result = useMemo(() => calculate(value), [value]);
	return <>
		<div>
			{result}
		</div>
	</>;
}
```

여기 `value`를 인자로 받는 Calculator 컴포넌트가 있다.

`value`는 일종의 값으로서, 이 값이 계속 바뀌는 경우라면 어쩔 수 없겠지만, 렌더링을 할 때마다 이 `value`값이 계속 바뀌는 게 아니라고 생각해보자.

그럼 이 값을 어딘가에 저장을 해뒀다가 다시 꺼내서 쓸 수만 있다면 굳이 `calculate` 함수를 호출할 필요도 없을 것이다. 

여기서 `useMemo` Hook을 사용할 수 있다.

위와 같이 `useMemo`를 호출하여 `calculate`를 감싸주면, 이전에 구축된 렌더링과 새로이 구축되는 렌더링을 비교해 `value`값이 동일할 경우에는 이전 렌더링의 `value`값을 그대로 재활용할 수 있게 된다.

이는 메모이제이션(Memoization) 개념과 긴밀한 관계가 있다.

### Memoization

메모이제이션은 알고리즘에서 자주 나오는 개념이다.

기존에 수행한 연산의 결과값을 메모리에 저장을 해두고, 동일한 입력이 들어오면 재활용하는 프로그래밍 기법을 말한다.

이 메모이제이션을 적절히 사용한다면 굳이 중복 연산을 할 필요가 없기 때문에 앱의 성능을 최적화할 수 있다.

`useMemo`는 바로 이 개념을 이용하여 복잡한 연산의 중복을 피하고 리액트 앱의 성능을 최적화 시킨다.

직접 메모이제이션 개념을 이용해 로직을 구현할 수도 있겠지만, `useMemo` Hook을 호출한다면 이런 로직을 직접 구현하는 것을 대신할 수 있어 간편!

## 📍useMemo를 이용한 앱 최적화

```jsx
import React, { useState, useMemo } from "react";
import "./styles.css";
import { add } from "./add";

export default function App() {
  const [name, setName] = useState("");
  const [val1, setVal1] = useState(0);
  const [val2, setVal2] = useState(0);
  // const answer = add(val1, val2);
 const answer = useMemo(() => add(val1, val2), [val1, val2]);
  return (
    <div>
      <input
        className="name-input"
        placeholder="이름을 입력해주세요"
        value={name}
        type="text"
        onChange={(e) => setName(e.target.value)}
      />
      <input
        className="value-input"
        placeholder="숫자를 입력해주세요"
        value={val1}
        type="number"
        onChange={(e) => setVal1(Number(e.target.value))}
      />
      <input
        className="value-input"
        placeholder="숫자를 입력해주세요"
        value={val2}
        type="number"
        onChange={(e) => setVal2(Number(e.target.value))}
      />
      <div>{answer}</div>
    </div>
  );
}
```

위 코드에서 이름을 입력할 경우에도 `add` 함수가 호출된다.

불필요한 렌더링 방지를 위해 `add`함수를 `useMemo`로 감싼 후 의존성을 설정

→ 이름을 입력 시 랜더링 되지 않고 숫자의 값이 변경될 경우만 랜더링 

# useCallback

---

## 📍useCallback이란

useMemo와 마찬가지로 메모이제이션 기법을 이용한 Hook이다. useMemo는 값의 재사용을 위해 사용하는 Hookdㅣ라면, useCallback은 함수의 재사용을 위해 사용하는 Hook이다.

```jsx
function Calculator({x, y}) {
	const add = () => x + y;
	return <>
		<div>
			{add()}
		</div>
	</>;
}
```

현재 이 `Calculator` 컴포넌트 내에는 `add`라는 함수가 선언이 되어 있는 상태이다.

이 `add`함수는 props로 넘어온 `x`와 `y`값을 더해 `<div>` 태그에 값을 출력하고 있다.

이 함수는 해당 컴포넌트가 렌더링 될때마다 새롭게 만들어질 것이다.

`useMemo`와 마찬가지로, 해당 컴포넌트가 리렌더링 되더라도 그 함수가 의존하고 있는 값인 `x`와 `y`가 바뀌지 않는다고 생각해보자. 

그렇다면 함수 또한 메모리 어딘가에 저장해 뒀다가 다시 꺼내서 쓸 수 있을 것이다.

이때 `useCallback` Hook을 사용하면 그 함수가 의존하는 값들이 바뀌지 않는 한 기존 함수를 계속해서 반환한다. → `x`와 `y`값이 돌일하다면 다음 렌더링 때 이 함수를 다시 사용한다.

```jsx
/* useCallback를 사용하기 전에는 꼭 import해서 불러와야 합니다. */
import React, { useCallback } from "react";

function Calculator({x, y}){

	const add = useCallback(() => x + y, [x, y]);

	return <>
      <div>
					{add()}
      </div>
  </>;
}
```

사실 `useCallback`만 사용해서는 `useMemo`에 비해 괄목할 만한 최적화를 느낄 수는 없다.

왜냐하면 `useCallback`은 함수를 호출하지 않는 Hook이 아니라, 그저 메모리 어딘가에 함수를 꺼내서 호출하는 Hook이기 때문이다.

따라서 단순히 컴포넌트 내에서 함수를 반복해서 생성하지 않기 위해서 `useCallback`을 사용하는 것은 큰 의미가 없거니와 오히려 손해인 경우도 있다.

자식 컴포넌트의 props로 함수를 전달해줄때 이 `useCallback`을 사용하기가 좋다.

## 📍useCallback과 참조 동등성

`useCallback`은 참조 동등성에 의존한다. 

React는 JavaScript언어로 만들어진 오픈소스 라이브러리이기 때문에 기본적으로 JavaScript 문법을 따른다.

JavaScript에서 함수는 객체이다. 

객체는 메모리에 저장할 때 값을 저장하는 게 아니라 값의 주소를 저장하기 때문에, 반환하는 값이 같을 지라도 일치연산자(===)로 비교했을 때 `false`가 출력된다. 

```jsx
function doubleFactory(){
    return (a) => 2 * a;
}
  
const double1 = doubleFactory();
const double2 = doubleFactory();
  
double1(8); // 16
double2(8); // 16
  
double1 === double2;  // false
double1 === double1;  // true
```

`double1`과 `double2`는 같은 함수를 할당했음에도 메모리 주소 값이 다르기 때문에 같다고 보지 않는다. 

자바스크립트에서 함수는 객체이다.

따라서 두개의 함수는 동일한 코드를 공유하더라도 메모리 주소가 다르기 때문에, 메모리 주소에 의한 참조 비교시 다른 함수로 본다.

이는 리액트 또한 같다.

리액트는 리렌더링 시 함수를 새로이 만들어서 호출한다.

새로이 만들어 호출된 함수는 기존의 함수와 같은 함수가 아니다.

그러나 `useCallback`을 이용해 함수 자체를 저장해서 다시 사용하면 함수의 메모리 주소값을 저장했다가 다시 사용한다는 것과 같다고 볼 수 있다. 

→ 리액트 컴포넌트 함수 내에서 다른 함수의 인자로 넘기거나 자식 컴포넌트의 prop으로 넘길 때 예상치 못한 성능 문제를 막을 수 있다.

## 📍useCallback을 이용한 앱 최적화

```jsx
import { useCallback, useState } from "react";
import "./styles.css";
import List from "./List";
import { useEffect } from "react";

export default function App() {
  const [input, setInput] = useState(1);
  const [light, setLight] = useState(true);

  const theme = {
    backgroundColor: light ? "White" : "grey",
    color: light ? "grey" : "white"
  };
  
  // const getItems = () => {
  //   return [input + 10, input + 100];
  // };
  const getItems = useCallback(() => {
    return [input + 10, input + 100];
  }, [input]);

  const handleChange = (event) => {
    if (Number(event.target.value)) {
      setInput(Number(event.target.value));
    }
  };

  return (
    <>
      <div style={theme} className="wall-paper">
        <input
          type="number"
          className="input"
          value={input}
          onChange={handleChange}
        />
        <button
          className={(light ? "light" : "dark") + " button"}
          onClick={() => setLight((prevLight) => !prevLight)}
        >
          {light ? "dark mode" : "light mode"}
        </button>
        <List getItems={getItems} />
      </div>
    </>
  );
}
```

초기 상태 : `input`의 숫자가 변경되거나 `button`을 눌렀을 때에도 “아이템을 가져옵니다.” 콘솔 창에 출력됨

<aside>
⚠️ 위처럼 동작하는 이유는 버튼을 누를 때도 앱이 리렌더링 되므로, App 내부의 `getItems()`함수가 다시 만들어졌기 때문, 새로 만들어진 함수는 이전의 함수와 참조 비교시 다른 함수이기 때문에 계속해서 리렌더링 발생

</aside>

→ `useCallback`을 이용하여 함수 자체를 저장해서 버튼을 눌러도 콘솔에 출력되지 않도록 수정