---
title: Algorithm - 가장 가까운 같은 글자
author: IN
date: 2023-1-22 1:01:00 +0800
categories: [Blogging, Algorithm]
tags: [Javascript, Algorithm]
pin: true
---

## 문제

문자열 s가 주어졌을 때, s의 각 위치마다 자신보다 앞에 나왔으면서, 자신과 가장 가까운 곳에 있는 같은 글자가 어디 있는지 알고 싶습니다.
<br />
예를 들어, s="banana"라고 할 때, 각 글자들을 왼쪽부터 오른쪽으로 읽어 나가면서 다음과 같이 진행할 수 있습니다.
<br />

- b는 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- a는 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- n은 처음 나왔기 때문에 자신의 앞에 같은 글자가 없습니다. 이는 -1로 표현합니다.
- a는 자신보다 두 칸 앞에 a가 있습니다. 이는 2로 표현합니다.
- n도 자신보다 두 칸 앞에 n이 있습니다. 이는 2로 표현합니다.
- a는 자신보다 두 칸, 네 칸 앞에 a가 있습니다. 이 중 가까운 것은 두 칸 앞이고, 이는 2로 표현합니다.

<br />
따라서 최종 결과물은 [-1, -1, -1, 2, 2, 2]가 됩니다.
<br />
<br />
문자열 s이 주어질 때, 위와 같이 정의된 연산을 수행하는 함수 solution을 완성해주세요.
<br />
<br />

## 입출력 예

| s        | result                  |
| -------- | ----------------------- |
| "banana" | [-1, -1, -1, 2, 2, 2]   |
| "foobar" | [-1, -1, 1, -1, -1, -1] |
| "10203"  | "15"                    |

<br />
<br />

## 내 코드

```js
function solution(s) {
  // 문자열에서 각 인덱스마다 자신과 같은 글자가 있는 거리를 배열에 담아서 리턴
  // 없으면 -1
  var answer = [];

  for (let i = 0; i < s.length; i++) {
    let calcArr = [];

    for (let k = 0; k <= i; k++) {
      // 현재 글자
      let curS = s[i];
      // 찾는 글자
      let findS = s[k];

      if (curS === findS) {
        // 찾는 글자의 인덱스를 푸쉬
        calcArr.push(k);
      }
    }

    // 찾은 횟수가 1이면, 자기 자신 밖에 없으므로 -1 푸쉬
    if (calcArr.length === 1) {
      answer.push(-1);

      // 아니라면, 마지막 인덱스에서 그 전 인덱스를 뺀 값을 푸쉬
    } else {
      answer.push(calcArr[calcArr.length - 1] - calcArr[calcArr.length - 2]);
    }
  }
  return answer;
}
```

<br />
<br />

## 결과

<img width="400" alt="스크린샷 2023-01-20 오후 9 05 23" src="https://user-images.githubusercontent.com/65399118/213871910-88316723-5436-4f2b-a0d6-ba1871447208.png">

<br />
<br />

## Develop

다른 사람의 풀이

```js
function solution(s) {
  const hash = {};

  return [...s].map((v, i) => {
    let result = hash[v] !== undefined ? i - hash[v] : -1;
    hash[v] = i;
    return result;
  });
}
```
