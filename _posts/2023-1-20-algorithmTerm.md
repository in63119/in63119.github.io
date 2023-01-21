---
title: Algorithm - 개인정보 수집 유효기간
author: IN
date: 2023-1-20 21:24:00 +0800
categories: [Blogging, Algorithm]
tags: [Javascript, Algorithm]
pin: true
---

## 문제
고객의 약관 동의를 얻어서 수집된 1~n번으로 분류되는 개인정보 n개가 있습니다. 약관 종류는 여러 가지 있으며 각 약관마다 개인정보 보관 유효기간이 정해져 있습니다. 당신은 각 개인정보가 어떤 약관으로 수집됐는지 알고 있습니다. 수집된 개인정보는 유효기간 전까지만 보관 가능하며, 유효기간이 지났다면 반드시 파기해야 합니다.
<br />
<br />
예를 들어, A라는 약관의 유효기간이 12 달이고, 2021년 1월 5일에 수집된 개인정보가 A약관으로 수집되었다면 해당 개인정보는 2022년 1월 4일까지 보관 가능하며 2022년 1월 5일부터 파기해야 할 개인정보입니다. 
<br />
당신은 오늘 날짜로 파기해야 할 개인정보 번호들을 구하려 합니다.
<br />
<br />
모든 달은 28일까지 있다고 가정합니다.
<br />
<br />
<br />
다음은 오늘 날짜가 `2022.05.19`일 때의 예시입니다.
<br />

| 약관 종류 | 유효기간 |
|-----------|----------|
| A         | 6 달     |
| B         | 12 달    |
| C         | 3 달     |

<br />

| 번호 | 개인정보 수집 일자 | 약관 종류 |
|------|--------------------|-----------|
| 1    | 2021.05.02         | A         |
| 2    | 2021.07.01         | B         |
| 3    | 2022.02.19         | C         |
| 4    | 2022.02.20         | C         |

<br />

- 첫 번째 개인정보는 A약관에 의해 2021년 11월 1일까지 보관 가능하며, 유효기간이 지났으므로 파기해야 할 개인정보입니다.
- 두 번째 개인정보는 B약관에 의해 2022년 6월 28일까지 보관 가능하며, 유효기간이 지나지 않았으므로 아직 보관 가능합니다.
- 세 번째 개인정보는 C약관에 의해 2022년 5월 18일까지 보관 가능하며, 유효기간이 지났으므로 파기해야 할 개인정보입니다.
- 네 번째 개인정보는 C약관에 의해 2022년 5월 19일까지 보관 가능하며, 유효기간이 지나지 않았으므로 아직 보관 가능합니다.

<br />

따라서 파기해야 할 개인정보 번호는 [1, 3]입니다.

오늘 날짜를 의미하는 문자열 `today`, 약관의 유효기간을 담은 1차원 문자열 배열 `terms`와 수집된 개인정보의 정보를 담은 1차원 문자열 배열 `privacies`가 매개변수로 주어집니다. 이때 파기해야 할 개인정보의 번호를 오름차순으로 1차원 정수 배열에 담아 return 하도록 solution 함수를 완성해 주세요.

<br />
<br />

## 입출력 예

|   today         |   terms                   |   privacies                                                                         |   result     |
|-----------------|---------------------------|-------------------------------------------------------------------------------------|--------------|
|   "2022.05.19"  |   ["A 6", "B 12", "C 3"]  |   ["2021.05.02 A", "2021.07.01 B", "2022.02.19 C", "2022.02.20 C"]                  |   [1, 3]     |
|   "2020.01.01"  |   ["Z 3", "D 5"]          |   ["2019.01.01 D", "2019.11.15 Z", "2019.08.02 D", "2019.07.01 D", "2018.12.28 Z"]  |   [1, 4, 5]  |

<br />
<br />

## 내 코드

```js
function solution(today, terms, privacies) {
    let answer = [];
    let count = 1;
    
    function calcDate(term, date) {
        term = Number(term);
        let todayDate = today;
        
        todayDate = todayDate.split('.');
        let todayYy = Number(todayDate[0]);
        let todayMm = Number(todayDate[1]);
        let todayDd = Number(todayDate[2]);
        
        date = date.split('.');
        let termYy = Number(date[0]);
        let termMm = Number(date[1]);
        let termDd = Number(date[2]);  
        
        // 월수가 12월이 넘어가면 
        if (termMm + term > 12) {
            termYy = termYy + 1;
            termMm = termMm + term - 12;
        } else {
            termMm = termMm + term;
        }
        
        // 일자별로 계산
        todayYy = todayYy * 28 * 12;
        todayMm = todayMm * 28;
        termYy = termYy * 28 * 12;
        termMm = termMm * 28;
        
        // 계산한 값이 today가 높으면 파기(false). 아니면 보관(true).
        if (todayYy + todayMm + todayDd >= termYy + termMm + termDd) {
            return false;
        } else {
            return true;
        }
    }
    
    
    for (let i = 0; i < privacies.length; i++) {
        for (let k = 0; k < terms.length; k++) {
            let term = terms[k].split(' ')[1];
            let termType = terms[k].split(' ')[0];
            let privacieDate = privacies[i].split(' ')[0];
            let privacieType = privacies[i].split(' ')[1];
            
            if (termType === privacieType) {
                // 파기해야(false) 하는 값만 담는다.
                if (!calcDate(term, privacieDate)) {
                    answer.push(count);
                }
            }
        }
        
        count++;
    }
    
    return answer;
}

```

<br />
<br />

## 결과
<img width="400" alt="스크린샷 2023-01-20 오후 9 05 23" src="https://user-images.githubusercontent.com/65399118/213866226-f1e9b61d-6551-4e06-8f7d-65256b3e2a7e.png">

<br />
<br />

## Develop
다른 사람의 풀이를 보면 **split을 했을 때 바로 분할하여 변수에 저장**하는 것과
<br />
**이중반복문이 아닌 forEach에 두 매개변수를 이용**한 부분이 나의 코드보다 훨씬 더 간결하고, 효율적이었다.

<br />

```js
function solution(today, terms, privacies) {
  var answer = [];
  var [year, month, date] = today.split(".").map(Number);
  var todates = year * 12 * 28 + month * 28 + date;
  var t = {};
  terms.forEach((e) => {
    let [a, b] = e.split(" ");
    t[a] = Number(b);
  });
  privacies.forEach((e, i) => {
    var [day, term] = e.split(" ");
    day = day.split(".").map(Number);
    var dates = day[0] * 12 * 28 + day[1] * 28 + day[2] + t[term] * 28;
    if (dates <= todates) answer.push(i + 1);
  });
  return answer;
}
```
