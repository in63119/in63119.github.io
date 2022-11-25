---
title: 솔리디티로 반감기 구현하기
author: IN
date: 2022-11-25 16:32:00 +0800
categories: [Blogging, Blockchain]
tags: [reduction, solidity]
pin: true
---

블록체인에서 반감기는 중요한 개념이다. 이번에 작업 중 반감기 구현하면서 좋은 내용이라 기록한다. 
<br />
먼저, 반감기가 무엇인지 알아보자.

<br />
<br />

## 반감기((Reduction or Halving or Half-Life))
암호화폐 시장에서 [반감기](http://wiki.hash.kr/index.php/%EB%B0%98%EA%B0%90%EA%B8%B0)란 **채굴량이 절반으로 줄어드는 시기**이다. 
<br />
프로젝트팀에서 암호화폐(or 토큰)를 출시할 경우 초반에는 많은 사람들에게 유통되어 사용되어야 하기 때문에 많은 양이 생성이 된다.
<br />
그러다 어느 순간이 되면, 해당 암호화폐(or 토큰)에 대한 희소성(가치)를 끌어올리기 위해 생성(채굴)되는 양을 반씩 줄이다가
<br />
애초에 목표했던 총 양에 도달하면 생성(채굴)이 더이상 안되게끔 설정하는 것이다. 
<br />

![비트코인_반감기의_이해](https://user-images.githubusercontent.com/65399118/203982042-ad5cb23f-30e9-4a07-b8a2-4a5dd735aa92.png)

<center>
비트코인에서도 약 2100만개까지 반감기가 적용되어 채굴이 가능하다. 
</center>

<br />
<br />

## 반감기 공식
사실 반감기에 대한 공식은 다른 곳에서 가져왔다. 아래 이미지에서 블록에 대한 부분이 바로 반감기 공식이다. 
<br /> 
해당 식은 **일정 기간에 따른 반감기를 적용한 회차별 수량**을 나타낸 식이다.
<br />

<img width="543" alt="스크린샷 2022-11-25 오후 9 17 16" src="https://user-images.githubusercontent.com/65399118/203984575-109c8ad4-92d8-4dd1-bfd7-e22a52b5578d.png">

<br />

이 부분을 우리가 파악하기 쉽게 변경하면 다음과 같다.

```
amount * (1-reductionRate))/(1-reductionRate^(duration/reductionTerm)))/reductionTerm*slicePeriodSeconds
```

<br />

## Solidity로 적용
이제 이 식을 솔리디티로 만들어보자.
<br />

```go
if (_reductionDiv) {
    uint256 indexNumber = 2**(totalDuration.div(YEAR));
    uint256 subForDownUint = DECIMAL1e18.div(indexNumber);

    downUint = DECIMAL1e18.sub(subForDownUint);

    upUint = totalAmount.mul(DECIMAL1e18).div(2);
    initialAmount = upUint.mul(MONTH).div(downUint).div(YEAR);
} else {
    upUint = totalAmount;
    initialAmount = upUint / _duration;
}
```
<br />
초기 설정에 반감기가 적용 되었으면(if 문)  
<br />
분자(upUint)와 분모(downUint)를 만들어서 
<br />
분자에는 총 량에 소수점을 위한 Decimal 값을 곱한 후 2(반감)를 나누고,
<br />
분모에는 기간(duration)에 따른 값을 구한다. 이 역시 소수점을 위한 처리를 해준다.
<br />
그리고 해당 프로젝트에서는 월(Month)에 한번씩 생성(채굴)이 되기 때문에 분자에 한 달을 곱한다.

<br />

> 반감기 설정을 안하면 그냥 `총 양 / 기간`만 하면 된다.

