---
layout: single
title: "[코틀린 쿡북] 6장 시퀀스"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

- 컬렉션에서 처리는 즉시 발생 - `map`, `filter`가 호출될 때 컬렉션의 모든 원소는 즉시 처리
- 시퀀스는 지연 처리됨 - 데이터를 처리하기 위해 시퀀스를 사용하면, 각각의 원소는 다음 원소가 처리되기 전에 전체 파이프라인을 완료함
- 지연 처리 방식은 데이터가 많거나 `first`같은 쇼트 서킷 연산의 경우에 도움이 되고, 원하는 값을 찾았을 때 시퀀스를 종료할 수 있게 도와줌

## 1. 지연 시퀀스 사용하기
> Q. 특정 조건을 만족시키는 데 필요한 최소량의 데이터만 처리하고 싶다.<br />
> A. 코틀린 시퀀스를 쇼트 서킷 함수와 함께 사용

- 시퀀스의 각 원소는 다음 원소로 진행하기 전에 완전한 전체 파이프라인에서 처리되기 때문에 오직 6개의 연산만이 수행됨

    ``` kotlin
    (100 until 2_000_000).asSequence()
        .map { println("doubling $it"); it * 2 }
        .filter { println("filtering $it"); it % 3 == 0 }
        .first()
    ```

## 2. 시퀀스 생성하기
> Q. 값으로 이뤄진 시퀀스를 생성하고 싶다.<br />
> A. 이미 원소가 있다면 `sequenceOf`, `Iterable`이 있다면 `asSequence`사용. 그 외의 경우는 시퀀스 생성기 사용

- 시퀀스  안에서 정수를 생성, 첫 번째 소수를 찾을 때까지 생성한 정수를 하나씩 평가함
- 최종 연산 없이는 시퀀스는 아무 값도 처리하지 않음. 최종 연산은 `first()`


    ``` kotlin
    fun nextPrime(num: Int) =
        generateSequence(num + 1) { it + 1 }
            .first(INt::isPrime)
    ```

## 3. 무한 시퀀스 다루기
> Q. 무한대의 원소를 갖는 시퀀스의 일부분이 필요하다.<br />
> A. 널을 리턴하는 시퀀스 생성기를 사용 or `takeWhile`같은 함수 사용

- 중간 연산은 새로운 시퀀스를 리턴
- 최종 연산은 시퀀스가 아닌 어떤 것이든 리턴 가능
- 시퀀스에서 함수 호출로 연결된 파이프라인을 생성할 때, 최종 연산이 수행될 때까지 어떤 데이터도 시퀀스의 파이프라인을 통과하지 않음

    ``` kotlin
    fun firstNPrimes(count: Int) = 
        generateSequence(2, ::nextPrime) // 무한 시퀀스
        .take(count) // 중간 연산
        .toList() // 최종 연산
    ```

## 4. 시퀀스에서 `yield`하기
> Q. 구간을 지정해 시퀀스에서 값을 생성하고 싶다.<br />
> A. `yield`중단 함수와 함께 `sequence`함수 사용

- 시퀀스를 생성하려면 기존 데이터에서 `sequenceOf` 사용
- 컬렉션을 시퀀스로 변환하려면 `asSequence`사용 or `generateSequence`에 함수를 제공해 값을 생성
- `sequence`함수의 경우에는 필요한 때 `yield`값을 생성하는 람다를 제공해야 함

    ``` kotlin
    val sequence = sequence {
        val start = 0
        yield(start) // 단일 값을 yield
        yieldAll(1..5 step 2) // 범위를 통해 순회 가능(1, 3, 5)를 yield
        yieldAll(generateSequence(8) { it * 3 }) // 8부터 시작해 각각 값에 3을 곱한 값을 원소로 갖는 무한 시퀀스를 yield
    }
    ```

<br />
<br /> 

시퀀스.. 시퀀스 뭔지 모르겠으나, 그냥 읽었다.. 시퀀스 이즈 왔!!!!!!!!!!!!
