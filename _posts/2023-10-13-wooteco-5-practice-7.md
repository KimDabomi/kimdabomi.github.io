---
layout: single
title: "[우테코] 프리코스 5기 1주차 연습 - 7번"
categories:
  - education
sidebar:
  nav: "sidebar-category"
---

[문제 7번. 친구 추천하기](https://github.com/woowacourse-precourse/java-onboarding/blob/main/docs/PROBLEM7.md)

친구리스트 - 내 친구 : 함께 아는 친구 <-- 중복없음<br />
추천리스트 - 친구 : 추천점수 <-- 방문횟수(+1) + 겹치는 친구(+10)<br />
이미 내 친구인 사람 리스트<br />
아이디는 1이상 30이하 소문자 문자열<br />
친구리스트와 방문자 리스트는 1이상 10000이하<br />
추천친구는 필수고, 추천점수가 0점이면 추천 노노, 점수 동일하면 이름 오름차순, 겹치는 친구에서 내이름은 빼<br />

아그리고 이번 거부터 기능별로 커밋하려고 노력중이다! 일단 쉬운 검사부터!


``` java
// User 제외
public static void isUser(String user, List<List<String>> friends) {
    for (List<String> friend : friends) {
        String friend1 = friend.get(0);
        String friend2 = friend.get(1);

        if (friend1.equals(user)) {
            userFriends.add(friend2);
        } else if (friend2.equals(user)) {
            userFriends.add(friend1);
        }
    }
}
```

``` java
// 아이디 길이, 소문자 검사
public static void validUserId (String user, List<List<String>> friends) {
    for (List<String> friend : friends) {
        String friend1 = friend.get(0);
        String friend2 = friend.get(1);
        String[] UserIds = {user, friend1, friend2};

        for (String UserId : UserIds) {
            if(!(Pattern.compile("^[a-z]*$").matcher(UserId).matches() && (UserId.length() >= 1 && UserId.length() <= 30))) {
                throw new IllegalArgumentException("사용자 아이디는 1 이상 30 이하의 소문자여야 합니다.");
            }
        }
    }
}
```

``` java
// 친구리스트, 방문자리스트 길이 검사
public static void validListSize (List<List<String>> friends, List<String> visitors) {
    if(!(friends.size() >= 1 && friends.size() <= 10) || !(visitors.size() >= 1 && visitors.size() <= 10000))
        throw new IllegalArgumentException("친구리스트와 방문자리스트는 1이상 10000이하까지만 가능합니다.");
}
```

``` java
// 추천친구 필수, 0점 제외 검사
public static void validRecommend(Map<String, Integer> friendsAndRecommendScore) {
    if (friendsAndRecommendScore.isEmpty()) {
        throw new IllegalArgumentException("추천할 친구가 없습니다.");
    }

    // 0점 제외
    friendsAndRecommendScore.entrySet().removeIf(entry -> entry.getValue() == 0);

    if (friendsAndRecommendScore.isEmpty()) {
        throw new IllegalArgumentException("추천 점수가 0점인 친구는 추천할 수 없습니다.");
    }
}
```

아니... 어제 계속 하는데 분명 추천친구 리스트에 넣는 과정에서 이미 나랑 친구인 아이디는 빼는 걸로 로직을 계속 썼는데 분명...

``` java
// 추천 친구 추가
public static void recommendFriends(String user, List<List<String>> friends, List<String> visitors) {
    for (List<String> friendPair : friends) {
        String friend1 = friendPair.get(0);
        String friend2 = friendPair.get(1);

        if (userFriends.contains(friend1) && !userFriends.contains(friend2) && !friend2.equals(user)) {
            friendsAndRecommendScore.put(friend2, friendsAndRecommendScore.getOrDefault(friend2, 0) + 10);
        } else if (userFriends.contains(friend2) && !userFriends.contains(friend1) && !friend1.equals(user)) {
            friendsAndRecommendScore.put(friend1, friendsAndRecommendScore.getOrDefault(friend1, 0) + 10);
        }
    }
}
```

아니 봐바 친구1,2 가지고 와서! 1이 내친구에 포함이 돼있고 2는 안돼있고! 2가 내자신도 아니면!!!! 친구 2를 추천에 넣고!!<br />
2가 내친구에 포함이 돼있고 1은 안돼있고! 1이 내자신도 아니면!!!! 친구 1을 추천에 넣는다고!!!<br />
분명 걸렀는데 자꾸 내친구들도 추천리스트에 뜬다.... 이문제만 한 두시간 잡고 있다가 결국 포기하고 슬픈취침을 청했다..<br />
근데 갑자기 자기 전에 생각해보니까? 여기서 나온 문제가 아닐 수도 있는 거잖아...? 추천 - 방문자 - 결과 순으로 호출해서<br />
결과에 리스트 불러와서 정렬하는 부분에 추가했더니 된다...맙소사

``` java
// 추천점수 높은 사람들 가져와서 정렬
public static List<String> getTopRecommendations() {
    List<String> sortedRecommendations = new ArrayList<>(friendsAndRecommendScore.keySet());

    sortedRecommendations.removeAll(userFriends); // 내친구는 제외

    sortedRecommendations.sort((user1, user2) -> {
        int score1 = friendsAndRecommendScore.get(user1);
        int score2 = friendsAndRecommendScore.get(user2);

        if (score1 == score2) {
            return user1.compareTo(user2);
        }
        return score2 - score1;
    });

    return sortedRecommendations;
}
```

이래서 ... .디버깅 모드가 중요한 건데....내가 잘못했다........ 다음부턴 꼭 디버깅모드.....!<br />
이제 그냥 마구잡이로 써내려간 앞선 코드들을 리팩토링 해보려한다..!
