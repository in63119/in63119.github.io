---
title: BlockChain Core - The latest gossip on BFT consensus 4
author: IN
date: 2023-5-31 14:31:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 3](https://in63119.github.io/posts/Tendermint3/)

<br />

---

<br />

## B. State Machine Replication(상태 머신 복제)
> State machine replication (SMR) is a general approach for replicating services modeled as a deterministic state machine [1], [2]. The key idea of this approach is to guarantee that all replicas start in the same state and then apply requests from clients in the same order, thereby guaranteeing that the replicas’ states will not diverge. Following Schneider [2], we note that the following is key for implementing a replicated state machine tolerant to (Byzantine) faults:

상태 머신 복제(SMR)는 결정론적 상태 머신으로 모델링된 서비스를 복제하는 일반적인 접근 방식입니다. 이 접근 방식의 핵심 아이디어는 모든 복제본이 동일한 상태에서 시작한 다음 클라이언트의 요청을 동일한 순서로 적용하여 복제본의 상태가 달라지지 않도록 보장하는 것입니다. 슈나이더에 따라, (비잔틴) 결함에 대해 내성이 있는 복제 상태 머신을 구현하기 위해서는 다음이 핵심입니다:

- *복제본 조정.* 모든 [결함이 없는] 복제본은 동일한 요청 **시퀀스**를 수신하고 처리합니다.
    > "시퀀스"는 복제본이 수신하고 처리하는 요청의 순서
    
> Moreover, as Schneider also notes, this property can be decomposed into two parts, Agreement and Order: Agreement requires all (non-faulty) replicas to receive all requests, and Order requires that the order of received requests is the same at all replicas.

또한 슈나이더도 언급했듯이 이 속성은 **동의**와 **순서**의 **두 부분으로 나눌 수 있습니다. 
**동의**는 모든(결함이 없는) 복제본이 모든 요청을 수신해야 하며, 
**순서**는 모든 복제본에서 수신된 요청의 순서가 동일해야 합니다.

<br />

> There is an additional requirement that needs to be ensured by Byzantine tolerant state machine replica- tion: only requests (called transactions in the Tendermint terminology) proposed by clients are executed. In Tendermint, transaction verification is the responsibility of the service that is being replicated; upon receiving a transaction from the client, the Tendermint process will ask the service if the request is valid, and only valid requests will be processed.

비잔틴 내성 상태 머신 복제에 의해 보장되어야 하는 추가 요구 사항이 있습니다. 클라이언트가 제안한 요청(텐더민트 용어로는 트랜잭션이라고 함)만이 실행됩니다. 클라이언트로부터 트랜잭션을 수신하면 Tendermint프로세스는 요청이 유효한지 서비스에 묻고 유효한 요청만 처리합니다.

<br />
<br />

## C. Consensus
> Tendermint solves state machine replication by sequentially executing consensus instances to agree on each block of transactions that are then executed by the service being replicated. We consider a variant of the Byzantine consensus problem called Validity Predicate-based Byzantine consensus that is motivated by blockchain systems [17]. The problem is defined by an agreement, a termination, and a validity property.

 텐더민트는 **합의 인스턴스**를 순차적으로 실행하여 각 트랜잭션 블록에 동의한 다음 복제되는 서비스에 의해 실행되는 방식으로 상태 머신 복제를 해결합니다. 
> **합의 인스턴스** 는 `proposal - pre-vote - pre-commit` 을 나타낸다. 이는 `1 round` 를 뜻하기도 한다.

저희는 블록체인 시스템에서 동기를 부여하는 **유효성 술어 기반 비잔틴 합의**라는 비잔틴 합의 문제의 변형을 고려합니다. 
> **유효성 술어 기반 비잔틴 합의(PBFT)** 는 비잔틴 장애가 있는 노드가 있는 분산 시스템에서 합의에 도달하는 문제를 해결하는 합의 프로토콜

<br />

이 문제는 합의, 종료, 유효성 속성으로 정의됩니다.
- *합의:* 두 개의 올바른 프로세스가 서로 다른 값을 결정할 수는 없습니다.
    > 합의 프로토콜은 모든 합법적인 노드가 동일한 값에 동의하도록 해야 합니다.

- *종료:* 모든 올바른 프로세스는 결국 값을 결정합니다.
    > 합의 프로토콜은 일정 시간이 지나면 합의에 도달해야 합니다.

- *유효성:* 결정된 값은 유효합니다. 즉, *valid()로* 표시된 미리 정의된 술어를 만족합니다.
    > 합의 프로토콜은 합의에 도달한 값이 유효해야 합니다.

<br />

> This variant of the Byzantine consensus problem has an application-specific valid() predicate to indicate whether a value is valid. In the context of blockchain systems, for example, a value is not valid if it does not contain an appropriate hash of the last value (block) added to the blockchain.

비잔틴 합의 문제의 이 변형에는 값이 유효한지 여부를 나타내는 **애플리케이션**별 *valid() 술어가 있습니다.*
> "애플리케이션"은 비잔틴 합의 프로토콜이 사용되는 특정 시스템 또는 응용 프로그램을 나타낸다. 예를 들어, 블록체인 시스템의 경우 애플리케이션은 블록체인 네트워크이고 valid() 술어는 블록이 유효한지 여부를 나타낸다.

예를 들어, 블록체인 시스템의 경우, 블록체인에 추가된 마지막 값(블록)의 적절한 해시가 포함되지 않은 값은 유효하지 않습니다.

---

<br />

다음에 계속...
