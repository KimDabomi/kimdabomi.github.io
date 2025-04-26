---
layout: single
title: "[코틀린 쿡북] 9장 테스트"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

## 1. 테스트 클래스 수명주기 설정하기
> Q. `JUnit5`의 테스트 수명주기를 기본값인 테스트 함수당 한 번 대신 클래스 인스턴스당 한 번씩 인스턴스화 하고 싶다. <br />
> A. `@TestInstance` 어노테이션을 사용하거나, `junit-platform.properties` 파일의 `lifecycle.default` 속성 설정

``` kotlin
@TestInstance(TestInstance.Lifecycle.PER_CLASS) // 모든 테스트의 테스트 클래스 인스턴스는 하나
class JUnit5ListTests {
    private val strings = listOf("a", "b", "c", "d") // 인스턴스화와 원소 채움이 한 번
    
    private lateinit val modifiable : MutableList<Int>
    
    @BeforeEach
    fun setUp() {
        modifiable = mutableListOf(3, 1, 4, 1, 5) // 테스트 할 때마다 실행 전에 다시 초기화
        println("Before: $modifiable")
    }
    
    @AfterEach
    fun finish() {
        println("After: $modifiable")
    }
    
    @Test
    fun test1() {
        // ...
    }
    
    @Test
    fun test2() {
        // ...
    }
}
```

## 2. 테스트에 데이터 클래스 사용하기
> Q. 코드를 부풀리지 않고 객체의 여러 속성을 체크하고 싶다.<br />
> A. 원하는 모든 속성을 캡슐화하는 데이터 클래스 생성

- 코틀린의 데이터 클래스는 `equals`, `toString`, `hashCode`, `copy`와 구조 분해를 위한 `component` 메서드가 자동으로 포함됨

    ``` kotlin
    data class Book(
        val isbn: String,
        val title: String,
        val author: String,
        val published: LocalDate,
    )

    // assertAll 사용
    @Test
    fun `use JUnit5 assertAll`() {
        val book = service.findBookById("123456789")
        assertAll("check all properties of a book"),
            { assertThat(book.isbn, `is`("123456789")) },
            { assertThat(book.title, `is`("kotlin cookbook")) },
            { assertThat(book.author, `is`("kotlin Kim")) },
            { assertThat(book.published, `is`(LocalDate.of(2025, Month.MARCH, 25)) }
    }

    // 테스트에 Book 데이터 클래스 사용하기
    @Test
    internal fun `use data class`() {
        val book = service.findBookById("123456789")
        val expected = 
            Book(
                "123456789",
                "kotlin cookbook",
                "kotlin Kim",
                LocalDate.of(2025, Month.MARCH, 25),
            )
        
        assertThat(book, `is`(expected)) // 단 하나의 단언이 모든 속성을 테스트함
    }
    ``` 

## 3. 기본 인자와 함께 도움 함수 사용하기
> Q. 테스트 객체를 빠르게 생성하고 싶다.<br />
> A. `copy` 함수를 사용하거나 기본 인자를 가진 도움 함수를 제공

- 최상위 레벨의 유틸리티 클래스에 팩토리 함수를 위치시키면, 테스트에서 팩토리 함수를 재사용할 수 있음

    ``` kotlin
    // Book 클래스를 수정하지 않고, 기본값을 생성하는 팩토리 함수 추가
    fun createBook(
        isbn: String = "123456789",
        title: String = "kotlin cookbook",
        author: String = "kotlin Kim",
        published: LocalDate = LocalDate.parse("2025-03-25"),
    ) = Book(isbn, title, author, publised)
    ```

## 4. 여러 데이터에 JUnit5 테스트 반복하기
> Q. 데이터 값 세트를 제공해서 `JUnit5` 테스트를 실행하고 싶다.<br />
> A. `JUnit5`의 파라미터화된 테스트와 동적 테스트 사용

- `@CsvSource` 어노테이션은 테스트 함수를 위한 입력 데이터로서 문자열 리스트를 인자로 받음
- `Arguments` 클래스는 두 입력 인자를 결합시킬 수 있는 of 라는 팩토리 메서드를 가짐. 리턴 타입은 `List<Argument>`

    ``` kotlin
    @JvmOverloads
    tailrec fun fibonacci(n: Int, a: Int = 0, b: Int = 1): Int =
        when (n) {
            0 -> a
            1 -> b
            else -> fibonacci(n - 1, b, a + b)
        }

    // CSV 데이터를 사용해 파라미터화된 테스트를 수행
    @ParameterizedTest
    @CsvSource("1,1", "2,1", "3,2",
        "4,3", "5,5", "6,8", "7,13",
        "8,21", "9,34", "10,55")
    fun `first 10 Fibonacci numbers (csv)`(n: Int, fib: Int) = 
        assertThat(fibonacci(n), `is`(fib))
        
    // 파라미터 소스로서 인스턴스 함수에 접근하기
    private fun fibnumbers() = listOf(
        Arguments.of(1, 1), Arguments.of(2, 1),
        Arguments.of(3, 2), Arguments.of(4, 3),
        Arguments.of(5, 5), Arguments.of(6, 8),
        Arguments.of(7, 13), Arguments.of(8, 21),
        Arguments.of(9, 34), Arguments.of(10, 55),
    )

    @ParameterizedTest(name = "fibonacci({0}) == {1}")
    @MethodSource("fibnumbers")
    fun `first 10 Fibonacci numbers (instance method)`(n: Int, fib: Int) =
        assertThat(fibonacci(n), `is`(fib))
        
    // 파라미터 소스 함수를 담기 위해 동반 객체 사용
    companion object {
        @JvmStatic // JUnit 라이브러리는 함수를 static으로 간주하기 때문에 필요함
        fun fibs() = listOf(
            Arguments.of(1, 1), Arguments.of(2, 1),
            Arguments.of(3, 2), Arguments.of(4, 3),
            Arguments.of(5, 5), Arguments.of(6, 8),
            Arguments.of(7, 13), Arguments.of(8, 21),
            Arguments.of(9, 34), Arguments.of(10, 55),
        )
    }

    @ParameterizedTest(name = "fibonacci({0}) == {1}")
    @MethodSource("fibs")
    fun `first 10 Fibonacci numbers (companion method)`(n: Int, fib: Int) =
        assertThat(fibonacci(n), `is`(fib))
    ```

## 5. 파라미터화된 테스트에 data 클래스 사용하기
> Q. 파라미터화된 테스트를 좀 더 쉽게 읽는 테스트 결과를 생성하고 싶다.<br />
> A. 입력 값과 예상 값을 감싸는 `data class`를 만들고, 만든 `data class` 기반의 테스트 데이터를 생성하는 함수를 테스트 메서드 소스로 사용

- `JUnit5`에는 테스트 데이터를 메서드 또는 파일로 제공하는 파라미터화된 테스트라는 기능이 있음

    ``` kotlin
    data class FibonacciTestData(val number: Int, val expected: Int)

    @ParameterizedTest
    @MethodSource("fibonacciTestData")
    fun `check fibonacci using data class`(data: FibonacciTestData) {
        assertThat(fibonacci(data.number), `is`(data.expected))
    }

    private fun fibonacciTestData() = 
        Stream.of(
            FibonacciTestData(number = 1, expected = 1),
            FibonacciTestData(number = 2, expected = 1),
            FibonacciTestData(number = 3, expected = 2),
            FibonacciTestData(number = 4, expected = 3),
            FibonacciTestData(number = 5, expected = 5),
            FibonacciTestData(number = 6, expected = 8),
            FibonacciTestData(number = 7, expected = 13),
        )
    ```

<br />
<br /> 

파라미터화 해서 테스트 하는 방법은 이전에 테스트 구현을 해본 적이 있어서 그런지 이번 장은 슉슉 읽혔다! 
요새는 `kotest`를 더 많이 째려봐서 그런지 `JUnit`은 약간 오랜만이군!
