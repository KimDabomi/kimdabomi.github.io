---
layout: single
title: "[코틀린 쿡북] 7장 영역 함수"
categories:
  - personal
sidebar:
  nav: "sidebar-category"
---

## 1. apply로 객체 생성 후에 초기화하기
> Q. 객체를 사용하기 전에 생성자 인자만으로는 할 수 없는 초기화 작업이 필요하다.<br />
> A. `apply`함수 사용

- `apply`함수는 `this`를 인자로 전달하고, `this`를 리턴하는 확장 함수
- 모든 제네릭 타입에 존재하는 확장 함수

    ``` kotlin
    @Repository
    class JdbcOfficerDAO(private val jdbcTemplate: JdbcTemplate) {
        private val insertOfficer = SimpleJdbcInsert(jdbcTemplate)
            .withTableName("OFFICERS")
            .usingGeneratedKeyColumns("id")
        
        fun save(officer: Officer) =
            officer.apply {
                id = insertOfficer.executeAndReturnKey(
                    mapOf(
                        "rank" to rank,
                        "first_name" to first,
                        "last_name" to last
                    )
                )
            }
    }
    ``` 

## 2. 부수 효과를 위해 `also` 사용하기
> Q. 코드 흐름을 방해하지 않고 메시지를 출력하거나 다른 부수 효과를 생성하고 싶다.<br />
> A. `also`함수 사용

- `also`는 모든 제네릭 타입에 추가됨
- `block`인자를 실행시킨 후에 자신을 리턴
- 일반적으로 객체에 함수 호출을 연쇄시키기 위해 사용

    ``` kotlin
    val book = createBook()
        .also { println(it) }
        .also { Logger.getAnonymousLogger().info(it.toString()) }
    ```    

## 3. `let`함수와 엘비스 연산자 사용하기
> Q. 오직 널이 아닌 레퍼런스의 코드 블록을 실행하고 싶지만 레퍼런스가 널이라면 기본값을 리턴하고 싶다.<br />
> A. 엘비스 연산자를 결합한 안전 호출 연산자와 함께 `let`영역 함수 사용

- `let`함수는 모든 제네릭 타입의 확장 함수
- `let`함수는 컨텍스트 객체가 아닌 블록의 결과를 리턴

    ``` kotlin
    fun processString(str: String) =
        str.let {
            when {
                it.isEmpty() -> "Empty"
                it.isBlank() -> "Blank"
                else -> it.capitalize()
            }
        }
    ```

## 4. 임시 변수로 `let` 사용하기
> Q. 연산 결과를 임시 변수에 할당하지 않고 처리하고 싶다.<br />
> A. 연산에 `let` 호출을 연쇄하고 `let`에 제공한 람다 또는 함수 레퍼런스 안에서 그 결과를 처리

- 결과를 연쇄시킨 `let` 호출을 사용하는 목적은 결과 자체를 컨텍스트 변수로서 사용하는 것이기 때문에 `let`에 제공한 블록 안에서 결과를 출력할 수 있음

    ``` kotlin
    val numbers = mutableListOf("one", "two", "three")
    val resultList = numbers.map { it.length }.filter { it > 3 }
    println(resultList)

    // 리팩터링 후
    val numbers = mutableListOf("one", "two", "three")
    numbers.map { it.length }.filter { it > 3 }.let {
        println(it)
    }
    ```
 
<br />
<br />

이번 장은 그래도 실제로 많이 써본 함수들이 많이 등장!! 반갑고 익숙해서 슉슉 읽혔다!
