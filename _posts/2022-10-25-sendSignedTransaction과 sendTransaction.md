---
title: sendSignedTransaction과 sendTransaction
author: IN
date: 2022-10-25 13:31:00 +0800
categories: [Blogging, Blockchain]
tags: web3.js
pin: true

---

**web3.js로 트랜잭션을 보낼 때, 두 가지 함수를 쓸 수 있다.**
<br />
`sendTransaction`과 `sendSignedTransaction`. 이 두 함수는 어떤 차이가 있을까?
<br />
<br />

## Web3.js Docs를 통해 두 함수의 차이를 살펴보자.
Docs에 나와있는 설명을 보면 뚜렷한 하나의 차이를 알아볼 수 있다.
<br />
바로, 함수 실행 시 `서명`의 여부이다.
<br />
<br />
### sendTransaction
밑에있는 설명을 보면, sendTransaction은 "네트워크에 트랜잭션을 보낸다."라고만 나와있다.
<br />
  <img src="https://user-images.githubusercontent.com/65399118/197683619-a96def06-765d-4690-9377-8344ed97d987.png" alt="sendTrancastion" width="600"/>
<br />

### sendSignedTransaction
밑에있는 설명을 보면, sendSignedTransaction은 "`web3.eth.accounts.signTransaction`같은 것을 사용하여 이미 서명된 트랜잭션을 보낸다."라고 나와있다.
<br />
  <img src="https://user-images.githubusercontent.com/65399118/197688920-166e6cc2-1b18-4ff8-8d22-ee6ba4c49a03.png" alt="sendSignedTrancastion" width="600"/>
<br />
<br />
- **sendTransaction**
   -  트랜잭션 객체를 노드로 보내서 서명.
   -  실행 조건:
      - 노드에 보내는(from) 주소가 등록되어 있어야 함. 
      - 보내는(from) 주소에 락을 풀어야 함.(unlock)
      - 트랜잭션 실행 후 해당 노드가 채굴(mining)을 하면 최종적으로 완료.
- **sendSignedTransaction**
   -  서명한 트랜잭션 객체를 바로 전송.
   -  실행 조건:
      - 함수를 실행시킬때 이미 서명(Sign)된 트랜잭션을 보내야 함.
      - 트랜잭션 객체를 서명하기 위해 `web3.eth.accounts.signTransaction` 같은 함수를 사용할 수 있음.
- 결론:
   - 내가 노드를 운영할 것이라면, **sendTransaction**을.
   - 노드 운영하지 않고 그냥 사용할 거면, **sendSignedTransaction**을 사용하는 것이 편하다.

<br />
<br />

## **sendTransaction** & **sendSignedTransaction** 예제
### 트랜잭션 객체 준비
두 함수 동일하게 트랜잭션 객체(transactionObject)가 필요하다. 다만, `sendSignedTransaction`의 경우 각 데이터마다 `Hex` 형식으로 변환해줘야 한다.
<br />
> 트랜잭션 객체 예시
```js
let transactionObject = {
  from: '0x...', // 보내는 주소
  to: '0x...', // 받는 주소
  value: 0.1, // 얼마를 보낼 것인지.(단위는 wei)
  ... // 필요한 경우 나머지 옵션 추가. nonce, gasPrice 등은 web3.js의 다른 함수로 구할 수 있다.
}
```
<br />

### sendTransaction 예제
1. 보내고자 하는 주소(from)의 잠금을 해채한다. [참고](https://web3js.readthedocs.io/en/v1.8.0/web3-eth-personal.html#unlockaccount)
```js
let fromAccount = web3.personal.unlockAccount('0x...`,'123');
```
2. 잠금 해체한 주소를 트랜잭션 객체에 집어 넣어서 함수 실행. [참고](https://web3js.readthedocs.io/en/v1.8.0/web3-eth.html#sendtransaction)

```js
let transactionObject = {
  from: fromAccount, // 보내는 주소
  to: '0x...', // 받는 주소
  value: 0.1, // 얼마를 보낼 것인지.(단위는 wei)
  ... // 필요한 경우 나머지 옵션 추가. nonce, gasPrice 등은 web3.js의 다른 함수로 구할 수 있다.
}
let transaction = web3.eth.sendTransaction(transactionObject);
```

3. geth 등을 이용해 해당 노드의 채굴을 일으켜 준다.

```bash
> miner.start()
```

<br />

### sendSignedTransaction 예제
1. 트랜잭션 객체를 만들 때 Hex화 시켜줘야 한다.(address는 안해도 됨) [참고](https://web3js.readthedocs.io/en/v1.8.0/web3-utils.html#tohex)
```js
let transactionObject = {
  ...
  value: web3.utils.toHex(0.1), 
  nonce: web3.utils.toHex(2)// nonce는 getTransactionCount 함수를 쓰자,
  ... // 필요한 경우 나머지 옵션 추가. nonce, gasPrice 등은 web3.js의 다른 함수로 구할 수 있다.
}
```
2. 트랜잭션 객체를 Hex화 해줬다면, 그 객체를 서명(Sign)한다.
```js
// 서명할 때, 보내는 주소(from)의 개인키(privateKey)가 필요하다.
const signedTx =web3.eth.accounts.signTransaction(transactionObject, privateKey);
```
3. 트랜잭션 객체를 통해 서명된 트랜잭션을 보낸다.
```js
const resultTx = web3.eth.sendSignedTransaction(
    signedTx.rawTransaction,
    function (error, hash) {
      if (!error) {
        console.log(
          "거래가 성사되었습니다. 해시는: ",
          hash
        );
        return hash;
      } else {
        console.log("❗거래중 문제가 발생했습니다.:", error);
      }
    }
);
```
