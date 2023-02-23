---
title: Upgradeable Contract
author: IN
date: 2023-2-18 22:10:00 +0800
categories: [Blogging, Algorithm]
tags: [Javascript, Algorithm, LinkedList, DataStructure]
pin: true
---

**Linked List**는 아래에 있는 이미지처럼 연결되어있는 선형 데이터 구조이다.
데이터는 노드에 저장되며, 리스트의 맨 앞 부분을 **head** 맨 뒤에 있는 부분을 **tail**이라고 하며 다음(next)노드의 정보를 가지고 있다.


<br />

![](https://images.velog.io/images/in63119/post/4db9da8e-a515-43d6-a584-3ccebf1be72f/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202020-12-06%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%201.35.40.png)


이렇게 인접한 노드와 연결되어있는 구조가 **블록체인**과 유사하다.

<br />
<br />

## Where are linked lists used in real life?
Linked List는 실생활 많은 곳에서 사용된다. 그 중 대표적인 것이 바로, **웹 브라우저**이다.
특히 여러 사이트들을 방문한 후 생기는 **history**는 Linked List를 설명할 수 있는 가장 좋은 예라고 볼 수 있다.

<br />
<br />

## 코드구현

Linked list의 구현에서 중요한 점은 바로 다음 노드(next)에 대한**연결**이다.
> **head**와 **tail**의 '위치' 그리고 **next(다음노드)**는 무엇인지
추가할 때는 어디를 이어 붙여야 하는지,
> 삭제할 때는 어디를 어떻게 이어야 하는지

다음은 코드구현을 위한 의사코드이다. 

<br />

### 노드 추가
![](https://images.velog.io/images/in63119/post/7aacbe80-c42c-4495-a803-d6810a7e0f17/LinkedList_PPT.001.jpeg)

<br />

![](https://images.velog.io/images/in63119/post/f4de9b73-668e-45fe-b3c7-714df5efcdc8/LinkedList_PPT.002.jpeg)

<br />

![](https://images.velog.io/images/in63119/post/015268d8-c0ff-4bb2-b531-5621486e7673/LinkedList_PPT.003.jpeg)

<br />

![](https://images.velog.io/images/in63119/post/0c1f8aad-49e7-41ec-86b1-8e2910a18bef/LinkedList_PPT.004.jpeg)

<br />

### 노드 삭제
![](https://images.velog.io/images/in63119/post/3843bbd5-807b-43bb-acbe-93dfe52199a9/LinkedList_PPT.005.jpeg)

<br />

![](https://images.velog.io/images/in63119/post/2e2fe10b-8834-45ff-9f6e-da7a5ec6be79/LinkedList_PPT.006.jpeg)

<br />

![](https://images.velog.io/images/in63119/post/dec15211-3141-4080-a842-2ef3830e6a2c/LinkedList_PPT.007.jpeg)

<br />

### 노드 순환
![](https://images.velog.io/images/in63119/post/a3a457ee-db40-4fb3-8c79-07019b4542cd/LinkedList_PPT.008.jpeg)
