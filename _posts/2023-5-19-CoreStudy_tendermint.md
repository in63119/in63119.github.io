---
title: BlockChain Core - The latest gossip on BFT consensus 1
author: IN
date: 2023-5-19 13:28:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---


> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)

<br />

[저번](https://in63119.github.io/posts/KlaytnDevMeet_metaverse/) 클레이튼 컨퍼런스에서 콜린님도 참여하시는 '블록체인 코어 스터디'에서 함께 공부해 볼 기회가 생겼다. 
> **블로깅 내용 중**
> <br />
> ![스크린샷 2023-05-19 오후 1 43 24](https://github.com/in63119/in63119.github.io/assets/65399118/f47c51df-50c9-4b45-b791-4c53be7daae3)

<br />

스터디 멤버들은 회사 이름만 들어도 다 알 수 있는 굇수 코어 개발자 분들과 블록체인 회사의 CEO & CPO 분들, 서울대 블록체인 연구원이 함께 했다. 
> 높은 자리에서도 끊임없이 공부를 하는 모습이 정말 도전이 되었다.

<br />

나같은 미천한(?) 개발자가 함께해도 괜찮은 자리인지는 모르겠지만... 이왕 참여할 기회를 주셨으니 최선을 다 할 생각이다!
> ![가즈아](https://github.com/in63119/in63119.github.io/assets/65399118/ef1a5c06-eb02-4f50-b84f-0df85661914d)

<br />

스터디는 블록체인 코어. 그 중에서도 컨센서스(합의)에 대한 부분이 중점인 것 같았고, 매 회마다 논문에 대해 공부한 후 돌아가면서 발표하는 형식이었다.

<br />

(하... 나 어떡하지?.. ㅋㅋ)

<br />

첫 번째 논문으로는 텐더민트의 `The latest gossip on BFT consensus` 이다. 

<br />

텐더민트가 구현한 합의 알고리즘은 내가 기존에 알고있던 비트코인의 PoW와는 달라서 지금도 이해하는 것에 애를 먹고 있다. ㅠ

<br />

그래서 까먹지 않기 위해 각 챕터마다 공부한 것을 기록하려 한다.

<br />
<br />

# Abstract
> The paper presents Tendermint, a new protocol for ordering events in a distributed network under adversarial conditions. 

  이 논문은 **적대적 조건**에서 분산 네트워크에서 이벤트를 주문하기 위한 새로운 프로토콜인 텐더민트를 소개 합니다. 

```
적대적 조건 : 
- 네트워크의 합의 메커니즘에 악의적인 영향을 미치려는 공격자의 존재를 설명하는 데 사용
- 텐더민트는 비잔틴 장애 허용(BFT) 프로토콜을 기반으로 하는 합의 프로토콜을 제공
```

<br />
<br />

> More commonly known as Byzantine Fault Tolerant (BFT) consensus or atomic broadcast, the problem has attracted significant attention in recent years due to the widespread success of blockchain-based digital currencies, such as Bitcoin and Ethereum, which successfully solved the problem in a public setting without a central authority. Tendermint modernizes classic academic work on the subject and simplifies the design of the BFT algorithm by relying on a peer-to-peer gossip protocol among nodes.

비잔틴 장애 허용(BFT) 합의 또는 원자 방송으로 더 잘 알려진 이 문제는 중앙 기관 없이 공개 환경에서 이 문제를 성공적으로 해결한 비트코인이나 이더리움과 같은 블록체인 기반 디지털 통화의 광범위한 성공으로 인 해 최근 몇 년간 큰 관심을 끌었습니다. 텐더민트는 이 주제에 대한 고전적인 학술 연구를 현대화하고 노드 간의 P2P **가십 프로토콜**에 의존하여 BFT 알고리즘의 설계를 단순화합니다.

```
가십 프로토콜 :
- 소문이 전파되는 방식과 유사하게 작동하기 때문에 가십 프로토콜이라고 한다.
```

<br />
<br />

# 1. INTRODUCTION
> Consensus is one of the most fundamental problems in distributed computing. It is important because of it’s role in State Machine Replication (SMR), a generic approach for replicating services that can be modeled as a deterministic state machine [1], [2].

합의는 분산 컴퓨팅에서 가장 근본적인 문제 중 하나입니다. **결정론적 상태 머신**으로 모델링할 수 있는 서비스를 복제하는 일반적인 접근 방식인 **상태 머신 복제(SMR)** 에서의 역할이 중요하기 때문입니다. 

```
결정론적 상태 머신
    - 입력과 출력 사이의 상호 작용을 모델링하기 위해 사용되는 추상적인 모델
    - 이전 상태와 입력에 의해 결정된 현재 상태를 갖고, 다음 상태를 결정한다.
    - 사용 분야
        - 프로그래밍 언어에서의 함수 호출
        - 운영 체제의 프로세스 관리
        - 네트워크 프로토콜의 패킷 전송
    - 특징
        - 입력과 상태가 고정되어 있으면 항상 같은 결과를 출력한다.
            - 시스템의 동작을 예측할 수 있어서, 시스템 설계와 분석에 유용.
    - In 블록체인
        - 결정론적 상태 머신은 블록체인의 핵심 기술 요소 중 하나
        - 네트워크 상태 변화 모델링
        - 트랜잭션 처리
        - 블록생성 등의 작업을 수행
```

```
상태 머신 복제(SMR)
    - 결정론적 상태 머신의 일종
    - 분산 시스템에서의 일관성과 신뢰성을 보장하기 위해 사용되는 기술.
    - 여러 대의 서버들이 동일한 상태 머신을 복제하여 동일한 상태 전이를 수행하도록 하는 것
    - 특징
        - 모든 요청이 일련의 명령어로 표현된다.
            - 이 명령어는 결정론적 상태 머신에서 실행.
        - 이를 통해 모든 서버가 동일한 상태를 유지 & 동일한 명령어를 실행하여 & 동일한 결과를 얻을 수 있음.
        - 즉, 모든 복제본이 동일한 순서로 트랜잭션을 수신하도록 보장하는 것.
    - In 블록체인
        - 분산된 노드들이 모두 동일한 상태 머신을 복제하여 실행하도록 한다.
            - 분산된 환경에서 일관성 & 신뢰성을 보장
        - 블록체인의 핵심 원리 중 합의 알고리즘에 적용된다.
```

<br />
<br />

> The key idea of this approach is that service replicas start in the same initial state, and then execute requests (also called transactions) in the same order; thereby guaranteeing that replicas stay in sync with each other. The role of consensus in the SMR approach is ensuring that all replicas receive transactions in the same order. Traditionally, deployments of SMR based systems are in data-center settings (local area network), have a small number of replicas (three to seven) and are

이 접근 방식의 핵심 아이디어는 서비스 복제본이 동일한 초기 상태에서 시작한 다음 동일한 순서로 요청(트랜잭션이라 고도 함)을 실행하여 복제본이 서로 동기화 상태를 유지하도록 보장하는 것입니다. SMR 접근 방식에서 합의의 역할은 모든 복제본이 동일한 순서로 트랜잭션을 수신하도록 보장하는 것입니다. 전통적으로 SMR 기반 시스템의 배포는 데이터센터 환경(로컬 영역 네트워크)에 있고, **복제본 수가 적으며(3~7개)**, 

```
복제본의 수가 많을수록 합의 메커니즘이 복잡해지고 성능이 저하됨.
대신, 복제본의 수가 많을수록 검증하는 노드가 많아지기 때문에 장애(악의적인 노드 판별)에는 강력해진다.
```

<br />

> typically part of a single administration domain (e.g., Chubby [3]); therefore they handle benign (crash) failures only, as more general forms of failure (in particular, malicious or Byzantine faults) are considered to occur with only negligible probability.

일반적으로 **단일 관리 도메인**의 일부이므로, 보다 **일반적인 형태의 장애(특히 악성 또는 비잔틴 장애)** 는 무시할 만한 확률로 발생하는 것으로 간주되므로 **양성(크래시) 장애**만 처리합니다.

```
단일 관리 도메인은 관리자가 하나의 단위로 관리할 수 있는 서버 또는 컴퓨터의 그룹.
```

```
양성(크래시) 장애와 악성 또는 비잔틴 장애(일반적)는 분산 시스템에 영향을 줄 수 있는 두 가지 유형의 장애

- 양성(크래시) 장애는 시스템의 정상적인 작동으로 인해 발생합니다. 예를 들어, 서버가 충돌하거나 네트워크 연결이 끊어질 수 있다. 이러한 유형의 장애는 시스템이 의도한 대로 작동하지 못하게 할 수 있지만 악의적인 의도가 있는 것은 아니다.
- 악성 또는 비잔틴 장애는 시스템의 작동을 악의적으로 방해하려는 공격자의 의도로 발생합니다. 예를 들어, 공격자는 잘못된 정보를 시스템에 보내거나 네트워크 트래픽을 조작하여 시스템을 분리할 수 있습니다. 이러한 유형의 장애는 시스템이 완전히 작동하지 못하게 할 수 있으며 복구하기가 더 어려울 수 있습니다.
```

<br />
<br />

다음에 계속...
