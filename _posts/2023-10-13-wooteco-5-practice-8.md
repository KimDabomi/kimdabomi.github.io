---
layout: single
title: "[우테코] 프리코스 5기 온보딩 끗!"
categories:
  - education
sidebar:
  nav: "sidebar-category"
---

내 온보딩 전에 2주차까진 풀어보려고 시작했지만, 하다가 어려워서 내 기수 시작도 전에 김막막됐다 ㅠ<br />
하지만 그래도 다 해내서 기분이 좋다! 리팩토링을 막 엄청나겐 못하겠고... 처음 해보는 거라 일단 예외 클래스만 따로 뺐다.

``` java
package onboarding;

import java.util.List;
import java.util.Map;
import java.util.regex.Pattern;

public class Exception {

    public static boolean isException (List<Integer> ListPage) {
        return ListPage.get(0) + 1 != ListPage.get(1);
    }

    public static boolean sizeOfList (List<Integer> ListPage, int size) {
        return ListPage.size() == size;
    }

    public static void rangeNumber (int number, int min_num, int max_num) {
        if (number < min_num || number > max_num)
            throw new IllegalArgumentException(number + "는 " + min_num + " 이상 " + max_num + " 이하인 자연수만 가능합니다.");
    }

    public static void rangeString (String str, int min_len, int max_len) {
        if (str.length() < min_len || str.length() > max_len)
            throw new IllegalArgumentException(str + "의 길이는 " + min_len +" 이상 " + max_len + " 이하만 가능합니다.");

    }

    public static void rangeList(List<String> list, int min_size, int max_size) {
        if(!(list.size() >= min_size && list.size() <= max_size))
            throw new IllegalArgumentException(list + "의 크기는 " + min_size + " 이상 " + max_size + "이하만 가능합니다.");
    }

    public static void isValidEmail(String email, String domain, int min_len, int max_len) {
        if (!(email.endsWith(domain) || (email.length() >= min_len && email.length() < max_len)))
            throw new IllegalArgumentException(domain + " 도메인으로만 신청가능합니다. 이메일의 전체 길이는 " + min_len + "자 이상 " + max_len + "자 미만만 가합니다.");
    }

    public static void isValidDoubleListSize (List<List<String> > doubleList, int min_size, int max_size) {
        if (!(doubleList.size() >= min_size && doubleList.size() <= max_size))
            throw new IllegalArgumentException(doubleList + "의 크기는 " + min_size +" 이상 " + max_size + " 이하만 가능합니다.");
    }

    public static void isValidNickname (String nickname) {
        if (!Pattern.compile("^[가-힣]*$").matcher(nickname).matches())
            throw new IllegalArgumentException("닉네임은 한글만 가능합니다.");
    }


    public static void isUser(List<String> List, String user, List<List<String>> friends) {
        for (List<String> friend : friends) {
            String friend1 = friend.get(0);
            String friend2 = friend.get(1);

            if (friend1.equals(user)) {
                List.add(friend2);
            } else if (friend2.equals(user)) {
                List.add(friend1);
            }
        }
    }

    public static void isValidUserId (String user, List<List<String>> friends, int min_len, int max_len) {
        for (List<String> friend : friends) {
            String friend1 = friend.get(0);
            String friend2 = friend.get(1);
            String[] UserIds = {user, friend1, friend2};

            for (String UserId : UserIds) {
                if(!(Pattern.compile("^[a-z]*$").matcher(UserId).matches() && (UserId.length() >= min_len && UserId.length() <= max_len))) {
                    throw new IllegalArgumentException("아이디는 " + min_len + " 이상 " + max_len +" 이하의 소문자만 가능합니다.");
                }
            }
        }
    }

    public static void isValidRecommend(Map<String, Integer> mapList) {
        mapList.entrySet().removeIf(entry -> entry.getValue() == 0);
        if (mapList.isEmpty()) {
            throw new IllegalArgumentException("추천 점수가 0점인 친구는 추천할 수 없습니다.");
        }
    }
}
```

겹치는 조건이 많다 생각했는데., 여러번 쓴 건 단순 문자열길이나 숫자범위, 리스트사이즈밖에 없었다.<br />
그래도 재사용블럭을 최초로 만들었다는 것이...!!<br />
근데 java.lang 자체에도 Exception이라는 클래스가 있어서 다른 이름으로 하는 게 좋다는 걸 푸시를 하고 알아버렸다ㅠㅠ<br />
다음부터는 패키지이름을 붙여서 쓸 것... 그래도 자바 아예 처음인데 온보딩을 해냈다는 것에 뿌듯하다!!<br />
물론 검색해서 참고도 많이하고 문법같은 거 뭐가 잘못됐는지 지피티한테도 물어보고 했지만, 최대한 혼자 하려고 노력했다....무사히 끗!<br />
이제 내 6기 온보딩은 두렵지 않아!!!!!
