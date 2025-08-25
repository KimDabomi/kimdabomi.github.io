---
layout: single
title: "[우테코] 프리코스 5기 1주차 연습 - 6번"
categories:
  - education
sidebar:
  nav: "sidebar-category"
---

[문제 6번. 닉네임 중복검사](https://github.com/woowacourse-precourse/java-onboarding/blob/main/docs/PROBLEM6.md)

오매 큰일이다. 여태껏 제한사항이 많아봐야 3개였는데, 이건 무려 두배! 뭐 어쩌겠나 일단 해봐야지...<br />
그리고 여태 거의 프로그래머스 푸는 느낌으로 풀었는데, 작년의 회고록들을 보니까 그러면 안될 것 같다.<br />
제한사항에 대한 검사도 하나도 안했네 ㅠ 일단 6번부터 그렇게 해본다.<br />

- 닉네임 한글자씩 쪼개서 배열에 추가
- 쪼갠 한글자들 다시 두개씩 이어붙여서 배열에 추가
- 이 때, 이미 배열에 있는 값이면 해당 값을 가진 이메일들을 answer에 추가

이런식으로 풀어가려면 Map, HashMap 가 필요할 것 같다. 하지만 들어만 본 정도라 공부부터!

- `map` 인터페이스를 구현. 데이터를 키와 값의 쌍으로 저장.
- 순서 X, `key`는 중복 X, `value`는 중복 O
- 순서를 유지하려면, `LinkedHashMap` 사용
- 해시함수로 해시테이블에 데이터를 저장 검색, 해시테이블은 배열과 연결리스트가 조합된 형태


``` java
Object put(Object key, Object value);
void putAll(Map m);
Object remove(Object key);
Object replace(Object key, Object value);
boolean replace(Object key, Object oldValue, Object newValue);

Set entrySet(); // key, value 
Set keySet(); // key
Collection value(); // value

Object get(Object key);
Object getOrDefault(Object key, Object defaultValue);
boolean containKey(Object key);
boolean containValue(Object value);

int size();
boolean isEmpty();
void clear();
Object clone();

```

아래는 문제의 코드! 너무 헷갈려서 주석을 써가면서 했다... 

``` java
package onboarding;

import java.util.*;

public class Problem6 {
    static Map<String, String> combinedNicknameWithEmail; // 두글자씩 자른 닉네임과 그에 따른 이메일 저장하는 맵
    static Set<String> checkedEmail; // 검사된 이메일을 가져오는 셋

    public static List<String> solution(List<List<String> > forms) {
        combinedNicknameWithEmail = new HashMap<>();
        checkedEmail = new HashSet<>();

        for (List<String> crewForm : forms) {
            String email = crewForm.get(0);
            String nickname = crewForm.get(1);

            checkedNickname(nickname, email);
        }

        List<String> answer = new ArrayList<>(checkedEmail);
        Collections.sort(answer);

        return answer;
    }

    private static void checkedNickname(String nickname, String email) {
        String[] charNicknames = nickname.split(""); // 한 글자씩 쪼개기
        List<String> splitedNicknames = new ArrayList<>(); // 쪼갠 글자 넣을 리스트

        // 쪼갠 글자 리스트에 넣기
        for (String charNickname : charNicknames) {
            splitedNicknames.add(charNickname);
        }

        for (int i = 0; i < splitedNicknames.size() - 1; i++) {
            // 쪼갠 글자들을 두글자씩 잇기
            String combinedNickname = splitedNicknames.get(i) + splitedNicknames.get(i + 1);

            // 두글자씩 이은 닉네임이 해시맵에 키값으로 있으면 이메일을 checkedEmail에 추가
            if (combinedNicknameWithEmail.containsKey(combinedNickname)) {
                checkedEmail.add(email);
                checkedEmail.add(combinedNicknameWithEmail.get(combinedNickname));
            } else {
                // 키값에 없으면 다른 닉네임의 확인을 위해 combinedNicknameWithEmail에 추가
                combinedNicknameWithEmail.put(combinedNickname, email);
            }
        }
    }
}
```

검사하는 메서드를 따로 분리<br />
닉네임 한글자씩 쪼개서 배열로 만들고 `charNicknames`<br />
한글자씩 쪼갠 글자들 리스트에 추가 `splitedNicknames`<br />
한글자씩 쪼갠 글자들을 다시 두개로 이어버려 `combinedNickname`<br />
이은 닉네임이 키값에 있으면 검사된 이메일 리스트에 추가 `checkedEmail` (원래 맵에 있던 이메일도)<br />
없으면 다음 검사를 위해 연결된 닉네임과 이메일 이 있는 맵에 추가<br />
검사된 이메일 리스트 정렬해서 리턴<br />

해서 구현했고, 예외처리를 다르게 해본다!!

``` java
for (List<String> crewForm : forms) {
    String email = crewForm.get(0);
    String nickname = crewForm.get(1);

    if (!validCrewSize(forms))
        throw new IllegalArgumentException("크루는 1명 이상 10,000명 이하여야 합니다.");

    if (!validEmail(email))
        throw new IllegalArgumentException("email.com 도메인으로만 신청가능합니다. 이메일의 전체 길이는 11자 이상 20자 미만이여야 합니다.");

    if (!validNickname(nickname))
        throw new IllegalArgumentException("닉네임은 한글만 가능하고 전체 길이는 1자 이상 20자 미만이여야 합니다.");

    checkingNickname(nickname, email);
}

private static boolean validEmail(String email) {
    return email.endsWith("@email.com") && email.length() >= 11 && email.length() < 20;
}

private static boolean validCrewSize (List<List<String> > forms) {
    return forms.size() >=1 && forms.size() <= 10000;
}

private static boolean validNickname (String nickname) {
    return Pattern.compile("^[가-힣]*$").matcher(nickname).matches() && nickname.length() >= 1 && nickname.length() < 20;
}
```

이렇게 해주고 테스트 코드 수정해보니까 에러가 잘 뜬다! 


#### 새로 알게 된 사실
- 맵...해시맵과 셋, js의 제이슨 같으면서도 뭔가 더 유용하다 중복제거도 해주고 바로 리스트로 정의할 수도 있으니 편했다. 계속 연마해서 앞으로의 프리코스들에도 잘 적용하면 좋을 것 같다.
- 이전까지는 이프문에 제한조건 넣고 바로 예외날렸는데, 따로 빼니까 코드도 깔끔하고 직관적이다. 뭘 검사하는지 눈에 딱 보이고!
- 정렬도 배열만 해봤는데 리스트 정렬은 `Collections.sort`
- 리스트 맵 배열 세개 합쳐서 쓸라니까 너무 헷갈렸다 처음에.. 순서없이 요소를 추가하거나 정렬해야될 때는 리스트가 낫다! 처음에 배열로 하다가 정말..뷁
- 그리고 처음에 맵셋을 솔루션 안에 정의했다가 밑에 검사에서 계속 빨간색 떠서 한참...못찾았다. 바보바보맨~
