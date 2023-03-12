---
title: Algorithm - 분수 덧셈
author: IN
date: 2023-3-12 22:56:00 +0800
categories: [Blogging, Algorithm]
tags: [Javascript, Algorithm, Recursion]
pin: true
---

## 문제

첫 번째 분수의 분자와 분모를 뜻하는 numer1, denom1, 두 번째 분수의 분자와 분모를 뜻하는 numer2, denom2가 매개변수로 주어집니다.

두 분수를 더한 값을 기약 분수로 나타냈을 때 분자와 분모를 순서대로 담은 배열을 return 하도록 solution 함수를 완성해보세요.

<br />

## 입출력 예

| numer1 | denom1 | numer2 | denom2 | result  |
| ------ | ------ | ------ | ------ | ------- |
| 1      | 2      | 3      | 4      | [5, 4]  |
| 9      | 2      | 1      | 3      | [29, 6] |

<br />

## 풀이

분수의 덧셈을 손으로 풀면 쉬운데, 코딩으로 푸는것이 어렵다고 느꼈던 문제이다.
<br />
분모의 계산부터 하여 곱해야 하는 값을 `cost`에 넣어 계산하였다.

<br />

문제는 `약분`이었다.

> **약분** :
> 수학에서 분수의 분자와 분모를 그의 공약수로 나눠서 간단하게 만드는 것

<br />

![스크린샷 2023-03-09 오후 9 34 57](https://user-images.githubusercontent.com/65399118/224024726-174e1c58-32ca-4ee9-bf41-126ee76bcad0.png)

<br />

아무리 코드 실행을 하면 문제가 없었는데... 채점하면 틀린 답으로 나와서 검색을 해보니 `공약수`로 나누는 `약분`을 해줘야 한다는 것을 찾았다.

## 내 코드

```js
function solution(numer1, denom1, numer2, denom2) {
  var answer = [];
  let cost = 0;
  let subCost = 0;

  if (denom1 % denom2 === 0) {
    cost = denom1 / denom2;
    denom2 = denom2 * cost;
    answer = [numer1 + numer2 * cost, denom2];
  } else if (denom2 % denom1 === 0) {
    cost = denom2 / denom1;
    denom1 = denom1 * cost;
    answer = [numer1 * cost + numer2, denom1];
  } else if (denom1 === denom2) {
    answer = [numer1 + numer2, denom2];
  } else {
    cost = denom1;
    subCost = denom2;
    numer1 = numer1 * subCost;
    numer2 = numer2 * cost;
    denom1 = denom1 * subCost;
    answer = [numer1 + numer2, denom1];
  }

  let small = 0;
  let count = 0;

  if (denom2 > denom1) {
    small = denom1;
  } else {
    small = denom2;
  }

  // 약분
  for (let i = 1; i <= small; i++) {
    if (answer[0] % i === 0 && answer[1] % i === 0) {
      count = i;
    }
  }

  if (count <= 1) {
    count = 1;
  }

  answer[0] = answer[0] / count;
  answer[1] = answer[1] / count;

  return answer;
}
```

<br />
<br />

## 결과

<img width="358" alt="스크린샷 2023-03-05 오후 3 17 01" src="https://user-images.githubusercontent.com/65399118/224025820-984873f6-260b-4702-8b49-642857efb21e.png">

<br />
<br />

## 다른 사람의 풀이

```js
function fnGCD(a, b) {
  return a % b ? fnGCD(b, a % b) : b;
}

function solution(denum1, num1, denum2, num2) {
  let denum = denum1 * num2 + denum2 * num1;
  let num = num1 * num2;
  let gcd = fnGCD(denum, num); //최대공약수

  return [denum / gcd, num / gcd];
}
```
