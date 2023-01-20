---
title: 알고리즘-개인정보 수집 유효기간
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
    
    // 개인 정보 수집일자(privacies) + 유효기간(terms) < today = true
    // 날짜의 연,월,일을 따로 분리해서 계산하는 
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
        }
        
        termMm = termMm + term;
        console.log(`계산된 값은 : ${termYy}.${termMm}.${termDd}`);
        
        if (termYy === todayYy && termMm > todayMm) {
            return false;
        } 
        if (termYy === todayYy && termMm === todayMm && termDd > todayDd) {
            return false;
        }
        
        return true;
    }
    
    
    for (let i = 0; i < privacies.length; i++) {
        for (let k = 0; k < terms.length; k++) {
            let term = terms[k].split(' ')[1];
            let termType = terms[k].split(' ')[0];
            let privacieDate = privacies[i].split(' ')[0];
            let privacieType = privacies[i].split(' ')[1];
            
            // 약관의 타입과 개인의 약관 타입이 맞으면 calcDate 실행
            if (termType === privacieType) {
                if(calcDate(term, privacieDate)) {
                    // calcDate가 true면 privacies의 순서를 리턴(i를 리턴하면 되는 것인데...)
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
<img width="792" alt="스크린샷 2023-01-20 오후 9 05 23" src="https://user-images.githubusercontent.com/65399118/213699419-a76c98e9-bee4-4f0f-9872-6d212317d6fb.png">