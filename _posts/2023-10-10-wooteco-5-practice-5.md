---
layout: single
title: "[우테코] 프리코스 5기 1주차 연습 - 5번"
categories:
  - education
sidebar:
  nav: "sidebar-category"
---

[문제 5번. 출금](https://github.com/woowacourse-precourse/java-onboarding/blob/main/docs/PROBLEM5.md)

이건 진짜 ㅋㅋㅋㅋㅋ 하면서도 아닌 걸 알면서 일단 해보는 나...

``` java
package onboarding;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Problem5 {
    public static List<Integer> solution(int money) {
        List<Integer> answer = new ArrayList<>();

        if (money < 1 || money > 1000000)
            throw new IllegalArgumentException("money는 1 이상 1,000,000 이하인 자연수이다.");

        if (money / 50000 != 0) {
            answer.add(money / 50000);
            money = money - 50000 * (money / 50000);
        } else {
            answer.add(0);
        }

        if (money % 50000 != 0) {
            answer.add(money % 50000);
            money = money - 10000 * (money % 50000);
        } else {
            answer.add(0);
        }

        if (money / 5000 != 0) {
            answer.add(money / 5000);
            money = money - 5000 * (money / 5000);
        } else {
            answer.add(0);
        }

        if (money % 5000 != 0) {
            answer.add(money % 5000);
            money = money - 1000 * (money % 5000);
        } else {
            answer.add(0);
        }

        if (money / 500 != 0) {
            answer.add(money / 500);
            money = money - 500 * (money / 500);
        } else {
            answer.add(0);
        }

        if (money % 500 != 0) {
            answer.add(money % 500);
            money = money - 100 * (money % 500);
        } else {
            answer.add(0);
        }

        if (money / 50 != 0) {
            answer.add(money / 50);
            money = money - 50 * (money / 50);
        } else {
            answer.add(0);
        }

        if (money % 50 != 0) {
            answer.add(money % 50);
            money = money - 10 * (money % 50);
        } else {
            answer.add(0);
        }

        if (money / 5 != 0) {
            answer.add(money / 5);
            money = money - 5 * (money / 5);
        } else {
            answer.add(0);
        }

        if (money % 500 != 0) {
            answer.add(money % 5);
            money = money - 1 * (money % 5);
        } else {
            answer.add(0);
        }

        return answer;
    }
}
```

당연히 결과는 실패다~ 아근데 아까 3번 한 거랑 비슷하게 하면 될 거 같은데.... 그리고 애초에 저렇게 이프문이 많이 나오는 게 말이 안된다...<br />
일단 5만원권~1원까지 배열 만들어서 생각해보기로 했다.

``` java
package onboarding;

import java.util.ArrayList;
import java.util.List;

public class Problem5 {
    public static List<Integer> solution(int money) {
        int[] units = {50000, 10000, 5000, 1000, 500, 100, 50, 10, 1};
        List<Integer> answer = new ArrayList<>();

        for (int i = 0; i < units.length; i++) {
            int count = money / units[i];
            answer.add(count);
            money -= count * units[i];
        }
        
        return answer;
    }
}
```

이렇게 하니까 성공! 근데 왜 똑같이 나눠주고 똑같이 애드해준 다음 똑같이 빼줬는데 왜 안된걸까...하드코딩하면서 일단 로직을 이해하고 줄여보려는데 하드코딩도 잘 안된다 ㅠㅋㅋㅋㅋㅋㅋ

#### 새로 알게 된 사실
- `ArrayList`에 초기값을 주면 안된다! 맨처음에 `new ArrayList<>(9)` 줬다가 안돼서 계속 헤맴
- 중간에 값을 계속 삽입해줘야될 때에는 `Array(X)`, `ArrayList(O)`
