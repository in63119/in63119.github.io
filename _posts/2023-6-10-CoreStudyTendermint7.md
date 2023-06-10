---
title: BlockChain Core - The latest gossip on BFT consensus 7
author: IN
date: 2023-6-10 13:36:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 6](https://in63119.github.io/posts/CoreStudyTendermint6/)

<br />

---

## A. Termination mechanism

<details>
<summary>원본 내용</summary>
<div markdown="1">
  
## A. Termination mechanism
<br />
  Tendermint ensures termination by a novel mechanism that benefits from the gossip based nature of communication (see Gossip communication property). It requires managing two additional variables, validV alue and validRound that are then used by the proposer during the propose step as explained above. The validValue
and validRound are updated to v and r by a correct process in a round r when the process receives valid PROPOSAL message for the value v and the corresponding 2f + 1 PREVOTE messages for id(v) in the round r (see the rule at line 36).
  We now give briefly the intuition how managing and proposing validValue and validRound ensures termination. Formal treatment is left for Section IV.
  The first thing to note is that during good period, because of the Gossip communication property, if a correct process p locks a value v in some round r, all correct processes will update validValue to v and validRound to r before the end of the round r (we prove this formally in the Section IV). The intuition is that messages that led to p locking a value v in the round r will be gossiped to all correct processes before the end of the round r, so it will update validV alue and validRound (the line 36).
  Therefore, if a correct process locks some value during good period, validV alue and validRound are updated by all correct processes so that the value proposed in the following rounds will be acceptable by all correct processes. Note that it could happen that during good period, no correct process locks a value, but some correct process q updates validV alue and validRound during some round. As no correct process locks a value in this case, validV alueq and validRoundq will also be acceptable by all correct processes as validRoundq > lockedRoundc for every correct process c and as the Gossip communication property ensures that the corresponding PREVOTE messages that q received in the round validRoundq are received by all correct processes ∆ time later.
  Finally, it could happen that after GST, there is a long sequence of rounds in which no correct process neither locks a value nor update validV alue and validRound. In this case, during this sequence of rounds, the proposed value suggested by correct processes was not accepted by all correct processes. Note that this sequence of rounds is always finite as at the beginning of every round there is at least a single correct process c such that validV aluec and validRoundc are acceptable by every correct process. This is true as there exists a correct process c such that for every other correct process p, validRoundc > lockedRoundp or validV aluec = lockedV aluep. This is true as c is the process that has locked a value in the most recent round among all correct processes (or no correct process locked any value). Therefore, eventually c will be the proper in some round and the proposed value will be accepted by all correct processes, terminating therefore this sequence of rounds.
  Therefore, updating validV alue and validRound variables, and the Gossip communication property, together ensures that eventually, during the good period, there exists a round with a correct proposer whose proposed value will be accepted by all correct processes, and all correct processes will terminate in that round. Note that this mechanism, contrary to the common termination mechanism illustrated in the Figure 1, does not require exchanging any additional information in addition to messages already sent as part of what is normally being called ”normal” case.
</div>
</details>

<br />

흔히 축구를 보다보면 "골 결정력"이라는 말을 듣게 된다. 상대 골대 앞까지 드리블을 하고 나서 골인까지 마무리하는 것을 골 결정력이라고 한다.
> <img src="https://thumbnews.nateimg.co.kr/view610///news.nateimg.co.kr/orgImg/sk/2021/07/21/SK007_20210721_210201.jpg" alt="goal" width="600"/>
> <br />
> 이미지 출처: 21년 네이트 기사

중요한 것은 "**마무리**"가 확실해야 한다는 것이다. 이것은 합의 알고리즘에도 같다. 합의가 끝난 후 마무리. 즉, 종료를 확실히 하는 것을 설명하는 부분이 이 ***A. Termination mechanism(종료 매커니즘)*** 이다.

<br />

*Termination mechanism*은 블록체인의 무결성을 보호하는 데 중요한 역할을 한다. 
> *Termination mechanism(종료 매커니즘)*은 말 그대로 ‘종료’를 뜻하는 것으로 각 블록체인의 **합의된 블록을 블록체인에 커밋하고, 일관성을 유지하는 역할**을 수행한다.

<br />

텐더민트의 ‘Termination mechanism’ 는 다음과 같은 특징으로 인해 블록체인의 무결성을 보호한다.

<br />

### 1. 텐더민트의 ‘Termination mechanism’은 validValue와 validRound를  이용하여 종료를 보장한다.
- validValue : 각 노드가 트랜잭션을 저장하기 위해 선택한 블록의 값
- validRound : 트랜잭션이 저장된 블록의 라운드
- `validValue`와 `validRound`는 노드가 2/3 이상의 검증자가 해당 블록에 대해 동의하는 것을 확인하면 커밋을 한다.
    > The validValue and validRound are updated to v and r by a correct process in a round r when the process receives valid PROPOSAL message for the value v and the corresponding 2f + 1 PREVOTE messages for id(v) in the round r
    >

```go
// validValue는 주어진 라운드의 유효한 값을 나타냅니다.
// 값은 검증인 주소와 제안된 블록 해시의 튜플입니다.
type validValue struct {
    validator Address
    blockHash tmhash.Hash
}

// validRound는 주어진 라운드의 유효한 라운드를 나타냅니다.
// 라운드는 uint64 유형입니다.
type validRound uint64
```

<br />

### 2. 모든 올바른 프로세스가 다음 라운드를 시작하기 전에 값을 잠그도록 요구함으로써 작동

새로운 블록이 생성되어 “해당 블록을 승인할지 여부”에 대한 합의가 끝나면(충분한 노드에 의해 승인이 되면) 해당 블록은 **잠기게(Lock)** 된다. 값이 잠기면, 노드들은 해당 값을 변경할 수 없고, 따라서 합의된 상태가 변경되지 않는다. 즉, 노드들이 하나의 합의된 상태를 유지할 수 있도록 만들어준다.

> The first thing to note is that during good period, because of the Gossip communication property, if a correct process p locks a value v in some round r, all correct processes will update validValue to v and validRound to r before the end of the round r
> 
- **`good period`** : 검증인이 블록을 제안할 수 있는 기간. 모든 올바른 프로세스가 동일한 값에 동의할 가능성이 가장 높기 때문에 “good period” 라고 한다.
- **`locks a value v`** : "잠금"은 올바른 프로세스가 라운드 r에서 값 v를 선택하고 다른 올바른 프로세스가 라운드 r이 끝나기 전에 해당 값을 변경할 수 없도록 하는 것을 의미
    
    > **값이 잠기는 기준(합의가 완료되었을 때)**
    > 1. 해당 값이 충분히 많은 노드에 의해 승인되었을 때
    > 2. 해당 값이 충분히 오랫동안 변경되지 않았을 때
    > 3. 해당 값이 합의된 상태와 일치할 때
    > <br />
    > 즉, 값이 잠긴다는 것은 ‘합의된 상태’를 뜻함.
    

이렇게 값이 잠기면, `validValue`(validBlock + validBlockParts)와 `validRound`는 잠긴 값으로 업데이트 된다.

> The first thing to note is that during good period, because of the Gossip communication property, if a correct process p locks a value v in some round r, all correct processes will update validValue to v and validRound to r before the end of the round r

<br />

---

다음에 계속...
