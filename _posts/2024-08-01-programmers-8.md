---
layout: single
title: "[프로그래머스] Lv.2 - JadenCase 문자열 만들기"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

💡 JadenCase란 모든 단어의 첫 문자가 대문자이고, 그 외의 알파벳은 소문자인 문자열입니다. 단, 첫 문자가 알파벳이 아닐 때에는 이어지는 알파벳은 소문자로 쓰면 됩니다. (첫 번째 입출력 예 참고)<br />
문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.
<br />
<br />
맨 앞 글자와 공백을 기준으로 다음 문자만 대문자로 바꿔주면 되는 줄 알고 쉽다 생각했다 히히<br />
하지만! 첫번째 문자가 아닌 중간에 있는 문자들은 다 소문자로 바꿔줘야된다.<br />
그래서 일단 문자열을 쪼개서 배열을 만들 때 다 소문자로 만들어서 넣고, 맨 앞 글자와 공백 뒤에 있는 것만 다시 대문자로 바꿔줬다.

``` python
def solution(s):
    charList = []
    
    for ch in s:
        charList.append(ch.lower())
        
    for i in range(1, len(charList) - 1):
        if charList[i] == ' ':
            charList[i + 1] = charList[i + 1].upper()

            
    charList[0] = charList[0].upper()
    
    return ''.join(charList)
```
