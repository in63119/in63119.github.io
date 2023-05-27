---
title: BlockChain Core - The latest gossip on BFT consensus 3
author: IN
date: 2023-5-27 19:21:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 2](https://in63119.github.io/posts/Tendermint2/)

<br />

---

<br />

# II. DEFINITIONS

## A. Model

> We consider a system of processes that communicate by exchanging messages. Processes can be correct or faulty, where a faulty process can behave in an arbitrary way, i.e., we consider Byzantine faults. We assume that each process has some amount of voting power (voting power of a process can be 0). Processes in our model are not part of a single administrative domain; therefore we cannot enforce a direct network connectivity between all processes. Instead, we assume that each process is connected to a subset of processes called peers, such that there is an indirect communication channel between all correct processes. Communication between processes is established using a gossip protocol [16].

우리는 메시지를 교환하여 통신하는 프로세스 시스템을 고려합니다. 프로세스는 올바르거나 결함이 있을 수 있으며, 결함이 있는 프로세스는 임의의 방식으로 작동할 수 있습니다. 각 프로세스는 일정량의 투표권을 가지고 있다고 가정합니다(프로세스의 투표권은 0이 될 수 있음). 이 모델에서 프로세스는 단일 관리 도메인의 일부가 아니므로 모든 프로세스 간에 직접적인 네트워크 연결을 강제할 수 없습니다. 대신 각 프로세스가 피어라고 하는 프로세스의 하위 집합에 연결되어 있다고 가정하여 모든 올바른 프로세스 간에 간접적인 커뮤니케이션 채널이 있다고 가정합니다. 프로세스 간의 통신은 가십 프로토콜을 사용하여 설정됩니다.

<br />

> Formally, we model the network communication using a variant of the partially synchronous system model [6]: in all executions of the system there is a bound ∆ and an instant GST (Global Stabilization Time) such that all communication among correct processes after GST is reliable and ∆-timely, 

공식적으로, 우리는 부분 동기 시스템 모델의 변형을 사용하여 네트워크 통신을 모델링합니다: 시스템의 모든 실행에서 ∆ 바운드와 즉각적인 GST(글로벌 안정화 시간)가 존재하여 GST 이후의 올바른 프로세스 간의 모든 통신이 신뢰할 수 있고 ∆-시기적절합니다. 

```
(Δ는 변화 또는 차이를 나타내는 기호)
바운드 Δ는 GST 이전에 올바른 프로세스 간에 전송된 메시지와 GST 이후에 올바른 프로세스 간에 전송된 메시지 사이의 지연을 나타낸다.
GST 이후에 올바른 프로세스 간에 전송된 메시지는 정확하고 제 시간에 도착함을 의미.
```

<br />

> i.e., if a correct process p sends message m at time t ≥ GST to a correct process q, then q will receive m before t + ∆5. In addition to the standard partially synchronous system model [6], we assume an auxiliary property that captures gossip-based nature of communication:

즉, 올바른 프로세스 p가 t ≥ GST 시간에 메시지 m을 올바른 프로세스 q에 전송하면, q는 t ∆5 전에 m을 수신합니다. 표준 부분 동기 시스템 모델 외에도, 우리는 통신의 가십 기반 특성을 포착하는 보조 속성을 가정합니다.

<br />

> - Gossip communication: If a correct process p sends some message m at time t, all correct processes will receive m before max{t, GST } + ∆. Furthermore, if a correct process p receives some message m at time t, all correct processes will receive m before max{t, GST } + ∆.
- 가십 통신: 올바른 프로세스 p가 시간 t에 메시지 m을 보내면 모든 올바른 프로세스는 max{t, GST } ∆ 이전에 m을 수신합니다.
또한 올바른 프로세스 p가 시간 t에 메시지 m을 수신하면 모든 올바른 프로세스는 max{t, GST } ∆ 이전에 m을 수신합니다.


<details>
<summary>쉽게 해석</summary>
<div markdown="1">
- 올바른 프로세스들은 GST를 알고 있다.
<br />
- GST 이후에 올바른 프로세스 p가 올바른 프로세스 q로 메시지를 보내면 q는 메시지를 p가 보내고 Δ 시간 후 또는 그 이전에 수신한다.
<br />
- 즉, 올바른 프로세스 간의 통신은 GST 이후에 신뢰할 수 있고 Δ 시간 이내에 이루어 져야 한다.
</div>
</details>

<br />

> The bound ∆ and GST are system parameters whose values are not required to be known for the safety of our algorithm. Termination of the algorithm is guaranteed within a bounded duration after GST. In practice, the algorithm will work correctly in the slightly weaker variant of the model where the system alternates between (long enough) good periods (corresponds to the after GST period where system is reliable and ∆-timely) and bad periods (corresponds to the period before GST during which the system is asynchronous and messages can be lost), but consideration of the GST model simplifies the discussion.

바운드 ∆ 및 GST는 알고리즘의 안전을 위해 값을 알 필요가 없는 시스템 매개변수입니다. 알고리즘의 종료는 GST 이후 제한된 기간 내에 보장됩니다. 실제로 알고리즘은 시스템이 (충분히 긴) 양호한 기간(시스 템이 안정적이고 ∆-시기 적절한 GST 이후 기간에 해당)과 불량한 기간(시스템이 비동기적이며 메시지가 손 실될 수 있는 GST 이전 기간에 해당)을 번갈아 가며 사용하는 약간 약한 변형 모델에서도 올바르게 작동하지만, GST 모델을 고려하면 논의가 단순화됩니다.

<br />

> We assume that process steps (which might include sending and receiving messages) take zero time. Processes are equipped with clocks so they can measure local timeouts. Spoofing/impersonation attacks are assumed to be impossible at all times due to the use of public-key cryptography, i.e., we assume that all protocol messages contains a digital signature. Therefore, when a correct process q receives a signed message m from its peer, the process q can verify who was the original sender of the message m and if the message signature is valid. We do not explicitly state a signature verification step in the pseudo-code of the algorithm to improve readability; we assume that only messages with the valid signature are considered at that level (and messages with invalid signatures are dropped).

프로세스 단계(메시지 송수신 포함)에는 시간이 0이 걸린다고 가정합니다. 프로세스에는 로컬 시간 초과를 측정할 수 있도록 시계가 장착되어 있습니다. 스푸핑/사칭 공격은 공개 키 암호화를 사용하기 때문에 항상 불가능하다고 가정합니다. 즉, 모든 프로토콜 메세지에는 디지털 서명이 포함되어 있다고 가정합니다. 따라서 올바른 프로세스 q가 피어로부터 서명된 메세지 m 을 수신하면, 프로세스 q는 메세지 m의 원래 발신자가 누구인지, 메세지 서명이 유효한지 확인할 수 있습니다. 가독성을 높이기 위해 알고리즘의 의사 코드에 서명 확인 단계를 명시적으로 명시하지 않았으며, 해당 수준에서는 유효한 서명이 있는 메세지만 고려한다고 가정합니다.(유효하지 않은 서명이 있는 메세지는 삭제됨)

---

<br />

다음에 계속...
