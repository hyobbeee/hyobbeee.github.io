---
layout: post
title:  "이진탐색(Binary Search)"
date:   2023-02-26
author: hyobbeee
categories: algorithm
tags: TIL algorithm
cover:  "/assets/instacode.png"
---

## 📍개념

이진 탐색이란 정렬된 리스트에서 검색 범위를 줄여나가면서 검색 값을 찾는 알고리즘이다.

이진 탐색은 정렬된 **리스트에서만 사용**할 수 있다는 단점이 있지만, 검색이 반복될 때마다 검색 범위가 절반으로 줄기 때문에 **속도가 빠르다**는 장점이 있다.

## 📍동작방식

이진 탐색 알고리즘은 리스트의 중간 값과 비교하여 검색값을 찾는다.

중간 값을 찾아야 하기 때문에 반드시 정렬된 배열에서만 사용가능하다.

1. 배열의 중간 값을 가져온다.
2. 중간 값과 검색 값을 비교한다.
    1. 중간 값이 검색 값과 같으면 종료 `mid = key`
    2. 중간 값보다 검색 값이 크다면 중간값 기준 배열의 오른쪽 구간을 대상으로 탐색 `mid < key`
    3. 중간 값보다 검색 값이 작다면 중간 값 기준 배열의 왼쪽 구간을 대상으로 탐색한다. `mid > key`
3. 값을 찾거나 간격이 비어있을 때까지 반복

## 📍검색 예시

이진 탐색을 사용하여 key = 32값을 찾는 과정을 보도록 하겠습니다.

1. 먼저 배열의 가운데를 결정합니다.

> mid  = low + (high - low) / 2
> 

![https://blog.kakaocdn.net/dn/cqSVub/btq5lyj0hdx/uueqouAwXkPUcQGJrFgEo0/img.png](https://blog.kakaocdn.net/dn/cqSVub/btq5lyj0hdx/uueqouAwXkPUcQGJrFgEo0/img.png)

2. 중앙 값과 검색 값을 비교합니다.

A [4] < key 이므로 배열의 오른쪽 구간을 검색 범위로 정합니다.

> low = mid + 1
> 

= 0 + (9 - 0) / 2

= 4

![https://blog.kakaocdn.net/dn/3SLNJ/btq5ffT6iar/WLKlWZO792tJTWVVNbXP5K/img.png](https://blog.kakaocdn.net/dn/3SLNJ/btq5ffT6iar/WLKlWZO792tJTWVVNbXP5K/img.png)

3. 중앙 값을 결정합니다.

> mid = 5+ (9-5)/2
> 

= 7

![https://blog.kakaocdn.net/dn/cTdRoI/btq5fDmvXe8/5zi2DU9psPgWYAVaLcZvGK/img.png](https://blog.kakaocdn.net/dn/cTdRoI/btq5fDmvXe8/5zi2DU9psPgWYAVaLcZvGK/img.png)

4. 중앙 값과 검색 값을 비교합니다.

A [7] > key 이므로 배열의 왼쪽 구간을 탐색 범위로 정합니다.

> high = mid -1
> 

= 7 - 1

= 6

![https://blog.kakaocdn.net/dn/KjVE5/btq5gvPmCiP/uTyoNRu1MuoKkgXpGb6ckK/img.png](https://blog.kakaocdn.net/dn/KjVE5/btq5gvPmCiP/uTyoNRu1MuoKkgXpGb6ckK/img.png)

5. 중앙 값을 결정합니다.

> mid = 5 + (6-5)/2
> 

=  5

![https://blog.kakaocdn.net/dn/0vmuK/btq5gvBOldB/xeNJojg5kJyJpFNi3jL3t1/img.png](https://blog.kakaocdn.net/dn/0vmuK/btq5gvBOldB/xeNJojg5kJyJpFNi3jL3t1/img.png)

6. 중앙 값과 검색 값을 비교합니다.

A [5] = key 이므로 탐색을 종료합니다.

## 📍시간복잡도

| Operation | Best | Average | Worst |
| --- | --- | --- | --- |
| Search | O(1) | Θ(log n) | O(log n) |

## 📍종료조건

이진 탐색의 종료 조건은 두 가지가 있다.

다음 조건중 하나라도 성립하면 검색을 종료한다.

1. 검색을 성공할 경우

- 리스트에서 검색할 값과 같은 요소를 발견한 경우
- a [mid] == key

![검색 성공](https://blog.kakaocdn.net/dn/EZfk8/btq5ffsWrSC/ISKOrApAubQrddI0WxYCuk/img.png)

검색 성공

2. 검색을 실패한 경우

- 더 이상 검색할 범위가 없을 경우
- low > high

![검색 실패](https://blog.kakaocdn.net/dn/u6MTp/btq5nbhLRfO/U66KTWbCgzh6Cq3780ZgN0/img.png)

검색 실패

## 📍구현

> 입출력 예시
> 

```jsx
let base = [1, 2, 3, 4, 5];
let sample = [1, 3];
let output = isSubsetOf(base, sample);
console.log(output); // --> true

sample = [6, 7];
output = isSubsetOf(base, sample);
console.log(output); // --> false

base = [10, 99, 123, 7];
sample = [11, 100, 99, 123];
output = isSubsetOf(base, sample);
console.log(output); // --> false
```

1. 이진 탐색을 활용한 구현

```jsx
function isSubsetOf(base, sample) {
  // base 오름차순으로 정렬
  base.sort((a, b) => a - b);
  
  // sample 배열의 모든 요소가 base 배열에 포함되는지
  for (let i = 0; i < sample.length; i++) {
    // 이진 검색을 사용하여 해당 요소가 base 배열에 존재하는지
    let left = 0;
    let right = base.length - 1;
    let found = false;
    while (left <= right) { // 이진 검색 활용
      let mid = Math.floor((left + right) / 2);
      if (base[mid] === sample[i]) {
        found = true;
        break;
      } else if (base[mid] < sample[i]) {
        left = mid + 1;
      } else {
        right = mid - 1;
      }
    }
    if (!found) {
      // sample 배열의 요소가 base 배열에 포함x
      return false;
    }
  }
  // sample 배열의 모든 요소가 base 배열에 포함o
  return true;
}
```

1. 시간 복잡도를 고려하지 않은 경우 includes 메서드를 이용해 좀 더 간결하게 작성할 수 있다.

```jsx
function isSubsetOf(base, sample) {
  // sample 배열의 모든 요소가 base 배열에 포함되는지
  for (let i = 0; i < sample.length; i++) {
    if (!base.includes(sample[i])) {
      // sample 배열의 요소가 base 배열에 포함되지 않으면
      return false;
    }
  }
  // sample 배열의 모든 요소가 base 배열에 포함되면
  return true;
}
```