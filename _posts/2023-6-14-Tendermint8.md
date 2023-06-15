---
title: BlockChain Core - The latest gossip on BFT consensus 8
author: IN
date: 2023-6-14 17:50:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 7](https://in63119.github.io/posts/CoreStudyTendermint7/)

<br />

---

# IV. PROOF OF TENDERMINT CONSENSUS ALGORITHM

## Lemma 1. For all f ≥ 0, any two sets of processes with voting power at least equal to 2f + 1 have at least one correct process in common.
> Proof: As the total voting power is equal to n = 3f + 1, we have 2(2f + 1) = n + f + 1. This means
that the intersection of two sets with the voting power equal to 2f + 1 contains at least f + 1 voting power in common, i.e., at least one correct process (as the total voting power of faulty processes is f). The result follows directly from this.

*모든 f* ≥ *0의 경우, 투표권이 2f* + 1 *이상인 두 프로세스 세트는 적어도 하나의 올바른 프로세스를 공통적으로 가지고 있습니다.*

증명: 총 투표권은 *n* = *3f* + 1이므로, 2(*2f* + 1) = *n* + *f* + 1이 됩니다. 즉, 투표권이 *2f* + 1인 두 집
합의 교집합에는 최소 *f* + 1의 공통 투표권, *즉* 적어도 하나의 올바른 프로세스가 포함되어 있습니다(
오류 프로세스의 총 투표권은 *f이므로)*. 결과는 바로 이로부터 나옵니다.

```
이 증명은 네트워크에 최대 f개의 비정상적인 노드가 있어도 올바른 블록에 동의할 수 있기 때문에 안전성에 대한 증명으로 사용된다.
```
- 총 투표권은 n = 3f + 1이다: 시스템에 참여하는 프로세스의 총 개수(n)는 악의적인 프로세스 수(f)의 세 배에 1을 더한 값이다.
- 2(2f + 1) = n + f + 1이 된다: 투표권이 2f + 1인 두 개의 집합이 있을 때, 이 두 집합의 교집합에는 최소 f + 1개의 공통 투표권이 존재한다. 즉, 적어도 하나의 올바른 프로세스가 교집합에 포함된다.

<br />

---

## Lemma 2. If f + 1 correct processes lock value v in round r0 (lockedValue = v and lockedRound = r0), then in all rounds r > r0, they send PREVOTE for id(v) or nil.
> `f + 1` 개의 올바른 프로세스가 v 값을 r0 라운드에서 잠금(`lockedValue = v and lockedRound = r0`) 했다면, 모든 r > r0 라운드에서 id(v) 또는 nil 에 대한 PREVOTE 를 보낸다.
> <br />
> 예를 들어, 5개의 노드가 있는 네트워크에서 3개의 노드가 값 1을 잠근다고 가정해 보면 나머지 2개의 노드는 다음 라운드부터 1에 대한 투표를 보낼 것이라는 말.(과반수 이상이 동의했기 때문에)

이  ‘Lemma2’는 `proposal → pre-vote → pre-commit` 에서 `pre-vote` 단계를 설명한다. 

- [*Base step*] r = r0 + 1
    
    C를 f + 1의 투표권을 가진 올바른 프로세스의 집합이라고 했을 때, 22번과 28번 규칙에 따라 C 집합의 프로세스는 v와 다른 값에 대한 PROPOSAL을 수락할 수 없으며, 따라서 **v′ ̸= v**인 경우 ⟨PREVOTE, heightp, r, id(v′)⟩ 메시지를 보낼 수 없다. 
    
    > **v′ ̸= v : 제안된 값 v와 다른 값인 v′가 제안되었다는 것을 의미**
    > 
    
    ```
    // 22번 규칙
    upon ⟨PROPOSAL, hp, roundp, v, −1⟩ from proposer(hp, roundp) while stepp = propose do 
    	
    	// 제안된 값 v가 유효하고, 잠긴 라운드가 -1이거나 잠긴 값이 v와 동일한 경우
    	if valid(v) ∧ (lockedRoundp = −1 ∨ lockedV aluep = v) then
    		broadcast ⟨PREVOTE, hp, roundp, id(v)⟩ // PREVOTE(값이 있는 투표)
    	else
    		broadcast ⟨PREVOTE, hp, roundp, nil⟩ // nil(아무 값이 없는 투표)
    	stepp ← prevote
    
    // 28번 규칙
    upon ⟨PROPOSAL, hp, roundp, v, vr⟩ from proposer(hp, roundp) AND 2f + 1 ⟨PREVOTE, hp, vr, id(v)⟩ while stepp = propose∧(vr ≥ 0∧vr < roundp) do
    	
    	// 제안된 값 v가 유효하고, 잠긴 라운드가 제안된 라운드보다 작거나 잠긴 값이 제안된 값과 동일한 경우
    	if valid(v) ∧ (lockedRoundp ≤ vr ∨ lockedV aluep = v) then 
    		broadcast ⟨PREVOTE, hp, roundp, id(v)⟩ // PREVOTE(값이 있는 투표)
    	else
    		broadcast ⟨PREVOTE, hp, roundp, nil⟩ // nil(아무 값이 없는 투표)
    	stepp ← prevote
    ```
    
- [*Induction step*] from r1 to r1 + 1
    - 각 라운드에서 블록을 단 하나만 커밋하도록 보장하는 방법을 설명한다.
    - C 집합의 프로세스 중 어느 것도 r1 + 1 라운드까지 id(v)와 nil 이외의 값에 대해 PREVOTE를 보내지 않았다고 가정
    - Lemma 1이 r1 + 1 라운드에도 적용됨을 증명
        - Lemma 1에서 ‘어떤 프로세스도 v와 다른 값 v'에 대해 2f + 1 PREVOTE 메시지를 수신할 수 없다’고 명시
        - r0 ≤ r ≤ r1 라운드에서 2f + 1 개의 PREVOTE 메시지를 받을 수 있는 v′ ̸= v 값이 없다고 명시
        - 값 v′가 있다면, C 집합의 프로세스 중 적어도 하나는 r0 ≤ r ≤ r1 라운드에서 v′에 대한 PREVOTE를 보냈을 것이고, 이는 C 집합의 프로세스 중 어느 것도 r1 + 1 라운드까지 id(v)와 nil 이외의 값에 대해 PREVOTE를 보내지 않았다는 가정에 위배
            - 모든 프로세스는 v값이 잠기면서 이전 라운드에서 동일한 값을 가지게 된다.
            
            ```
            // (29번 째 줄) 제안된 값 v가 유효하고, 잠긴 라운드가 제안된 라운드보다 작거나 잠긴 값이 제안된 값과 동일한 경우
            	if valid(v) ∧ (lockedRoundp ≤ vr ∨ lockedV aluep = v) then 
            		broadcast ⟨PREVOTE, hp, roundp, id(v)⟩ // PREVOTE(값이 있는 투표)
            	else
            		broadcast ⟨PREVOTE, hp, roundp, nil⟩ // nil(아무 값이 없는 투표)
            	stepp ← prevote
            ```

<br />

---

## Lemma 3. Algorithm 1 satisfies Agreement.
알고리즘 1은 합의를 만족합니다.

> Proof: Let round r0 be the first round of height h such that some correct process p decides v. We now prove that if some correct process q decides v′ in some round r ≥ r0, then v = v′.

증명: 높이 h의 첫 번째 라운드인 r0에서 일부 올바른 프로세스 p가 v를 결정한다고 가정합니다. 이제 r ≥ r0인 어떤 라운드에서 일부 올바른 프로세스 q가 v'를 결정한다면, v = v'임을 증명하겠습니다.

```
[내용 정리]
1. 높이 h의 첫 번째 라운드인 r0에서 어떤 올바른 프로세스 p가 값 v를 결정했다고 가정합니다.
2. 이제 r0 이후의 어떤 라운드 r에서 다른 올바른 프로세스 q가 값 v'를 결정했다고 가정합니다.
(올바른 합의로 인해 값 v' 이 잠기면 다른 프로세스들도 해당 라운드에서 v'으로 결정된다.)
3. 이 증명에서는 v와 v'가 항상 같음을 증명하려고 합니다.
```

- 합의 알고리즘이 한번 결정된 값은 변경되지 않아야 하기 때문에, 합의에 도달할 때 결정된 값이 일관되는지를 증명하는 것.
- 합의의 ‘일관성’을 보여주는 부분.

<br />

### r = r0 인 경우 (q가 라운드 r0에서 v'를 결정한 경우)
> 관련 수도코드
> <br />
> ```
> // 49번째 줄
> // proposer(hp, r)으로부터 ⟨PROPOSAL, hp, r, v, ∗⟩ 메시지와 2f + 1개의 ⟨PRECOMMIT, hp, r, id(v)⟩ 메시지를 받을 때까지 실행
> upon ⟨PROPOSAL, hp, r, v, ∗⟩ from proposer(hp, r) AND 2f + 1 ⟨PRECOMMIT, hp, r, id(v)⟩ while decisionp[hp] = nil do
>	if valid(v) then
>		decisionp [hp ] = v
>		hp←hp+1
>		reset lockedRoundp , lockedV aluep , validRoundp and validV aluep to initial values and empty message log 
>		StartRound(0)
>  ```

- q ⇒ 2f + 1개의 ⟨PRECOMMIT, hp, r0, id(v')⟩ 메시지를 받았다.
- p ⇒ 2f + 1개의 ⟨PRECOMMIT, hp, r0, id(v)⟩ 메시지를 받았다.
- Lemma 1 에 의하여, 2f + 1개의 투표권을 가진 메시지의 두 집합은 적어도 하나의 올바른 프로세스에서 교차한다는 것을 의미하기 때문에
    - 올바른 프로세스는 한 라운드에서 단 하나의 PRECOMMIT 메시지를 보내므로, `v = v'` 이다.

<br />

### [모순 증명] r > r0 인 경우
p와 q의 동작을 비교하고, 모순을 이용하여 증명

1. 첫번째 상황
- (위의 '49 규칙'에 의하여) p ⇒ 2f + 1개의 ⟨PRECOMMIT, hp, r0, id(v)⟩ 메시지를 받았다.
    - 값 v를 결정했다
- 즉, 적어도 f + 1 투표 권한에 해당하는 올바른 프로세스들이 라운드 r0에서 값 v를 잠근 후 이러한 메시지를 보냈습니다 (i). 이러한 메시지 집합을 C로 표시

<br />

2. 두번째 상황 (다음 라운드)
- 역시, q ⇒ 2f + 1개의 ⟨PRECOMMIT, hp, r0, id(v')⟩ 메시지를 받았다.
    - 값 v'를 결정했다
    - q 역시 과반수 이상의 메시지를 받았다. ⇒ 2f + 1
        - 이 부분에서 모순이 발생!
- 악의적 프로세스의 투표 권한은 f 이하이므로, 일부 올바른 프로세스 c가 이러한 메시지 중 하나를 보냈다.
    - 한 번 투표한 프로세스는 그 이후에 다시 투표하지 않아야 한다.
    - 이미 r0에서 값 v를 결정하기 위해 투표를 한 f + 1개 이상의 올바른 프로세스들이 그 이후에 다시 투표를 진행할 필요가 없기 때문
- 즉, 알고리즘이 올바른 합의를 보장하기 위해 동일한 값을 선택하도록 강제하는 역할

<br />

---

## Lemma 4. Algorithm 1 satisfies Validity.
정리 4. 알고리즘 1은 유효성을 만족합니다.

> Proof: Trivially follows from the rule at line 50 which ensures that only valid values can be decided.

*증명*: 유효한 값만 결정할 수 있도록 보장하는 50행의 규칙에서 간단히 따릅니다.

- 알고리즘이 항상 유효한 값을 선택한다는 것을 보여줌.
    - 유효한 값이란, “합의된 값”.
- 즉, 알고리즘 논리에 따라 적절한 조건을 충족하는지

```
upon ⟨PROPOSAL, hp, r, v, ∗⟩ from proposer(hp, r) AND 2f + 1 ⟨PRECOMMIT, hp, r, id(v)⟩ while decisionp[hp] = nil do

// 50번째 줄
	if valid(v) then // 이 부분! 값 v가 유효한 값인지 확인함.
		decisionp [hp ] = v
		hp←hp+1
		reset lockedRoundp , lockedV aluep , validRoundp and validV aluep to initial values and empty message log 
StartRound(0)
```

<br />

---

다음에 계속...
