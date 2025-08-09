---
layout: single
title: "[코틀린 쿡북] 11장 그 밖의 코틀린 기능"
categories:
  - personal
sidebar:
  nav: "sidebar-category"
---

## 1. 코틀린 버전 알아내기
> Q. 코드를 작성해 현재 사용 중인 코틀린의 버전을 알고 싶다.<br />
> A. `KotlinVersion` 클래스 동반 객체의 `CURRENT` 속성 사용

- `Comparable` 인터페이스 구현 - <, > 와 같은 연산자 사용 가능
- `isAtLeast` 메서드로 주어진 값보다 상위 버전인지 확인 가능

    ```kotlin
    fun main(args: Array<String>) {
        println("The current Kotlin version is ${KotlinVersion.CURRENT}")
    }
    ```

## 2. 반복적으로 람다 실행하기
> Q. 주어진 람다 식을 여러 번 실행하고 싶다. <br />
> A. 코틀린 내장 `repeat` 함수 사용

``` kotlin
fun main(args: Array<String>) {
    repeat(5) {
        println("Counting: $it")
    }
}

// Counting: 0
// Counting: 1
// Counting: 2
// Counting: 3
// Counting: 4
```

## 3. 완벽한 `when` 강제하기
> Q. 코틀린 컴파일러가 `when` 문에서 가능한 모든 절을 가지도록 강제하게 만들고 싶다. <br />
> A. 값을 리턴하는 제네릭 타입에 `exhaustive` 라는 간단한 확장 속성을 추가하고, `when block`에 이를 연결

- `when`절은 `if`문과 마찬가지로 값을 반환함 

    ``` kotlin
    fun printMod3(n: Int) {
        when (n % 3) {
            0 -> println("$n % 3 == 0")
            1 -> println("$n % 3 == 1")
            2 -> println("$n % 3 == 2")
            else -> println("done") // println 함수는 아무것도 리턴하지 않지만 컴파일러는 else 문을 요구함. 코틀린의 완벽한 조건식 요구
        }
    }
    ```

## 4. 정규표현식과 함께 `replace` 함수 사용하기
> Q. 부분 문자열의 모든 인스턴스를 어떤 값으로 교체하고 싶다. <br />
> A. 문자열 인자 또는 정규식을 받도록 중복된 `String`의 `replace`함수를 사용

- String 클래스는 `CharSequence` 인터페이스를 구현함 - 두 개의 `replace` 함수가 존재

    ``` kotlin
    // 중복된 두 가지 replace 사용
    @Test
    fun `demonstrate replace with a string vs regex`() {
        assertAll(
            { assertEquals("one*two", "one.two".replace(".", "*")) },
            { assertEquals("********", "one.two.".replace(".".toRegex(), "*")) }
        )
    }
    ```

## 5. 바이너리 문자열로 변환하고 되돌리기
> Q. 숫자를 바이너리 문자열로 변환하거나 바이너리 문자열을 정수로 파싱하고 싶다. <br />
> A. 기저를 인자로 받는 `toString` 또는 `toInt` 함수 중복을 사용

- `StringsKt` 클래스에는 `Int`에 기저를 인자로 받는 인라인 확장 함수 `toString`이 있음
- 이 클래스에는 이를 반대로 수행하는 확장 함수도 String에 들어 있음 - `toString` 메서드를 호출하면 정수를 바이너리 문자열로 변환할 수 있음

    ``` kotlin
    @Test
    internal fun toBinaryStringAndBack() {
        val str = 42.toString(radix = 2)
        assertThat(str, `is`("101010"))
        
        val num = "101010".toInt(radix = 2)
        assertThat(num, `is`(42))
    }
    ``` 

## 6. 실행 가능한 클래스 만들기
> Q. 클래스에서 단일 함수를 간단하게 호출하고 싶다. <br />
> A. 함수를 호출할 클래스에서 `invoke` 연산자 함수를 재정의

- 코틀린에서는 여러 연산자를 재정의할 수 있음. 연산자를 재정의하려면 오직 해당 연산자와 연관된 함수만 재정의하면 됨
- 연산자를 구현하려면 멤버 함수 또는 올바른 이름과 인자를 가진 확장 함수를 제공해야 함
- 연산자를 중복하는 모든 함수는 `operator` 변경자를 포함해야 함

    ``` kotlin
    class AstroRequest {
        companion object {
            private const val ASTRO_URL = "..."
        }
        
        // fun execute(): AstroResult { // 포함된 함수를 위한 임의의 이름
        operator fun invoke(): AstroResult { // invoke 연산자 함수를 통해 실행 가능한 클래스가 됨
            val responseString = URL(ASTRO_URL).readText()
            return Gson().fromJSON(responseString, AstroResult::class.java)
        }   
    }
    ```
 

## 7. 경과 시간 측정하기
> Q. 코드 블록이 실행되는 데 걸린 시간을 알고 싶다. <br />
> A. 코틀린 표준 라이브러리의 `measureTimeMillis`/`measureNanoTime` 함수 사용

``` kotlin
fun doubleIt(x: Int): Int {
    Thread.sleep(100L)
    println("doubling $x with on thread ${Thread.currentThread().name}")
    return x * 2
}

fun main() {
    pringln("${Runtime.getRuntime().availableProcessors()} processors")
    var tim = measureTimeMills {
        IntStream.rangeClosed(1, 6)
            .map { doubleIt(it) }
            .sum()
    }
    println("Sequential stream took ${time}ms")
    
    time = measureTimeMillis {
        IntStream.rangeClosed(1, 6)
            .parallel()
            .map{ doubleIt(it) }
            .sum()
    }
    println("Parallel stream took ${time}ms")
}
```

## 8. 스레드 시작하기
> Q. 코드 블록을 동시적 스레드에서 실행하고 싶다. <br />
> A. `kotlin.concurrent` 패키지의 `thread` 함수 사용

``` kotlin
// 다수의 스레드를 임의의 간격으로 시작하기
(0..5).forEach { n -> 
    val sleepTime = Random.nextLong(range = 0..1000L)
    thread {
        Thread.sleep(sleepTime)
        println("${Thread.currentThread().name} for $ n after ${sleepTime}ms")
    }
}

// 데몬 스레드 시작하기
(0..5).forEach { n ->
    val sleepTime = Random.nextLong(range = 0..1000L)
    thread(isDaemon = true) {
        Thread.sleep(sleepTime)
        println("${Thread.currentThread().name} for $n after $sleepTime}ms")
    }
}

// 여러 스레드 조인하기
(0..5).forEach { n ->
    val sleepTime = Random.nextLong(range = 0..1000L)
    thread {
        Thread.sleep(sleepTime)
        println("${Thread.currentThread().name} for $n after ${sleepTime}ms")
    }.join()
}
```

## 9. `TODO`로 완성 강제하기
> Q. 개발자가 특정 함수나 테스트를 구현을 끝마칠 수 있게 하고 싶다. <br />
> A. 함수 구현을 완성하지 않으면 예외를 던지는 `TODO` 함수 사용

``` kotlin
fun compleateThis() {
    TODO()
}
``` 

## 10. `Random`의 무작위 동작 이해하기
> Q. 난수를 생성하고 싶다. <br />
> A. `Random` 클래스에 있는 함수 중 하나를 사용 

## 11. 함수 이름에 특수 문자 사용하기
> Q. 함수 이름을 읽기 쉽게 작성하고 싶다. <br />
> A. 밑줄을 사용하거나 함수 이름을 백틱으로 감싸 읽기 쉽게 만들 수 있음 (테스트에서만 사용)

## 12. 자바에게 예외 알리기
> Q. 코틀린 함수가 자바에서 체크 예외차로 여겨지는 예외를 던지는 경우, 자바에게 해당 예외가 체크 예외임을 알려 주고 싶다. <br />
> A. 함수 시그니처에 `@Throws` 어노테이션 추가

``` kotlin
@Throws(IOException::class) // 자바에서 이 함수가 IOException을 던진다고 알려줌
fun problem() {
    throw IOException()
}
```

<br />
<br />

이번 장은 좀 길었고 주제가 정해져 있지 않지만, 다른 장보다 비교적 많이 쓰이는 메서드와 속성? 들을 알 수 있었다!
