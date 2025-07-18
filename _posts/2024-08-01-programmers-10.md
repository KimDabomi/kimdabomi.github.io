---
layout: single
title: "[프로그래머스] Lv.2 - 숫자의 표현"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

💡Finn은 요즘 수학공부에 빠져 있습니다. 수학 공부를 하던 Finn은 자연수 n을 연속한 자연수들로 표현 하는 방법이 여러개라는 사실을 알게 되었습니다. 예를들어 15는 다음과 같이 4가지로 표현 할 수 있습니다.
- 1 + 2 + 3 + 4 + 5 = 15
- 4 + 5 + 6 = 15
- 7 + 8 = 15
- 15 = 15
자연수 n이 매개변수로 주어질 때, 연속된 자연수들로 n을 표현하는 방법의 수를 return하는 solution를 완성해주세요.
<br />
처음에는 등차수열의 합으로... 어캐어캐 해보려다가 그냥 하나씩 접근<br />
일단 i 고정에 1씩 더해가는 j 값을 더해주고 n 과 같아지면 answer에 1씩 더해준다! <br />
for > while 이여서 시간초과 뜰 줄 알았는데 그냥 됐다...!

``` python
def solution(n):
    answer = 0
    
    for i in range(1, n + 1):
        summ = 0
        j = i
        
        while summ < n:
            summ += j 
            j += 1
            
        if summ == n:
            answer += 1
            
    return answer
```
