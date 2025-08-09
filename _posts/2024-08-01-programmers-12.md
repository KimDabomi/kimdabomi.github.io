---
layout: single
title: "[프로그래머스] Lv.2 - 피보나치 수"
categories:
  - codingtest
sidebar:
  nav: "sidebar-category"
---

💡 피보나치 수는 F(0) = 0, F(1) = 1일 때, 1 이상의 n에 대하여 F(n) = F(n-1) + F(n-2) 가 적용되는 수 입니다.<br />
2 이상의 n이 입력되었을 때, n번째 피보나치 수를 1234567으로 나눈 나머지를 리턴하는 함수, solution을 완성해 주세요.
<br />
<br />
단순하게.. 재귀로 피보나치 수 구현하고 1234567로 나눈 나머지 리턴했는데 시간초과랑 런타임에러가 났다 ㅠ<br />
그래서 피보나치 값을 리스트에 저장하고, 마지막 요소만 꺼내와서 나머지값 리턴해줬다.

``` python
def solution(n):
    result = fibo(n)
    return result[n] % 1234567
    
def fibo(n):
    fibList = [0, 1]
    if n >= 2:
        for i in range(2, n + 1):
            fibList.append(fibList[i - 2] + fibList[i - 1])
        
    return fibList
```
