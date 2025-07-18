---
layout: single
title: "[프로그래머스] Lv.2 - 짝지어 제거하기"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

💡 짝지어 제거하기는, 알파벳 소문자로 이루어진 문자열을 가지고 시작합니다. 먼저 문자열에서 같은 알파벳이 2개 붙어 있는 짝을 찾습니다. 그다음, 그 둘을 제거한 뒤, 앞뒤로 문자열을 이어 붙입니다. 이 과정을 반복해서 문자열을 모두 제거한다면 짝지어 제거하기가 종료됩니다. 문자열 S가 주어졌을 때, 짝지어 제거하기를 성공적으로 수행할 수 있는지 반환하는 함수를 완성해 주세요. 성공적으로 수행할 수 있으면 1을, 아닐 경우 0을 리턴해주면 됩니다.<br />
예를 들어, 문자열 S = baabaa 라면<br />
b aa baa → bb aa → aa →<br />
의 순서로 문자열을 모두 제거할 수 있으므로 1을 반환합니다.
<br />
<br />
처음에는 아래처럼 빈문자열이 될 때까지 문자열을 리스트로 바꿔서 같은 요소들은 지우고 다시 이어붙이는 작업을 했는데, 시간초과!!! 으아아악~

``` python
def solution(s):
    answer = 0
    
    while s:  
        charList = list(s)  
        i = 0
        while i < len(charList) - 1:
            if charList[i] == charList[i + 1]:
                charList[i] = ''
                charList[i + 1] = ''
                i = max(0, i - 1)  
            else:
                i += 1

        s = ''.join(charList) 
        
        if s == '':
            answer += 1  

    return answer
```

그래서 스택으로 해냄! <br />
스택이 비어있으면 일단 문자들을 넣고, 가장 위에 있는 문자와 지금 문자가 동일하면 꺼낸다.<br />
그렇게 해서 스택이 비어있으면 1을 리턴하고, 아니면 0을 리턴한다.

``` python
def solution(s):
    stack = []

    for ch in s:
        if stack and stack[-1] == ch:
            stack.pop() 
        else:
            stack.append(ch)

    return 1 if not stack else 0
```
