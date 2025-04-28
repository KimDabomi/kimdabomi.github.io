---
layout: single
title: "[코틀린 쿡북] 10장 입력/출력"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

## 1. `use`로 리소스 관리하기
> Q. 파일 같은 리소스를 처리하고 사용을 끝마쳤을 때 확실하게 리소스를 닫고 싶지만 코틀린은 자바의 `try-with-resources` 구문을 지원하지 않는다.<br />
> A. `kotlin.io` 패키지의 `use`/`java.io.Reader`의 `useLines` 확장 함수 사용

- `try-with-resource` 구문 - 코틀린 지원 X
  - `try` 키워드와 해당 블록 사이에 위치한 괄호 안의 리소스를
  - 블록이 시작할 때 JVM이 개발자를 대신해 자동으로 열어주고
  - 블록이 끝나면 열렸던 리소스를 JVM이 닫아줌
  - 사용할 리소스가 `Closeable` 인터페이스를 구현한 클래스여야 함
- 코틀린에서는 `Closeable`에는 `use`, `Reader`와 `File`에는 `useLine`을 추가함
 
## 2. 파일에 기록하기
> Q. 파일에 기록을 하고 싶다. <br />
> A. `File` 클래스의 확장 함수에는 일반적인 자바 입출력 메서드뿐만 아니라, 출력 스트림과 `Writer`를 리턴하는 확장 함수가 있음

- `forEachLine` - 파일을 순회할 수 있음
- `File`의 `readLines` - 파일이 매우 크지 않은 경우, 모든 줄이 담긴 컬렉션 획득 가능
- `readText`/`readBytes` - 파일의 크기가 작은 경우, 전체 내용을 각각 문자열이나 바이트 배열로 읽어올 수 있음

    ``` kotlin
    File(fileName).pritWriter().use { writer ->
        writer.println(data)
    }
    ```
<br />
<br />

와 짧다!! 기뻐!! 마침 며칠 전에 파일 읽는 거 찾아보고 잠깐 봤던 메서드들인데 딱 나와서 신기하고 반갑다.<br />
나중에 꼭 써먹을 수 있길..... 바라지 않는 게 좋은가? 크흠..
