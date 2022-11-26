---
title: address(0) 쓰임새
author: IN
date: 2022-11-26 15:32:00 +0800
categories: [Blogging, Blockchain]
tags: [Solidity, Address]
pin: true
---

솔리디티에서 modifier를 사용하여 컨트랙트의 특정 기능이 내가 원하는 주소에만 사용가능하도록 해줄 수 있다. 
<br />
보통 해당 컨트랙트의 오너만 기능을 사용 가능하게끔 만들어 주는데, 다음과 같이 만들어준다.
<br />

```go
// modifier에 require를 넣어서 어떠한 값과 비교하는데,
// 이 때 들어가는 mainOwner 같은 값은 해당 컨트랙트를 세팅하는 함수를 만들어서 해당 변수에 msg.sender를 넣어주면 된다.
modifier onlyOwner() {
    require(msg.sender != mainOwner, "Error : Not Owner!");
    _;
}
```


<br />
또, 들어오는 주소가 실제로 존재하는 주소값이 맞는지 확인하기위해 다음과 같은 방법으로 구성하기도 한다.
<br />

```go
modifier checkZero() {
  require(msg.sender != address(0), "Error : _contributor address is Zero.");
  _;
}
```

<br />
나는 주로 `address(0)`을 걸러내는 용으로 쓰는데, 다른 개발자들은 어떻게 사용하는지 문득 궁금해졌다. 
<br />
<br />

## 검색으로 알아보는 address(0)
> [참조](https://stackoverflow.com/questions/48219716/what-is-address0-in-solidity)


구글에 검색하면 제일 먼저 나오는 **스택오버플로우**페이지를 살펴보자.
<br />

<img width="569" alt="스크린샷 2022-11-26 오후 3 55 28" src="https://user-images.githubusercontent.com/65399118/204076430-dd0573c0-0b93-402e-ab67-f141185db169.png">

<br />
이 질문자도 address(0)에 대해 찾아보았는데, 해당 문서는 트랜잭션을 보낼 때 to 값에 `0`값을 넣은 경우를 말하는 것 같다.
<br />
답변을 요약하자면, **"트랜잭션은 송금의 기능 뿐만 아니라 함수를 실행하는 기능도 있는데, to값에 0이 아니면 이더를 보내고 0이면 value에 인코딩된 함수를 실행시킨다."** 라고 한다.
<br />
좋은 정보이긴 하지만 이것 말고 다른 부분이 있는지 찾아보고 싶었다.

<br />

## 커뮤니티에 알아보는 address(0)
<img width="326" alt="스크린샷 2022-11-26 오후 4 06 47" src="https://user-images.githubusercontent.com/65399118/204076884-2409121b-ce08-475e-90e6-9f41a1bd0c70.png">

<br />

솔리디티 개발자 톡방에서 물어본 결과, **"address(0)은 토큰을 버닝(burning)할 때 주로 사용한다"** 고 한다.

<br />
예를 들어, 필요없는 수량의 토큰을 없애고 싶을 때 address(0)에 보낸다는 것이다. 
<br />
이것이 가능한 이유는 address(0)이 토큰을 받기 때문이고, 그래서 '블랙홀 주소'라고 지칭한 것 같다.
<br />
<br />

요약하자면, address(0)은 

- **0x000000...** 의 주소를 가진다.
- 트랜잭션을 보낼 때, **to** 값에 address(0)을 넣으면 value에 인코딩된 함수를 실행한다.
- 토큰을 소각(burn)할 때 address(0)을 사용하기도 한다.
- 사용자가 주소 입력을 잘못할 경우도 있기 때문에 require 등으로 처리를 해주는 편이다.

<br />

## 번외 - 그러면, Address(0)은 누구의 소유인가?!
개발자 톡방에 질문을 한 뒤 누군가 또 다른 의문을 던졌다.
<br />

<img width="321" alt="스크린샷 2022-11-26 오후 4 19 50" src="https://user-images.githubusercontent.com/65399118/204077344-ed5c9f78-9cdc-44d7-a97c-ccdcb88a2713.png">

<br />
의문인 즉, address(0)이 개인키를 가지고 있는 EOA라면 완전 누워서 토큰을 쓸어먹는거 아니냐는 것이다.
<br />
생각해보니 재미있는 생각인 것 같아서 그런지 다른 개발자들도 검색에 나섰다.
<br />
**개발자 들이란..... ㅎㅎ**
<br />
결국, 어떤 분이 찾으셨는데 이미 외국에서도 같은 의문을 품었던 사람이 있었던 모양이다.
<br />

> [참조](https://ethereum.stackexchange.com/questions/37221/if-someone-found-a-private-key-to-0x0-would-they-be-able-to-access-all-the-toke)

요약하자면, **이론상으론 가능하지만 이더리움 블록체인에서는 address(0)값을 null로 인식한다.** 고 한다.
<br />
<br />

즐거웠다..




