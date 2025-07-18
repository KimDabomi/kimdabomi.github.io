---
layout: single
title: "[프로그래머스] Lv.1 - 과일 장수"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

🥸 과일 장수가 사과 상자를 포장하고 있습니다. 사과는 상태에 따라 1점부터 k점까지의 점수로 분류하며, k점이 최상품의 사과이고 1점이 최하품의 사과입니다. 사과 한 상자의 가격은 다음과 같이 결정됩니다.
- 한 상자에 사과를 m개씩 담아 포장합니다.
- 상자에 담긴 사과 중 가장 낮은 점수가 p (1 ≤ p ≤ k)점인 경우, 사과 한 상자의 가격은 p * m 입니다.
과일 장수가 가능한 많은 사과를 팔았을 때, 얻을 수 있는 최대 이익을 계산하고자 합니다.(사과는 상자 단위로만 판매하며, 남는 사과는 버립니다)<br />
예를 들어, k = 3, m = 4, 사과 7개의 점수가 [1, 2, 3, 1, 2, 3, 1]이라면, 다음과 같이 [2, 3, 2, 3]으로 구성된 사과 상자 1개를 만들어 판매하여 최대 이익을 얻을 수 있습니다.
- (최저 사과 점수) x (한 상자에 담긴 사과 개수) x (상자의 개수) = 2 x 4 x 1 = 8
사과의 최대 점수 k, 한 상자에 들어가는 사과의 수 m, 사과들의 점수 score가 주어졌을 때, 과일 장수가 얻을 수 있는 최대 이익을 return하는 solution 함수를 완성해주세요.

| k | m | score                          | result |
|---|---|--------------------------------|--------|
| 3 | 4 | [1, 2, 3, 1, 2, 3, 1]          | 8      |
| 4 | 3 | [4, 1, 2, 2, 4, 4, 4, 4, 1, 2, 4, 2] | 33     |

``` java 
import java.util.*;
import java.util.stream.IntStream;


class Solution {
    public int solution(int k, int m, int[] score) {
        int answer = 0;
        List<Integer> list = new ArrayList<Integer>();
        
        Arrays.sort(score);
        
        for (int i=score.length-1; i>=score.length%m; i--) {
            if (score[i] <= k) {
                list.add(score[i]);
            }
        }
        
        for (int i=0; i<list.size();){
            int min = list.get(i);
            for (int j=0; j<i+m; j++) {
                if (list.get(j) < min) {
                    min = list.get(j);
                }
            }
            answer += min * m;
            i += m;
        }
        
        System.out.print(list);

        return answer;
    }
}
```

좀 더럽긴 하지만 일단 정렬해주고, 배열의 길이만큼 나누고 나서 남은 부분은 버려주는 리스트를 생성!<br />
그리고 리스트에서 m단위로 잘라서 최솟값과 m을 곱한 값을 answer에 더해줬다.<br />
근데 이렇게 하니까 시간 초과 문제로 한 다섯개가 통과가 안됨 ㅠㅠ<br />
그래서 스트림으로 바꿔주니까 됐다! 안그래도 코드리뷰하면서 스트림이 유용하다 생각했는데, 바로 써먹으니까 좋네~

``` java 
import java.util.*;
import java.util.stream.IntStream;


class Solution {
    public int solution(int k, int m, int[] score) {
        int answer = 0;
        List<Integer> list = new ArrayList<Integer>();
        
        Arrays.sort(score);
        
        for (int i=score.length-1; i>=score.length%m; i--) {
            if (score[i] <= k) {
                list.add(score[i]);
            }
        }
        
        answer = IntStream.range(0, (list.size() + m - 1) / m)
                .map(i -> list.subList(i * m, Math.min((i + 1) * m, list.size()))
                .stream()
                .min(Integer::compare)
                .orElse(0))
                .sum() * m;

        return answer;
    }
}
```
