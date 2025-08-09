---
layout: single
title: "[프로그래머스] Lv.2 - 최댓값과 최솟값"
categories:
  - codingtest
sidebar:
  nav: "sidebar-category"
---

💡 문자열 s에는 공백으로 구분된 숫자들이 저장되어 있습니다. str에 나타나는 숫자 중 최소값과 최대값을 찾아 이를 "(최소값) (최대값)"형태의 문자열을 반환하는 함수, solution을 완성하세요. 예를들어 s가 "1 2 3 4"라면 "1 4"를 리턴하고, "-1 -2 -3 -4"라면 "-4 -1"을 리턴하면 됩니다.
<br />
<br />
- 공백을 기준으로 쪼개서 리스트를 만들고, 각 요소들을 숫자로 바꾼 다음 오름차순 정렬 
- 첫 번째 요소와 마지막 요소를 다시 문자로 바꿔서 공백과 함께 리턴해주면 된다.

``` python
def solution(s):
    answer = ''
    
    strList = s.split(" ")
    numList = []
    
    for str in strList:
        numList.append(int(str))
    
    numList.sort()
    
    min = "{}".format(numList[0]) 
    max = "{}".format(numList[len(numList) - 1])
    
    return min + " " + max
```
