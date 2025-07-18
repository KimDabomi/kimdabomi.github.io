---
layout: single
title: "[프로그래머스] Lv.0 - 주사위 게임 3"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

``` java
class Solution {
    public int solution(int a, int b, int c, int d) {
   
    int[] dice = {a, b, c, d};
        int[] count = new int[7]; // 주사위의 숫자는 1~6이므로 크기 7의 배열을 사용

        for (int num : dice) {
            count[num]++;
        }

        if (count[a] == 2 && count[b] == 2) {
            return (a + b) * Math.abs(a - b);
        }

        
        for (int i = 1; i <= 6; i++) {
            if (count[i] == 4) {
                return 1111 * i;
            }
            
            if (count[i] == 3) {
                for (int j = 1; j <= 6; j++) {
                    if (count[j] == 1) {
                        return (10 * i + j) * (10 * i + j);
                    }
                }
            }
            
            if (count[i] == 2) {
                int q = 0, r = 0;
                for (int j = 1; j <= 6; j++) {
                    if (count[j] == 1) {
                        if (q == 0) {
                            q = j;
                        } else {
                            r = j;
                            break;
                        }
                    }
                }
                return q * r;
            }
        }
        return Math.min(a, Math.min(b, Math.min(c, d)));

    }
}
```

처음엔.. 단순 배열에다가 카운터를 늘렸는데, 이렇게 하니까 문제에 나와있는 테스트 케이스는 되는데 채점하면 몇몇개가 안됐다 ㅠㅠ<br />
그리고 이렇게 하면 이상하게 2개 + 2개 일 때는 count[i] == 2 이프문에 넣어도 안된다...!! 그래서 한창 괴로워하던 중...<br />
나의 뇌섹남이 조언을 해주셔따...해시맵 쓰면 될 거 같다고!!!!?!

``` java
import java.util.*;

class Solution {
    public int solution(int a, int b, int c, int d) {
        int[] dice = {a, b, c, d};
        HashMap<Integer, Integer> countMap = new HashMap<>();

        for (int num : dice) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }

        if (countMap.containsValue(4)) {
            for (int key : countMap.keySet()) {
                if (countMap.get(key) == 4) {
                    return 1111 * key;
                }
            }
        }

        
        if (countMap.containsValue(3)) {
            for (int key : countMap.keySet()) {
                if (countMap.get(key) == 3) {
                    for (int otherKey : countMap.keySet()) {
                        if (countMap.get(otherKey) == 1) {
                            return (10 * key + otherKey) * (10 * key + otherKey);
                        }
                    }
                }
            }
        }
        
        if (countMap.containsValue(2)) {
            if (countMap.size() == 2) {
                int p = 0, q = 0;
                for (int key : countMap.keySet()) {
                    if (p == 0) {
                        p = key;
                    } else {
                        q = key;
                    }
                }
                return (p + q) * Math.abs(p - q);
            }
        
            if (countMap.size() == 3) {
                int value = 1;
                for (int key : countMap.keySet()) {
                    if (countMap.get(key) == 1) {
                        value *= key;
                    } 
                }
                return value;
            }
        }
        return Math.min(a, Math.min(b, Math.min(c, d)));
    }
}
```

그래서 이렇게 하니까 됐다!! 해시맵은 해도해도 헷갈린다... 처음에 정의하는 것도 <int,int> 이렇게 했는데 바보 아니야 어제 지네릭스 공부해놓고?! 인마! 여튼 그래도 했다.. 이거때문에 화가 잔뜩나서 쪼꼬렛도 먹었는데 풀고 자서 다행이다 히히 
