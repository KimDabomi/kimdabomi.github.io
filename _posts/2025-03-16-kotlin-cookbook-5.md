---
layout: single
title: "[코틀린 쿡북] 5장 컬렉션"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

## 1. 배열 다루기
> Q. 코틀린에서 배열을 생성하고 배열에 데이터를 추가하고 싶다.<br />
> A. `arrayOf`함수 이용해 배열 만들고, `Array`클래스를 사용해서 배열 다루기

- 기본 타입을 나타내는 클래스들 - `booleanArrayOf`, `byteArrayOf`, `shortArrayOf`, `charArrayOf`, `intArrayOf`, `longArrayOf`, `floatArrayOf`, `doubleArrayOf`
- 인덱스 값을 알고 싶다면 `indices`, 인덱스 값을 함께 사용하고 싶다면 `withIndex` 사용

    ``` kotlin
    val strings = arrayOf(“a”, “b”, “c”)
    val nullStringArray = arrayOfNulls<String>(5)
    val squares = Array(5) { i -> (i * i).toString() }

    @Test
    fun `withIndex returns IndexValues`() {
        val string = arrayOf(“a”, “b”, “c”)
        for ((index, value) in strings.withIndex()) {
            println(“Index $index maps to $value”)
            asserTrue(index in 0..2)
        }
    }

    // Index 0 maps to a
    // Index 1 maps to b
    // Index 2 maps to c
    ```

## 2. 컬렉션 생성하기
> Q. 리스트, 세트, 맵을 생성하고 싶다.<br />
> A. `listOf`, `setOf`, `mapOf` - 변경 불가능한 컬렉션 생성, `mutableListOf`, `mutableSetOf`, `mutableMapOf` - 변경 가능한 컬렉션 생성

``` kotlin
var numList = listOf(1, 2, 3, 4, 5)
var numSet = setOf(1, 2, 3, 4, 5)
val map = mapOf(1 to “one”, 2 to “two”, 3 to “three”)

var mutableNumList = mutableListOf(1, 2, 3, 4, 5)
var mutableNumSet = mutableSetOf(1, 2, 3, 4, 5)
var mutableMap = mutableMapOf(1 to “one”, 2 to “two”, 3 to “three”)
```

## 3. 컬렉션에서 읽기 전용 뷰 생성하기
> Q. 변경 불가능한 리스트, 세트, 맵이 있을 때 해당 컬렉션의 읽기 전용 버전을 생성하고 싶다.<br />
> A. `toList`, `toSet`, `toMap` 사용

- `mutableList`에는 `add`, `remove` 사용 가능
- 읽기 전용 버전 생성 방법
  - `List`타입을 리턴하는 `toList`메서드 사용 -> `add`, `remove` 사용 불가
  - `List`타입의 레퍼런스에 가변 리스트 하당 -> `add`, `remove` 사용 가능
 
## 4. 컬렉션에서 맵 만들기
> Q. 키 리스트가 있을 때 각각의 키와 생성한 값을 연관시켜서 맵을 만들고 싶다.<br />
> A. `associateWith`함수에 각 키에 대해 실행되는 람다를 제공해서 사용

- `associate`, `associateWith`는 동일한 기능. 인자만 `Pair<Char, String>`, `String`으로 다름

    ``` kotlin
    val keys = ‘a’..’f’
    val map = keys.associate { it to it.toString().repeat(5).capitalize() }
    // val map = keys.associateWith { it to it.toString().repeat(5).capitalize() } 동일한 기능, associateWith의 인자는 String
    println(map)
    // { a = Aaaaa, b = Bbbbb, c = Ccccc, d = Ddddd, e = Eeeee }
    ```

## 5. 컬렉션이 빈 경우 기본값 리턴하기
> Q. 컬렉션을 처리할 때 컬렉션의 모든 원소가 선택에서 제외되지만 기본 응답을 리턴하고 싶다.<br />
> A. 컬렉션이나 문자열이 비어있는 경우에는 `ifEmpty`, `ifBlank`함수 사용

    ``` kotlin
    fun onSaleProducts_ifEmptyCollection(products: List<Product>) =
        products.filter { it.onSale }
            .map { it.name }
            .ifEmpty { listOf(“none”) }
            .joinToString(separator = “, “)

    fun onSaleProducts_ifEmptyString(products: List<Product>) =
        products.filter { it.onSale }
            .map { it.name }
            .joinToString(separator = “, “)
            .ifEmpty { listOf(“none”) }
    ``` 

## 6. 주어진 범위로 값 제한하기
> Q. 값이 주어졌을 때, 주어진 값이 특정 범위 안에 들면 해당 값을 리턴하고, 그렇지 않다면 범위의 최솟값 또는 최댓값을 리턴하고 싶다.
> A. `kotlin.ranges`의 `coerceIn` 사용

- 범위 함수 `coerceIn`에는 2개의 중복이 존재. 하나는 닫힌 범위를 인자로 받고, 다른 하나는 최솟값과 최댓값을 인자로 받음

    ``` kotlin
    @Test
    fun `coerceIn given a range`() {
        val range = 3..8
        
        assertThat(5, `is`(5.coerceIn(range))) // 5
        assertThat(range.start, `is`(1.coerceIn(range))) // 3
        assertThat(range.endInclusive, `is`(9.coerceIn(range))) // 8
    }

    @Test
    fun `coerceIn given min and max`() {
        val min = 2
        val max = 6
        assertThat(5, `is`(5.coerceIn(min, max))) // 5
        assertThat(min, `is`(1.coerceIn(min, max))) // 2
        assertThat(max, `is`(9.coerceIn(min, max))) // 6
    }
    ```

## 7. 컬렉션을 윈도우로 처리하기
> Q. 값 컬렉션이 주어진 경우 컬렉션을 횡단하는 작은 윈도우를 이용해 컬렉션을 처리하고 싶다.<br />
> A. 컬렉션을 같은 크기로 나누고 싶다면 `chunked`함수 사용. 정해진 간격으로 컬렉션을 따라 움직이는 블록을 원한다면 `windowed`함수 사용

``` kotlin
@Test
internal fun chunked() {
    val range = 0..8
    val chunked = range.chunked(3)
    
    assertThat(chunked =, contains(listOf(0, 1, 2), listOf(3, 4, 5), listOf(6, 7, 8))
}

@Test
fun windowed() {
    val range = 0..8
    
    assertThat(range.windowed(3, 3),
        contains(listOf(0, 1, 2), listOf(3, 4, 5), listOf(6, 7, 8)))
}
``` 

## 8. 리스트 구조 분해하기
> Q. 리스트의 원소에 접근할 수 있게 구조 분해하고 싶다.<br />
> A. 최대 5개의 원소를 가진 그룹에 리스트를 할당

``` kotlin
val list = listOf("a", "b", "c", "d", "e")
val (a, b, c) = list
println("$a $b $c") // a b c
```

## 9. 다수의 속성으로 정렬하기
> Q. 클래스를 어떤 속성으로 정렬한 다음, 동일한 값을 다른 속성으로 정렬하는 등. 이처럼 계속해서 클래스를 다수의 속성으로 정렬하고 싶다.<br />
> A. `sortedWith`, `comparedBy`함수 사용

- `compareBy` - `Comparator`를 생성
- `sortedWith` - `Comparator`를 인자로 받음

    ``` kotlin
    data class Golfer(
        val score: Int, 
        val first: String, 
        val last: String,
    )
    val golfers = listOf( 생략 )
    // 아래 두 개는 동일
    val sorted = 
        golfers.sortedWith(
            compareBy({ it.score }, { it.last }, { it.first })
        )
    val comparator = 
        compareBy<Golfer>(Golfer::score)
            .thenBy(Golfer::last)
            .thenBy(Golfer::first)
    ```

## 10. 사용자 정의 이터레이터 정의하기
> Q. 컬렉션을 감싼 클래스를 손쉽게 순회하고 싶다.<br />
> A. `next`, `hasNext`함수를 모두 구현한 이터레이터를 리턴하는 연산자 함수 정의

``` kotlin
operator fun Team.iterator(): Iterator<Player> = players.iterator()

for(player in team) {
    println(player)
}

// Player(name = kim), Player(name = da), Player(name = bo), Player(name = mi)
```

## 11. 타입으로 컬렉션을 필터링하기
> Q. 여러 타입이 섞여 있는 컬렉션에서 특정 타입의 원소로만 구성된 새 컬렉션을 생성하고 싶다.<br />
> A. `filterIsInstance`, `filterIsInstanceTo` 확장 함수 사용

``` kotlin
val list = listOf("a", LocalDate.now(), 3, 1, 4, "b")

val all = list.filterIsInstanceTo(mutableListOf())
val strings = list.filterIsInstanceTo(mutableListOf<String>())
val ints = list.filterIsInstanceTo(mutableListOf<Int>())
val dates = list.filterIsInstanceTo(mutableListOf<LocalDate>())

assertThat(all, `is`(list))
assertThat(strings, containsInAnyOrder("a", "b"))
assertThat(ints, containsInAnyOrder(1, 3, 4))
assertThat(dates, contains(LocalDate.now()))
```

## 12. 범위를 수열로 만들기
> Q. 범위를 순회하고 싶지만 범위가 간단한 정수 또는 문자로 구성되어 있지 않다.<br />
> A. 사용자 정의 수열 생성

<br />
<br /> 

컬렉션에서 자주 쓰이는 것만 정리해둔 것 같은데, 역시 많다...<br />
뭔가 기본적인 리스트 같은 거 조작하는..? 그런 게 좍 한번 정리된 것 같다! 굿!
