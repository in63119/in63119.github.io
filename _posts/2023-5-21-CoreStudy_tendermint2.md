---
title: BlockChain Core - The latest gossip on BFT consensus 2
author: IN
date: 2023-5-21 16:37:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 1](https://in63119.github.io/posts/CoreStudy_tendermint/)

<br />

---

<br />

>  The success of cryptocurrencies or blockchain systems in recent years (e.g., [4], [5]) pose a whole new set of challenges on the design and deployment of SMR based systems: reaching agreement over wide area network, among large number of nodes (hundreds or thousands) that are not part of the same administration domain, and where a subset of nodes can behave maliciously (Byzantine faults).

최근 몇 년간 암호화폐 또는 블록체인 시스템의 성공은 SMR 기반 시스템의 설계와 배포에 완전 히 새로운 도전 과제를 제기했습니다. **동일한 관리 도메인**에 속하지 않고, 노드의 하위 집합이 악의적으로 행동 할 수 있는(비잔틴 결함) 대규모 노드(수백 또는 수천 개) 사이에서 광역 네트워크를 통해 합의에 도달하는 것이 그것입니다.

```
동일한 관리 도메인
 - 동일한 조직 또는 기관 내에 있는 노드들이 서로 통신하고 상호 작용하면서 분산 시스템을 운영하는 경우를 의미.
 - 노드들이 동일한 보안 및 네트워크 인프라를 공유하며, 관리자가 노드들의 구성과 운영을 직접 관리할 수 있다.
 - 이에 반해, **비잔틴 장군 문제를 해결하기 위해 설계된 분산 시스템**에서는 동일한 관리 도메인 내에서의 신뢰성만으로는 부족하며, 여러 조직이나 기관 간에 분산된 노드들이 서로 합의를 이루어야 한다.
     - 이러한 상황에서는 동일한 보안 및 네트워크 인프라를 공유하지 않으며, 관리자가 노드들의 구성과 운영을 직접 제어할 수 없다. 따라서, 비잔틴 장군 문제를 해결하기 위해서 악의적인 노드들의 동작을 탐지하고, 그들의 동작에 대한 합의를 이루는 등의 추가적인 보안 및 합의 프로토콜이 필요하다.
```

<br />
<br />

> Furthermore, contrary to the previous data-center deployments where nodes are fully connected to each other, in blockchain systems, a node is only connected to a subset of other nodes, so communication is achieved by gossip-based peer-to-peer protocols. The new requirements demand designs and algorithms that are not necessarily present in the classical academic literature on Byzantine fault tolerant consensus (or SMR) systems (e.g., [6], [7]) as the primary focus was different setup.

또한, 노드가 서로 완전히 연결된 이전의 데이터센터 배포와 달리 블록체인 시스템에서는 노드가 다른 노드의 하위 집합에만 연결되므로 가십 기반 P2P 프로토콜을 통해 통신이 이루어집니다. 새로운 요구사항은 비잔틴 장애 허용 합의(또는 SMR) 시스템에 대한 고전적인 학술 문헌에 반드시 존재하지 않는 설계와 알고리즘을 요구하는데, 이는 다른 설정이 주요 초점이었기 때문입니다.

<br />
<br />

> In this paper we describe a novel Byzantine-fault tolerant consensus algorithm that is the core of the BFT SMR platform called Tendermint1. The Tendermint platform consists of a high-performance BFT SMR implementation written in Go, a flexible interface for building arbitrary deterministic applications above the consensus, and a suite of tools for deployment and management.

본 논문에서는 BFT SMR 플랫폼의 핵심인 Tendermint라는 새로운 Byzantine-fault tolerant 합의 알고리즘에 대해 설명합니다. Tendermint 플랫폼은 Go로 작성된 고성능 BFT SMR 구현, 합의 위에 임의의 결정론적 애플리케이션을 구축하기 위한 유연한 인터페이스, 배포 및 관리를 위한 도구 모음으로 구성됩니다.

<br />
<br />

> The Tendermint consensus algorithm is inspired by the PBFT SMR algorithm [8] and the DLS algorithm for authenticated faults (the Algorithm 2 from [6]). Similar to DLS algorithm, Tendermint proceeds in rounds2, where each round has a dedicated proposer (also called coordinator or leader) and a process proceeds to a new round as part of normal processing (not only in case the proposer is faulty or suspected as being faulty by enough processes as in PBFT). The communication pattern of each round is very similar to the ”normal” case of PBFT. Therefore, in preferable conditions (correct proposer, timely and reliable communication between correct processes), Tendermint decides in three communication steps (the same as PBFT).

**텐더민트 합의 알고리즘은 PBFT SMR 알고리즘과 인증된 오류에 대한 DLS 알고리즘에서 영감을 받았습니다.** 
텐더민트는 DLS 알고리즘과 유사하게 **라운드**로 진행되며, 각 라운드에는 전담 제안자(코디네이터 또는 리더라고도 함)가 있고 정상적인 처리의 일부로 프로세스가 새로운 라운드로 진행됩니다 (PBFT에서처럼 제안자가 결함이 있거나 충분한 프로세스를 통해 결함이 의심되는 경우만 해당). 각 라운드의 통신 패턴은 PBFT의 "정상적인" 경우와 매우 유사합니다. 따라서 바람직한 조건(올바른 제안자, 올바른 프로세 스 간의 적시에 신뢰할 수 있는 통신)에서 텐더민트는 세 가지 통신 단계(PBFT와 동일)로 결정합니다.

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9537237c-94ec-43ab-9561-d3f879b09596%2FUntitled.png?id=f7c1f3dd-13b9-4ed0-8959-e07b2fb7a052&table=block&spaceId=3511b675-8146-4402-8b33-6557b1d7a04f&width=1820&userId=02c4368e-974a-4326-a4cb-cb61e4a152b7&cache=v2" alt="sendSignedTrancastion" width="600"/>

```
- DLS 알고리즘과 비슷하게 라운드로 진행
- 각 라운드에 해당하는 리더가 있음
- PBFT와 비슷하게 3번의 communication 과정을 거침
- PBFT 로직
- PBFT와 tendermint의 차이점? 
   - PBFT: pre-prepare → prepare → commit 
   - tendermint: proposal → pre-vote → pre-commit
```
```
라운드
  - 분산 시스템에서 상호작용과 통신의 단위로 사용되는 개념(각 라운드는 일련의 단계를 의미)
  - 텐더민트에서는 ‘전담 제안자'가 존재하여 해당 라운드의 작업을 주도.
  - 텐더민트에서는 프로세스들 간의 통신과 상호 작용을 통해 진행.
      - 각 라운드에서의 통신과 상호 작용은 시스템의 일관성을 유지하고, 분산된 노드들 간의 합의를 이루기 위해 필요한 단계
  - 각 라운드는 텐더민트의 합의 알고리즘에 따라 진행.
```

<br />
<br />

> The major novelty and contribution of the Tendermint consensus algorithm is a new termination mechanism.

텐더민트 합의 알고리즘의 주요 참신함과 기여는 새로운 종료 메커니즘입니다. 

```
종료 메커니즘
  - 분산 시스템에서 합의 알고리즘이 어떻게 종료되는지를 나타내는 개념.
  - 합의를 이루는 과정에서 정해진 조건이 충족되면 종료되는데, 이때 종료 조건을 제어하는 메커니즘
```

<br />
<br />

> As explained in [9], [10], the existing BFT consensus (and SMR) algorithms for the partially synchronous system model (for example PBFT [8], [6], [11]) typically relies on the communication pattern illustrated in Figure 1 for termination.

[9], [10]에서 설명한 바와 같 이 **부분 동기 시스템 모델**을 위한 기존 BFT 합의(및 SMR) 알고리즘은 일반적으로 그림 1에 설명된 통신 패턴에 종료를 의존합니다. 

```
부분 동기 시스템 모델
  - 분산 시스템에서의 타이밍과 통신에 대한 가정을 나타내는 모델.
  - 실제 분산 시스템에서의 상황을 반영하고, 동기 시스템과 동기 시스템 간의 타협점으로 사용.
```

<br />
<br />

> The Figure 1 illustrates messages exchanged during the proposer change when processes start a new round3. It guarantees that eventually (ie. after some Global Stabilization Time, GST), there exists a round with a correct proposer that will bring the system into a univalent configuration. 

그림 1은 프로세스가 새 라운드를 시작할 때 제안자가 변경되는 동 안 교환되는 메시지를 보여줍니다. 이는 결국(즉, 일부 **글로벌 안정화 시간(GST)** 이후) 올바른 제안자가 있는 라운드가 존재하여 시스템을 단일 구성으로 만들 수 있음을 보장합니다. 

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8a9d9e95-a212-405b-80ff-88d393024dc4%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-05-17_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.01.58.png?id=9cc8dbe8-22e3-4ac4-8a74-d21361f11e47&table=block&spaceId=3511b675-8146-4402-8b33-6557b1d7a04f&width=1820&userId=02c4368e-974a-4326-a4cb-cb61e4a152b7&cache=v2" alt="sendSignedTrancastion" width="600"/>

```
글로벌 안정화 시간(GST)은 분산 시스템에서 합의 메커니즘이 합의에 도달하는 데 걸리는 시간
```

<br />
<br />

> Intuitively, in a round in which the proposed value is accepted by all correct processes, and communication between correct processes is timely and reliable, all correct processes decide.

직관적으로, 제안된 값이 모든 올바른 프로세스에 의해 수락되고 올바른 프로세스 간의 통신이 적시에 신뢰할 수 있는 라운드에서는 모든 **올바른 프로세스**가 결정합니다.

```
"올바른 프로세스"는 비잔틴 장애가 없는 프로세스
```

<br />
<br />

> To ensure that a proposed value is accepted by all correct processes4 a proposer will 1) build the global state by receiving messages from other processes, 2) select the safe value to propose and 3) send the selected value together with the signed messages received in the first step to support it. The value vi that a correct process sends to the next proposer normally corresponds to a value the process considers as acceptable for a decision:
> <br />
> • in PBFT [8] and DLS [6] it is not the value itself but a set of 2f + 1 signed messages with the same value id,

제안된 값이 모든 올바른 프로세스에서 수락되도록 하기 위해 제안자는 
1) 다른 프로세스로부터 메시지를 수신하여 글로벌 상태를 구축하고, 
2) 제안할 안전한 값을 선택하고, 
3) 첫 번째 단계에서 수신한 서명된 메시지 와 함께 선택한 값을 전송하여 이를 뒷받침합니다. 
올바른 프로세스가 다음 제안자에게 보내는 값 vi 는 일반적 으로 프로세스가 결정에 허용되는 것으로 간주하는 값에 해당합니다.

<br />

- PBFT 및 DLS에서는 값 자체가 아니라 동일한 값 ID를 가진 `2f + 1` 부호화(암호화)된 메시지 집합입니다.

```
- 여기서 `f` 는 비잔틴 장애가 있는 노드의 수이다.
- 값 ID는 각각의 합의 ID를 뜻합.
```

```
2f + 1
2f + 1은 비잔틴 장애가 있는 노드가 시스템에 최대 f개 있을 수 있다고 가정하는 공식이다.  2f개의 암호화된 메시지만 있으면 악의적인 노드가 시스템을 방해하기에 충분할 수 있기 때문에 노드가 합의에 도달하기 위해 최소한 `2f + 1` 개의 암호화된 메세지가 필요함을 의미한다.
여기서 f 앞에있는 2는 비잔틴 장애가 있는 노드가 시스템의 과반수가 될 수 없다는 것을 의미한다.
예를 들어 시스템에 10개의 노드가 있고 f가 3이면 시스템에 최대 3개의 비잔틴 장애가 있는 노드가 있을 수 있다. 노드가 합의에 도달하려면 최소한 7개의 암호화된 메시지가 있어야 한다. 7개 미만의 암호화된 메시지가 있으면 악의적인 노드가 시스템을 방해할 수 있다.
```

<br />
<br />

> • in Fast Byzantine Paxos [11] the value itself is being sent.

- 빠른 비잔틴 팍소스에서는 값 자체가 전송됩니다.

```
- Paxos는 분산 시스템에서 합의 문제를 해결하기 위한 프로토콜 그룹인데, 원래 버전의 Paxos는 비잔틴 장애가 없는 시스템을 위해 설계되었지만 이후 비잔틴 장애가 있는 시스템을 위해 수정되었다.
- 빠른 비잔틴 Paxos는 비잔틴 장애가 있는 시스템을 위해 설계된 Paxos의 변형이다. 노드가 값 자체를 전송하는 기능을 추가하여 기존 Paxos 프로토콜을 개선
    - 원래의 팍소스는 노드에서 값 자체를 전송하는 대신 값에 대한 참조를 전송했었다.
- 악의적인 노드가 값을 조작하려면 다른 노드에 값 자체를 보내야 합니다.
```

```
값 자체를 전송하면 악의적인 값 조작을 수행할 수 없는 이유

1. 다른 노드에 값 자체를 보내면 다른 노드에서 해당 값을 확인할 수 있습니다.
2. 이렇게 하면 공격이 노출될 가능성이 더 높아집니다.
3. 공격자는 값 자체를 변경하기 위해 다른 노드의 권한이 필요합니다.
4. 이것은 매우 어려울 것이며 다른 노드에서 쉽게 감지할 수 있습니다.
```

<br />
<br />

> In both cases, using this mechanism in our system model (ie. high number of nodes over gossip based network) would have high communication complexity that increases with the number of processes: in the first case as the message sent depends on the total number of processes, 

두 경우 모두, 저희 시스템 모델에서 이 메커니즘을 사용하면(즉, 가십 기반 네트워크에 노드 수가 많은 경우) 첫 번째 경우는 전송되는 메시지가 총 프로세스 수에 따라 달라지고, 두 번째 경우는 각 프로세스에서 값(트랜잭션 블록)을 전송하기 때문에 프로세스 수에 따라 통신 복잡성이 증가합니다. 

```
PBFT 및 DLS 합의 메커니즘은 값 자체를 전송하지 않고 동일한 값 ID를 가진 2f + 1 암호화된 메시지 집합을 전송한다. 값 자체를 전송하지 않는 것은 공격자가 값을 조작하는 것을 방지하기 위한 것이다.
빠른 비잔틴 팍소스 합의 메커니즘은 값 자체를 전송한다. 이것은 시스템의 확장성을 위해 수행되는데, 값 자체를 전송하면 각 노드가 값을 확인할 수 있기 때문이다.

두 경우 모두 메시지 수가 노드 수에 따라 달라진다.

```

<br />
<br />

> and in the second case as the value (block of transactions) is sent by each process. The set of messages received in the first step are normally piggybacked on the proposal message (in the Figure 1 denoted with [v1..4]) to justify the choice of the selected value x. Note that sending this message also does not scale with the number of processes in the system.

두 번째 경우에는 값(트랜잭션 블록)이 각 프로세스에서 전송됩니다. 첫 번째 단계에서 수신된 메시지 집합은 일반적으로 제안 메시지(그림 1에서 [v1..4 ]로 표시됨)에 피기백되어 선택된 값 x의 선택을 정당화합니다. 이 메시지를 보내는 것도 시스템의 프로세스 수에 따라 확장되지 않는다는 점에 유의하세요.

<img src="https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8a9d9e95-a212-405b-80ff-88d393024dc4%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-05-17_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.01.58.png?id=9cc8dbe8-22e3-4ac4-8a74-d21361f11e47&table=block&spaceId=3511b675-8146-4402-8b33-6557b1d7a04f&width=1820&userId=02c4368e-974a-4326-a4cb-cb61e4a152b7&cache=v2" alt="sendSignedTrancastion" width="600"/>

```
첫 번째 단계 : proposal(제안)

제안 단계에서는 하나도 빠짐없이 모든 노드가 값을 제안해야 합니다. 합의 프로세스는 노드 간에 정보가 공유되는 탈중앙화 시스템이므로 모든 노드가 값을 제안해야 합니다.
```

<br />
<br />

> We designed a novel termination mechanism for Tendermint that better suits the system model we consider. It does not require additional communication (neither sending new messages nor piggybacking information on the existing messages) and it is fully based on the communication pattern that is very similar to the normal case in PBFT [8]. Therefore, there is only a single mode of execution in Tendermint, i.e., there is no separation between the normal and the recovery mode, which is the case in other PBFT-like protocols (e.g., [8], [12] or [13]). We believe this makes Tendermint simpler to understand and implement correctly.
> <br />
> Note that the orthogonal approach for reducing message complexity in order to improve scalability and decentralization (number of processes) of BFT consensus algorithms is using advanced cryptography (for example Boneh-Lynn-Shacham (BLS) signatures [14]) as done for example in SBFT [15].

저희는 텐더민트가 고려하는 시스템 모델에 더 적합한 새로운 종료 메커니즘을 설계했습니다. 이 메커니즘 은 추가 통신이 필요하지 않으며(새 메시지를 보내거나 기존 메시지에 정보를 피기백하지 않음), PBFT의 일반적인 경우와 매우 유사한 통신 패턴을 완전히 기반으로 합니다. 따라서 Tendermint에는 단일 실행 모드만 존 재하며, 다른 PBFT 유사 프로토콜(예: [8], [12] 또는 [13])에서처럼 정상 모드와 복구 모드가 분리되어 있지 않습니다. 따라서 텐더민트를 더 쉽게 이해하고 올바르게 구현할 수 있다고 생각합니다. 
<br />
BFT 합의 알고리즘의 확장성과 탈중앙화(프로세스 수)를 개선하기 위해 메시지 복잡성을 줄이기 위한 직교 접근 방식은 SBFT [15]에서와 같이 고급 암호화(예: BLS(Boneh-Lynn-Shacham) 서명[14])를 사용하는 것입니다. 

```
직교 접근 방식은 비잔틴 결함 내성을 구현하는 방법.
- 각 라운드에서 노드는 값을 제안하고 다른 노드는 해당 값을 확인. 합의에 도달하려면 모든 노드가 동일한 값에 동의해야 함.
- 특징 : 안전함(모든 노드가 동일한 값에 동의해야 하기 때문에), 비효율적(모든 노드간에 여러 라운드의 메세지를 교환해야하기 때문에 속도가 느림), 낮은 확장성(모든 노드가 동일한 값에 동의해야하기 때문에) 
```

```
SBFT(Scalable Byzantine Fault Tolerance) vs PBFT(Practical Byzantine Fault Tolerance)
- SBFT는 라운드 기반 접근 방식을 사용하고 PBFT는 직교 접근 방식을 사용. 
- SBFT는 메시지 수가 적고 처리 시간이 짧기 때문에 PBFT보다 효율적. 또한 SBFT는 직교 접근 방식보다 확장성이 뛰어다.

즉, SBFT는 PBFT보다 효율적이고 확장성이 뛰어나지만 복잡.
```

<br />
<br />

> The remainder of the paper is as follows: Section II defines the system model and gives the problem definitions. Tendermint consensus algorithm is presented in Section III and the proofs are given in Section IV. We conclude in Section V.

백서의 나머지 부분은 다음과 같습니다: 섹션 II에서는 시스템 모델을 정의하고 문제 정의를 제공합니다. 섹 션 III에서는 텐더민트 합의 알고리즘을 제시하고, 섹션 IV에서는 증명을 제시합니다. 섹션 V에서 결론을 내립니 다.

<br />

---

<br />

다음에 계속...
