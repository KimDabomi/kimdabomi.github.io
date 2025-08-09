---
layout: single
title: "[코틀린 쿡북] 4장 함수형 프로그래밍"
categories:
  - book
sidebar:
  nav: "sidebar-category"
---

함수형 프로그래밍이란?
- 불변성을 선호
- 순수 함수를 사용하는 경우에 동시성을 쉽게 구현할 수 있으며
- 반복보다는 변형을 사용
- 조건문보다는 필터를 사용하는 코딩 스타일

## 1. 알고리즘에서 `fold` 사용하기
> Q. 반복 알고리즘을 함수형 방식으로 구현하고 싶다.<br />
> A. `fold`함수를 사용해 시퀀스나 컬렉션을 하나의 값으로 축약시킨다.

- `fold`함수는 배열 또는 반복 가능한 컬렉션에 적용할 수 있는 축약 연산

    ``` kotlin
    fun sum(vararg nums: Int) =
        nums.fold(0) { acc, n -> acc + n }
        
    @Test
    fun `sum using fold`() {
        val numbers = intArrayOf(3, 1, 4, 1, 5, 9)
        assertEquals(numbers.sum(), sum(*numbers))
    }

    // acc = 0, n = 3
    // acc = 3, n = 1
    // acc = 4, n = 4
    // acc = 8, n = 1
    // acc = 9, n = 5
    // acc = 14, n = 9
    ```

## 2. `reduce`함수를 사용해 축약하기
> Q. 비어 있지 않는 컬렉션의 값을 축약하고 싶지만 누적자의 초기값을 설정하고 싶지 않다.<br />
> A. `fold`대신 `reduce`사용

- `reduce`함수는 `fold`함수랑 거의 같은데 사용 목적도 같음
- `reduce`함수에는 누적자의 초기값 인자가 없음
- 초기값은 컬렉션의 첫 번째 값으로 초기화됨
- `reduce`연산은 비어있는 컬렉션에서 예외를 던짐

    ``` kotlin
    fun sumReduce(vararg nums: Int) =
        nums.reduce { acc, i -> acc + i }
        
    @Test
    fun `sum using reduce`() {
        val numbers = intArrayOf(3, 1, 4, 1, 5, 9)
        assertAll(
            { assertEquals(numbers.sum(), sumReduce(*numbers)) },
            { assertThrows<UnsupportedOperationException> {
                sumReduce()
            }
        )
    }

    // acc = 3, n = 1
    // acc = 4, n = 4
    // acc = 8, n = 1
    // acc = 9, n = 5
    // acc = 14, n = 9
    ```

## 3. 꼬리 재귀 적용하기
> Q. 재귀 프로세스를 실행하는 데 필요한 메모리를 최소화하고 싶다.<br />
> A. 꼬리 재귀를 사용해 프로세스 알고리즘을 표현하고 해당 함수에 `tailrec` 키워드를 추가

- `tailrec` 키워드는 컴파일러에게 해당 재귀 호출을 최적화해야 한다고 알려줌
- `tailrec` 변경자를 적용할 수 있는 함수의 자격 요건
  - 반드시 수행하는 마지막 연산으로서 자신을 호출해야 함
  - `try`, `catch` ,`finally` 블록 안에서는 사용 불가
  - 오직 JVM 환경에서만 꼬리 재귀가 지원됨

    ``` kotlin
    @JvmOverloads
    tailrec fun factorial(
        n: Long,
        acc: BigInteger = BigInteger.ONE,
    ): BigInteger =
        when (n) {
            0L -> BigInteger.ONE
            1L -> acc
            else -> factorial(n - 1, acc * BigInteger.valueOf(n))
        }
    ```

<br />
<br /> 

`reduce`는 자바에서도 있는 속성이지만, `fold`는 코틀린에만 있는 거 같아서 새롭게 알게 되었다!<br />
재귀 함수는 매번 공부할 때마다, 코테 같은 거 풀 때마다 메모리 때문에 뻑났었는데!!!<br />
컴파일러한테 알려주는 키워드가 있다...? 뭔가 레볼루션 넋낌..!
