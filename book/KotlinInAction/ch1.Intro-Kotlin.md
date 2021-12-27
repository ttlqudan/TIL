# 코틀린 맛보기

* 예제코드: https://www.manning.com/books/kotlin-in-action

### 코틀린 맛보기

* playground: https://try.kotl.in/

```kotlin
data class Person // data class
  (val name: String,
    val age: Int? = null)  // null 가능한 타입과 파라미터 디폴트값

fun main() {  // 최상위 함수
    println("Hello, world!!!")

    val persons = listOf(Person("영희"),
        Person("철수", age = 29)) // 이름붙은 파라미터

    val oldest = persons.maxByOrNull{ it.age?: 0 } // 람다식과 엘비스 연산자
    println("oldeset: $oldest") // 문자열 템플릿
}

// 결과: oldeset: Person(name=철수, age=29)  <-- toString 자동 생성
```

### 1.2 코틀린 주요 특성
* 대상 플랫폼: 서버,안드 등 자바 플랫폼
* 정적 타입 언어 (동적인 ruby, groovy와 다르다.) : 컴파일 시 타입에 따라 오류 도출
  - 성능, 신뢰성, 유지보수성, 도구지원, nullable type
* 함수형 프로그래밍
  - first class Function : 함수를 값처럼 다룸. 변수저장, 파라미터, 반환
  - 불변성 + no side effect
* 코틀린 철학
  - 실용, 간결, 안정, 상호운영
