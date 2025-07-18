---
layout: single
title: "[프로그래머스] Lv2. - 최솟값 만들기"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

💡 길이가 같은 배열 A, B 두개가 있습니다. 각 배열은 자연수로 이루어져 있습니다.<br />
배열 A, B에서 각각 한 개의 숫자를 뽑아 두 수를 곱합니다. 이러한 과정을 배열의 길이만큼 반복하며, 두 수를 곱한 값을 누적하여 더합니다. 이때 최종적으로 누적된 값이 최소가 되도록 만드는 것이 목표입니다. (단, 각 배열에서 k번째 숫자를 뽑았다면 다음에 k번째 숫자는 다시 뽑을 수 없습니다.)
<br />
<br />
두 배열을 하나는 오름차순, 하나는 내림차순으로 정렬하여 각 인덱스의 요소끼리 곱한 후 더해주면 된다.

``` python
def solution(A,B):
    answer = 0
    
    A.sort()
    B.sort(reverse=True)
    
    for i in range(len(A)):
        answer += A[i] * B[i]
    
    return answer
```
