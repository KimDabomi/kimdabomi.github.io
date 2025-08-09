---
layout: single
title: "[코틀린 쿡북] 3장 코틀린 객체 지향 프로그래밍"
categories:
  - book
sidebar:
  nav: "sidebar-category"
---

**자바처럼 코틀린도 객체 지향 프로그래밍 언어**

## 1. `const`와 `val`의 차이 이해하기
> Q. 런타임보다는 컴파일 타임에 변수가 상수임을 나타내야 한다.<br />
> A. 컴파일 타임 상수에 `const`변경자를 사용. `val`키워드는 변수에 한 번 할당되면 변경 불가! 하지만 이 할당은 실행 시간에 일어남

- `val`은 값이 변경 불가능한 변수 ( like 자바의 `final` )
- `const`는 키워드가 아니라 변경자

## 2. 사용자 정의 획득자와 설정자 생성하기
> Q. 값을 할당하거나 리턴하는 방법을 사용자 정의하고 싶다.<br />
> A. 코틀린 클래스의 속성에 `get`, `set`함수를 추가

- 코틀린은 모든 것이 기본적으로 `public`
- 아래처럼 속성을 정의하면, 사용자 정의 획득자와 설정자를 추가할 수 있음
- 생성자에서 선언한 속성에는 할당된 기본값이 있더라도 반드시 타입 정의가 들어 있어야 함

    ``` kotlin
    class Task(val name: String) {
        var priority = 3
    }
    ```

## 3. 데이터 클래스 정의하기
> Q. `equals`, `hashCode`, `toString` 등이 완벽하게 갖춰진 엔티티를 나타내는 클래스를 생성하고 싶다.<br />
> A. `data`키워드 사용

- 데이터를 담는 특정 클래스 `data class`

    ``` kotlin
    data class Product(
        val name: String,
        val price: Double,
    )
    ``` 

## 4. 지원 속성 기법
> Q. 클래스의 속성을 클라이언트에게 노출하고 싶지만 해당 속성을 초기화하거나 읽는 방법을 제어해야 한다.<br />
> A. 같은 타입의 속성을 하나 더 정의하고 사용자 정의 획득자와 설정자를 이용해 원하는 속성에 접근

- `null`허용의 `private`속성 초기화 후 불러올 속성에 대한 `private`함수 `get()`을 통해 메시지를 불러올 수 있음

    ``` kotlin
    class Customer(val name: String) {
        private var _messages: List<String>? = null

        val messages: List<String>
            get() {
                return _messages!!
            }
    }
    ```

## 5. 연산자 중복
> Q. 라이브러리에 정의된 클래스와 더불어 + 와 * 같은 연산자를 사용할 수 있는 클라이언트를 만들고 싶다.<br />
> A. 코틀린의 연산자 중복 메커니즘을 사용

- 코틀린에서는 +, -, * 기호를 사용하면 해당 연산자와 연관된 함수에 처리를 위임
 
## 6. 나중 초기화를 위해 `lateinit` 사용하기
> Q. 생성자에 속성 초기화를 위한 정보가 충분하지 않으면 해당 속성을 널 비허용 속성으로 만들고 싶다.<br />
> A. `lateinit` 변경자 사용

- 모든 객체가 생성될 때까지 의존성 주입이 일어나지 않는 의존성 프레임워크에서 발생하거나, 유닛 테스트의 설정 메서드 안에서 발생
- 클래스 몸체에서만 선언되고, 사용자 정의 획득자와 설정자가 없는 `var`속성에서만 사용할 수 있음
- 널 할당이 불가능한 타입이어야 하며, 기본 타입에는 사용할 수 없음

## 7. `equals` 재정의를 위해 안전 타입 변환, 레퍼런스 동등, 엘비스 사용하기
> Q. 논리적으로 동등한 인스턴스인지를 확인하도록 클래스의 `equals`메서드를 잘 구현하고 싶다.<br />
> A. 레퍼런스 동등 연산자(`===`), 안전 타입 변환 함수(`as?`), 엘비스 연산자(`?:`) 를 다같이 사용

- `equals`구현은 반사성, 대칭성, 추이성, 일관성이 있어야 하며, 널도 적절하게 처리할 수 있어야 함
- `===` 로 레퍼런스 동등성 확인
- `as?` 로 원하는 타입으로 변경
- `?:` 로 널인 경우 처리

    ``` kotlin
    class Customer(val name: String) {
        override fun equals(other: Any?): Boolean {
            if (this === other) return true
            val otherCustomer = (other as? Customer) ?: return false
            return this.name == otherCustomer.name
        }
        
        override fun hashCode() = name.hashCode()
    }
    ```

## 8. 싱글톤 생성하기
> Q. 클래스 하나당 인스턴스는 딱 하나만 존재하게 만들고 싶다.<br />
> A. `class`대신 `object`키워드 사용

- 싱글톤을 정의하는 방법
  - 클래스의 모든 생성자를 `private`으로 정의
  - 필요하다면 해당 클래스를 인스턴스화하고, 그 인스턴스 레퍼런스를 리턴하는 정적 팩토리 메서드를 제공
- `object` 키워드 사용! 하지만 `object`는 생성자를 가질 수 없기 때문에 쉽게 인자를 전달할 수 있는 방법이 없음

    ``` kotlin
    object Singleton{
        val property = 3
        
        fun function() = "hello"
    }
    ```

## 9. `Nothing`에 관한 야단법석
> Q. `Nothing`클래스를 사용법에 맞게 적절하게 사용하고 싶다.<br />
> A. 절대 리턴하지 않는 함수에 사용

- 리턴 타입을 명시해야 하는데, 리턴하지 않는 메서드인 경우 리턴 타입은 `Nothing`
- 널이 할당 가능한 변수에 대해 컴파일러가 추론한 타입은 `Nothing`

<br />
<br />

뭔가 기초인듯 기초아닌 기초같은 장이다.<br />
`Nothing`이라는 타입은 난생 처음봤고요!<br /> 
`data`, `object`같은 키워드는 알고 있던 거라 반갑기도 했고,<br />
지원 속성 기법?? 언더바(_) 붙은 변수를 본 적은 있지만 어떤 상황에서 사용하는지 몰랐는데 새롭게 알게 되었다!
