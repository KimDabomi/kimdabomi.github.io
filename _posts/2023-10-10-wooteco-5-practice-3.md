---
layout: single
title: "[우테코] 프리코스 5기 1주차 연습 - 3번"
categories:
  - education
sidebar:
  nav: "sidebar-category"
---

[문제 3번. 369 게임](https://github.com/woowacourse-precourse/java-onboarding/blob/main/docs/PROBLEM3.md)


``` java
package onboarding;

public class Problem3 {
    public static int solution(int number) {
        int answer = 0;

        if (number < 1 || number > 10000)
            throw new IllegalArgumentException("number는 1이상 10,000이하인 자연수여야 합니다.");

        for (int i = 1; i <= number; i++) {
            int temp = i;
            while (temp > 0) {
                if (temp % 10 != 0 && (temp % 10) % 3 == 0) {
                    answer++;
                }
                temp /= 10;
            }
        }
        return answer;
    }
}
```

엄청난 이프삽질을 하다가 무슨 경우의 수가 세자리수에는 16개가 나오길래 이건 아닌가 싶어 검색을 좀 했다...

#### 새로 알게 된 사실
- `/= 10`을 하면 십의 자리수가 일의 자리수가 되는구나!!! 맨날 `/=`자체가 뭔가 자주 안쓰는 생소한 느낌이라 생각조차 못했다..
- 여태 예외 처리하는 걸 그냥 문구만 나오게 했는데 예외를 던지는 게 있었다니! 하긴 다 있을텐데 찾아볼 생각을 안했다. 반성반성 `throw new IllegalArgumentException` 명심명심
