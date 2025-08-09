---
layout: single
title: "[프로그래머스] Lv.2 - N개의 최소공배수"
categories:
  - codingtest
sidebar:
  nav: "sidebar-category"
---

💡 두 수의 최소공배수(Least Common Multiple)란 입력된 두 수의 배수 중 공통이 되는 가장 작은 숫자를 의미합니다. 예를 들어 2와 7의 최소공배수는 14가 됩니다. 정의를 확장해서, n개의 수의 최소공배수는 n 개의 수들의 배수 중 공통이 되는 가장 작은 숫자가 됩니다. n개의 숫자를 담은 배열 arr이 입력되었을 때 이 수들의 최소공배수를 반환하는 함수, solution을 완성해 주세요.
<br />
<br />
두 수를 곱하고 최대공약수로 나누면 최소공배수니까 정의하고~ 두개씩 비교해서 최소공배수 구하면 된다.

``` python
import math

def lcm(a, b):
    return abs(a * b) // math.gcd(a, b)

def solution(arr):
    answer = lcm(arr[0], arr[1])
    
    for i in range(2, len(arr)):
        answer = lcm(answer, arr[i])
    
    return answer
```
