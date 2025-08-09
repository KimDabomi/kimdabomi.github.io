---
layout: single
title: "[코틀린 쿡북] 12장 스프림 프레임워크"
categories:
  - book
sidebar:
  nav: "sidebar-category"
---

## 1. 확장을 위해 스프링 관리 빈 클래스 오픈하기
> Q. 스프링은 개발자가 작성한 클래스를 확장하는 프록시를 생성해야 한다. 하지만 코틀린 클래스는 기본으로 닫혀있다.<br />
> A. 확장을 위해 자동으로 필요한 스프링 관리 클래스를 열어주는 코틀린 스프링 플러그인을 빌드 파일에 추가

- 프록시와 실체는 둘 다 같은 인터페이스를 구현하거나 같은 클래스를 확장함
- 들어오는 요청을 프록시가 가로채고, 이 프록시는 서비스가 요구하는 모든 것을 적용한 다음 실체로 요청을 전달
- 코틀린은 기본으로 정적으로 결합함. 클래스가 `open` 키워드를 사용해 확장을 위한 열림으로 표시되지 않으면 메서드 재정의 또는 클래스 확장이 불가능함
- `all-open` 플러그인으로 코틀린 클래스를 여는 어노테이션을 명시할 수 있음
  - `@Component`, `@Async`, `@Transactional`, `@Cacheable`, `@SpringBootTest`
 

## 2. 코틀린 `data class`로 퍼시스턴스 구현하기
> Q. 코틀린 `data class`로 자바 퍼시스턴스 API를 사용하고 싶다.<br />
> A. `kotlin-jpa` 플러그인을 빌드 파일에 추가

- JPA는 모든 속성에 기본값을 제공하지 않는 이상 기본 생성자가 필수지만 `data class`에는 기본 생성자가 없음
- `val` 속성과 함께 `data class`를 생성하면 불변 객체가 생성되는데, JPA는 불변 객체와 더불어 잘 동작하도록 설계되지 않음
- `kotlin-jpa` 플러그인은 다음 어노테이션으로 자동 표시된 클래스에 기본 생성자를 추가함
  - `@Entity`, `@Embeddable`, `@MappedSuperclass`
- JPA가 엔티티에 불변 클래스를 사용하고 싶어하지 않음. 엔티티로 사용하고 싶은 코틀린 클래스에 필드 값을 변경할 수 있게 속성에 `var` 타입을 사용하는 단순 클래스의 사용을 추천함

    ``` kotlin
    @Entity
    class Article(
        var title: String,
        var headline: String
        var content: String,
        @ManyToOne var author: User,
        var slug: String = title.toSlug(),
        var addedAt: LocalDateTime = LocalDateTime.now(),
        @Id @GeneratedValue var id: Long? = null,
    )
    ```

## 3. 의존성 주입하기
> Q. 오토와이어링이 필요한 빈과 필요하지 않는 빈을 선언하고 의존성 주입을 사용해서 빈을 서로 오토와이어링하고 싶다.<br />
> A. 코틀린 스프링은 생성자 주입을 제공핳지만, 필드 주입에는 `lateinit var` 구조를 사용해야 함. 선택적인 빈은 널 허용 타입으로 선언

- 스프링은 가능하다면 의존성을 생성자로 주입하는 것을 선호함
- 코틀린에서도 `@Autowired` 어노테이션을 생성자 인자에 직접 사용할 수 있음
- 클래스에서 생성자가 하나뿐이라면 스프링이 자동으로 주입해주기 때문에 `@Autowired` 어노테이션을 사용할 필요가 없음
- `autowired` 생성자를 사용하면 `lateinit var` 접근 방식을 사용할 필요가 없음

<br />
<br /> 

스프링이랑 같이 나오는 특성이 왜이렇게 적지!! 이건 JPA를 따로 더 봐야될 것 같다!
