---
layout: single
title: "[프로그래머스] Lv.0 - 배열의 길이를 2의 거듭제곱으로 만들기"
categories:
  - codingtest
sidebar:
  nav: "sidebar-category"
---

Q. 정수 배열 arr이 매개변수로 주어집니다. arr의 길이가 2의 정수 거듭제곱이 되도록 arr 뒤에 정수 0을 추가하려고 합니다. arr에 최소한의 개수로 0을 추가한 배열을 return 하는 solution 함수를 작성해 주세요.
<br />
배열의 길이가 2의 거듭제곱이면 그대로, 아니면 바로 다음에 오는 2의 제곱수만큼 배열을 늘리고 0을 채워넣는 문제!

``` java
class Solution {
    public int[] solution(int[] arr) {
        int[] answer = {};
        int len = arr.length;
        int count = 0;
        
        while (len % 2 == 0) {
            len /= 2;
            count++;
        }
        
        if (len == 1) {
            return arr;
        }
        else { 
            answer = new int[(int) (Math.pow(2,count + 2))];
            for (int i = 0; i < arr.length; i++) {
                answer[i] = arr[i];
            }
        }
        return answer;
    }
}
```

처음엔 이런식으로 배열의 길이를 2로 계속 나누고, 나눌 때마다 카운트 업하고<br />
마지막에 남는 값이 1이면 배열을 그대로 리턴<br />
아니면 새로 제곱수 길이의 배열을 만들어서 넣어주고 어쩌구...했는데, 45점 나와서 ㅠ 으잉? 하고 세수하고 보니까<br />
바보아냐! 저렇게 하면 이미 길이가 제일 작아져있잖음 ㅠㅠ 바보바보 멍청쓰... 그래서 

``` java 
class Solution {
    public int[] solution(int[] arr) {
        int len = arr.length;
        
        int powLength = 1;
        while (powLength < len) {
            powLength *= 2;
        }
        
        int[] answer = new int[powLength];
        for (int i = 0; i < len; i++) {
            answer[i] = arr[i];
        }
        for (int i = len; i < powLength; i++) {
            answer[i] = 0;
        }
        
        return answer;
    }
}
```

이런식으로.... 일단 제곱수부터 구하고! 그 제곱수 길이 배열을 만들어서 복사... 처음에 한 거랑 비슷한 거 같은데,
뭔가 처음 거는 계속 저 이미 제곱수일 때는 그대로 리턴하는 거에 갇혀서 생각을 못했다 ㅠㅠ 어쨌든 해결!