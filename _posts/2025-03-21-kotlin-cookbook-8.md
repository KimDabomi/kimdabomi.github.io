---
layout: single
title: "[코틀린 쿡북] 8장 코틀린 대리자"
categories:
  - book
sidebar:
  nav: "sidebar-category"
---

## 1. 대리자를 사용해서 합성 구현하기
> Q. 다른 클래스의 인스턴스가 포함된 클래스를 만들고, 그 클래스에 연산을 위임하고 싶다.<br />
> A. 연산을 위임할 메서드가 포함된 인터페이스 생성 -> 클래스에서 해당 인터페이스 구현 -> `by`키워드를 사용해 바깥쪽에 래퍼 클래스 생성

- `by`키워드는 포함된 객체에 있는 모든 `public`함수를 이 객체를 담고 있는 컨테이너를 통해 노출할 수 있음
- 그러려면 포함된 객체의 `public`메서드의 인터페이스를 생성해야 함

    ``` kotlin
    interface Dialable {
        fun dial(number: String): String
    }

    class Phone: Dialable {
        override fun dial(number: String) =
            "Dialing $number..."
    }

    class SmartPhone(
        private val phone: Dialable = Phone(),
    ): Dialable by phone
    ```

## 2. `lazy`대리자 사용하기
> Q. 어떤 속성이 필요할 때까지 해당 속성의 초기화를 지연시키고 싶다.<br />
> A. 코틀린 표준 라이브러리의 `lazy`대리자 사용

``` kotlin
val ultimateAnswer: Int by lazy {
    println("computing the answer")
    42
}
``` 

## 3. 값이 널이 될 수 없게 만들기
> Q. 처음 접근이 일어나기 전에 값이 초기화되지 않았다면 예외를 던지고 싶다.<br />
> A. `notNull`함수를 이용해 값이 설정되지 않았다면 예외를 던지는 대리자를 제공

- 속성 초기화를 지연시키는 한 가지 방법은 속성에 처음 접근하기 전에 속성이 사용되면 예외를 던지는 대리자를 제공하는 `notNull`함수 사용하는 것
 
## 4. `observable`과 `vetoable`대리자 사용하기
> Q. 속성의 변경을 가로채서, 필요에 따라 변경을 거부하고 싶다.<br />
> A. 변경 감지에는 `observable`함수 사용. 변경의 적용 여부를 결정할 때에는 `vetoable`함수와 람다 사용

- `ObservableProperty`클래스는 모든 제네릭 타입의 속성을 저장하고, `ReadWriteProperty`인터페이스를 구현함
- `ObservableProperty`클래스가 `ReadWriteProperty`에서 보여주는 시그니처의 `getValue`와 `setValue`함수를 제공해야 한다는 의미
- `vetoable`함수도 `ObservableProperty`를 확장하는 클래스로부터 객체를 생성하지만 `vetoable`함수는 `beforeChange`함수를 오버라이드함
- `vetoable`의 람다 구현은 인자로 제공되고 해당 람다는 반드시 해당 속성의 변경 여부를 결정할 수 있는 불리언을 리턴해야 함

    ``` kotlin
    var watched: Int by Delegates.observable(1) { prop, old, new ->
        println("${prop.name} changed from $old to $new")
    }

    var checked: Int by Delegates.vetoable(0) { prop, old, new ->
        println("Trying to change ${prop.name} from $old to $new")
        new >= 0
    }
    ```

## 5. 대리자로서 `Map` 제공하기
> Q. 여러 값이 들어있는 맵을 제공해 객체를 초기화하고 싶다.<br />
> A. 코틀린 맵에는 대리자가 되는 데 필요한 `getValue`, `setValue`함수 구현 존재

- 객체 초기화에 필요한 값이 맵 안에 있다면 해당 클래스 속성을 자동으로 맵에 위임할 수 있음

    ``` kotlin
    private fun getMapFromJSON() = // JSON 문자열로 속성 맵을 가공
        Gson().fromJson<MutableMap<String, Any?>>(
            """{ "name":"Learn Kotlin", "priority":5, "completed":true }""",
            MutableMap::class.java
        )
        
    @Test
    fun `create project from map parsed from JSON string`() {
        val project = Project(getMapFromJSON()) // 인스턴스화를 위해 맵을 사용
        asswetAll(
            { assertEquals("Learn Kotlin", project.name) },
            { assertEquals(5, project.priority) },
            { assertTrue(project.completed) },
        )
    }
    ``` 

## 6. 사용자 정의 대리자 만들기
> Q. 어떤 클래스의 속성이 다른 클래스의 획득자와 설정자를 사용하게끔 만들고 싶다.<br />
> A. `ReadOnlyProperty`/`ReadWriteProperty` 를 구현하는 클래스를 생성함으로써 직접 속성 대리자를 작성

- 사용자 정의 속성 대리자를 생성하려면 `ReadOnlyProperty` 또는 `ReadWriteProperty` 인터페이스에 존재하는 함수를 제공해야 함

    ``` kotlin
    val myProperty: String by project // project 속성 myProperty를 사용 가능하게 만듦
    val myNullableProperty: String? by project // 널이 될 수 있는 속성을 사용 가능하게 만듦

    val myNewProperty by extra("initial value") // extra 속성 myNewProperty를 만들고 초기화
    val myOtherNewProperty by extra { "lazy initial value" } // 처음 접근이 일어날 때 초기화되는 속성을 생성
    ```   

<br />
<br />

대리자... 그게 뭔데 날 울려..이해한 건 lazy 밖에 없어요 엉엉
