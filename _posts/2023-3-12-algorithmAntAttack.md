---
title: Algorithm - 개미 군단
author: IN
date: 2023-3-12 22:56:00 +0800
categories: [Blogging, Algorithm]
tags: [Javascript, Algorithm, Recursion]
pin: true
---

## 문제

개미 군단이 사냥을 나가려고 합니다. 개미군단은 사냥감의 체력에 딱 맞는 병력을 데리고 나가려고 합니다. 
<br />
장군개미는 5의 공격력을, 병정개미는 3의 공격력을 일개미는 1의 공격력을 가지고 있습니다. 예를 들어 체력 23의 여치를 사냥하려고 할 때, 일개미 23마리를 데리고 가도 되지만, 장군개미 네 마리와 병정개미 한 마리를 데리고 간다면 더 적은 병력으로 사냥할 수 있습니다. 
<br />
사냥감의 체력 hp가 매개변수로 주어질 때, 사냥감의 체력에 딱 맞게 최소한의 병력을 구성하려면 몇 마리의 개미가 필요한지를 return하도록 solution 함수를 완성해주세요.

<br />

## 입출력 예

| hp     | result | 
| ------ | ------ | 
| 23     | 5      | 
| 24     | 6      | 
| 999    | 201    | 

<br />

## 풀이
주말동안 재귀함수를 공부하고 프로그래머스에 적용해보았다.
<br />
근래 [크립토좀비](https://github.com/in63119/cryptoZombie)를 공부하면서 나만의 web3 게임을 만들어볼까 생각중이었는데,
<br />
비슷한 느낌이라 재밌게 풀었다.

<br />

이번 문제를 재귀가 아닌 다른 방식으로 풀면, `몫(ex. Math.floor(hp / ant))`과 `나머지(ex. hp % ant)`를 구해서 
<br />
풀어야 될 것 같은데, 게임적인 느낌이 나도록 재귀함수인 `attack`을 만들어보았다.

<br />

## 내 코드

```js
function solution(hp) {
    var answer = 0;
    
    // 각 유닛의 공격력
    const general = 5;
    const knight = 3;
    const normal = 1;
    
    // 몬스터의 hp가 공격력보다 낮을 때까지 공격
    // 일개미가 1이기 때문에 재귀 탈출 가능
    const attack = (unit) => {
        if (hp < unit) {
            return hp;
        }
        
        hp = hp - unit;
        answer++;
        
        return attack(unit);
    }
    
    // 장군이 먼저 공격
    attack(general);
    // 병정개미 그 다음에 공격
    attack(knight);
    // 일개미가 그 다음에 공격
    attack(normal);
    
    return answer;
}
```

<br />
<br />

## 결과

<img width="450" alt="스크린샷 2023-03-05 오후 3 17 01" src="https://user-images.githubusercontent.com/65399118/224550295-4bad4cf2-bdd4-4274-b059-0dce81ef49be.png">

<br />
<br />

## 다른 사람의 풀이

```js
function solution(hp) {
    return Math.floor(hp/5)+Math.floor((hp%5)/3)+(hp%5)%3;
}
```
