---
layout: single
title: "[프로그래머스] Lv.2 - 멀리 뛰기"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

 💡 효진이는 멀리 뛰기를 연습하고 있습니다. 효진이는 한번에 1칸, 또는 2칸을 뛸 수 있습니다.<br />
칸이 총 4개 있을 때, 효진이는<br />
(1칸, 1칸, 1칸, 1칸)<br />
(1칸, 2칸, 1칸)<br />
(1칸, 1칸, 2칸)<br />
(2칸, 1칸, 1칸)<br />
(2칸, 2칸)<br />
의 5가지 방법으로 맨 끝 칸에 도달할 수 있습니다.<br />
멀리뛰기에 사용될 칸의 수 n이 주어질 때, 효진이가 끝에 도달하는 방법이 몇 가지인지 알아내, 여기에 1234567를 나눈 나머지를 리턴하는 함수, solution을 완성하세요. 예를 들어 4가 입력된다면, 5를 return하면 됩니다.
<br />
<br />
```
n = 0 --> 0
n = 1 / (1) --> 1
n = 2 / (1, 1) (2) --> 2
n = 3 / (1, 1, 1) (1, 2) (2, 1) --> 3
n = 4 / (1, 1, 1, 1) (1, 1, 2) (1, 2, 1) (2, 1, 1) (2, 2) --> 5
n = 5 / (1, 1, 1, 1) (1, 1, 1, 2) (1, 1, 2, 1) (1, 2, 1, 1) (2, 1, 1, 1) (1, 2, 2) (2, 1, 2) (1, 2, 2) --> 8
```
보니까 피보나치랑 똑같다! 피보나치로 바로 풀어버렸다.

``` python
def solution(n):
    arr = [0] * (n + 2)
    arr[1] = 1
    arr[2] = 2
    
    if n <= 2:
        return arr[n] % 1234567
    else:
        for i in range(3, n + 1):
            arr[i] = arr[i - 1] + arr[i - 2]
        return arr[n] % 1234567
```
