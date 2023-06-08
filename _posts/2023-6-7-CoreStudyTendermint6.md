---
title: BlockChain Core - The latest gossip on BFT consensus 6
author: IN
date: 2023-6-7 21:11:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 5](https://in63119.github.io/posts/Tendermint5/)

<br />

---

이번 챕터는 텐더민트의 수도코드를 보면서 각각의 코드 또는 변수에 대한 설명을 하는 글이다. 이후에 나올 Lemma 챕터에서 코드에 대한 증명을 하기 때문에 "아 이렇게 생겼구나!" 하면서 읽어보면 좋다.

<br />

텐더민트 합의 알고리즘 수도코드

![스터디1_page-0006](https://github.com/in63119/in63119.github.io/assets/65399118/45f60db5-9bb3-467d-b05c-390cd846e1b9)

<br />

---

<br />

> The Tendermint consensus algorithm is designed for the blockchain context where the value to decide is a block of transactions (ie. it is potentially quite large, consisting of many transactions). Therefore, in the Algorithm 1 (similar as in [7]) we are explicit about sending a value (block of transactions) and a small, constant size value id (a unique value identifier, normally a hash of the value, i.e., if id(v) = id(v′), then v = v′). The PROPOSAL message is the only one carrying the value; PREVOTE and PRECOMMIT messages carry the value id. A correct process decides on a value v in Tendermint upon receiving the PROPOSAL for v and 2f + 1 voting-power equivalent PRECOMMIT messages for id(v) in some round r. In order to send PRECOMMIT message for v in a round r, a correct process waits to receive the PROPOSAL and 2f + 1 of the corresponding PREVOTE messages in the round r. Otherwise, it sends PRECOMMIT message with a special nil value. This ensures that correct processes can PRECOMMIT only a single value (or nil) in a round.

Tendermint 합의 알고리즘은 결정할 값이 트랜잭션 블록인 블록체인 컨텍스트를 위해 설계되었습니다. 즉, 트랜잭션 블록은 많은 트랜잭션으로 구성될 수 있으므로 상당히 클 수 있습니다.

합의 알고리즘의 효율성을 보장하기 위해 트랜잭션 블록의 값이 모든 메시지에 전송되지 않습니다. 대신 작은 고정 크기의 값 ID만 전송됩니다. ID는 일반적으로 값의 해시인 고유한 값 식별자입니다. 즉, 두 값의 ID가 같으면 동일한 값입니다.

PROPOSAL 메시지는 트랜잭션 블록의 값을 전달하는 유일한 메시지입니다. PREVOTE 및 PRECOMMIT 메시지는 트랜잭션 블록의 ID만 전달합니다.

올바른 프로세스는 라운드 r에서 v에 대한 PROPOSAL 및 id(v)에 대한 2f + 1 투표력 동등한 PRECOMMIT 메시지를 수신하면 Tendermint에서 값 v를 결정합니다. 라운드 r에서 v에 대한 PRECOMMIT 메시지를 보내려면 올바른 프로세스는 라운드 r에서 PROPOSAL과 해당 PREVOTE 메시지 2f + 1을 수신하기를 기다립니다. 그렇지 않으면 특별한 nil 값으로 PRECOMMIT 메시지를 보냅니다. 

이렇게 하면 올바른 프로세스가 라운드에서 단일 값(또는 nil)만 PRECOMMIT할 수 있습니다.

```
nil 값은 값이 없다는 것을 의미한다.
nil 값으로 PRECOMMIT을 하는 것은 악의적인 노드의 의한 공격으로 볼 수는 없다. 프로세스가 제안된 값을 수락하지 않았음을 나타내는 방법일 뿐.
```

<br />

> As proposers may be faulty, the proposed value is treated by correct processes as a suggestion (it is not blindly accepted), and a correct process tells others if it accepted the PROPOSAL for value v by sending PREVOTE message for id(v); otherwise it sends PREVOTE message with the special nil value.

제안자가 오류가 있을 수 있으므로 제안된 값은 올바른 프로세스에서 제안으로 처리됩니다(맹목적으로 수락되지 않음). 올바른 프로세스는 id(v)에 대한 PREVOTE 메시지를 보내서 값 v에 대한 PROPOSAL을 수락했는지 다른 사람에게 알립니다. 그렇지 않으면 특별한 nil 값으로 PREVOTE 메시지를 보냅니다. 이렇게 하면 다른 프로세스가 올바른 프로세스가 값 v에 대한 PROPOSAL을 수락했는지 확인할 수 있습니다.

<br />

> Every process maintains the following variables in the Algorithm 1: step, lockedV alue, lockedRound, validV alue and validRound. The step denotes the current state of the internal Tendermint state machine, i.e., it reflects the stage of the algorithm execution in the current round.

알고리즘 1에서 각 프로세스는 다음과 같은 변수를 유지합니다: step, lockedValue, lockedRound, validValue 및 validRound. step은 내부 Tendermint 상태 기계의 현재 상태를 나타냅니다. 
즉, 현재 라운드에서 알고리즘 실행의 단계를 반영합니다. 

```
여기서 알고리즘1은 위의 수도코드를 뜻한다.
```

<br />

> The lockedV alue stores the most recent value (with respect to a round number) for which a PRECOMMIT message has been sent. The lockedRound is the last round in which the process sent a PRECOMMIT message that is not nil. We also say that a correct process locks a value v in a round r by setting lockedV alue = v and lockedRound = r before sending PRECOMMIT message for id(v). As a correct process can decide a value v only if 2f + 1 PRECOMMIT messages for id(v) are received, this implies that a possible decision value is a value that is locked by at least f + 1 voting power equivalent of correct processes. Therefore, any value v for which PROPOSAL and 2f + 1 of the corresponding PREVOTE messages are received in some round r is a possible decision value. The role of the validV alue variable is to store the most recent possible decision value; the validRound is the last round in which validV alue is updated. Apart from those variables, a process also stores the current consensus instance (hp, called height in Tendermint), and the current round number (roundp) and attaches them to every message. Finally, a process also stores an array of decisions, decisionp (Tendermint assumes a sequence of consensus instances, one for each height).

lockedValue는 PRECOMMIT 메시지가 전송된 가장 최근의 값(해당 라운드 번호에 대해)을 저장합니다. lockedRound는 프로세스가 nil이 아닌 PRECOMMIT 메시지를 보낸 마지막 라운드입니다. 올바른 프로세스가 값 v를 라운드 r에서 PRECOMMIT 메시지를 보내기 전에 lockedValue = v 및 lockedRound = r로 설정함으로써 값 v를 잠그는 것이라고 말합니다. 2f + 1 PRECOMMIT 메시지(id(v)에 대해)가 수신되어야만 올바른 프로세스가 값 v를 결정할 수 있기 때문에, 가능한 결정 값은 적어도 f + 1개의 투표 권한과 동등한 올바른 프로세스에 의해 잠긴 값입니다. 따라서 어떤 값 v에 대해 PROPOSAL 및 해당하는 PREVOTE 메시지 2f + 1개가 라운드 r에서 수신된다면, 해당 값 v는 가능한 결정 값입니다. validValue 변수의 역할은 가장 최근의 가능한 결정 값(validValue)을 저장하는 것이며, validRound는 validValue가 업데이트된 마지막 라운드입니다. 이 외에도 프로세스는 현재 합의 인스턴스(hp, Tendermint에서는 height라고 함)와 현재 라운드 번호(roundp)를 저장하고 각 메시지에 첨부합니다. 마지막으로, 프로세스는 결정(decisionp) 배열도 저장합니다(Tendermint는 각 높이에 대해 하나의 합의 인스턴스 시퀀스를 가정합니다).

<br />

> Every round starts by a proposer suggesting a value with the PROPOSAL message (see line 19). In the initial round of each height, the proposer is free to chose the value to suggest. In the Algorithm 1, a correct process obtains a value to propose using an external function getV alue() that returns a valid value to propose.

각 라운드는 PROPOSAL 메시지를 통해 제안자가 값 제안으로 시작합니다 (라인 19 참조). 각 높이의 초기 라운드에서는 제안자가 제안할 값 선택에 자유롭습니다. 알고리즘 1에서 올바른 프로세스는 getValue()라는 외부 함수를 사용하여 제안할 값(value)을 얻습니다. 이 함수는 유효한 값을 반환합니다. 

> In the following rounds, a correct proposer will suggest a new value only if validV alue = nil; otherwise validV alue is proposed (see lines 15-18). In addition to the value proposed, the PROPOSAL message also contains the validRound so other processes are informed about the last round in which the proposer observed validV alue as a possible decision value. Note that if a correct proposer p sends validV alue with the validRound in the PROPOSAL, this implies that the process p received PROPOSAL and the corresponding 2f + 1 PREVOTE messages for validV alue in the round validRound. If a correct process sends PROPOSAL message with validV alue (validRound > −1) at time t > GST , by the Gossip communication property, the corresponding PROPOSAL and the PREVOTE messages will be received by all correct processes before time t+∆. Therefore, all correct processes will be able to verify the correctness of the suggested value as it is supported by the PROPOSAL and the corresponding 2f + 1 voting power equivalent PREVOTE messages.

이후의 라운드에서는 validValue = nil이면 올바른 제안자는 새로운 값을 제안하며, 그렇지 않으면 validV alue가 제안됩니다 (라인 15-18 참조). 제안된 값 외에도 PROPOSAL 메시지에는 validRound가 포함되어 있어 다른 프로세스가 제안자가 validV alue를 가능한 결정 값으로 관찰한 마지막 라운드를 알 수 있습니다. 정확한 제안자 p가 validV alue와 validRound를 PROPOSAL과 함께 보낸다면, 이는 프로세스 p가 validV alue에 대한 PROPOSAL 및 해당하는 2f + 1 PREVOTE 메시지를 라운드 validRound에서 받았음을 의미합니다. 올바른 프로세스가 시간 t > GST에 validV alue (validRound > -1)와 함께 PROPOSAL 메시지를 보내면, Gossip 통신 속성에 의해 해당 PROPOSAL 및 PREVOTE 메시지는 시간 t+∆ 이전에 모든 올바른 프로세스에게 수신될 것입니다. 따라서 모든 올바른 프로세스는 PROPOSAL과 해당하는 2f + 1 투표 권한과 동등한 PREVOTE 메시지를 통해 제안된 값의 정확성을 확인할 수 있습니다.

<br />

> A correct process p accepts the proposal for a value v (send PREVOTE for id(v)) if an external valid function returns true for the value v, and if p hasn’t locked any value (lockedRound = −1) or p has locked the value v (lockedValue = v); see the line 23. In case the proposed pair is (v,vr ≥ 0) and a correct process p has locked some value, it will accept v if it is a more recent possible decision value9, vr > lockedRoundp, or if lockedValue = v (see line 29). Otherwise, a correct process will reject the proposal by sending PREVOTE message with nil value. A correct process will send PREVOTE message with nil value also in case timeoutPropose expired (it is triggered when a correct process starts a new round) and a process has not sent PREVOTE message in the current round yet (see the line 57).

올바른 프로세스 p는 값 v에 대한 제안을 수락합니다 (id(v)에 대해 PREVOTE를 전송) 경우, 외부의 유효한 함수가 값 v에 대해 true를 반환하고, p가 어떤 값을 잠그지 않았거나 (lockedRound = -1), p가 값 v를 잠궜을 경우 (lockedValue = v)입니다 (라인 23 참조). 제안된 쌍이 (v, vr ≥ 0)인 경우, 올바른 프로세스 p는 어떤 값을 잠궜을 경우에만 가장 최근의 가능한 결정 값을 수락합니다9, vr > lockedRoundp 또는 lockedValue = v인 경우에는 v를 수락합니다 (라인 29 참조). 그렇지 않은 경우, 올바른 프로세스는 nil 값을 가진 PREVOTE 메시지를 보내어 제안을 거부합니다. 올바른 프로세스는 nil 값을 가진 PREVOTE 메시지도 보낼 수 있으며, 이는 timeoutPropose가 만료되었을 경우 (새로운 라운드를 시작할 때 트리거됨) 및 프로세스가 현재 라운드에서 PREVOTE 메시지를 아직 보내지 않았을 경우에 해당합니다 (라인 57 참조).

<br />

> If a correct process receives PROPOSAL message for some value v and 2f + 1 PREVOTE messages for id(v), then it sends PRECOMMIT message with id(v). Otherwise, it sends PRECOMMIT nil. A correct process will send PRECOMMIT message with nil value also in case timeoutPrevote expired (it is started when a correct process sent PREVOTE message and received any 2f + 1 PREVOTE messages) and a process has not sent PRECOMMIT message in the current round yet (see the line 65). A correct process decides on some value v if it receives in some round r PROPOSAL message for v and 2f + 1 PRECOMMIT messages with id(v) (see the line 51). To prevent the algorithm from blocking and waiting forever for this condition to be true, the Algorithm 1 relies on timeoutPrecommit. It is triggered after a process receives any set of 2f + 1 PRECOMMIT messages for the current round. If the timeoutP recommit expires and a process has not decided yet, the process starts the next round (see the line 65). When a correct process p decides, it starts the next consensus instance (for the next height). The Gossip communication property ensures that PROPOSAL and 2f + 1 PREVOTE messages that led p to decide are eventually received by all correct processes, so they will also decide.

올바른 프로세스가 값 v에 대한 PROPOSAL 메시지와 id(v)에 대한 2f + 1 PREVOTE 메시지를 받으면, 해당 값 v로 PRECOMMIT 메시지를 전송합니다. 그렇지 않은 경우, PRECOMMIT nil을 보냅니다. 올바른 프로세스는 nil 값을 가진 PRECOMMIT 메시지도 보낼 수 있으며, 이는 timeoutPrevote가 만료된 경우 (올바른 프로세스가 PREVOTE 메시지를 보내고 2f + 1 PREVOTE 메시지를 받은 후 시작됨) 및 프로세스가 현재 라운드에서 PRECOMMIT 메시지를 아직 보내지 않았을 경우에 해당합니다 (라인 65 참조). 올바른 프로세스는 값 v에 대한 PROPOSAL 메시지와 id(v)에 대한 2f + 1 PRECOMMIT 메시지를 받으면 (라인 51 참조), 어떤 값 v에 대해 결정을 내립니다. 알고리즘이 이 조건이 참이 될 때까지 계속해서 블록되고 영원히 대기하지 않도록 하기 위해, 알고리즘 1은 timeoutPrecommit에 의존합니다. 이는 프로세스가 현재 라운드에 대해 2f + 1 PRECOMMIT 메시지 세트를 받은 후 트리거됩니다. timeoutPrecommit이 만료되고 프로세스가 아직 결정하지 않았을 경우, 프로세스는 다음 라운드를 시작합니다 (라인 65 참조). 올바른 프로세스 p가 결정을 내리면, 다음 합의 인스턴스(다음 높이)를 시작합니다. Gossip 통신 속성은 p가 결정하게 된 PROPOSAL 및 2f + 1 PREVOTE 메시지가 결국 모든 올바른 프로세스에게 수신됨을 보장하기 때문에, 그들도 결정하게 됩니다.

---

다음에 계속...
