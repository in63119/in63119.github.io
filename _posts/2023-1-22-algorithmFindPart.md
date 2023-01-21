---
title: Algorithm - 크기가 작은 부분 문자열
author: IN
date: 2023-1-22 1:01:00 +0800
categories: [Blogging, Algorithm]
tags: [Javascript, Algorithm]
pin: true
---

## 문제
숫자로 이루어진 문자열 t와 p가 주어질 때, t에서 p와 길이가 같은 부분문자열 중에서, 이 부분문자열이 나타내는 수가 p가 나타내는 수보다 작거나 같은 것이 나오는 횟수를 return하는 함수 solution을 완성하세요.
<br />
<br />
예를 들어, t="3141592"이고 p="271" 인 경우, t의 길이가 3인 부분 문자열은 314, 141, 415, 159, 592입니다. 이 문자열이 나타내는 수 중 271보다 작거나 같은 수는 141, 159 2개 입니다.
<br />
<br />
 

## 입출력 예

| t              | p     | result |
|----------------|-------|--------|
| "3141592"      | "271" | 2      |
| "500220839878" | "7"   | 8      |
| "10203"        | "15"  | 3      |

<br />
<br />

## 내 코드

```js
function solution(t, p) {
    // p길이 만큼 t의 부분 집합을 만들어내서
    // 각 부분집합이 p보다 작거나 같은 '횟수'를 리턴
        // (주의) 만약 '02'면 '2'와 같음
    
    let answer = 0;
    let arrT = t.split("");
    
    // 작거나 같은 수 인지 계산
    function calc(arr) {
        // 숫자로 변환
        let convertArrToNum = Number(arr.join(""));
        
        if (convertArrToNum <= p) {
            return true;
        }
    }
    
    for (let i = 0; i < t.length; i++) {
        // p 길이만큼 매번 잘라서
        let pLengthArrT = arrT.slice(i, i + p.length);
        
        // (길이가 적어질 수 있으니) 맞는 길이만 calc함수를 실행
        if (pLengthArrT.length === p.length) {
            if(calc(pLengthArrT)) {
                answer++;
            }
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
이번 문제는 사실 쉬운 편이기 때문에 어렵지 않게 구현할 수 있었지만 역시나 다른 사람의 풀이를 보면 이렇게 간결하게 짜는 것에 감탄을 하게 된다.
<br />


```js
function solution(t, p) {
    let count = 0;
    for(let i=0; i<=t.length-p.length; i++) {
        let value = t.slice(i, i+p.length);
        if(+p >= +value) count++;
    }
    return count;
}
```
