---
layout: single
title: "[우테코] 프리코스 5기 1주차 연습 - 2번"
categories:
  - education
sidebar:
  nav: "sidebar-category"
---

[문제 2번. 브라운 암호](https://github.com/woowacourse-precourse/java-onboarding/blob/main/docs/PROBLEM2.md)


``` java
package onboarding;


public class Problem2 {
    public static String solution(String cryptogram) {
        StringBuilder answer = new StringBuilder(cryptogram);

        for (int i = 0; i < answer.length() - 1; i++) {
            if (answer.charAt(i) == answer.charAt(i + 1)) {
                answer.deleteCharAt(i);
                answer.deleteCharAt(i);
                i = Math.max(0, i - 2);
            }
        }

        for (int i = 0; i < 1; i++) {
            if (answer.charAt(i) == answer.charAt(i + 1)) {
                answer.deleteCharAt(i);
                answer.deleteCharAt(i);
            }
        }

        return answer.toString();
    }
}
```

#### 새로 알게 된 사실
- 스트링 조작이 쉽지 않았는데,  `StringBuilder`로 새로 생성해주면 자바스크립트 처럼 굉장히 직관적인 명령어들이 있다.
- 삭제를 `i -> i + 1` 했는데, 보니까 `i`를 삭제하면 기존 `i + 1`이 다시 `i`가 돼서 이상해진 거였다! 이마 탁

``` java
package onboarding;


public class Problem2 {
    public static String solution(String cryptogram) {
        StringBuilder answer = new StringBuilder(cryptogram);

        for (int i = 0; i < answer.length() - 1; i++) {
            if (answer.charAt(i) == answer.charAt(i + 1)) {
                answer.deleteCharAt(i);
                answer.deleteCharAt(i);
                i = Math.max(0, i - 2);
                for (int j = 0; j < 1; j++) {
                    if (answer.charAt(j) == answer.charAt(j + 1)) {
                        answer.deleteCharAt(j);
                        answer.deleteCharAt(j);
                    }
                }
            }
        }

        return answer.toString();
    }
}
```

`for - if`문을 합쳐봤지만 그닥...와닿지 않는다.

``` java
package onboarding;


public class Problem2 {
    public static String solution(String cryptogram) {
        StringBuilder answer = new StringBuilder(cryptogram);

        for (int i = 0; i < answer.length() - 1; i++) {
            if (answer.charAt(i) == answer.charAt(i + 1)) {
                answer.deleteCharAt(i);
                answer.deleteCharAt(i);
                i = Math.max(0, i - 2);
                if (answer.length() == 2) {
                    answer.deleteCharAt(i);
                    answer.deleteCharAt(i);
                }
            }
        }
        return answer.toString();
    }
}
```

현재는 이것이 그나마 나은듯하다. 세 코드 다 테스트는 잘된다.
