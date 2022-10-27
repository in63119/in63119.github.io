---
title: Cannot assign to read only property 'exports' of object 이슈
author: IN
date: 2022-10-27 14:46:00 +0800
categories: [Blogging, Javascript]
tags: [ECMAScript, CommonJS]
pin: true
---

**`npm build`를 하는 와중에 다음과 같은 에러가 떴다.**
<br />

```bash
TypeError: Cannot assign to read only property 'exports' of object '#<Object>'
```
<br />
어떠한 모듈 내보내기가 되지 않는다는 말인데, 분명 배포 전 테스트할 때에는 문제가 없었지만 
<br />

배포하기 위해 `npm build`를 하면 이런 에러가 뜬다.
<br />

결론부터 말하자면, 해당 에러는 **ECMAScript와 CommonJS의 `import`와 `export` 문법이 다른 기본적인 문제**였다.(민망...)
<br />
<br />

## ECMAScript vs CommonJS
먼저, ECMAScript와 CommonJS를 알아보자.
<br />

### ECMAScript
ECMAScript는 **Ecma 인터내셔널에 의해 제정된 ECMA-262 기술 규격에 의해 정의된 범용 스크립트 언어**이다.
<br />
우리가 사용하는 Javascript(이하 JS)는 뒤에 script가 붙은 것처럼 이 ECMAScript라는 표준화된 스크립트 프로그래밍 언어 사양을 준수한다.
<br />
즉, **ECMAScript가 정의한 기술 규격에 따라 만든 프로그래밍 언어가 바로 JS**이다.
<br />

> 흔히 들을 수 있는 **ES6문법**이 바로, **ECMA-262 표준의 6버전**이라는 뜻이다.

<br />

### CommonJS
CommonJS는 **JS를 서버사이드로 활용하기 위해 만들어진 표준 프로젝트**이다.
<br />
JS는 기본적으로 브라우져가 있어야 사용할 수 있는 스크립트 언어이다.
<br />
CommonJS가 없을 떄에는 서버를 JAVA나 Python 등으로 서버를 만들 수 밖에 없었다. 
<br />
하지만, JS를 서버사이드로 사용하고 싶은 개발자들이 CommonJS라는 이름의 프로젝트로 만들어낸 것이 바로, **NodeJS**이다.
<br />
<br />

- ECMAScript
   - 클라이언트 사이드 언어인 JS에서 사용하는 스크립트 언어 표준.
- CommonJS
   - 서버 사이드 언어인 NodeJS에서 사용하는 언어 표준 스크립트.

<br />
<br />

## Cannot assign to read only property 'exports' of object 해결
ECMAScript와 CommonJS는 목적성이 다른 언어들이기 때문에 문법이 상이한 부분이 있다.
<br />
해당 에러는 `import`와 `export`의 문법을 서로 달리 사용해야 한다.
<br />

### ECMAScript(Client)
클라이언트쪽 문법은 다음과 같이 해야한다.
<br />

```js
// import
import 000 from 000;

// export
export default 000;
```

### CommonJS(Server)
서버쪽 문법은 다음과 같이 해야한다.
<br />

```js
// import
const 000 require('000');

// export
module.export = ...
```
