---
title: BlockChain Core - Sharding on Blockchain 1
author: IN
date: 2023-7-4 20:09:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Sharding]
pin: true
---

<br />

> 자료 : [SoK: Sharding on Blockchain](https://in-nft.s3.ap-northeast-2.amazonaws.com/hotStuff.pdf)
> <br />

<br />

---

<br />

저번 텐더민트 알고리즘에 이어 오늘부터 블록체인 샤딩에 대해 공부를 시작한다!

<br />

이번 논문은 여러 블록체인의 샤딩 프로토콜에 대한 내용이다. 
<br />
샤딩은 트랜잭션을 여러개로 분할하여 처리하는 것을 말한다.(앞으로 더 자세히 나옴)
<br />
블록체인의 확장성을 위해 사용되는 샤딩이지만 여러가지 딜레마가 있다.(이것도 앞으로... 헤헷...)

<br />

자, 그럼 샤딩에 대해 공부해보자.

<br />

--- 

<details>
 <summary>1. INTRODUCTION 본문</summary>
 <div markdown="1">
The blockchain has become a key technology for imple- menting distributed ledgers. It allows a group of partici- pating nodes (or parties) that do not trust each other to provide trustworthy and immutable services. Distributed ledgers were initially used as tamper-evident logs to record data. They are typically maintained by independent parties without a central authority, for example, in systems like SUNDR [95], SPORC [66], and Tamper-Evident Logging [53]. The blockchain became popular because of its success in crypto-currencies, e.g., Bitcoin [107]. Blockchain stands in the tradition of distributed protocols for both secure mul- tiparty computation and replicated services for tolerating Byzantine faults [101]. With blockchain, a group of parties can act as a dependable and trusted third party for main- taining a shared state, mediating exchanges, and providing a secure computing engine [34].
</div>
<div markdown="2">
  <br />
Consensus is one of the most important problems in block- chain, as in any distributed systems where many nodes must reach an agreement, even in the presence of faults. Current consensus algorithms are only applicable to small-scale sys- tems because of complexity, e.g., the Practical Byzantine Fault Tolerance protocol (PBFT) [37] with less than 20 partic- ipating nodes. Scalability is an issue that has to be addressed before adopting blockchain in large-scale applications. Re- cently, many solutions have been proposed to achieve the scale-out throughput by allowing participating nodes only to acquire a fraction of the entire transaction set, for ex- ample, an Off-chain solution [114], Directed Acyclic Graph (DAG) [115] and blockchain sharding [100]. However, the off- chain solution is more subject to forks and the transactions in the DAG layout are not organized in a chain structure. Among all proposed methods, sharding schemes seem to be the most effective candidate as it can overcome both perfor- mance and scalability problems. A sharding scheme splits the processing of transactions among smaller groups of nodes, called shards. As a result, shards can work in parallel to max- imize the performance and improve the throughput while requiring significantly less communication, computation, and storage overhead, allowing the scheme to work in large systems [141]. 
</div>
<div markdown="3">
  <br />
Particularly, sharding technology utilizes the concept of committees. The term committee is also used to refer to a subset of participating nodes that collaborate to finish a spe- cific function. The notion of committees in the context of consensus protocols was first introduced by Bracha [25] to reduce the round complexity of Byzantine agreement. Using committees to reduce the communication and computation overhead of Byzantine agreement dates back to the work of King et al. [88, 89]. However, they provided only theoret- ical results and the techniques cannot be directly used in a blockchain setting. Sharding-based blockchain protocols can increase the transaction throughput when more partic- ipants join the network because more committees can be formed to process transactions in parallel. The total num- ber of transactions processed in each consensus round by the entire network is multiplied by the number of commit- tees. For security reasons, a sharding scheme needs to fairly and randomly divide the network into small shards with the vanishing probability of any shard having an overwhelming number of adversaries.
</div>
<div markdown="4">
  <br />
Although sharding is promising, it still faces many spe- cific design challenges. We need to identify key components in blockchain sharding, understand the challenges in each component, and systematically study potential solutions to each challenge. To date, there has been no systematic and comprehensive study or review on blockchain sharding. To fill the gap, this paper presents a comprehensive and system- atic study of sharding techniques in blockchain. We identify the key components in sharding schemes and the major chal- lenges in each component. As a systematization of knowl- edge on blockchain sharding, we also analyze and compare the state-of-the-art solutions.
</div>
<div markdown="5">
  <br />
The rest of the paper is organized as follows. Section 2 introduces various models and taxonomies of blockchain systems. Section 3 gives an overview of sharding. Section 4 discusses consensus protocols. Section 5 presents the ap- proaches to generating epoch randomness. Section 6 dis- cusses how to deal with cross-sharding transactions. Sec- tion 7 discusses the reconfiguration of epochs. Section 8 compares the state-of-the-art sharding protocols. Section 9 concludes this paper.
</div>
</details>

# 1. INTRODUCTION(a.k.a 기승전 확장성)

기존 분산 원장은 데이터를 기록하기 위한 **변경 불가능한 로그**로 사용되었다.

이에 대한 예시로 *“SUNDR [95], SPORC [66], and Tamper-Evident Logging [53].”* 에 대해서 소개하는데, **공통적으로 `확장성` 을 향상시키기 위한 시스템**들이다.

> **변경 불가능한 로그로 사용되었던 기존 분산원장 예)**
> 
> <br />
>
> 각 블록은 이전 블록의 해시를 참조하는 체인 형식으로 데이터를 저장했다. 암호화폐, 계약 관리, 공급망 관리 등 다양한 분야에서 사용할 수 있다.
> 
> - SUNDR: Secure, Unforgeable, Non-repudiation, and Tamper-evident Data Recording
>     - 블록체인에서 분할을 구현하는 방법 중 하나
>     - 블록체인을 여러 개의 분할로 나누고, 각 분할에 대한 로그를 별도로 저장
>     - 로그는 암호화되어 저장되며, 모든 분할의 노드가 로그를 동기화
>     - 블록체인의 **확장성**을 향상시키고, 보안성을 강화하는 데 도움
> - SPORC: "Scalable Privacy-Preserving Off-Chain Routing for Payment Channel Networks"라는 논문에서 제안된 시스템
>     - 블록체인의 확장성 문제를 해결하기 위해 페이먼트 채널 네트워크(payment channel network)에서의 오프체인 라우팅(off-chain routing)을 위한 확장 가능하고 개인 정보 보호를 보장하는 방법을 제시
>         - 기존의 블록체인에서는 모든 거래를 체인에 기록해야 했기 때문에 처리량에 제한이 있다.
>         - 그에대한 대안으로 ‘페이먼트 채널 네트워크’가 블록체인 외부에서 거래를 처리하고, 그 결과만을 체인에 기록함으로써 확장성 문제를 해결
>         - 하지만 페이먼트 채널 네트워크에서의 라우팅 문제와 개인 정보 보호 문제가 제기
>         - SPORC 시스템은 위 문제를 해결하기 위해 개인 정보 보호를 유지하면서 효율적인 오프체인 라우팅을 수행하는 방법을 제안
>     - 논문에서 제안된 SPORC 시스템은 블록체인에서의 샤딩과 오프체인 라우팅을 결합한 접근 방식으로, 확장성 문제와 개인 정보 보호 문제에 대한 해결책을 제시
>         - 샤딩을 활용하여 네트워크를 분할하고, 각 샤드에서 오프체인 거래를 처리
>         - 이를 통해 개인 정보 보호를 강화하고, 라우팅 경로를 최적화하여 **확장성**을 향상
> - Tamper-Evident Logging: Tamper-Evident Logging
>     - 변경할 수 없는 로깅을 위한 시스템.
>     - 블록체인에서 분할을 구현하는 방법 중 하나
>         - 블록체인을 여러 개의 분할로 나누고, 각 분할에 대한 로그를 별도로 저장
>         - 로그는 암호화되어 저장되며, 모든 분할의 노드가 로그를 동기화
>     - 블록체인의 **확장성**을 향상시키고, 보안성을 강화하는 데 도움

<br />

대규모 트래픽이 존재하는 애플리케이션에서는 `확장성 문제` 로 인해 블록체인을 사용하기가 어렵다.

- ❓**궁금한 것**
    
    > **Current consensus algorithms are only applicable to small-scale sys- tems because of complexity, e.g., the Practical Byzantine Fault Tolerance protocol (PBFT) [37] with less than 20 partic- ipating nodes.**
    > 
    
    논문에 있는 위의 내용은 “현재 합의 알고리즘은 복잡성으로 인해 소규모 시스템에만 적용 가능”하다는 말인데, 이 말이 `PBFT` 를 적용하는 블록체인에만 해당이 되는 것인지 궁금.
  - (자문자답) 논문의 내용 자체가 헷갈리게 나와서 그렇지 합의 알고리즘의 복잡성이 PBFT에만 나오는 것이 아니다. 분명히 PBFT는 알고리즘이 복잡하고 네트워크 오버헤드가 크기 때문에 대규모 시스템에 적합하지 않을 수 있다. 이와 비슷한 이유로 `Raft, Paxos` 등의 블록체인 등이 복잡성과 성능에 대한 Trade Off가 이루어지고 있다. 
    
---

<br />

그래서 `확장성 문제` 를 해결하기 위한 솔루션으로 다음을 예로 들 수 있다.

- **Off-chain solution** : 블록체인 외부에서 트랜잭션을 처리하는 기술
    - 블록체인에 기록해야 하는 트랜잭션의 수를 줄일 수 있으므로 블록체인의 확장성을 높일 수 있다.
    - 하지만, 보안이나 탈중앙화와 같은 다른 특성은 저하될 수 있다.
- **Directed Acyclic Graph (DAG)** : 방향성 비순환 그래프(블록체인에서 블록을 연결하는 방식의 한 유형)
    - DAG 블록체인에서는 각 블록이 이전 블록에만 연결되는 것이 아니라 다른 블록에도 연결
        - 이로 인해 DAG 블록체인은 기존 블록체인보다 더 빠르고 효율적일 수 있다.
    - 하지만, 각 블록이 다른 블록에 연결되어 있기 때문에, 모든 노드가 모든 블록을 승인하는 것이 어렵다.
        
        > 예를 들어, **노드 A가 블록 1을 승인하고, 노드 B가 블록 2를 승인**했다고 가정
        **노드 A는 블록 2를 승인할 수 없고**, **노드 B는 블록 1을 승인할 수 없다.** 왜냐하면 **블록 1과 블록 2는 서로 연결되어 있지 않기 때문**
        > 
        - 즉, 블록체인에 영구적으로 기록되기 전에 모든 노드가 블록을 승인하는 과정이 어렵다.
        그로인한 영향으로
            - 블록체인의 보안성이 취약해질 수 있다.
            - 블록체인의 확정성이 떨어질 수 있다.
            - 블록체인의 성능이 저하될 수 있다.
- **Sharding** : 블록체인 네트워크를 여러 개의 작은 샤드로 나누는 기술
    - 논문에서는 샤딩을 가장 효과적인 후보로 설명.

## Sharding
![스크린샷 2023-06-05 오후 6.00.00.png](https://github.com/in63119/in63119.github.io/assets/65399118/5dc3765d-d495-42da-b205-c91e01d6a4e3)

- 샤드로 나누면 각 샤드는 특정 데이터만 저장하고 처리하게 되므로, 블록체인 네트워크의 성능을 향상
    - 샤드는 병렬로 작동하여 성능을 극대화하고 처리량을 향상
    - 통신, 계산 및 저장 공간의 과부하가 크게 줄어들어 대규모 시스템에서 이 방식을 사용하기 좋다.
- **이더리움의 경우** 샤딩을 통해 **샤드를 3가지 유형**으로 나눈다.
    - **데이터** 샤드: 이 샤드는 블록체인에 저장된 데이터를 저장하고 관리
    - **계정** 샤드: 이 샤드는 블록체인에 저장된 계정 정보를 저장하고 관리
    - **트랜잭션** 샤드: 이 샤드는 블록체인에 저장된 트랜잭션 정보를 저장하고 관리
- 즉, **샤딩은 블록체인의 확장성 문제를 해결하기 위해 트랜잭션 처리를 더 작은 그룹으로 분할하는 것**

### committees

> **Elastico 샤딩 프로토콜**
> 
> - 이더리움 2.0에 채택된 샤딩 프로토콜
> - 여기서 C1 ~ C4 가 트랜잭션을 처리하는 committees.
> - C5는 트랜잭션을 검증하는 committees.
> 
> ![Each-epoch-of-sharding-protocol-eg-Elastico-2-includes-three-major-stages](https://github.com/in63119/in63119.github.io/assets/65399118/8c9be485-d9bc-427d-8257-518c9521bcfa)

- 샤딩 기술은 **committees**(위원회)라는 개념을 사용한다. **committees**(위원회)는 특정 기능을 완료하기 위해 협력하는 참가 노드의 하위 집합을 나타낸다.
    - 여러가지 위원회 **구성 방법**
        - 노드의 지분에 따라
        - 노드의 성능에 따라
        - 노드의 신뢰도에 따라
    - 위원회 **역할**
        - 트랜잭션의 유효성 검증
        - 트랜잭션을 블록에 포함
        - 블록을 검증
- 샤딩 기반 블록 체인 프로토콜은 더 많은 참가자가 네트워크에 참여할 때 더 많은 위원회를 형성하여 병렬로 트랜잭션을 처리 할 수 있으므로 트랜잭션 처리량을 높일 수 있다.
- 전체 네트워크가 각 컨센서스 라운드에서 처리하는 총 트랜잭션 수는 위원회 수로 곱해진다.
- 이 **committees**(위원회)을 만들때 보안상의 이유로, 공정하고 무작위로 분할해야 한다.(압도적인 수의 적을 가질 확률이 매우 낮아야 한다.)
    - 특정 샤드에 악의적인 노드가 몰리는 것을 방지하기 위해.

여기서 핵심은 샤딩 기술이 블록 체인 네트워크의 `확장성`을 향상시킬 수 있다는 것. 샤딩을 통해 블록 체인 네트워크는 더 많은 트랜잭션을 처리 할 수 있다. 

### Sharding의 과제(외계의 기술이 아니다)

샤딩은 블록체인의 확장성을 높이는 유망한 기술이지만, 아직 해결해야 할 많은 특정 설계 과제가 있다.

- 이 후 논문에서 알려줄 것
