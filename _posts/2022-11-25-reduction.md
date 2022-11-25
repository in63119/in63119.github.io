---
title: 솔리디티로 반감기 구현하기
author: IN
date: 2022-11-25 16:32:00 +0800
categories: [Blogging, Blockchain]
tags: [reduction, solidity]
pin: true
---

블록체인에서 반감기는 중요한 개념이다. 이번에 작업을 하면서 반감기 구현하면서 좋은 내용이라 기록한다. 
<br />
먼저, 반감기가 무엇인지 알아보자.

<br />
<br />

## 반감기((Reduction or Halving or Half-Life))
암호화폐 시장에서 [반감기](http://wiki.hash.kr/index.php/%EB%B0%98%EA%B0%90%EA%B8%B0)란 **채굴량이 절반으로 줄어드는 시기**이다. 
<br />
프로젝트팀에서 암호화폐(or 토큰)를 출시할 경우 초반에는 많은 사람들에게 유통되어 사용되야하기 때문에 많은 양이 생성이 된다.
<br />
그러다 어느 순간이 되면, 해당 암호화폐(or 토큰)에 대한 희소성(가치)를 끌어올리기 위해 생성(채굴)되는 양을 반씩 줄이다가
<br />
애초에 목표했던 총 양에 도달하면 생성(채굴)이 더이상 안되게끔 설정하는 것이다. 
<br />

![비트코인_반감기의_이해](https://user-images.githubusercontent.com/65399118/203982042-ad5cb23f-30e9-4a07-b8a2-4a5dd735aa92.png)


