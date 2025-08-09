---
layout: single
title: "[프로그래머스] Lv.1 - 문자열 내 마음대로 정렬하기"
categories:
  - codingtest
sidebar:
  nav: "sidebar-category"
---

🥸 문자열로 구성된 리스트 strings와, 정수 n이 주어졌을 때, 각 문자열의 인덱스 n번째 글자를 기준으로 오름차순 정렬하려 합니다. 예를 들어 strings가 ["sun", "bed", "car"]이고 n이 1이면 각 단어의 인덱스 1의 문자 "u", "e", "a"로 strings를 정렬합니다.
<br />

| strings                        | n | return                                 |
|-------------------------------|---|----------------------------------------|
| `["sun", "bed", "car"]`       | 1 | `["car", "bed", "sun"]`                |
| `["abce", "abcd", "cdx"]`     | 2 | `["abcd", "abce", "cdx"]`              |

해시맵 + 스트림을 써야겠다고 생각은 해냈다! 구현을 못해서 그렇지 ㅋㅋㅎㅋㅎㅎㅋ<br />
그래도 value기준으로 정렬하고 key값을 배열로 리턴까지는 수월했다.<br />
하지만 모두가 그랬듯 value값이 동일할 때 그 해당 항목들만 key값 기준으로 재배열했어야됐는데,<br />
저 thenComparing이라는 걸 몰라서 ㅠ 다른 방법으로 계속 실패하다가 결국 찾아냈따!  

``` java 

import java.util.*;
import java.util.stream.Collectors; // Collectors는 직접 임포트 해줘야 되나?

class Solution {
    public String[] solution(String[] strings, int n) {
        Map<String,String> map = new HashMap<>();
        List<String> answer = new ArrayList();
        
        for (int i = 0; i < strings.length; i++) {
        	// 원래 문자열은 key, n번째 문자는 value
            map.put(strings[i],Character.toString(strings[i].charAt(n)));
        }

        List<Map.Entry<String, String>> entries = map.entrySet().stream()
                .sorted(Map.Entry.<String, String>comparingByValue() // value기준 정렬
                        .thenComparing(Map.Entry.comparingByKey()))	// value가 같으면 key 기준으로 정렬
                .collect(Collectors.toList());
        
        for (Map.Entry<String, String> entry : entries) {
            answer.add(entry.getKey());
        }
        
        return answer.toArray(new String[0]);
    }
}
```
