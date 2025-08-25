---
layout: single
title: "[우테코] 프리코스 5기 1주차 연습 - 4번"
categories:
  - education
sidebar:
  nav: "sidebar-category"
---

[문제 4번. 청개구리](https://github.com/woowacourse-precourse/java-onboarding/blob/main/docs/PROBLEM4.md)


``` java
package onboarding;

public class Problem4 {
    public static String solution(String word) {
        char[] answer = word.toCharArray();
        char[] arrLower = new char[26];
        char[] arrUpper = new char[26];

        for (int i = 0; i < 26; i++) {
            arrLower[i] = (char) ('a' + i);
            arrUpper[i] = (char) ('A' + i);
        }

        for (int j = 0; j < answer.length; j++) {
            if (answer[j] >= 'A' && answer[j] <= 'Z') {
                int index = new String(arrUpper).indexOf(answer[j]);
                answer[j] = arrUpper[25 - index];
            } else if (answer[j] >= 'a' && answer[j] <= 'z') {
                int index = new String(arrLower).indexOf(answer[j]);
                answer[j] = arrLower[25 - index];
            }
        }

        return new String(answer);
    }
}
```

이렇게 했다가 `for`문 두 번 쓰는 게 좀...이상한가 싶어서

``` java
package onboarding;

public class Problem4 {
    public static String solution(String word) {
        char[] answer = word.toCharArray();
        char[] arrLower = "abcdefghijklmnopqrstuvwxyz".toCharArray();
        char[] arrUpper = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".toCharArray();


        for (int j = 0; j < answer.length; j++) {
            if (answer[j] >= 'A' && answer[j] <= 'Z') {
                int index = new String(arrUpper).indexOf(answer[j]);
                answer[j] = arrUpper[25 - index];
            } else if (answer[j] >= 'a' && answer[j] <= 'z') {
                int index = new String(arrLower).indexOf(answer[j]);
                answer[j] = arrLower[25 - index];
            }
        }


        return new String(answer);
    }
}
```

이렇게 바꿨다. 둘 다 테스트는 통과~

#### 새로 알게 된 사실
- `소문자 - 32 = 대문자`, `대문자 + 32 = 소문자`
- 배열에서 인덱스 찾으려면 문자열로 바꾸고 `(new String) indexOf`

오노 뒤늦게 예외 또 빠트려서 추가했다. 정신차려~~

``` java
if (word.isEmpty() || word.length() > 1000)
    throw new IllegalArgumentException("word 길이가 1이상 1000이하여야 합니다.");
```