---
layout: single
title: "[프로그래밍 규칙] 4주차 실행하기"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

## 1. 너무 추측을 안하진 않고! 사용자나 개발자의 입장에서 행동들을 생각해보기

이번 한 주는 코드 작업보단 워드프레스 작업을 했기 때문에 그 의논 과정에서 생각한 예상치 못한 부분! 을 정리해보겠슴다.
- 우리가 생각해야 될 사용자의 경우의 수 생각하기 
- 사용자가 어떻게 해야 덜 귀찮을지 생각하기
  - 신규 회원가입에서 회원 통합 안내
  - 팝업에서 회원가입 / 회원 전환 선택
  - 회원 전환 페이지에서 신규 가입 / 기존 회원 선택
- 개발자가 어떻게 해야 일을 덜 할지 생각하기
  - 회원 전환 페이지에서 로그인 여부 판별
  -  회원 전환 폼 제출에서 로그인 여부 판별
  - 회원 전환 폼 받을 때 필요한 데이터를 모아서 테이블에 추가
    - 두 팀장님과 논의를 통해 점점 더 우리가 나중에 데이터 작업할 때 더 편하게 추가됨! 

## 2. 객체지향적으로 생각하여, 모든 행동과 클래스를 객체로 본다면 그 행동을 누가 갖고 있어야 하는지 추측

블랙잭 미션을 할 때 아래와 같은 리뷰를 받았슴다.
![프로그래밍 규칙 10주차](/assets/images/programming-rule-4.png)
> 예상치 못했다. 당연하게 MVC 패턴으로 하던 여성 몹시 당황~ 서비스 코드를 없애고, 각각의 메서드들이 가진 행동을 어떤 객체가 가지고 있어야 하는지 고민 앤 고민 앤 반영했다.

![프로그래밍 규칙 10주차](/assets/images/programming-rule-4-2.png)

![프로그래밍 규칙 10주차](/assets/images/programming-rule-4-3.png)
> 예상치 못했다!! enum의 value까지도 view에서 처리되어야 하나..! 아래처럼 변경했다.

``` kotlin
private val cardNumberDisplay =
    mapOf(
        CardNumber.ACE to "A",
        CardNumber.TWO to "2",
        CardNumber.THREE to "3",
        CardNumber.FOUR to "4",
        CardNumber.FIVE to "5",
        CardNumber.SIX to "6",
        CardNumber.SEVEN to "7",
        CardNumber.EIGHT to "8",
        CardNumber.NINE to "9",
        CardNumber.TEN to "10",
        CardNumber.JACK to "J",
        CardNumber.QUEEN to "Q",
        CardNumber.KING to "K",
    )

private val cardShapeDisplay =
    mapOf(
        CardShape.HEART to "하트",
        CardShape.CLUB to "클로버",
        CardShape.SPADE to "스페이드",
        CardShape.DIAMOND to "다이아",
    )
```