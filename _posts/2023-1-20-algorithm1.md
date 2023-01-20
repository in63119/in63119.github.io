---
title: Algorithm - 개인정보 수집 유효기간
author: IN
date: 2023-1-20 21:24:00 +0800
categories: [Blogging, Algorithm]
tags: [Javascript, Algorithm]
pin: true
---

## 배경

리액트 네이티브를 이용하여 개발할 때 ios simulator를 실행시켜서 디바이스에서의 뷰를 확인하곤 한다.

<br />

<img width="300" alt="simulator" src="https://user-images.githubusercontent.com/65399118/212580866-1d28d3f4-a6a0-4625-aec2-204bd7f310b4.png">

<br />
<br />

## 이슈

프로젝트 단에서 `react-native run-ios`를 통해서 simulator를 실행시키려면 `프로젝트의 설정`과 `Xcode의 디바이스의 버전`을 맞춰야 하는 등의 번거로움이 발생한다.

<br />

<img width="400" alt="스크린샷 2022-12-09 오후 1 30 33" src="https://user-images.githubusercontent.com/65399118/212581102-edf1f400-e374-4e07-955a-19c35070cf1e.png">

<br />

> Xcode로 지정된 디바이스는 `iPhone 13`이 아니라는 에러.

<br />
<br />

### 해결과정

> [참고 레퍼런스](https://medium.com/xcblog/simctl-control-ios-simulators-from-command-line-78b9006a20dc)

<br />

사용 가능한 시뮬레이터 디바이스 버전 리스트에서 `디바이스 id`를 가져와서 버전을 지정하여 런 시키는 방법으로 해결했다.

<br />

`xcrun simctl list`로 시뮬레이터 버전 리스트 가져온다.

<br />

<img width="400" alt="스크린샷 2022-12-09 오후 1 30" src="https://user-images.githubusercontent.com/65399118/212581606-8c55196f-6a31-41bb-8e1b-1dda4627d6cf.png">

<br />

`react-native run-ios --udid (디바이스id)`실행

> 나는 `(iPhone 14)006B56E9-1BE3-43F4-8345-5BCCCA1CF9AA`을 가져왔다.

<br />

<img width="700" alt="스크린샷 2022-12-09 오후 1 30" src="https://user-images.githubusercontent.com/65399118/212582444-014dba20-fc33-4f38-90ec-3696236d53a3.png">

<br />
<br />

## 결과

성공적으로 실행시킬 수 있게 되었다.

<br />

<img width="500" alt="스크린샷 2022-12-09 오후 1 30 33" src="https://user-images.githubusercontent.com/65399118/212582547-9cbf4473-f01d-4b9e-b0f3-d29f369dae66.png">

<br />

### 느낀점

처음엔 Xcode 설정만 만지작 했었는데, 간단히 해결이 되어 버렸다.
<br />
개발엔 답이 없다.
<br />
