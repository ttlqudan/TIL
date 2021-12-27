# 2장 코틀린 기초

## 2.1 기본: 함수와 변수
* 클래스 안에 함수를 넣을 필요는 없음
* 자바 라이브러리 간편히 쓸 wrapper 를 제공 (println 등)
* 배열도 일반적 클래스와 동일, 자바와 달리 배열 처리를 위한 문법 없음

```kotlin
fun main(args: Array<String>) {
  println("Hello, world!")
}

fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
```

* 문 (statement) vs 식 (expression)
 - 식은 값을 만들어내며, 다른식의 하위로 계산에 참여가능
  - 문은 블록의 최상위 요소, 아무런 값 안 만듦
  - 코틀린에선 if 는 식이다.루브를 제외한 대부분 제어가 식이다. (자바에선 모든 제어가 문)
  - 대입문은 자바에선 식이지만 코틀린에선 문 ->

* 타입추론 (type inference) : 프로그램 구성요소의 타입을 정해줌
  - 블록이 본문인 함수가 값을 반환하면 반드시 반환 타입을 명시하고 return 명시 필요

* 변수
  - 초기화 안하고 사용하려면 타입 지정 필수
  - val 은 변경 불가하고 초기화를 단 한번 해야되지만 블럭 실행 시 컴파일러가 한번 초기화인걸 알면 여러번 초기화 선언 가능
  - val은 불변이지만 참조 객체의 내부값은 변경 가능
  - var 라도 타입은 고정
* 문자열 템플릿 (string template)
  - 문자열 리터럴 안에서 $변수 혹은 ${변수} 로 사용
  - 중괄호 안 " 사용 가능 `println("Hello, ${if (args.size > 0) args[0] else "someone"}!")`


## 2.2 클래스와 프로퍼티
* 프로퍼티: 필드+접근자
```kotlin
class Person(
  val name: String,       // 읽기전용: private field + getter
  var isMarried: Boolean  // 쓸수있음: private field + getter + setter
  )


  class Rectangle(val height: Int, val width: Int) {
      // 커스텀 접근자
      val isSquare: Boolean
          get() {
              return height == width
          }

      fun isS(): Boolean {
          return height == width
      }
  }

  /*
  println(rectangle.isSquare)
  println(rectangle.isS())
  */
```

* 자바와 달리 클래스명과 파일 이름 달라도 됨 -> 작으면 같이 관리
* 자바와 달리 패키지와 디렉터리 구조 안맞아도 됨 -> 웬만하면 동일하게 하자


## 2.3 선택 표현과 처리: enum과 when
* enum은 소프트 키워드이다.
  - 한개로만 쓰면 이름으로 쓸 수 있고, 자바enum 처럼 쓰려면 enum class 로 써야됨
  - 자바와 달리 break 안해도 됨, 여러개 매칭하려면 콤마(,) 쓰기
  - when 도 if 처럼 식
  - enum에서는 조건 객체와 equality (동등성)을 사용

```kotlin
fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num  // 불필요 casting
        return n.value
    }
    if (e is Sum) {
        return eval(e.right) + eval(e.left) // smart cast 사용 됨.
    }
    throw IllegalArgumentException("Unknown expression")
}
```

* 코를린은 3항연산자가 없다

## 2.4 이터레이션, while 과 for

* 1 .. 100 : 1에서 100 (100 포함)
  - 100 downTo 1 step 2 (100 에서 1 까지 2씩 감소)
  - 1 until 100 : 1 ~ 100 미만 (100 미포함)
  - for ( (k,v) in map )
  - for ( (index, element) in list.withIndex() )
  - println( "Kotlin" in "Java".."Scala")  <-- true
    - Comparable 구현이 두 문자열을 알파벳 순서로 비교
  - when 에서 in 과 !in 을 이용


## 2.5 예외처리
* 예외 처리할때 클래스처럼 new 없어도 됨

* 처리하지 않는 예외를 catch 처리해야되는데, 코틀린에선 다른 최신 jvm 언어와 마찬가지로 checked exception과 unchecked exception 구분 안함
* java 7 에서 부터 제공하는 try-with-resource 를 위한 문법은 없다
  - try-with-resource란?
    - 참고: https://codechacha.com/ko/java-try-with-resources/
