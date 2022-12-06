---
title: 잔액 소수점 반올림 구현
author: IN
date: 2022-12-5 19:26:00 +0800
categories: [Blogging, Blockchain]
tags: [wallet]
pin: true
---

예전 포스트에서 여러 유명한 월렛에서는 소수점을 어디까지 사용하나 알아보았다.
<br />

> [지난 포스트 : 각 월렛에서 소수점 잔액은 몇 자리까지 표시할까?](https://in63119.github.io/posts/%EA%B0%81-%EC%9B%94%EB%A0%9B%EC%97%90%EC%84%9C-%EC%86%8C%EC%88%98%EC%A0%90-%EC%9E%94%EC%95%A1%EC%9D%80-%EB%AA%87-%EC%9E%90%EB%A6%AC%EA%B9%8C%EC%A7%80-%ED%91%9C%EC%8B%9C%ED%95%A0%EA%B9%8C/)

<br />

이번에는 소수점 넷째자리 까지만 보여주도록 변경해보겠다.

<br />

```
예를 들어, 0.444445라는 잔액이 있다면
0.4444 까지만 보여주도록 할 것이다.
```

<br />
<br />

## 기존 잔액 표기
기존 월렛의 잔액 표시는 다음과 같이 긴 소수점을 보여주고 있었다.

<br />

<img width="569" alt="배포 잔액" src="https://user-images.githubusercontent.com/65399118/205914699-7245a963-dd4a-4fdf-bc1b-d2aeb83bccce.png">

<br />

### 코드
다음은 web3-js를 이용하여 해당 `address`의 잔액을 가져와서 `fromWei`로 단위를 변경 시켜주는 코드이다. 

<br />

```js
// 잔액 조회
export async function getBalance(address) {
  const balance = await web3.eth.getBalance(address).then((data) => {
    return data;
  });
  const convertBalance = await web3.utils.fromWei(`${balance}`, "ether");

  return convertBalance;
}
```

<br />

## 소수점 째자리까지 표기하기
이제 다음과 같이 잔액을 네째자리 까지만 보여주는 코드로 수정해보자.

<br />

<img width="569" alt="로컬 잔액" src="https://user-images.githubusercontent.com/65399118/205915355-6e128e57-02e2-4a84-bfc7-f8e3afe10a67.png">

<br />

### 코드

```js
// 잔액 조회
export async function getBalance(address) {
  const balance = await web3.eth.getBalance(address).then((data) => {
    return data;
  });
  const convertBalance = await web3.utils.fromWei(`${balance}`, "ether");
  
  let floorBalance;
  let decimal;
  let sliceIndex = 0;
  
  // convertBalance를 만자기 쉽게 배열로 만들어준다.
  const arrayBalance = convertBalance.split("");

  // 소수점(".")이 몇 번 인덱스인지 찾는다.
  for (let i = 0; i < arrayBalance.length; i++) {
    if (arrayBalance[i] === ".") {
      sliceIndex = i;
      break;
    }
  }

  // 0 이상의 양의 정수는 제외하고 소수점부터만 변수에 저장한다.
  decimal = convertBalance.slice(sliceIndex + 1);

  // 만약, 소수점 넷째자리보다 커지면 반올림시킨다.
  if (decimal.length > 4) {
    floorBalance = Number(convertBalance).toFixed(4);
    return floorBalance;
  }

  // 아니면, 기존 그대로 리턴
  return convertBalance;
}
```

<br />

### 다른 체인의 잔액도 확인해보자.

- 소수점 첫째자리로 떨어질 때


<img width="569" alt="바이낸스 잔액" src="https://user-images.githubusercontent.com/65399118/205933919-27a76bdc-364c-409f-ad79-4163d0badf7b.png">

<br />

- 0 이상의 정수 포함한 소수 넷째자리 표기

<img width="569" alt="클레이튼 잔액" src="https://user-images.githubusercontent.com/65399118/205934813-d59bcbf9-b99d-47ba-b8cf-7b3e90fdb88c.png">
