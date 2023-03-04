---
layout: post
title:  "정렬(bubble, insertion, selection) 알고리즘"
date:   2023-02-25
author: hyobbeee
categories: algorithm
tags: TIL algorithm
cover:  "/assets/instacode.png"
---

## 🙀summary

무작위로 섞여 있는 데이터를 어떤 기준에 맞춰 정렬하는 알고리즘은 여러개가 있다.

자바스크립트에는 이미 `sort()` 라는 메소드가 이미 존재한다. 하지만 퍼포먼스가 보장 되지 않기 때문에 유명한 정렬 알고리즘은 반드시 알고 있어야 한다고 한다. ~~(뭔들 많이 알고 있으면 안좋으려나..)~~

그럼에도 불구하고 우리가 정렬 알고리즘을 배워야하는 이유는 시스템 정렬이 항상 좋은 퍼포먼스를 보장하지 않기 때문이다 또한 내가 가진 데이터베이스의 양이 상황에 따라 어떤 정렬을 사용하는 것이 좋을지 달라지기 때문에 우리는 기본적으로 유명한 정렬 알고리즘들은 반드시 알고 있어야 한다.

오늘은 그중에서도 가장 기본적이고 쉬운 버블 정렬과 삽입 정렬, 선택 정렬에 대해 정리해보았다.

## 📍Bubble Sort

버블 정렬은 요소들이 마치 거품이 일어나듯 연쇄적으로 자기 자리를 찾아간다고 해서 버블 정렬이라고 불린다. 

![https://blog.kakaocdn.net/dn/bUgECt/btqwWZBXDYj/jJwP1TnMQL3u1nbzt5Gzb1/img.gif](https://blog.kakaocdn.net/dn/bUgECt/btqwWZBXDYj/jJwP1TnMQL3u1nbzt5Gzb1/img.gif)

위 그림을 보면 알 수 있듯이 데이터를 두개씩 묶어서 비교한 후 크기가 큰 쪽이 오른쪽으로 가도록 자리를 바꿔가며 크기가 큰 데이터를 오른쪽으로 민다. 그러면 1 회전이 끝남과 동시에 이 리스트에서 가장 큰 값이 오른쪽으로 가게 된다. 즉, n번째 정렬 회차가 끝나면 뒤에서 n번째 자리의 데이터가 확정된다.

### Big O

- Worst Case : O(n^2) → 정렬이 하나도 안되어 있는 경우
- Best Case : O(n) → 이미 정렬이 되어있는 경우

버블 정렬은 최악의 경우에 O(n^2)의 시간 복잡도를 가진다. 왜냐하면 각 자리를 찾기 위해서 n번의 순회를 해야하며 n번의 회전동안에 요소의 개수만큼 또 순회를 해야 하기 때문이다. 그러나 이미 정렬이 되어 있는 경우에는 한 번의 순회로 정렬 여부를 알 수 있다.

### 장점

버블 정렬은 **in place** 알고리즘이기 때문에 **메모리가 절약**된다는 장점이 있다. 여기서 “in place” 라는 것은 자료를 정렬할 때 추가적인 메모리 공간이 필요한 것이 아니고 데이터가 저장된 그 공간 내에서 정렬한다는 뜻이다.

또한 구현하기가 매우 쉽다?는 장점도 있다. 그 외에도 이미 정렬된 데이터를 순회하는 경우 O(n)번만 순회하면 되기 때문에 정렬이 되었는지 안되었는지 테스트하는 용도로 사용할 수도 있다.

### 단점

버블 정렬은 가장 큰 단점은 자료의 개수가 많아질수록 **성능**이 매우 떨어진다는 점이다. 왜냐면 최악의 경우 O(n^2)이 소요되기 떄문이다. 만약 데이터가 위 이미지처럼 5개밖에 없다면 최대 25번 순회를 해야하지만 데이터가 1,000개 라면 1,000,000번이나 순회를 해야한다.

### Stable

버블 정렬은 중복 데이터가 있을 경우 데이터의 위치를 교환하지 않고 지나가기 때문에 **stable한 정렬**이다. 여기서 stable이란 중복 데이터가 있는 경우 기존 중복 데이터의 순서가 정렬이 다 끝난 후에도 유지되는 것을 말한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ee3faedc-e814-4d77-b001-565b49fea795/Untitled.png)

```jsx
const bubbleSort = function (arr) {
   for (let i = 0; i < arr.length; i++) {
    let swap;
    for (let j = 0; j < arr.length - 1 - i; j++) {
		// arr.length - 1 - i 인 이유 
		// array[j]와 array[j + 1]을 비교할 예정이기 때문에
		// 배열의 마지막 데이터를 포함하지 않아도 검색 대상에 포함되므로 1을 빼줘야하고
		// 각 회전이 끝날 때마다 마지막 데이터의 정렬이 끝나기 떄문에 i를 뺴줘야 한다.
      if (arr[j] > arr[j + 1]) {
				// swap이란 변수를 만들어 array[j]와 array[j+1]을 비교하여 array[j]가 더 크면 자리 교환
        swap = arr[j];
        arr[j] = arr[j + 1];
        arr[j + 1] = swap;
      }
    }
    console.log(`${i}회전: ${arr}`);
    if (!swap) {
		// 만약 회전을 끝내고 swap이란 변수가 undefined 상태라면 정렬이 다되었다는 뜻이다.
      break;
    }
  }
  return arr;
};

console.log(bubbleSort([5, 4, 3, 2, 1]));
```

![버블 정렬 출력](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3153343d-ef01-496d-9646-0998b87f072a/Untitled.png)

버블 정렬 출력

## 📍Insertion Sort

삽입 정렬은 왼쪽에서 오른쪽으로 가면서 각 요소들을 왼쪽 요소들과 비교하여 알맞은 자리에 삽입하는 형식의 정렬 방법이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e1a0b4da-31a7-4972-b71d-ec43f4b07c5c/Untitled.png)

삽입 정렬은 위 그림처럼 항상 두 번째 요소를 왼쪽 요소와 비교하면서 시작한다. 1회전에서는 왼쪽 요소보다 두번째 요소가 크기 때문에 아무런 일이 일어나지 않는다. 2회전에서는 2를 왼쪽 두 개의 데이터와 비교하여 가장 왼쪽에 끼워넣는다. 3회전에서는 5를 왼쪽 데이터와 비교하여 3과 7사이에 끼워 넣는다. 이런식으로 회전하다보면 정렬되는 방식이다.

삽입 정렬은 항상 왼쪽 비교 대상 데이터들이 정렬되어있다는 가정하에 진행된다.

### Big O

- Worst Case: O(n^2) → 정렬이 하나도 안되어있는 경우
- Best Case : O(n) → 이미 정렬이 되어있는 경우

삽입 정렬 역시 버블 정렬과 똑같은 시간 복잡도를 가진다.

### 장점

삽입 정렬도 버블 정렬과 같이 In place 알고리즘 이기 때문에 메모리가 절약된다.

또한 구현하기 쉽?고 이미 정렬된 데이터를 순회하는 경우 O(n)번만 순회하면 되기 때문에 정렬이 되었는지 안되었는지 테스트 하는 용도로 사용될 수 있다.

### 단점

삽입 정렬도 중복된 데이터는 위치를 교환하지 않기 때문에 **stable한 정렬**이다.

```jsx
function insertionSort (arr) {
	for (let i = 1; i < array.length; i++) {
	// 두번쨰 요소부터 선택해서 앞의 요소와 비교
		let cur = arr[i]; // 현재 데이터 저장
		let left = i - 1;

		while (left >= 0 && arr[left] > cur) {
		// 두 데이터를 교환하고 cur에 arr[left]를 담고 left는 -1을 해줌
			arr[left + 1] = arr[left];
			arr[left] = cur;
			cur = arr[left];
			left--;
		}
	}
	return arr;
}
```

처음에 구현한 삽입 정렬 코드이다. 

두번째 요소부터 선택해서 앞의 요소들이랑 비교해야하니까 for문을 1부터 시작한다.

그리고 cur변수에 현재 데이터를 저장하고 left변수에 i - 1을 넣어준다.

while문을 돌려서 left가 0보다 크거나 같고, arr[left]가 cur보다 큰 경우

두 데이터를 교환하고 cur변수에 arr[left]를 담고 left는 -1을 해준다.

즉, 1번째 데이터를 뽑아서 바로 앞의 0번째 데이터와 비교한 후, 0번째 데이터가 더 크면 두 값을 교환한다.

그 다음 2번째 데이터를 뽑아서 1번째 데이터와 비교한다. 1번째 데이터가 더 크면 두 값을 다시 교환하고

이번엔 1번째 데이터와 0번째 데이터를 비교한다. 0번째 데이터가 1번째 데이터보다 크면 두 값을 교환한다.

만약 데이터가 원래 정렬되어 있었다면 while문 안의 코드는 실행되지 않으므로 O(n)의 시간복잡도가 성립된다.

이런식으로 흘러가는데 인터넷을 찾아보니 데이터 교환을 좀 더 간단하게 표현할 수 있다는 사실을 깨달았다.

```jsx
function insertionSort (arr) {
	for (let i = 1; i < arr.length; i++) {
		let cur = arr[i];
		let left = i - 1;
	
		while (left >= 0 && arr[left] > cur) {
			arr[left + 1] = arr[left];
			left--;
		}
		arr[left + 1] = cur;
		console.log(`${i}회전: ${arr}`);
	}
	return arr;
}

console.log(insertionSort([5, 4, 3, 2, 1]));
```

바로 이렇게 하면 된다. arr[left + 1]에 arr[left]값을 넣고 left값을 하나씩 줄여나가며 왼쪽과 비교하다가 교환이 다 끝나고 while문을 빠져나오면 맨 앞의 요소에 cur값을 넣는다.

## 📍****Selection Sort****

선택 정렬 알고리즘은 이렇게 진행된다.

1. 먼저 주어진 리스트 중에 최소값을 찾는다.
2. 그 값을 맨 앞에 위치한 값과 교환한다.
3. 이제 맨 앞을 젱외하고 다시 순회하며 최소값을 찾는다.
4. 그 값을 맨 앞위치 바로 다음 위치와 교체 한다… 반복

버블 정렬이 각 회전이 끝날 때마다 맨 마지막 데이터의 위치가 정해졌던 것과 반대로

선택 정렬은 n번째 회전이 끝날 때마다 앞에서 n번째 데이터의 위치가 정해진다.

다음의 그림을 보면 이해가 쉽다.

![https://blog.kakaocdn.net/dn/cSAFhj/btqwXd06SNh/kaKR9rl4IGEwH9JFRQSsw0/img.gif](https://blog.kakaocdn.net/dn/cSAFhj/btqwXd06SNh/kaKR9rl4IGEwH9JFRQSsw0/img.gif)

### Big O

- Worst Case: O(n^2) → 정렬이 하나도 안되어있는 경우
- Best Case: O(n^2) → 이미 정렬이 되어있는 경우

선택 정렬은 위의 두 정렬과는 다르게 정렬이 이미 되어있는 경우에도 O(n^2)의 시간 복잡도를 가진다.

그 이유는 매번 정해진 자리에 올 수 있는 최소값을 찾아야하기 때문이다. 그렇기 때문에 성능이 매우 떨어진다.

### 장점

선택 정렬도 위의 두 정렬과 같이 in place 알고리즘 이기 떄문에 메모리가 정렬된다는 장점이 있으며 알고리즘이 직관적이므로 이해하기도 쉽고 구현하기도 쉽다.

### 단점

선택 정렬은 최선의 경우에도, 최악의 경우에도 O(n^2)의 시간이 걸리는 만큼 성능이 매우 떨어짐

### Unstable

선택 정렬은 데이터가 중복된 경우 위치가 바뀔 수 있기 때문에 Unstable한 정렬이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4ab19bfb-5931-4aca-ad31-3f5eb9ab6903/Untitled.png)

위의 그림을 살펴보면 첫 번째 회전 시에 5가있는 0번째 칸 위 리스트의 최소값인 1이 들어가야 하므로 

1과 5를 교환해야 하고 그러면 중복데이터인 5의 순서가 변하는 것을 알 수 있다.

```jsx
function selectionSort (arr) {
	for (let i = 0; i < arr.length; i++) {
	// 첫번째 자리를 기준으로 정렬해야 하므로 0부터 시작
		let minIndex = i; // 가장 작은 값이라고 가정
		for (let j = i + 1; j < arr.length; j++) { 
			// 다음 자리부터 순회하며 arr[i]보다 작은 값이 있는지 검사
			if (arr[minIndex] > arr[j]) minIndex = j;
		}
		if (minIndex !== i) { 
		// 검사가 끝나고 minIndex값이 i와 같지 않다면 그 값과 i번째 값을 교환
			let swap = arr[minIndex];
			arr[minIndex] = arr[i];
			arr[i] = swap;
		}
		console.log(`${i}회전: ${arr}`);
  }
  return arr;
}
console.log(selectionSort([5, 4, 3, 2, 1]));
```

1회전만에 정렬이 되었으나 선택 정렬은 데이터가 정렬된 상태에서도 계속 순회하며

최소값을 찾는 과정을 하기 때문에 매우 비효율적인 정렬인 것을 확인할 수 있다.