---
layout: single
title: "[프로그래밍 규칙] 2주차 실행하기"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

신기하게도 2주차 때 읽은 내용과 이펙티브 코틀린 수업에서 본 TDD 시연이 뭔가 맥락이 맞았다! <br />
수업에서도 저번주는 TDD가 막막해서 안했지만.. 책도 읽고 수업도 듣고 나니까 뭔가!!!! 해볼만한데!! 라는 생각이 들어서 열심히 했다.

## 1. 기능 명세서 작성부터!

전에는 냅다 도메인 클래스부터 만들면서 시작했는데, 이번에는 기능 명세서를 작성하고, 한 줄 마다 테스트와 클래스를 그냥 한 파일에서 작성하면서 실행했다.
![프로그래밍 규칙 10주차](/assets/images/programming-rule-2.png)

## 2. 클래스, 패키지 분리와 실행을 위한 controller, view 생성

이렇게 하고 나니까 어떤 테스트 때 어떤 클래스가 쓰이는지, 
어떤 기능은 클래스가 가지고 있어야 되고 어떤 기능은 서비스가 가지고 있어야 되는지 등을 더 잘 구분할 수 있었다!
![프로그래밍 규칙 10주차](/assets/images/programming-rule-2-2.png)

## 3. 이 과정에서! 무작위 번호를 보여줄 때 작은... 작은 추상화 해냄

``` kotlin
class LottoTicket {
    private val lottoNumbers = (1..45).shuffled().take(6).sorted()

    fun numbers(): List<Int> {
        return lottoNumbers
    }
}
```
원래는 이렇게 로또 티켓에서 냅다 로또 번호들을 무작위로 생성해서 숫자리스트로 보여줬다. <br />
하지만 이렇게 하니까 뭐...테스트를 어떻게 합니까? 아예 감도 안왔다. <br />
그래서 메서드를 하나 더 만들어줘서 사이즈를 검증하도록 추가했다.
```kotlin
class LottoTicket(numbers: List<Int>? = null) {
    private val lottoNumbers = numbers ?: (1..45).shuffled().take(6).sorted()

    fun numbers(): List<Int> {
        return lottoNumbers
    }
    
    fun matchCount(winningNumbers: List<Int>): Int {
        return numbers().intersect(winningNumbers.toSet()).size
    }
}

// 테스트 코드
"구매한 로또 목록에서 당첨 번호와 일치하는 게 4개이면 4등이다. (50000원)" {
    val winningNumbers = listOf(1, 2, 3, 4, 5, 6)

    var matchingTicket: LottoTicket? = null
    while (matchingTicket == null) {
        val lottoTickets = List(14000) { LottoTicket() }
        matchingTicket = lottoTickets.firstOrNull { ticket ->
            ticket.matchCount(winningNumbers) == 4
        }
    }

    val rank = LottoRank.from(matchingTicket!!.matchCount(winningNumbers))
    rank shouldBe LottoRank.FOURTH
    rank.prize shouldBe 50_000
}
```
이런식으로! 많이 사서 이중에 4개가 맞는 게 나오면 성공인 것...! <br />
근데 아직 '많이 사야 된다'는 게 완벽한 추상화는 아니다 흑흑

## 4. 공통 메서드 분리!

로또 5등부터 1등까지 같은 로직이기 때문에 아래처럼 나눠줬다. 너무 깔끔해진 테스트들...!
``` kotlin
fun verifyLottoRank(matchCount: Int, expectedRank: LottoRank, expectedPrize: Int) {
    val winningNumbers = listOf(1, 2, 3, 4, 5, 6)
    var matchingTicket: LottoTicket? = null
    while (matchingTicket == null) {
        val lottoTickets = List(14000) { LottoTicket() }
        matchingTicket = lottoTickets.firstOrNull { ticket ->
            ticket.matchCount(winningNumbers) == matchCount
        }
    }

    val rank = LottoRank.from(matchingTicket!!.matchCount(winningNumbers))
    rank shouldBe expectedRank
    rank.prize shouldBe expectedPrize
}

"구매한 로또 목록에서 당첨 번호와 일치하는 게 3개이면 5등이다. (5000원)" {
        verifyLottoRank(3, LottoRank.FIFTH, 5000)
}

"구매한 로또 목록에서 당첨 번호와 일치하는 게 4개이면 4등이다. (50000원)" {
    verifyLottoRank(4, LottoRank.FOURTH, 50_000)
}

"구매한 로또 목록에서 당첨 번호와 일치하는 게 5개이면 3등이다. (1500000원)" {
    verifyLottoRank(5, LottoRank.THIRD, 1_500_000)
}

"구매한 로또 목록에서 당첨 번호와 일치하는 게 6개이면 1등이다. (2000000000원)" {
    verifyLottoRank(6, LottoRank.FIRST, 2_000_000_000)
}
```

