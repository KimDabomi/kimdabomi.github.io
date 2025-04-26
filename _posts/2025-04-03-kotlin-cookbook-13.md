---
layout: single
title: "[코틀린 쿡북] 13장 코루틴과 구조적 동시성"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

## 1. 코루틴 빌더 선택하기
> Q. 코루틴을 생성하는 올바른 함수를 선택해야 한다. <br />
> A. 사용 가능한 빌더 함수 중에 선택

- 새 코루틴을 생성하려면 빌더 함수 `runBlocking`, `launch`, `async` 중 하나를 사용할 수 있음
- `GlobalScope`에 정의된 `launch`, `async`는 사용하지 말자
- `runBlocking`은 현재 스레드를 블록하고 모든 내부 코루틴이 종료될 때까지 블록함
- `runBlocking` 함수는 인자로서 `CoroutineScope`에 확장 함수로 추가될 `suspend` 함수를 받고, 이 인자로 받은 함수를 실행하고, 실행한 함수가 리턴하는 값을 리턴함

    ``` kotlin
    import kotlinx.coroutines.delay
    import kotlinx.coroutines.runBlocking

    fun main() {
        println("Before creating coroutine")
        runBlocking {
            print("Hello, ")
            delay(200L)
            println("World!")
        }
        println("After coroutine is finished")
    }
    ```

- 독립된 프로세스를 실행하는 코루틴을 시작하고, 해당 코루틴에서 리턴값을 받을 필요가 없다면 `launch` 코루틴 빌더를 사용
- `launch` 함수는 `CoroutineScope`의 확장 함수이기 때문에 `CoroutineScope`이 사용 가능한 경우에만 사용할 수 있음
- `launch` 함수는 코루틴 취소가 필요하면 사용할 수 있는 `Job` 인스턴스를 리턴함

    ```kotlin
    import kotlinx.coroutines.delay
    import kotlinx.coroutines.launch
    import kotlinx.coroutines.runBlocking

    fun main() {
        println("Before runBlocking")
        runBlocking {
            print("Before launch")
            launch {
                print("Hello, ")
                delay(200L)
                println("World!")
            }
            println("After launch")
        }
        println("After runBlocking")
    }
    ```

- 값을 리턴해야 하는 경우에는 일반적으로 `async` 빌더를 사용함
- 함수가 생산된 값을 리턴하기 전에 코루틴이 완료될 때까지 기다리는 `await` 

    ``` kotlin
    import kotlinx.coroutines.async
    import kotlinx.coroutines.coroutineScope
    import kotlinx.coroutines.delay
    import kotlinx.random,Random

    suspend fun add(x: Int, y: Int): Int {
        delay(Random.nextLong(1000L)) // 최대 1000밀리초의 랜덤 지연
        return x + y
    }

    suspend fun main() = coroutineScope { 
        val firstSum = async { // async를 사용해서 코루틴 시작하기
            println(Thread.currentThread().name)
            add(2, 2)
        }
        val secondSum = async {
            println(Thread.currentThread().name)
            add(3, 4)
        }
        println("Awaitin concurrent sums...")
        val total = firstSum.await() + secondSum.await() // 코루틴이 종료될 때까지 블록하기 위해서 await 호출
        println("Total is $total")
    }
    ```

- `coroutineScope` 함수는 종료 전에 포함된 모든 코루틴이 완료될 때까지 기다리는 일시 중단 함수
- 메인 스레드를 블록하지 않는 것이 장점이지만 반드시 일시 중단 함수의 일부로서 호출돼야 함

    ``` kotlin
    import kotlinx.coroutiines.coroutineScope
    import kotlinx.coroutiines.delay
    import kotlinx.coroutiines.launch

    suspend fun main() = coroutineScope { // coroutineScope 빌더
        for (i in 0 until 10) { // 10개의 코루틴 시작
            launch {
                delay(1000L - i * 10) // 줄어드는 시간으로 각각을 지연
                print("$i ")
            }
        }
    }
    ```

## 2. `async`/`await`을 `withContext`로 변경하기
> Q. `async`로 코루틴을 시작하고 바로 다음에 코루틴이 완료될 동안 기다리는 `await` 코드를 간소화하고 싶다.<br />
> A. `async`/`await` 조합을 `withContext`로 변경

- `withContext`는 주어진 코루틴 컨텍스트와 함께 명시한 일시정지 블록을 호출하고, 완료될 때까지 일시정지한 후에 그 결과를 리턴함

    ``` kotlin
    // async와 await를 withContext로 대체하기
    suspend fun retrieve1(url: String) = coroutineScope {
        async(Dispatchers.IO) {
            println("Retrieving data on ${Thread.currentThread().name}")
            delay(100L)
            "asyncResults"
        }.await()
    }

    suspend fun retrieve2(url: String) =
        withContext(Dispatcher.IO) {
            println("Retrieving data on ${Thread.currentThread().name}")
            delay(100L)
            "withContextResults"
        }
        
    fun main() = runBlocking<Unit> {
        val result1 = retrieve1("www.site.com")
        val result2 = retrieve2("www.site.com")
        println("printing result on ${Thread.currentThread().name} $result1")
        println("printing result on ${Thread.currentThread().name} $result2")
    }
    ```

## 3. 디스패처 사용하기
> Q. `I/O` 또는 다른 작업을 위한 전용 스레드  풀을 사용해야 한다.<br />
> A. `Dispatchers` 클래스에서 적당한 디스패처를 사용

``` kotlin
// 기본 디스패처와 I/O 디스패처 사용하기
fun main() = runBlocking<Unit> {
    launchWithIO()
    launchWithDefault()
}

suspend fun launchWithIO() {
    withContext(Dispatchers.IO) {
        delay(100L)
        println("Using Dispatchers.IO")
        println(Thread.currentThread().name)
    }
}

suspend fun launchWithDefault() {
    withContext(Dispatchers.Default) {
        delay(100L)
        println("Using Dispatchers.Default")
        println(Thread.currentThread().name)
    }
}
```
 
## 4. 자바 스레드 풀에서 코루틴 실행하기
> Q. 코루틴을 사용하는 사용자 정의 스레드 풀을 제공하고 싶다.<br />
> A. 자바 `ExecutorService`의 `asCoroutineDispatcher`함수 사용

``` kotlin
// use를 사용해서 디스패처 닫기
Executors.newFixedThreadPool(10).asCoroutineDispatcher().use {
    withContext(it) {
        delay(100L)
        println(Thread.currentThread().name)
    }
}
```

## 5. 코루틴 취소하기
> Q. 코루틴 내의 비동기 처리를 취소하고 싶다.<br />
> A. `launch` 빌더 또는 `withTimeout` 이나 `withTimeoutOrNull` 같은 함수가 리턴하는 `Job` 래퍼런스를 사용

- `launch` 빌더는 코루틴을 취소하기 위해 사용할 수 있는 `Job` 타입의 인스턴스를 리턴하고 이를 지역 변수에 할당함
 
## 6. 코루틴 디버깅
> Q. 코루틴의 실행 정보가 더 많이 필요하다.<br />
> A. JVM에서 `-Dkotlinx.coroutines.debug` 플래그를 사용해서 실행

<br />
<br />

코루틴은 진짜진짜 말로만 들어봐서... 초면...하지만 이제 얼굴 텄으니까 계속 볼 힘이 생겼달까....조금이지만..
