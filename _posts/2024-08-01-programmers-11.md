---
layout: single
title: "[프로그래머스] Lv.2 - 다음 큰 숫자"
categories:
  - codingtest
sidebar:
  nav: "sidebar-category"
---

💡 자연수 n이 주어졌을 때, n의 다음 큰 숫자는 다음과 같이 정의 합니다.
- 조건 1. n의 다음 큰 숫자는 n보다 큰 자연수 입니다.
- 조건 2. n의 다음 큰 숫자와 n은 2진수로 변환했을 때 1의 갯수가 같습니다.
- 조건 3. n의 다음 큰 숫자는 조건 1, 2를 만족하는 수 중 가장 작은 수 입니다.
예를 들어서 78(1001110)의 다음 큰 숫자는 83(1010011)입니다.<br />
자연수 n이 매개변수로 주어질 때, n의 다음 큰 숫자를 return 하는 solution 함수를 완성해주세요.
<br />
<br />
n을 이진수로 변환하고, 1의 개수를 센 다음에<br />
n에서 1씩 더하면서 이진수로 변환했을 때 1의 개수가 동일한 수가 나올 때까지 반복해서 찾아준 다음!<br />
다시 십진수로 변환해서 리턴~ 한방에 풀어서 기분 좋다잉

``` python
def solution(n):
    binN = bin(n)[2:]
    oneCount = binN.count("1")
    count = 0
    i = 1
    
    while count != oneCount:
        binNext = bin(n + i)[2:]
        count = binNext.count("1")
        i += 1
        
        if count == oneCount:
            return int(binNext, 2)
```
