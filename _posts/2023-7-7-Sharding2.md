---
title: BlockChain Core - Sharding on Blockchain 2
author: IN
date: 2023-7-7 13:20:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Sharding]
pin: true
---

<br />

> 자료 : [SoK: Sharding on Blockchain](https://in-nft.s3.ap-northeast-2.amazonaws.com/hotStuff.pdf)
> <br />
> [지난 포스트 - SoK: Sharding on Blockchain 1](https://in63119.github.io/posts/Sharding1/)

<br />

---

<details>
 <summary>2. PRELIMINARIES 본문</summary>
 <div markdown="1">
This section introduces various models and taxonomies for blockchain protocols, followed by discussion on typical block- chain settings and scalability issues. In this paper, we con- sider the terms node, replica, party, entity, and participant having the same meaning as participating node.
</div>
<div markdown="2">
  <br />
2.1 Models in Blockchain
</div>
<div markdown="3">
  <br />
2.1.1 Communication Models. A consensus protocol for dis- tributed systems is greatly dependent on the underlying communication network. Typically, we can categorize com- munication networks into three types [5]: strongly synchro- nous, partially synchronous, and asynchronous. A network is said to be strongly synchronous if there exists a known fixed bound, δ , such that every message takes at most δ time units to travel from one node to another in the network. A network is said to be partially synchronous if there exists a fixed bound, δ , on the network delay and one of the following conditions holds: 1) δ always holds, but is unknown; 2) δ is known, but only starts at some unknown time. A network is said to be asynchronous if there is no upper bound on the net- work delay. It is worth mentioning that the communication network models also vary by the network adversarial models, e.g., adversarial network scheduling models and oblivious adversarial models [6].
</div>
<div markdown="4">
  <br />
A consensus protocol must meet three requirements [103]: (a) Non-triviality. If a correct entity outputs a value v, then some entity proposed v; (b) Safety. If a correct entity outputs a value v, then all correct entities output the same value v; (c) Liveness. If all correct entities initiated the protocol, then, eventually, all correct entities output some value. Note that Fisher, Lynch and Paterson (FLP) [68] proved that a deterministic agreement protocol in an asynchronous net- work cannot guarantee liveness if one entity may crash, even when links are assumed to be reliable. In an asynchronous system, one cannot distinguish between a crashed node and a correct one. Theoretically, deciding the full network’s state and deducing from it an agreed-upon output is impossible. However, there exist some extensions to circumvent the FLP result to achieve an asynchronous consensus, e.g., random- ization, timing assumptions, failure detectors, and strong primitives [6].
</div>
<div markdown="5">
  <br />
2.1.2 Fault Models. We distinguish two types of fault con- sensus: crash fault-tolerant consensus (CFT) and non-crash (Byzantine) fault-tolerant consensus (BFT) [98]. Different failure models have been considered in the literature, and they have distinct behaviors. In general, a crash fault is where a machine simply stops all computation and communication, and a non-crash fault is where it acts arbitrarily, but can- not break the cryptographic primitives, e.g., cryptographic hashes, MACs, message digests, and digital signatures. For instance, in a crash fault model, nodes may fail at any time. When a node fails, it stops processing, sending, or receiv- ing messages. Typically, failed nodes remain silent forever although some distributed protocols have considered node recovery. Tolerating the crash faults (e.g., corrupted par- ticipating nodes) as well as network faults (e.g., network partitions or asynchrony) reflects the inability of otherwise correct machines to communicate among each other in a timely manner. This reflects how a typical CFT fault affects the system functionalities. At the heart of these systems typically lies a CFT-based state-machine replication (SMR) primitive [39]. However, these systems cannot deal with non-crash faults, which is also called Byzantine failure. In Byzantine failure models, failed nodes may take arbitrary actions, including sending and receiving messages that are specially crafted to break the consensus process.
</div>
<div markdown="6">
  <br />
Classic CFT and BFT explicitly model machine faults only. These are then combined with an orthogonal network fault model, for either synchronous or asynchronous networks. Thus, the related work can be classified into four categories: synchronous CFT [52], asynchronous CFT [109], synchro- nous BFT [59], and asynchronous BFT [76] [36]. The Byzan- tine setting is of relevance to security-critical settings and traditional consensus protocols that tolerate crash failures only.
</div>
</details>

# 2 PRELIMINARIES
이 섹션에서는 블록체인에서 샤딩의 개념과 기본 사항에 대해 설명한다. 샤딩 기반 프로토콜의 잠재적인 epoch 무작위성을 논의하고 블록체인에 대한 최첨단 epoch 무작위성 생성을 요약한다.

<br />

## 2.1 Models in Blockchain

### 1) Communication Models

분산 시스템의 합의 프로토콜은 기본 통신 네트워크에 크게 의존한다. 일반적으로 세 가지 유형으로 분류할 수 있다.

- **강력한 동기식**
    - 모든 메시지가 네트워크의 한 노드에서 다른 노드로 이동하는 데 최대 Δ(델타) 시간 단위가 걸리는 알려진 고정된 한계(Δ)가 있는 네트워크.
        - **쉽게 해석**
            
            네트워크의 **모든 노드는 서로 연결**되어 있고, **메시지가 한 노드에서 다른 노드로 이동하는 데 걸리는 시간은 최대 Δ 시간**
            
            - 예를 들어, Δ가 10이라고 하면, 메시지가 네트워크의 한 노드에서 다른 노드로 이동하는 데 최대 10초가 걸린다는 것을 의미한다.
    - **특징** : 보안 및 확장성 측면에서 우수하지만, 구축 및 유지 관리가 복잡.
    - **강력한 동기식 블록체인** : 비트코인, 이더리움(PoW), Hyperledger Fabric
- **부분 동기식**
    - 네트워크 지연에 고정된 한계 δ가 존재하고 다음 조건 중 하나가 충족된 네트워크.
        - 1) δ는 항상 유지되지만 알 수 없거나,
            - **네트워크의 지연은 항상 존재하지만 노드들은 그것을 알 수 없다.**
        - 2) δ는 알 수 있지만 알 수 없는 시간에만 시작.
            - **네트워크의 지연은 알 수 있지만, 노드들은 그것이 언제 시작될지 모른다.**
        - **쉽게 해석**
            - **네트워크의 지연**은 데이터가 **한 노드에서 다른 노드로 이동하는 데 걸리는 시간**을 말한다.
            - 네트워크의 지연에 **고정**된 한계 δ(델타)가 존재한다는 것은 데이터가 한 노드에서 다른 노드로 이동하는 데 걸리는 시간이 항상 δ와 **같음**을 의미한다.
    - **특징** : 부분적으로 동기화된 네트워크는 강력하게 동기화된 네트워크보다 안전하진 않지만, 구축 및 유지 관리가 더 쉽다.
    - **부분 동기식 블록체인** : Cosmos, Solana, Algorand, Cardano, Tezos, Polkadot.
- **비동기식**
    - 네트워크 지연에 상한이 없는 네트워크.
        - 한 노드에서 다른 노드로 이동하는데 걸리는 시간에 제한이 없는 네트워크.
    - **특징** : 실시간 응용 프로그램에는 적합하지 않지만, 네트워크 지연이 심한 네트워크에서 작동할 수 있다.
    - **비동기식 블록체인** : Avalanche, Solana & Tezos(부분적으로 비동기식)
        
        > ❗챗GPT는 “비동기식 블록체인”이 없다고 했지만, 바드(Bard)가 너무 확고하게 “챗GPT가 "비동기식 블록체인"이 없다고 말한 경우 이는 정확하지 않을 수 있지만, 분명히 존재한다.” 했음…
        > 

통신 네트워크 모델은 적대적 네트워크 스케줄링 모델과 무의식적 적대적 모델과 같은 네트워크 적대적 모델에 따라 달라질 수 있다.

- "adversarial models"은 네트워크의 보안을 침해하려고 시도하는 악의적인 모델.
- 시스템이나 네트워크의 보안 취약성을 평가하고, 악의적인 행위에 대응할 수 있는 방법을 탐구하는 데 사용되는 모델링

<br />

**합의 프로토콜**은 **세 가지 요구 사항**을 충족해야 한다.

> 여기서 *“entity”*는 **노드**라고 생각하면 될 듯.
> 
- **Non-triviality**(의미 있는 것): 올바른 엔티티가 값 v를 출력하면 어떤 엔티티가 v를 제안한 것.
    - 만약 올바른 엔티티가 특정한 값에 동의한다면, 그 값은 반드시 어떤 엔티티에 의해 제안되어야 한다. 이를 통해 합의 프로토콜이 의사 결정 과정에서 의미 있는 결과를 도출하도록 보장한다.
- **Safety** : 올바른 엔티티가 값 v를 출력하면 모든 올바른 엔티티가 동일한 값 v를 출력합니다.
    - 올바른 주체들이 동일한 결정에 도달하도록 보장.
    - 일관성을 유지하며 전체 시스템이 동일한 결정을 내려야 한다.
- **Liveness** : 모든 올바른 엔티티가 프로토콜을 시작하면 결국 모든 올바른 엔티티가 어떤 값을 출력합니다.
    - 모든 올바른 주체들이 프로토콜을 시작하면 어떤 값이든 결국 도출되며, 모든 주체들은 동일한 결정에 도달해야 한다.

FPL(Fisher, Lynch, Paterson)이 비동기 네트워크 환경에서 분산 합의의 불가능성(liveness를 보장할 수 없음)을 증명했지만,

- 비동기 시스템에서는 중단된 노드와 정상적인 노드의 구별이 불가능.
- 그래서 전체 상태를 결정하고 그로부터 합의된 출력을 도출하는 것이 불가능.

몇 가지 방법을 통해 비동기 합의에 도달할 수 있다.

> **예를 들어,**
> <br />
> - 노드들이 랜덤하게 행동함으로써 서로의 상태를 파악
> - 노드들이 특정 시간 내에 메시지를 전송하지 않으면 실패로 간주함으로써 실패를 감지

### 2) Fault Models

두 가지 종류의 장애 허용 합의로 구분한다.

- 충돌 장애 허용 합의 (Crash fault-tolerant consensus) - CFT
    - 충돌 결함을 용인하고 네트워크에서 일부 실패를 처리하는 합의 방식
        - 기계가 갑자기 멈추는 경우에도 합의를 유지
    - 노드가 실패하면 계산과 통신을 중단하고 메시지를 보내거나 받지 않게 된다.
        - 일반적으로 실패한 노드는 영원히 침묵 상태로 남지만 일부 분산 프로토콜에서는 노드 복구가 고려된다.
    - **CFT-based state-machine replication (SMR) primitive**가 CFT 합의 시스템의 핵심 요소
        
        > **CFT-based state-machine replication (SMR) primitive
        (CFT 기반의 상태 기계 복제 (SMR) 원시)**
        > 
        > - CFT(Crash Fault Tolerance)를 기반으로 하는 SMR(State-Machine Replication)의 기본 요소
        > - CFT-based SMR은 CFT를 사용하여 노드의 장애를 처리
        > - **CFT-based SMR 작동방식**
        >     1. 각 노드는 자신의 상태 머신을 실행합니다.
        >     2. 노드들은 서로 통신하여 상태 머신의 최신 상태를 유지합니다.
        >     3. 노드 중 하나가 충돌하면 나머지 노드들은 충돌한 노드의 상태를 무시합니다.
        >     4. 나머지 노드들은 충돌한 노드의 역할을 맡을 새로운 노드를 선택합니다.
        >     5. 새로운 노드는 충돌한 노드의 상태를 복제합니다.
        >     6. 새로운 노드는 나머지 노드들과 통신하여 상태 머신의 최신 상태를 유지합니다.
        
- 충돌이 아닌 (비잔틴) 장애 허용 합의 (Non-crash (Byzantine) fault-tolerant consensus) - BFT
    - 비충돌 결함을 처리하고 임의의 동작을 하는 노드에 대해 안전성을 제공하는 합의 방식
        - 악의적인 노드가 존재하거나 오동작을 할 수 있음
            
            > 악의적인 행동은 암호학적인 기본 요소를 깨트리지 않는 범위 내에서 제한
            > 
        - 이러한 상황에서 합의를 보장하는 것이 합의 알고리즘의 핵심 목표
    - 즉, BFT 합의 알고리즘은 **비충돌 결함을 처리**하고, **악의적인 노드의 동작을 식별**하며, 이러한 동작으로부터 **합의 프로세스를 보호**한다.
    

CFT는 블록체인을 제외한 중앙화된 서버가 있는 형태를 말하기도 한다. 즉, 포괄적으로 쓰일 수 있다.

<br />

---

다음에 계속...
