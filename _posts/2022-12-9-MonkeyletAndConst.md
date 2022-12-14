---
title: let과 const 그리고 할당
author: IN
date: 2022-12-9 13:56:00 +0800
categories: [Blogging, Javascript]
tags: [wallet, Monkey]
pin: true
---

기존 프로젝트를 리팩토링 하던 중 아주 초보적인 소중한 깨달음을 얻어 블로깅을 한다.
<br />
앞으로 초보적인 실수는 [Monkey]라는 태그로 잊지 않도록 모아놓을 것이다.
<br />
<br />

## 배경
개인적으로 만들고 있는 월렛 프로젝트의 체인은 마지막으로 추가한 Polygon을 합해서 **총 4개**이다.
<br />

<img width="569" alt="총 네트워크" src="https://user-images.githubusercontent.com/65399118/206627412-e22c781a-6ca2-4aed-9e03-dd7bad252bfd.png">

<br />

이 중 `Ethereum`, `Binance`, `Polygon`은 노드와 상호작용 해주는 **Web3-js 라이브러리**를 공통적으로 사용하다보니 하나의 js파일에서 관리해주고 싶었다.

<br />

### 코드
코드 플로우는 `config.js`라는 파일에서 Rpc URL을 관리해주고, `import`해서 `switching`하는 형식이었다.
<br />

<img width="462" alt="스크린샷 2022-12-09 오후 1 30 48" src="https://user-images.githubusercontent.com/65399118/206628689-6fd800c1-b4d9-406e-9496-0623ff6d6b1c.png">

<br />
<br />

## 이슈
코드를 수정하고, 로컬 클라이언트를 돌려보니 다음과 같은 에러가 났다.
<br />

<img width="938" alt="스크린샷 2022-12-09 오후 1 30 33" src="https://user-images.githubusercontent.com/65399118/206628998-ef98dc54-112e-4090-ba8a-bf9389b52ba1.png">

<br />

### 해결과정

`Provider not set or invalid` 이 에러는 위 코드의 `rpcURL` 값이 제대로 들어오지 않아 생기는 문제였다. 

<br />

다시한번 코드를 살펴보아도, `rpcURL`에 `let` 키워드로 선언을 해주어서 스윗칭이 될 것이라고 생각했기 때문에

<br />

근본적인 문제를 찾지 못하고 브라우저 단에서 `console.log`와 `debug`만 찍어대는 원숭이 같은 짓만 했다(ㅎㅎ)

<br />
그러던 중 문제를 해결을 위해 생각의 흐름을 정리 해보았다.
<br />

- config에서 데이터는 잘 들어오나?
  - 잘 들어옴.
- browser 단에서는 rpcURL이 잘 들어오나?
  - 들어오지 않음.
- 그렇다면, Provider를 부르는 함수에서는 rpcURL 데이터가 무엇으로 나오나?
  - `undefined`로 들어옴.

<br />

여기까지 생각이 미치자 처음 `rpcURL`을 선언해주었던 상단 코드가 눈에 들어오기 시작한다. 

<br />

```js
// web3.js
const Web3 = require("web3");
let rpcURL;
const web3 = new Web3(rpcURL);

// web3-js를 사용하는 체인만 스위칭
export function whichChain(chain) {
  switch (chain) {
    case "BNB":
      rpcURL = config.binance.rpcURL;
      break;
    case "ETH":
      rpcURL = config.ethereum.rpcURL;
      break;
    case "MATIC":
      rpcURL = config.polygon.rpcURL;
      break;
  }
}

```

<br />

코드를 보면 `web3`를 `const`로 선언만 해주고, 아무런 액션을 취하고 있지 않기 때문에 

<br />

아무리 `rpcURL`을 재할당 시켜줘도 `web3`의 값은 초기 그대로 `let rpcURL; => undefined`가 될 뿐이었다.

<br />
이제 이 코드를 재할당이 가능하도록 만들어보자.
<br />

<img width="460" alt="스크린샷 2022-12-09 오후 1 44 13" src="https://user-images.githubusercontent.com/65399118/206633160-55347857-67aa-4ef7-b1f7-c1b67ec34a12.png">

<br />

`const`로 선언된 `web3`는 `let`으로 변경해주고, `whichChain` 함수에서 재할당을 시켜주었다.

<br />
<br />

## 결과
다행히 실행이 안되었던 기능이 잘 돌아간다.

<br />

<img width="324" alt="스크린샷 2022-12-09 오후 2 49 32" src="https://user-images.githubusercontent.com/65399118/206633491-e2673c50-578f-451e-bfdd-298979452d21.png">

<br />

### 느낀점
개발의 재미는 **문제를 해결해가는 과정 속에서 나오는 카타르시스**라고 생각한다.
<br />
비록 다른 분들이 보면 혀를 끌끌 찰 정도의 초보적인 수준이지만
<br />
좀 더 논리적인 사고를 통해 문제를 해결해나가는 개인의 성장에 감사했다.

