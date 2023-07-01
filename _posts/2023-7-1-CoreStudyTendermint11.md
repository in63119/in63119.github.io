---
title: BlockChain Core - The latest gossip on BFT consensus 11
author: IN
date: 2023-7-1 14:43:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 10](https://in63119.github.io/posts/CoreStudyTendermint10/)

<br />

--- 

## Lemma 7. Algorithm 1 satisfies Termination.
*정리 7. 알고리즘 1은 종료 조건을 만족합니다.*

<details>
 <summary>Lemma 7 본문</summary>
 <div markdown="1">
  Proof: Lemma 5 defines a scenario in which all correct processes decide. We now prove that within a bounded duration after GST such a scenario will unfold. Let’s assume that at time GST the highest round started by a correct process is r0, and that there exists a correct process p such that the following holds: for every correct process c, lockedRoundc ≤ validRoundp. Furthermore, we assume that p will be the proposer in some round r1 > r (this is ensured by the proposer function).
</div>
<div markdown="2">
  <br />
  We have two cases to consider. In the first case, for all rounds r ≥ r0 and r < r1, no correct process locks a value (set lockedRound to r). So in round r1 we have the scenario from the Lemma 5, so all correct processes decides in round r1.          
</div>
<div markdown="3">
  <br />
  In the second case, a correct process locks a value v in round r2, where r2 ≥ r0 and r2 < r1. Let’s assume that r2 is the highest round before r1 in which some correct process q locks a value. By Lemma 6 at the end of round r2 the following holds for all correct processes c: validValuec = lockedV alueq and validRoundc = r2. Then in round r1, the conditions for the Lemma 5 holds, so all correct processes decide.
</div>
</details>

<br />

### Proof

> *Proof:* Lemma 5 defines a scenario in which all correct processes decide. We now prove that within a bounded duration after GST such a scenario will unfold. Let’s assume that at time GST the highest round started by a correct process is r0, and that there exists a correct process p such that the following holds: for every correct process c, lockedRoundc ≤ validRoundp. Furthermore, we assume that p will be the proposer in some round r1 > r (this is ensured by the proposer function).
> 
- Lemma 5는 모든 올바른 프로세스가 동의하는 시나리오를 정의하는 내용.
    
    > Lemma 5: 모든 올바른 프로세스가 라운드 r1에서 결정한다면, 모든 올바른 프로세스는 값 v를 선택하고 validValuec = v 및 validRoundc = r1을 갖습니다.
    > 
    - Lemma 5는 ‘가정이 성립할 때, 올바른 프로세스들이 특정 라운드에서 결정을 내릴 것을 보장한다’는 것을 설명하는 것.
        - 알고리즘이 정상적으로 동작하기 위한 조건을 제시
        - 알고리즘이 정상적으로 동작할 때, **일정 시간 안에 결정(합의)이 이루어짐을 보장**하는 내용
- GST 후 제한된 시간 내(Lemma 6)에 위(Lemma 5)의 시나리오가 전개될 것임을 증명합니다.
- 시간 GST에서 올바른 프로세스에서 시작된 가장 높은 라운드는 r0입니다.
- 올바른 프로세스 c마다 lockedRoundc ≤ validRoundp입니다.
- p는 r1 > r인 일부 라운드 r1의 제안자입니다.

<br />

### 고려할 두 가지 경우

1. 첫 번째 경우
- *r*  ≥  *r*0  및 *r < r*1 (r0 이상 r1 미만) 의 모든 라운드에서 올바른 프로세스가 값을 잠그지 않으면,
    - 라운드 r1에서 Lemma 5의 시나리오가 발생한다. 따라서 모든 올바른 프로세스는 라운드 r1에서 결정
    - Lemma 5는 모든 올바른 프로세스가 동의하는 시나리오를 정의하는 내용.
        
        > Lemma 5: 모든 올바른 프로세스가 라운드 r1에서 결정한다면, 모든 올바른 프로세스는 값 v를 선택하고 validValuec = v 및 validRoundc = r1을 갖습니다.
        > 
        - Lemma 5는 ‘가정이 성립할 때, 올바른 프로세스들이 특정 라운드에서 결정을 내릴 것을 보장한다’는 것을 설명하는 것.
            - 알고리즘이 정상적으로 동작하기 위한 조건을 제시
            - 알고리즘이 정상적으로 동작할 때, **일정 시간 안에 결정이 이루어짐을 보장**하는 내용

2. 두 번째 경우
- r2 라운드에서 올바른 프로세스가 값 v를 잠근 경우
    - 어떤 올바른 프로세스 *q가* 값을 잠그는 *r*1 이전의 가장 높은 라운드가 *r*2 이라고 가정
    - *r*2 라운드가 끝 날때 Lemma6에 따라 모든 올바른 프로세스는 validValuec = lockedValueq 및 validRoundc = r2를 갖는다.
        
        > Lamma6: 어떤 프로세스가 특정 라운드에서 값을 고정하고, 그 라운드의 *timeoutPrecommit*이 2∆보다 크면, 네트워크의 모든 올바른 프로세스는 그 값을 채택하고 그 라운드를 유효한 라운드로 간주한다는 것
        > 
        - 렘마 6은 모든 노드가 과반수 이상 동의했을 때, 제한 시간이 지나면 다음 라운드가 실행된다.
    - 그런 다음 *r*1 라운드에서 Lemma 5의 조건이 유지되므로 모든 올바른 프로세스가 결정되어 종료됩니다.

즉, Lemma7은 **블록 생성 과정이 무한히 지속되지 않도록 보장**한다. 왜냐하면 노드가 51% 이상의 투표를 받지 못하면 블록을 생성할 수 없기 때문이다.

<br />

---

이렇게 텐더민트 합의 알고리즘에 대해 긴 공부가 끝이났다. 사실 완벽하게 이해했다고 할 순 없지만... 이번 기회로 인하여 BFT에 대한 실질적인 동작방식과 코어 코드단의 구현에 대해 이해하게 되었다. 
> 아흙... 힘들었다...

<br />

다음 주제는 블록체인 확장성에 필요한 샤딩(Sharding)이다! 아자아자!
