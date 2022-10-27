---
title: caver.js 사용시 webpack 5 호환 문제
author: IN
date: 2022-10-26 13:34:00 +0800
categories: [Blogging, Blockchain]
tags: [caver.js, webpack]
pin: true
---

**caver.js를 사용하고 `npm build`를 하는 와중에 다음과 같은 에러가 떴다.**
<br />
```bash
BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules by default.
This is no longer the case. Verify if you need this module and configure a polyfill for it.

If you want to include a polyfill, you need to:
        - add a fallback 'resolve.fallback: { "stream": require.resolve("stream-browserify") }'
        - install 'stream-browserify'
If you don't want to include a polyfill, you can use an empty module like this:
        resolve.fallback: { "stream": false }
```
<br />
해당 에러는 react 버전과 caver.js의 호환성 문제였다. 
<br />
react-scripts 버전이 5를 넘을 시, 최신 ECMAscript 환경을 만들어주는 polyfill을 포함하고 있지 않아서 생기는 문제이다.
<br />
위에 코드를 보면 polyfill을 포함하기위한 옵션 등을 알려준다.
<br />
<br />
즉, 해당 에러를 해결하기 위해서는 두 가지 방법이 있다.
<br />

1. 버전이 문제였으니 버전을 맞춘다.


2. 에러 코드에 나온대로 `resolve.fallback` 등을 맞춘다.

<br />

## 1. 버전을 맞추는 방법
caver.js 관련 오류를 확인하는 과정에서 내가 맞춘 것들은 다음과 같다.
<br />
<br />
### Node.js 14 버전
> caver.js 깃 허브 리드미: Node.js 버전은 12 또는 14로 맞춰야 한다. [참고](https://github.com/klaytn/caver-js#requirements)

<img src="https://user-images.githubusercontent.com/65399118/197941228-628e452a-4923-44fe-951a-6a707e212c79.png" alt="caverReadMe" width="600"/>

<br />
먼저, nvm을 이용하여 호환가능한 node 버전이 있는지 확인한다.
<br />

```bash
$ nvm ls
```
<br />
없으면, 나와있는 버전에 맞춰 설치한다.
<br />

```bash
$ nvm install 14.16.0
```

<br />
설치만으로 버전이 바뀌겠지만, 혹시 모르니 사용하도록 설정해보자.
<br />

```bash
$ nvm use 14.16.0
```
<br />

### react-scripts 4 버전 
react-scripts는 Create React App에서 사용하는 스크립트 구성이 포함되어있는 라이브러리이다.
<br />
해당 라이브러리의 버전을 맞추면 Create React App 역시 맞춰지기 때문에 나는 react-scripts 버전을 맞췄다.

> Create React App을 사용한다면 Create React App 버전을 맞추면 된다.

<br />

package.json에 있는 `react-script`를 찾아서 해당 버전을 `"4.0.2"`로 맞춘다.

<br />

<img src="https://user-images.githubusercontent.com/65399118/197944122-beb6263f-a83c-47a6-b602-53aa86c7a9f2.png" alt="reactScripts" width="300"/>

<br />
<br />

모든 설정이 끝난 후에는 프로젝트 디렉토리에서 `node_modules` 파일을 삭제 후
npm install로 다시 설치해주자.
<br />

```bash
$ npm install
```

<br />
<br />

## 2. 누락된 모듈을 설치하여 `webpack.config.js` 설정하기
버전을 맞추기 싫다면, 누락된 모듈을 설치 후 `webpack.config.js`를 설정해 줄 수 있다.

<br />

### `webpack.config.js` 설정
<br />

caver.js의 [Trouble shooting and known issues](https://github.com/klaytn/caver-js#using-webpack--5)를 보면
<br />
각자의 프로젝트 구성이 다르기 때문에 자신의 상황에 맞게 `webpack.config.js`를 구성 해야한다.
<br />
<img src="https://user-images.githubusercontent.com/65399118/197945970-ed15e1db-c4c0-4e94-8f67-fe11a3e07e92.png" alt="trouble" width="600"/>

<br />

처음에 봤던 에러코드에서 필요하다는 모듈을 설치하면 된다.

<br />

```bash
BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules by default.
This is no longer the case. Verify if you need this module and configure a polyfill for it.

If you want to include a polyfill, you need to:
        - add a fallback 'resolve.fallback: { "stream": require.resolve("stream-browserify") }'
        - install 'stream-browserify'
If you don't want to include a polyfill, you can use an empty module like this:
        resolve.fallback: { "stream": false }
        ...
```

<br />
난 다음과 같은 모듈을 설치 했다.

<br />

```bash
$ npm install stream-browserify

$ npm install crypto-browserify

$ npm install stream-http

$ npm install https-browserify

$ npm install os
```

<br />

모듈을 설치 후 `webpack.config.js` 파일 안에 설정을 추가해준다.

<br />

```js
module.exports = {
    ...
    resolve: {
        fallback: {
            fs: false,
            net: false,
            stream: require.resolve('stream-browserify'),
            crypto: require.resolve('crypto-browserify'),
            http: require.resolve('stream-http'),
            https: require.resolve('https-browserify'),
            os: require.resolve('os-browserify/browser'),
            ...
        },
    },
}
```

<br />

> `webpack.config.js`은 `package.json`이 있는 위치에 있어야 한다.
