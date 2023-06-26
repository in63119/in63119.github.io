---
title: BlockChain Core - The latest gossip on BFT consensus 10
author: IN
date: 2023-6-25 13:40:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 9](https://in63119.github.io/posts/Tendermint9/)

<br />

--- 

## Lemma 6. If a correct process p locks a value v at time t0 > GST in some round r (lockedValue = v and lockedRound = r) and timeoutP recommit(r) > 2∆, then all correct processes set validValue to v and validRound to r before starting round r + 1.

*올바른 프로세스p가 일부 라운드r에서 시간t*0 *> GST 에 값v를 잠그고 (lockedValue* = *v 및 lockedRound* = *r) timeoutPrecommit*(*r*) *>* 2∆인 *경우, 모든 올바른 프로세스는 라운드 r* + 1을 *시작하기 전에 validValue를 v로, validRound를 r로 설정합니다.*

즉, 어떤 프로세스가 특정 라운드에서 값을 고정하고, 그 라운드의 *timeoutPrecommit*이 2∆보다 크면, 네트워크의 모든 올바른 프로세스는 그 값을 채택하고 그 라운드를 유효한 라운드로 간주한다는 것

- 이 Lemma는 텐더민트 알고리즘이 BFT를 달성하기 위해(고장난 노드가 존재하더라도 합의에 도달) 사용하는 방법

### Proof

- 프로세스 p가 시간 t0에 값 v를 잠그면,
    - 어떤 올바른 프로세스도 시간 **t0 + ∆** 전에 라운드 r을 떠나지 않을 것(validValue를 v로, validRound를 r로 설정하지 않는 한)을 증명해야 합니다.
        
        > **t0 + ∆**는 프로세스 p가 값 v를 고정한 시간 이후 네트워크의 다른 프로세스가 그 값을 알게 되는 시간
        > 
- 왜냐하면, Gossip 통신 속성으로 인해 모든 정확한 프로세스는 시간 t0 + ∆에 p가 수신한 메시지를 수신하고, 이 메시지는 p가 값 v를 잠갔음을 나타냅니다.
- 따라서 라운드 r에 아직 남아있는 모든 올바른 프로세스는 규칙 36에 따라 validValue를 v로, validRound를 r로 설정합니다.
    
    ```
    // 36번째 줄
    
    // proposer(hp, roundp)가 보낸 ⟨PROPOSAL, hp, roundp, v, ∗⟩ 메시지와 2f + 1 개의 ⟨PREVOTE, hp, roundp, id(v)⟩ 메시지를 받았을 때
    upon ⟨PROPOSAL, hp, roundp, v, ∗⟩ from proposer(hp, roundp) AND 2f + 1 ⟨PREVOTE, hp, roundp, id(v)⟩ while 
    	
    	// 조건 valid(v) ∧ stepp ≥ prevote가 참이 될 때까지 반복 (while)
    	valid(v) ∧ stepp ≥ prevote for the first time do
    ```
    
- 예시)

네트워크에 5개의 노드가 있습니다.
노드 A가 시간 t0 = 0에 값 v = 1을 고정합니다.
노드 B, C, D, E는 시간 t0 + 1에 노드 A로부터 메시지를 수신합니다.
노드 B, C, D, E는 시간 t0 + 2에 2f + 1개의 PRECOMMIT 메시지를 수신합니다.
따라서, 노드 B, C, D, E는 시간 t0 + 2까지 라운드 r을 떠나지 않을 것입니다(validValue를 v = 1로, validRound를 r = 0으로 설정하지 않을 것입니다).

```
Lemma 6 사용 변수
- lockedRound: 각 프로세스가 현재 라운드에서 락을 건(round에서 투표한 값으로 결정한) 라운드 번호를 나타내는 변수입니다. lockedRound는 해당 프로세스가 결정을 확정한 라운드를 나타냅니다.
- validRound: 각 프로세스가 현재 유효한(valid) 라운드에서 투표한 값으로 결정한 라운드 번호를 나타내는 변수입니다. validRound는 해당 프로세스가 투표한 값이 유효한 라운드를 나타냅니다.
- timeoutPropose: 라운드 r에서 제안을 보내는 시간 제한을 나타내는 변수입니다. timeoutPropose(r)은 라운드 r에서 제안을 보내는 시간이 최대 얼마나 걸리는지를 나타냅니다.
- timeoutPrecommit: 라운드 r에서 PRECOMMIT 메시지를 보내는 시간 제한을 나타내는 변수입니다. timeoutPrecommit(r)은 라운드 r에서 PRECOMMIT 메시지를 보내는 시간이 최대 얼마나 걸리는지를 나타냅니다.
- timeoutPrevote: 라운드 r에서 PREVOTE 메시지를 보내는 시간 제한을 나타내는 변수입니다. timeoutPrevote(r)은 라운드 r에서 PREVOTE 메시지를 보내는 시간이 최대 얼마나 걸리는지를 나타냅니다.
```

- 프로세스 p가 시간 t0에 값 v를 락(lock)하려면, p는 시간 t0에 유효한 PROPOSAL 메시지와 2f + 1 개의 ⟨PREVOTE, h, r, id(v)⟩ 메시지를 받아야 합니다. 이 중 적어도 f + 1 개의 메시지는 올바른 프로세스에 의해 보내진 메시지여야 합니다. 올바른 프로세스의 집합을 C로 표기하면, 라운드 r의 2f + 1개의 PREVOTE 메시지 집합에는 집합 C에서 보낸 메시지가 적어도 하나 포함되어 있습니다.
    - 예를 들어, 51%의 노드가 정상적으로 작동하는 환경에서 프로세스 p가 시간 t0에 값 v를 락하려면, p는 시간 t0에 유효한 PROPOSAL 메시지와 11개의 ⟨PREVOTE, h, r, id(v)⟩ 메시지를 받아야 합니다. 이 중 적어도 6개의 메시지는 정상적으로 작동하는 노드에 의해 보내진 메시지여야 합니다.
    
- 올바른 프로세스 c1이 timeoutPrevote(r)을 발동하는 가장 빠른 시점 t에서, c1은 2f + 1개의 PREVOTE 메시지를 받았습니다 (line 34의 규칙 참조), 이 중 적어도 하나의 메시지는 집합 C에 속한 프로세스 c2에 의해 보내졌습니다. 따라서 프로세스 c2는 시간 t 이전에 PROPOSAL 메시지를 받았습니다. 고립된 통신 특성에 따라, 모든 올바른 프로세스들은 PROPOSAL 및 2f + 1개의 PREVOTE 메시지를 라운드 r에 대해 시간 t + ∆까지 받게 됩니다. p가 timeoutPrevote(r)를 발동하는 가장 늦은 시점은 t + ∆10입니다. 따라서 p가 라운드 r에서 값을 v로 락(lock)하는 가장 늦은 시점은 t0 = t + ∆ + timeoutPrevote(r)입니다 (이 시점에서 timeoutPrevote(r)가 만료되므로 프로세스는 PRECOMMIT nil을 보내고 step을 precommit으로 업데이트합니다 (line 61 참조)).
    
    > **텐더민트 알고리즘에서 timeoutPropose, timeoutPrecommit, timeoutPrevote가 있는 이유**
    > <br />
    > 고장난 노드가 존재할 경우, 합의에 도달하는 시간이 지연되거나, 합의에 도달하지 못할 수도 있기 떄문에 타임아웃에 도달했을 때, 더 이상 해당 라운드에 참여하지 않도록 하여, 합의에 도달하는 시간을 단축하고, 합의에 도달하지 못하는 가능성을 줄이는 기능


- 알고리즘 1에 따르면, 올바른 프로세스는 **2f + 1개의 PREVOTE 메시지를 받기 전에 PRECOMMIT 메시지를 보낼 수 없습니다**. 따라서 올바른 프로세스는 시간 t 이전에 라운드 r에서 PRECOMMIT 메시지를 보낼 수 없습니다.
    
    > **2f + 1개의 PREVOTE 메시지를 받으면** 텐더민트 알고리즘은 **합의를 이뤄냈다**고 판단
    > 
- 올바른 프로세스가 nil에 대해 PRECOMMIT 메시지를 보낸다면, 이는 timeoutPrevote(r)의 전체 기간을 기다렸음을 의미합니다 (line 63 참조). 따라서 올바른 프로세스는 시간 t + timeoutPrevote(r) 이전에 nil에 대해 PRECOMMIT을 보낼 수 없습니다 (*).

- 라운드 r+1에 진입하는 올바른 프로세스 q는 (i) timeoutRecommit(r) (line 67 참조) 또는 (ii) 라운드 r + 1에서 f + 1개의 메시지를 받기까지 기다려야 합니다 (line 55 참조). 전자의 경우, q는 timeoutPrecommit(r)가 시작되기 전에 2f + 1개의 PRECOMMIT 메시지를 받게 됩니다. 올바른 프로세스(이들 메시지의 투표력 중 적어도 f + 1이 올바른 프로세스에 의해 보내진 것) 중 최소한 하나의 PRECOMMIT 메시지가 nil을 위한 것인 경우, q는 t1 = t + timeoutPrevote(r) + timeoutPrecommit(r) 시간 이전에 라운드 r + 1을 시작할 수 없습니다 ( (*) 참조). 따라서 이 경우에는 t0 + ∆ < t1, 즉 t + 2∆ + timeoutPrevote(r) < t + timeoutPrevote(r) + timeoutPrecommit(r)가 성립하며, timeoutPrecommit(r) > 2∆인 경우에는 Lemma가 성립합니다.
    
    > **(i)와 (ii)의 차이**는 q가 라운드 r+1에 진입한 경우 어떤 조건을 만족해야 하는지
    > 
    > 
    > (i)의 경우, q는 timeoutPrecommit(r)를 기다려야 합니다. 즉, 이전 라운드인 r에서 2f+1개의 PRECOMMIT 메시지를 받은 후에 timeoutPrecommit(r)이 시작되어야 합니다.
    > 
    > (ii)의 경우, q는 라운드 r+1에서 f+1개의 메시지를 받아야 합니다. 즉, 적어도 f+1개의 올바른 프로세스로부터 라운드 r+1에서의 메시지를 받아야 합니다.
    > 
    - 만약 timeoutPrecommit(r) > 2∆이라면, 정상적인 프로세스 q는 라운드 r+1을 시작하기 전에 라운드 r에서 2f+1개의 PRECOMMIT 메시지를 받거나, 라운드 r+1에서 f+1개의 메시지를 받기 전에 timeoutPrecommit(r)이 만료되기를 기다려야 함을 보여줍니다.
    - 신뢰할 수 있는 프로세스 q는 라운드 r+1(다음 라운드)에 진입하기 전에 2f + 1개의 PRECOMMIT 메시지를 수신하거나 f + 1개의 메시지를 수신(최소한 과반수의 노드로부터 메시지를 수신)해야 합니다. 만약 단 하나의 PRECOMMIT 메시지라도 nil을 위해 전송된 경우, q는 라운드 r + 1을 시작하기 전에 timeoutPrevote(r) + timeoutP recommit(r) 시간만큼 기다려야 합니다.
        
        > **timeoutPrevote(r) + timeoutP recommit(r) 시간**
        > <br />
        > 다른 노드가 PRECOMMIT 메시지를 전송할 수 있는 시간
  
    - 만약, timeoutPrevote(r) + timeoutP recommit(r) 시간만큼 기다리지 않으면, 블록체인이 분열되면서 ‘하드포크’가 일어남. (이건 다른 분께 확인해야 함.)
    
- 만약 q가 수신한 2f + 1개의 PRECOMMIT 메시지 중 적어도 하나가 올바른 프로세스 c로부터 id(v) 메시지라면, q는 라운드 r + 1을 가장 일찍 t + timeoutPrecommit(r) 시간에 시작할 수 있습니다. 이 경우, Gossip 통신 특성에 따라 모든 올바른 프로세스는 PROPOSAL 및 2f + 1개의 PREVOTE 메시지 (c가 t 시간 전에 수신한 것)를 최소한 t+∆ 시간에 수신합니다. 따라서 q는 t + ∆ 시간에 가장 늦게 validValue를 v로, validRound를 r로 설정합니다. t + ∆ < t + timeoutPrecommit(r)이므로 timeoutPrecommit(r) > ∆인 경우에도 이 리마(Lemma)는 이 경우에도 성립합니다.
    - 요약
        - q가 2f + 1개의 PRECOMMIT 메시지를 수신(합의)한 경우, 그 중 적어도 하나는 올바른 프로세스 c로부터 온 것
        - 따라서 q는 라운드 r + 1을 가장 일찍 t + timeoutPrecommit(r) 시간에 시작할 수 있다.
            - 이 시간은 Gossip 통신 특성에 따라 모든 올바른 프로세스가 PROPOSAL 및 2f + 1개의 PREVOTE 메시지 (c가 t 시간 전에 수신한 것)를 최소한 t+∆ 시간에 수신하기 때문
        - 즉, q는 라운드 r + 1을 시작하기 전에 반드시 validValue와 validRound를 설정해야 함.
            - validValue와 validRound를 설정하는 데 걸리는 시간이 t + ∆보다 길다면, q는 라운드 r + 1을 시작할 수 없다.

- (ii)의 경우, q는 라운드 r + 1에서 적어도 하나의 올바른 프로세스 c로부터 메시지를 받았습니다. c가 2f + 1개의 PRECOMMIT 메시지 중에서 id(v)에 대한 PRECOMMIT 메시지를 받았다면, c가 라운드 r + 1을 시작한 가장 빠른 시점은 t + timeoutPrecommit(r)입니다. 위와 동일한 추론이 이 경우에도 성립하므로, q는 t + ∆ 시간까지 validValue를 v로 설정하고 validRound를 r로 설정할 수 있습니다. 
t + ∆ < t + timeoutPrecommit(r)이므로, timeoutPrecommit(r) > ∆인 경우에도 Lemma가 성립합니다.

---

다음에 계속...
