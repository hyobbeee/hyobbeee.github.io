---
layout: post
title:  "μ΄μ§νμ(Binary Search)"
date:   2023-02-26
author: hyobbeee
categories: algorithm
tags: TIL algorithm
cover:  "/assets/instacode.png"
---

## πκ°λ

μ΄μ§ νμμ΄λ μ λ ¬λ λ¦¬μ€νΈμμ κ²μ λ²μλ₯Ό μ€μ¬λκ°λ©΄μ κ²μ κ°μ μ°Ύλ μκ³ λ¦¬μ¦μ΄λ€.

μ΄μ§ νμμ μ λ ¬λ **λ¦¬μ€νΈμμλ§ μ¬μ©**ν  μ μλ€λ λ¨μ μ΄ μμ§λ§, κ²μμ΄ λ°λ³΅λ  λλ§λ€ κ²μ λ²μκ° μ λ°μΌλ‘ μ€κΈ° λλ¬Έμ **μλκ° λΉ λ₯΄λ€**λ μ₯μ μ΄ μλ€.

## πλμλ°©μ

μ΄μ§ νμ μκ³ λ¦¬μ¦μ λ¦¬μ€νΈμ μ€κ° κ°κ³Ό λΉκ΅νμ¬ κ²μκ°μ μ°Ύλλ€.

μ€κ° κ°μ μ°ΎμμΌ νκΈ° λλ¬Έμ λ°λμ μ λ ¬λ λ°°μ΄μμλ§ μ¬μ©κ°λ₯νλ€.

1. λ°°μ΄μ μ€κ° κ°μ κ°μ Έμ¨λ€.
2. μ€κ° κ°κ³Ό κ²μ κ°μ λΉκ΅νλ€.
    1. μ€κ° κ°μ΄ κ²μ κ°κ³Ό κ°μΌλ©΄ μ’λ£ `mid = key`
    2. μ€κ° κ°λ³΄λ€ κ²μ κ°μ΄ ν¬λ€λ©΄ μ€κ°κ° κΈ°μ€ λ°°μ΄μ μ€λ₯Έμͺ½ κ΅¬κ°μ λμμΌλ‘ νμ `mid < key`
    3. μ€κ° κ°λ³΄λ€ κ²μ κ°μ΄ μλ€λ©΄ μ€κ° κ° κΈ°μ€ λ°°μ΄μ μΌμͺ½ κ΅¬κ°μ λμμΌλ‘ νμνλ€. `mid > key`
3. κ°μ μ°Ύκ±°λ κ°κ²©μ΄ λΉμ΄μμ λκΉμ§ λ°λ³΅

## πκ²μ μμ

μ΄μ§ νμμ μ¬μ©νμ¬ key = 32κ°μ μ°Ύλ κ³Όμ μ λ³΄λλ‘ νκ² μ΅λλ€.

1. λ¨Όμ  λ°°μ΄μ κ°μ΄λ°λ₯Ό κ²°μ ν©λλ€.

> midΒ  = low + (high - low) / 2
> 

![https://blog.kakaocdn.net/dn/cqSVub/btq5lyj0hdx/uueqouAwXkPUcQGJrFgEo0/img.png](https://blog.kakaocdn.net/dn/cqSVub/btq5lyj0hdx/uueqouAwXkPUcQGJrFgEo0/img.png)

2. μ€μ κ°κ³Ό κ²μ κ°μ λΉκ΅ν©λλ€.

A [4] < key μ΄λ―λ‘ λ°°μ΄μ μ€λ₯Έμͺ½ κ΅¬κ°μ κ²μ λ²μλ‘ μ ν©λλ€.

> low = mid + 1
> 

= 0 + (9 - 0) / 2

= 4

![https://blog.kakaocdn.net/dn/3SLNJ/btq5ffT6iar/WLKlWZO792tJTWVVNbXP5K/img.png](https://blog.kakaocdn.net/dn/3SLNJ/btq5ffT6iar/WLKlWZO792tJTWVVNbXP5K/img.png)

3. μ€μ κ°μ κ²°μ ν©λλ€.

> mid = 5+ (9-5)/2
> 

= 7

![https://blog.kakaocdn.net/dn/cTdRoI/btq5fDmvXe8/5zi2DU9psPgWYAVaLcZvGK/img.png](https://blog.kakaocdn.net/dn/cTdRoI/btq5fDmvXe8/5zi2DU9psPgWYAVaLcZvGK/img.png)

4. μ€μ κ°κ³Ό κ²μ κ°μ λΉκ΅ν©λλ€.

A [7] > key μ΄λ―λ‘ λ°°μ΄μ μΌμͺ½ κ΅¬κ°μ νμ λ²μλ‘ μ ν©λλ€.

> high = mid -1
> 

= 7 - 1

= 6

![https://blog.kakaocdn.net/dn/KjVE5/btq5gvPmCiP/uTyoNRu1MuoKkgXpGb6ckK/img.png](https://blog.kakaocdn.net/dn/KjVE5/btq5gvPmCiP/uTyoNRu1MuoKkgXpGb6ckK/img.png)

5. μ€μ κ°μ κ²°μ ν©λλ€.

> mid = 5 + (6-5)/2
> 

=  5

![https://blog.kakaocdn.net/dn/0vmuK/btq5gvBOldB/xeNJojg5kJyJpFNi3jL3t1/img.png](https://blog.kakaocdn.net/dn/0vmuK/btq5gvBOldB/xeNJojg5kJyJpFNi3jL3t1/img.png)

6. μ€μ κ°κ³Ό κ²μ κ°μ λΉκ΅ν©λλ€.

A [5] = key μ΄λ―λ‘ νμμ μ’λ£ν©λλ€.

## πμκ°λ³΅μ‘λ

| Operation | Best | Average | Worst |
| --- | --- | --- | --- |
| Search | O(1) | Ξ(log n) | O(log n) |

## πμ’λ£μ‘°κ±΄

μ΄μ§ νμμ μ’λ£ μ‘°κ±΄μ λ κ°μ§κ° μλ€.

λ€μ μ‘°κ±΄μ€ νλλΌλ μ±λ¦½νλ©΄ κ²μμ μ’λ£νλ€.

1. κ²μμ μ±κ³΅ν  κ²½μ°

- λ¦¬μ€νΈμμ κ²μν  κ°κ³Ό κ°μ μμλ₯Ό λ°κ²¬ν κ²½μ°
- a [mid] == key

![κ²μ μ±κ³΅](https://blog.kakaocdn.net/dn/EZfk8/btq5ffsWrSC/ISKOrApAubQrddI0WxYCuk/img.png)

κ²μ μ±κ³΅

2. κ²μμ μ€ν¨ν κ²½μ°

- λ μ΄μ κ²μν  λ²μκ° μμ κ²½μ°
- low > high

![κ²μ μ€ν¨](https://blog.kakaocdn.net/dn/u6MTp/btq5nbhLRfO/U66KTWbCgzh6Cq3780ZgN0/img.png)

κ²μ μ€ν¨

## πκ΅¬ν

> μμΆλ ₯ μμ
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

1. μ΄μ§ νμμ νμ©ν κ΅¬ν

```jsx
function isSubsetOf(base, sample) {
  // base μ€λ¦μ°¨μμΌλ‘ μ λ ¬
  base.sort((a, b) => a - b);
  
  // sample λ°°μ΄μ λͺ¨λ  μμκ° base λ°°μ΄μ ν¬ν¨λλμ§
  for (let i = 0; i < sample.length; i++) {
    // μ΄μ§ κ²μμ μ¬μ©νμ¬ ν΄λΉ μμκ° base λ°°μ΄μ μ‘΄μ¬νλμ§
    let left = 0;
    let right = base.length - 1;
    let found = false;
    while (left <= right) { // μ΄μ§ κ²μ νμ©
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
      // sample λ°°μ΄μ μμκ° base λ°°μ΄μ ν¬ν¨x
      return false;
    }
  }
  // sample λ°°μ΄μ λͺ¨λ  μμκ° base λ°°μ΄μ ν¬ν¨o
  return true;
}
```

1. μκ° λ³΅μ‘λλ₯Ό κ³ λ €νμ§ μμ κ²½μ° includes λ©μλλ₯Ό μ΄μ©ν΄ μ’ λ κ°κ²°νκ² μμ±ν  μ μλ€.

```jsx
function isSubsetOf(base, sample) {
  // sample λ°°μ΄μ λͺ¨λ  μμκ° base λ°°μ΄μ ν¬ν¨λλμ§
  for (let i = 0; i < sample.length; i++) {
    if (!base.includes(sample[i])) {
      // sample λ°°μ΄μ μμκ° base λ°°μ΄μ ν¬ν¨λμ§ μμΌλ©΄
      return false;
    }
  }
  // sample λ°°μ΄μ λͺ¨λ  μμκ° base λ°°μ΄μ ν¬ν¨λλ©΄
  return true;
}
```