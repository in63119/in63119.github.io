---
title: BlockChain Core - The latest gossip on BFT consensus 9
author: IN
date: 2023-6-21 10:21:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 8](https://in63119.github.io/posts/Tendermint8/)

<br />

--- 

## Lemma 5. If we assume that:
### 1)  a correct process p is the first correct process to enter a round r > 0 at time t > GST (for every correct process c, roundc ≤ r at time t)
올바른 프로세스 p는 ‘시간 t > GST’에 라운드 ‘r > 0’로 처음 진입하는 첫 번째 올바른 프로세스입니다. (모든 올바른 프로세스 c에 대해, t 시점에서 roundc ≤ r입니다.)

### 2)  the proposer of round r is a correct process q
라운드 r의 제안자는 올바른 프로세스 q입니다.

### 3)  for every correct process c, lockedRoundc ≤ validRoundq at time t
모든 올바른 프로세스 c에 대해, t 시점에서 lockedRoundc ≤ validRoundq입니다.

### 4) timeoutPropose(r) > 2∆+timeoutPrecommit(r−1), timeoutPrevote(r) > 2∆ and timeoutPrecommit(r) > 2∆,
timeoutPropose(r) > 2∆ + timeoutPrecommit(r-1), timeoutPrevote(r) > 2∆, timeoutPrecommit(r) > 2∆입니다. (∆는 일정 시간 간격을 나타냅니다.)

<br />

*then all correct processes decide in round* r *before* t + 4∆ + timeoutP recommit(r − 1)*.*

위의 가정이 충족된다면, 모든 올바른 프로세스는 t + 4∆ + timeoutPrecommit(r-1) 시점 이전에 라운드 r에서 결정을 내리게 됩니다.

<br />

```
Lemma 5 사용 변수
- lockedRound: 각 프로세스가 현재 라운드에서 락을 건(round에서 투표한 값으로 결정한) 라운드 번호를 나타내는 변수입니다. lockedRound는 해당 프로세스가 결정을 확정한 라운드를 나타냅니다.
- validRound: 각 프로세스가 현재 유효한(valid) 라운드에서 투표한 값으로 결정한 라운드 번호를 나타내는 변수입니다. validRound는 해당 프로세스가 투표한 값이 유효한 라운드를 나타냅니다.
- timeoutPropose: 라운드 r에서 제안을 보내는 시간 제한을 나타내는 변수입니다. timeoutPropose(r)은 라운드 r에서 제안을 보내는 시간이 최대 얼마나 걸리는지를 나타냅니다.
- timeoutPrecommit: 라운드 r에서 PRECOMMIT 메시지를 보내는 시간 제한을 나타내는 변수입니다. timeoutPrecommit(r)은 라운드 r에서 PRECOMMIT 메시지를 보내는 시간이 최대 얼마나 걸리는지를 나타냅니다.
- timeoutPrevote: 라운드 r에서 PREVOTE 메시지를 보내는 시간 제한을 나타내는 변수입니다. timeoutPrevote(r)은 라운드 r에서 PREVOTE 메시지를 보내는 시간이 최대 얼마나 걸리는지를 나타냅니다.
```

<br />

- Lemma 5는 ‘위와 같은 가정이 성립할 때, 올바른 프로세스들이 특정 라운드에서 결정을 내릴 것을 보장한다’는 것을 설명하는 것.
    - 알고리즘이 정상적으로 동작하기 위한 조건을 제시
    - 알고리즘이 정상적으로 동작할 때, **일정 시간 안에 결정이 이루어짐을 보장**하는 내용
 
<br />

### 증명
- p가 라운드 r에 처음으로 진입한 올바른 프로세스이다.
    - timeoutPrecommit(r−1)이 만료된 p는 67번째 줄 처럼 새로운 라운드를 실행했다.
    
    ```
    Function OnTimeoutPrecommit(height, round) : 
    	if height = hp ∧ round = roundp then
    		// 67번째 줄
    		StartRound(roundp + 1) // 새로운 라운드 실행
    ```
    
- timeoutPrecommit(r-1)이 만료되었다는 것은 p가 라운드 r-1에서 2f + 1개의 PRECOMMIT 메시지를 수신했다는 것을 의미
    - 과반수 이상의 합의를 도출
- 고립된 통신 특성에 따라, 모든 올바른 프로세스들은 최대한 t + ∆ 시간에 이러한 메시지를 수신할 것
    - validValue 값과 validRound 값을 업데이트 해야하니까
- t 시점에서 라운드 r-1보다 낮은 라운드에 있는 올바른 프로세스들은 라운드 r-1로 이동 (56번 줄의 규칙 참조)
    
    ```
    // 만약 f + 1개 이상의 메시지가 도착하고, 해당 메시지의 라운드 번호가 현재 프로세스의 라운드 번호보다 크다면,
    upon f + 1 ⟨∗, hp, round, ∗, ∗⟩ with round > roundp do 
    	// 56번째 줄
    	StartRound(round) // 새로운 라운드 시작
    ```
    
- t + ∆ 시간에 timeoutPrecommit(r − 1)이 시작될 것입니다 (47번 줄의 규칙 참조).
    - t + ∆ 시간 후, 노드가 라운드 r - 1의 timeoutPrecommit이 시작되어 라운드 r - 1을 종료하고 라운드 r로 이동
    
    ```
    // 47번째 줄
    // 만약 2f + 1개 이상의 ⟨PRECOMMIT, hp, roundp, ∗⟩ 형식의 메시지가 처음으로 도착하면,
    upon 2f + 1 ⟨PRECOMMIT, hp, roundp, ∗⟩ for the first time do
    
    	// timeoutPrecommit(roundp) 이후에 OnTimeoutPrecommit(hp, roundp)를 실행하도록 예약합니다.
    	// OnTimeoutPrecommit 을 실행하면 다음 라운드 시작
    	schedule OnTimeoutPrecommit(hp,roundp) to be executed after timeoutPrecommit(roundp)
    ```
    
- 따라서, 모든 올바른 프로세스들은 t + ∆ + timeoutPrecommit(r − 1) 시간까지에는 라운드 r을 시작할 것입니다 (i).
    
    > 정리
    > <br />
    > • t 시점에서 라운드 < r - 1에 있는 올바른 프로세스들은 라운드 r - 1에 진입할 것입니다.
    > <br />
    > • t + ∆ 시간에 timeoutPrecommit(r - 1)이 시작될 것입니다.
    > <br />
    > • timeoutPrecommit이 시작되면 노드는 라운드 r - 1을 종료하고 라운드 r로 이동합니다.
     

### 프로세스 q가 마지막으로 라운드 r에 진입하는 가장 최악의 경우

- q는 t + ∆ + timeoutPrecommit(r − 1) 시간에 라운드 r을 시작
    - 어떤 값 v에 대한 PROPOSAL 메시지를 전송
- 모든 올바른 프로세스들은 최대한 t + 2∆ + timeoutPrecommit(r − 1) 시간까지 q로부터 PROPOSAL 메시지를 수신
- 이로 인해, 만약 timeoutPropose(r) > 2∆ + timeoutPrecommit(r − 1)이라면, 모든 올바른 프로세스들은 *timeoutPropose(r)이 만료되기 전*에 PROPOSAL 메시지를 수신할 것이다.
