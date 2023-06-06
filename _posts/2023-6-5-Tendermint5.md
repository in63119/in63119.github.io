---
title: BlockChain Core - The latest gossip on BFT consensus 5
author: IN
date: 2023-6-5 14:31:00 +0800
categories: [Blogging, Blockchain]
tags: [BlockChain, Core, Consensus, Tendermint, BFT]
pin: true
---

<br />

> 자료 : [The latest gossip on BFT consensus](https://in-nft.s3.ap-northeast-2.amazonaws.com/The+latest+gossip+on+BFT+consensus.pdf)
> <br />
> [지난 포스트 - The latest gossip on BFT consensus 4](https://in63119.github.io/posts/CoreStudyTendermint4/)

<br />

---

<br />

# III. TENDERMINT CONSENSUS ALGORITHM
> In this section we present the Tendermint Byzantine fault-tolerant consensus algorithm. The algorithm is specified by the pseudo-code listing in Algorithm 1. We present the algorithm as a set of upon rules that are executed atomically. 

이 섹션에서는 텐더민트 비잔틴 내결함성 합의 알고리즘을 소개합니다. 알고리즘은 알고리즘 1의 의사 코드 목록으로 지정됩니다. 저희는 이 알고리즘을 원자적으로 실행되는 *규칙*의 집합으로 제시합니다.

```
컴퓨터 과학(CS)에서 원자적(atomic)이라는 용어는 시스템이 작업을 완료하지 못하면 시작조차 하지 않을 것임을 말한다. 
원자성은 데이터 무결성을 보장하는 데 중요하다. 모든 작업이 성공적으로 완료되거나 아무 작업도 수행되지 않도록 보장함으로써 데이터 무결성을 보장한다.
```

<br />

> We assume that processes exchange protocol messages using a gossip protocol and that both received and sent messages are stored in a local message log for every process. An upon rule is triggered once the message log contains messages such that the corresponding condition evaluates to true.

프로세스가 가십 프로토콜을 사용하여 프로토콜 메시지를 교환하고 수신 및 전송된 메시지가 모든 프로세스에 대해 로컬 메시지 로그에 저장된다고 가정합니다. 
메시지 로그에 해당 조건이 true로 평가되는 메시지가 포함되면 upon 규칙이 트리거됩니다.

```
"upon 규칙"은 특정 조건이 충족될 때 실행되는 규칙.
예를 들어, upon 규칙은 특정 조건이 충족될 때 메시지를 전송하거나 수신하도록 프로토콜을 구성하는 데 사용
```

<br />

> The condition that assumes reception of X messages of a particular type and content denotes reception of messages whose senders have aggregate voting power at least equal to X. For example, the condition 2f + 1 ⟨PRECOMMIT, hp, r, id(v)⟩, evaluates to true upon reception of PRECOMMIT messages for height hp, a round r and with value equal to id(v) whose senders have aggregate voting power at least equal to 2f + 1.

특정 유형 및 내용의 메시지 X개를 수신한다고 가정하는 조건은 발신자의 총 투표권이 적어도 X와 동일한 메시지를 수신하는 것을 나타냅니다. 예를 들어, **2f + 1 ⟨PRECOMMIT, hp, r, id(v)⟩ 조건은 hp 높이, r 라운드의 PRECOMMIT 메시지를 수신하고, id(v)와 동일한 값을 가지며, 송신자의 집합 투표 권한이 2f + 1 이상일 때 true로 평가**됩니다.

```
2f + 1 이상의 노드가 동일한 블록을 승인했을 때 트리거됩니다. 이 조건이 트리거되면 Tendermint 합의 알고리즘은 해당 블록을 커밋한다.
```

<br />

> Some of the rules ends with ”for the first time” constraint to denote that it is triggered only the first time a corresponding condition evaluates to true. This is because those rules do not always change the state of algorithm variables so without this constraint, the algorithm could keep executing those rules forever.

일부 규칙은 해당 조건이 처음으로 true로 평가되는 경우에만 트리거됨을 나타내는 **"for the first time"** 제약으로 끝납니다. 이는 이러한 규칙이 항상 알고리즘 변수의 상태를 변경하지는 않기 때문입니다. 이 제약 조건이 없으면 알고리즘은 영원히 이러한 규칙을 실행할 수 있습니다.

```
"for the first time" 제약은 특정 규칙이 한 번만 실행되도록 보장하는 제약이다.

이 규칙은 2f + 1 이상의 노드가 동일한 블록을 승인했을 때 실행된다. 그러나 이 규칙은 블록이 한 번만 커밋되도록 보장한다.
이는 조건이 true로 평가되는 첫 번째 경우에만 규칙이 실행되도록 보장하기 때문이다. 조건이 true로 평가된 후 다시 true로 평가되면 규칙이 다시 실행되지 않는다.

"for the first time" 제약은 알고리즘이 상태를 반복하지 않도록 하는 데 중요하다. 예를 들어, 알고리즘이 블록을 커밋하는 경우 "for the first time" 제약을 사용하여 블록이 한 번만 커밋되도록 할 수 있다.
```

<br />

> The variables with index p are process local state variables, while variables without index p are value placeholders. The sign ∗ denotes any value.

인덱스 p가 있는 변수는 프로세스별 상태 변수이고, 인덱스 p가 없는 변수는 값 자리 표시자입니다. 기호 '*'는 임의의 값을 나타냅니다.

<br />

> We denote with n the total voting power of processes in the system, and we assume that the total voting power of faulty processes in the system is bounded with a system parameter f. The algorithm assumes that n > 3f, i.e., it requires that the total voting power of faulty processes is smaller than one third of the total voting power. For simplicity we present the algorithm for the case n = 3f + 1.

시스템 내 프로세스의 총 투표권을 n으로 표시하고, 시스템 내 결함이 있는 프로세스의 총 투표권은 시스템 파라미터 f로 제한된다고 가정합니다. 이 알고리즘은 n > 3f, 즉 결함이 있는 프로세스의 총 투표권이 총 투표권의 1/3보다 작아야 한다고 가정합니다. 간단하게 설명하기 위해 n = 3f + 1인 경우에 대한 알고리즘을 제시합니다.

```
이 단락은 시스템의 대부분의 프로세스가 합의에 도달할 수 있도록 하는 투표 알고리즘에 관한 것이다.
알고리즘은 시스템에 특정 수의 오류 프로세스가 있고 이러한 오류 프로세스의 총 투표 권한이 모든 프로세스의 총 투표 권한의 1/3 미만이라고 가정한다.
그리고 난 후,  모든 프로세스의 투표를 계산하고 투표의 과반수가 특정 결정에 찬성하는 경우 해당 결정을 합의로 취한다.

위의 논문에서는 알고리즘이 시스템에 3f + 1개의 프로세스가 있는 경우 단순화(효율 최적화)할 수 없다고 말하고 있다.

다음은 알고리즘에 순서를 준 것이다. 
1. 시스템의 총 프로세스 수를 n이라고 합니다.
2. 시스템의 오류 프로세스 수를 f라고 합니다.
3. n>3f라고 가정합니다.
4. 각 프로세스에서 vi는 프로세스 i의 투표입니다.
5. 가능한 각 결정에 대한 투표 수를 계산합니다.
6. 투표의 과반수가 특정 결정에 찬성하는 경우 해당 결정을 합의로 취합니다.
```

<br />

```
앞에서 나온 2f+1과 3f+1의 차이를 알아보자.

투표의 결과가 정상적인지 확인하는 과정을 알고리즘이 최소화(단순화) 시켜야만 네트워크의 속도가 빠를것이다. 중요한 것은 "투표의 과반수가 특정 결정에 찬성하는 경우 해당 결정을 합의로 취하는 부분"

2f+1 : 오류 프로세스(f) 수 만큼의 정상적인 프로세스 수가 있어야 합의 도출이 가능하고, 단순화가 가능하다.(투표를 해도 정상적인 과반수를 도출할 수 있으니까)
3f+1 :  그런데 시스템에 3f + 1개의 프로세스가 있는 경우 알고리즘을 단순화할 수 없습니다. 이 경우 알고리즘은 모든 프로세스의 투표를 계산하고 투표의 과반수가 특정 결정에 찬성하는지 확인하는 것 외에도 오류 프로세스가 잘못된 결정에 투표할 가능성을 고려해야 하기 때문이다. 
```

<br/>
<br/>

> The algorithm proceeds in rounds, where each round has a dedicated proposer. The mapping of rounds to proposers is known to all processes and is given as a function proposer(h, round), returning the proposer for the round round in the consensus instance h.

알고리즘은 라운드별로 진행되며, 각 라운드마다 특정한 제안자가 있습니다. 라운드와 제안자 사이의 매핑은 모든 프로세스에게 알려져 있으며, proposer(h, round)라는 함수로 제공됩니다. 이 함수는 합의 인스턴스 h의 라운드 round에 대한 제안자를 반환합니다.

- h는 인스턴스의 높이.

<br/>

> We assume that the proposer selection function is weighted round- robin, 

우리는 제안자 선택 함수가 **가중치를 고려한 라운드 로빈 방식**으로 작동한다고 가정합니다. 

```
가중치 라운드 로빈은 모든 프로세스에 가중치를 부여하고, 각 프로세스는 할당된 양의 시간을 실행한 후 다음 프로세스로 넘어가는 스케줄링 알고리즘.
가중치는 프로세스의 중요도에 따라 다를 수 있으며, 가중치가 높은 프로세스는 더 많은 시간을 할당받는다.
```

<br/>

> where processes are rotated proportional to their voting power. The internal protocol state transitions are triggered by message reception and by expiration of timeouts. There are three timeouts in Algorithm 1: timeoutPropose, timeoutPrevote and timeoutPrecommit. The timeouts prevent the algorithm from blocking and waiting forever for some condition to be true, ensure that processes continuously transition between rounds, and guarantee that eventually (after GST) communication between correct processes is timely and reliable so they can decide. The last role is achieved by increasing the timeouts with every new round r, i.e, timeoutX (r) = initT imeoutX + r ∗ timeoutDelta; they are reset for every new height (consensus instance).

여기서 프로세스는 투표 파워에 비례하여 순환됩니다. 내부 프로토콜 상태 전이는 메시지 수신 및 타임아웃 만료에 의해 트리거됩니다. 알고리즘 1에는 세 가지 타임아웃이 있습니다. timeoutPropose, timeoutPrevote 및 timeoutPrecommit. 이러한 타임아웃은 알고리즘이 블로킹되거나 어떤 조건이 참이 될 때까지 영원히 기다리지 않도록 하며, 프로세스가 지속적으로 라운드 간 전이하도록 보장하며, 올바른 프로세스 간 통신이 GST(Greatest Seen Time) 이후로 시기적절하고 신뢰성 있게 결정할 수 있도록 합니다. 마지막 역할은 모든 새로운 라운드 r마다 타임아웃을 증가시켜 구현됩니다. 즉, timeoutX(r) = initTimeoutX + r * timeoutDelta입니다. 이 타임아웃은 새로운 높이(합의 인스턴스)마다 재설정됩니다.

<br/>

> Processes exchange the following messages in Tendermint: PROPOSAL, PREVOTE and PRECOMMIT.

Tendermint에서 프로세스들은 PROPOSAL, PREVOTE, PRECOMMIT 메시지를 교환합니다.

- PROPOSAL 메시지는 제안자가 제안하고자 하는 블록의 정보를 담고 있다. 
- PREVOTE 메시지는 네트워크의 노드들이 제안된 블록에 대해 투표하는 메시지이다. 
- PRECOMMIT 메시지는 네트워크의 노드들이 제안된 블록에 대해 최종적으로 투표하는 메시지.

<br/>

> The PROPOSAL message is used by the proposer of the current round to suggest a potential decision value, while PREVOTE and PRECOMMIT are votes for a proposed value.
> <br/>
> According to the classification of consensus algorithms from [10], Tendermint, like PBFT [7] and DLS [6], belongs to class 3, so it requires two voting steps (three communication exchanges in total) to decide a value.

PROPOSAL 메시지는 현재 라운드의 제안자가 잠재적인 결정값을 제안하는 데 사용되며, PREVOTE와 PRECOMMIT은 제안된 값에 대한 투표입니다. [10]의 합의 알고리즘 분류에 따르면 Tendermint는 PBFT [7]와 DLS [6]와 마찬가지로 **클래스 3**에 속하므로 값을 결정하기 위해 두 가지 투표 단계 (총 세 가지 통신 교환)가 필요합니다.

```
비잔틴 장애 허용 알고리즘은 일반적으로 3가지 클래스로 분류된다.
 
1. 클래스 1: 만일 악의적인 동작을 하는 노드의 수가 전체 노드 수의 1/3 이하라면 일관된 결정을 내릴 수 있습니다. 예를 들어, Practical Byzantine Fault Tolerance (PBFT) 알고리즘이 여기에 속합니다.
2. 클래스 2: 만일 악의적인 동작을 하는 노드의 수가 전체 노드 수의 1/2 이하라면 일관된 결정을 내릴 수 있습니다. 예를 들어, Delegated Byzantine Fault Tolerance (DBFT) 알고리즘이 여기에 속합니다.
3. 클래스 3: 만일 악의적인 동작을 하는 노드의 수가 전체 노드 수의 1/3 이하라면 일관된 결정을 내릴 수 있으며, 추가적인 제한 사항이 필요합니다. 예를 들어, Tendermint 프로토콜이 여기에 속합니다. 이러한 알고리즘은 2단계의 투표 과정과 3단계의 통신 교환을 통해 값을 결정합니다.

여기서 Tendermint는 PBFT와 DLS와 마찬가지로 클래스 3에 속한다.는 말이다.
```

---

다음은 '텐더민트 알고리즘 수도코드'이다. 이 수도코드는 논문 내용에서 빠짐없이 나온다. 위에서 설명하는 내용을 증명할 수 있다. 

<br/>

![스터디1_page-0006](https://github.com/in63119/in63119.github.io/assets/65399118/45f60db5-9bb3-467d-b05c-390cd846e1b9)

---

다음에 계속...
