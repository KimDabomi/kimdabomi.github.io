---
layout: single
title: "[우테코] 프리코스 5기 1주차 연습 - 1번"
categories:
  - education
sidebar:
  nav: "sidebar-category"
---

우테코 6기 지원 후에 연습하려고 작년 거 연습!<br />
[문제 1번. 번호게임](https://github.com/woowacourse-precourse/java-onboarding/blob/main/docs/PROBLEM1.md)

### 1. 첫 구현은 으어어어엄청난 하드코딩..^^;;

``` java
package onboarding;

import java.util.List;
import java.util.stream.Stream;

class Problem1 {
    public static int solution(List<Integer> pobi, List<Integer> crong) {
        int answer = Integer.MAX_VALUE;

        int[] arrPobiLeft = Stream.of(String.valueOf(pobi.get(0)).split("")).mapToInt(Integer::parseInt).toArray();
        int[] arrPobiRight = Stream.of(String.valueOf(pobi.get(1)).split("")).mapToInt(Integer::parseInt).toArray();
        int[] arrCrongLeft = Stream.of(String.valueOf(crong.get(0)).split("")).mapToInt(Integer::parseInt).toArray();
        int[] arrCrongRight = Stream.of(String.valueOf(crong.get(1)).split("")).mapToInt(Integer::parseInt).toArray();

        int sumPobiLeft = 0;
        int sumPobiRight = 0;
        int sumCrongLeft = 0;
        int sumCrongRight = 0;
        int timePobiLeft = 1;
        int timePobiRight = 1;
        int timeCrongLeft = 1;
        int timeCrongRight = 1;


        for (int i = 0; i < arrPobiLeft.length; i++) {
            sumPobiLeft += arrPobiLeft[i];
        }
        for (int i = 0; i < arrPobiRight.length; i++) {
            sumPobiRight += arrPobiRight[i];
        }
        for (int i = 0; i < arrCrongLeft.length; i++) {
            sumCrongLeft += arrCrongLeft[i];
        }
        for (int i = 0; i < arrCrongRight.length; i++) {
            sumCrongRight += arrCrongRight[i];
        }
        for (int i = 0; i < arrPobiLeft.length; i++) {
            timePobiLeft *= arrPobiLeft[i];
        }
        for (int i = 0; i < arrPobiRight.length; i++) {
            timePobiRight *= arrPobiRight[i];
        }
        for (int i = 0; i < arrCrongLeft.length; i++) {
            timeCrongLeft *= arrCrongLeft[i];
        }
        for (int i = 0; i < arrCrongRight.length; i++) {
            timeCrongRight *= arrCrongRight[i];
        }

        System.out.printf("포비왼합: %d", sumPobiLeft);
        System.out.printf("포비우합: %d", sumPobiRight);
        System.out.printf("포비왼곱: %d", timePobiLeft);
        System.out.printf("포비우곱: %d", timePobiRight);
        System.out.printf("크롱왼합: %d", sumCrongLeft);
        System.out.printf("크롱우합: %d", sumCrongRight);
        System.out.printf("크롱왼곱: %d", timeCrongLeft);
        System.out.printf("크롱우곱: %d", timeCrongRight);


        int maxPobi = sumPobiLeft;
        if (sumPobiRight > maxPobi) maxPobi = sumPobiRight;
        if (timePobiLeft > maxPobi) maxPobi = timePobiLeft;
        if (timePobiRight > maxPobi) maxPobi = timePobiRight;
        System.out.printf("pobi: %d", maxPobi);

        int maxCrong = sumCrongLeft;
        if (sumCrongRight > maxCrong) maxCrong = sumCrongRight;
        if (timeCrongLeft > maxCrong) maxCrong = timeCrongLeft;
        if (timeCrongRight > maxCrong) maxCrong = timeCrongRight;
        System.out.printf("crong: %d", maxCrong);


        if (maxPobi == maxCrong)
            answer = 0;
        else if (maxPobi > maxCrong)
            answer = 1;
        else if (maxPobi < maxCrong)
            answer = 2;
        if (isException(pobi) || isException(crong)) {
            System.out.println("페이지는 연속해야 합니다.");
            answer = -1;
        }

        return answer;
    }
}
```
뷁 ㅠ 되긴 되는데 너무 정신없고 비효율적이라.. 나눠본다! 아직 자바 함수도 다 모르는 상태라 벼락치고 해본다.

#### 새로 알게 된 사실
- `List<>`의 요소는 `get(i)`로 받아오기, 길이는 `size()`
- `Stream.of` 는 잘 사용하면 엄청 유용할 것 같다. 맵이 이렇게 쉽게 돌아가다니..
- `Math.max` 도 처음 써봤다. 자바스크립트랑 똑같네! 알고리즘 책 보면서 구현한 바보같은 나의 모습.. 하지만 해낸 게 중요하지~

``` java
package onboarding;

import java.util.List;
import java.util.stream.Stream;

class Problem1 {
    public static int solution(List<Integer> pobi, List<Integer> crong) {
        int answer = Integer.MAX_VALUE;
        int maxPobi = maxScore(pobi);
        int maxCrong = maxScore(crong);

        if (maxPobi == maxCrong)
            answer = 0;
        else if (maxPobi > maxCrong)
            answer = 1;
        else
            answer = 2;
        

        if (isException(pobi) || isException(crong)) {
            System.out.println("페이지는 연속해야 합니다.");
            answer = -1;
        }

        return answer;
    }

    public static boolean isException (List<Integer> ListPage) {
        return ListPage.get(0) + 1 != ListPage.get(1);
    }

    public static int getScore (int page) {
        int[] arrDigits = Stream.of(String.valueOf(page).split(""))
                .mapToInt(Integer::parseInt)
                .toArray();

        int sum = 0;
        int time = 1;
        for (int i = 0; i < arrDigits.length; i++) {
            sum += arrDigits[i];
            time *= arrDigits[i];
        }

        return Math.max(sum, time);
    }

    public static int maxScore (List<Integer> pages) {
        int leftScore = getScore(pages.get(0));
        int rightScore = getScore(pages.get(1));
        return Math.max(leftScore, rightScore);
    }
}
```

결과는 두 코드 모두 다 테스트 통과! 얼른 책 와서 문법부터 공부하고 싶다 ㅠㅠ<br />
마지막에 제한사항 하나 놓쳤길래 추가했다.

``` java
if (!(RightLength(pobi) && RightLength(crong))) {
    System.out.println("페이지의 개수는 2개여야 합니다.");
    answer = -1;
}

public static boolean RightLength (List<Integer> ListPage) {
    return ListPage.size() == 2;
}
```
