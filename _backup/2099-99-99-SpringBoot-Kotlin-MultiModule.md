---
title: Spring Boot & Kotlin - Multi Module
author: Yeonseo Nam
date: 2020-05-13 19:34:00 +0900
categories: [Kotlin, Tutorial]
tags: [Spring, Kotlin, Web, Backend,TIL, Today I Leaned]
comments : true
---

본 포스팅은 Kotlin In Action - Dmitry Jemerov, Svetlana Isakova 책을 읽고, 기록하기 위한 용도로 작성되었습니다. 이 책의 대상 독자는 어느 정도 자바를 알고 있는 개발자를 대상으로 합니다. 서버 개발자나 안드로이드 개발자, JVM에서 실행되는 프로젝트를 구축중인 개발자들이라면 이 책이 도움이 될 것입니다.


<img class="image fit" src="/post/images/springKotlin.png">


# 코틀린의 함수

## 컬렉션

```kotlin
// hashset
var set = hashSetOf(1, 3, 27)

// list
var list = arrayList(1, 3, 27)

//map
var map = hashMapOf(1 to "one", 3 to "three", 27 to "twenty-seven")
```

위에서 사용한 객체들은 모두 java.util에 속하는 객체들입니다. 즉, 코틀린에서 제공하는 컬렉션 프레임워크는 자바로부터 온 것인데, 코틀린만의 컬렉션을 만들지 않은 이유는 자바 코드와 상호작용하기 쉽도록 설계했기 때문이라고 합니다.

<img class="image fit" src="/post/images/2020-07-27-collections-diagram.png">


## 함수를 호출하기 쉽게

[kotlinlang - functions](https://kotlinlang.org/docs/reference/functions.html#named-arguments)


### (1) 생성자를 오버로딩할 필요가 없음 

* 알아서 인자를 하나씩 제거하며 생성자를 만들어 주기 떼문에 생성자가 없다거나 파라미터 갯수가 틀리다는 오류로부터 자유로워요!!

* 파라미터 입력 시, 필드명도 입력할 수 있어서 가시성이 좋아시면서 혼동이 줄어들고 몇 번째 파라미터 값이 잘못되었다는 오류는 이제 그만!! 볼 수 있습니다.

```kotlin
fun foo(bar: Int = 0, baz: Int) { /*...*/ }

foo(baz = 1) // bar는 디폴드 값인 0이 됩니다.
```

```kotlin
fun reformat(str: String,
             normalizeCase: Boolean = true,
             upperCaseFirstLetter: Boolean = true,
             divideByCamelHumps: Boolean = false,
             wordSeparator: Char = ' ') {
/*...*/
}

// 차례대로 입력해도 되고,
reformat(str, true, true, false, '_')

// 필드명과 함께 차례대로 입력해도 되고,
reformat(str,
    normalizeCase = true,
    upperCaseFirstLetter = true,
    divideByCamelHumps = false,
    wordSeparator = '_'
)

// 넣고 싶은 필드값만 지정해서 입력해도 됩니다.
reformat(str, wordSeparator = '_')
```


### (2) 함수를 클래스로 감쌀 필요가 없음

코틀린에서는 함수를 선언하기 위해서 클래스를 만들 필요가 없습니다. 

```kotlin
package some.package

fun someFunction(...) { ... }
```

자바로 본다면, 코틀린 컴파일러가 생성하는 클래스의 이름은 함수가 들어있던 소스 파일의 이름과 대응됩니다.

만약 some.kt에 위의 내용이 적혀 있었다면, 자바 소스 코드로 볼 때, 다음과 같습니다.

```java
package some.package;

public class SomeKt {
  public static someFunction(...) { ... };
}
```

### (3) 확장 함수와 확장 프로퍼티

[kotlinlang - Extensions](https://kotlinlang.org/docs/reference/extensions.html)

확장 함수는 자바에서 상속을 받은 후, 오버라이드 하여 사용하는 것과 같은 개념입니다. 하지만 상속을 받거나 데코레이터 같은 디자인 패턴이 필요하지 않습니다. 

```kotlin
open class Shape

class Rectangle: Shape()

fun Shape.getName() = "Shape"

fun Rectangle.getName() = "Rectangle"

fun printClassName(s: Shape) {
    println(s.getName())
}    

printClassName(Rectangle())
```

이 코드는 "Shape"를 출력합니다. Rectangle이 Shape 타입으로 만들어졌기 때문에, Shape의 getName()이 실행됩니다.

다음 예제에서 다시 보면,
```kotlin
class Example {
    fun printFunctionType() { println("Class method") }
}

fun Example.printFunctionType() { println("Extension function") }

Example().printFunctionType()
```

결과 값이 "Extension function"로 나타날 것 같지만, 실제로는 "Class method"가 출력됩니다. "kotlinlang.org"에서는 이것을 <strong>"member always wins."</strong> 이라고 하는데요, 클래스가 가지고 있는 함수가 항상 우위를 가집니다.

* 클래스에 멤버함수가 있고,
* 확장함수와 멤버함수가 이름이 같고,
* 확장함수와 멤버함수가 같은 파라미터를 가지면,

-> <strong style="color: red">"member always wins."</strong>

다음과 같이 다른 파라미터 값을 가지는 경우라면, 

```kotlin
class Example {
    fun printFunctionType() { println("Class method") }
}

fun Example.printFunctionType(i: Int) { println("Extension function") }

Example().printFunctionType(1)
```

"Extension function"이 출력됩니다. Int 값의 파라미터를 가진 확장함수가 실행이 된 것이죠. ✨
확장 함수를 만들 때는, 멤버 함수가 더 우선되어서 실행된다는 것을 주의해야 겠습니다.


## 문자열 다루기

### (1) 문자열 나누기

```kotlin
// '.'을 이스캐이프 하는 경우
>>> println("13.58-5".split("\\.|-".toRegex))

// 여러 문자열을 지정하는 경우
>>> println("13.58-5".split(".","-"))

// 두가지 모두 같은 결과값을 볼 수 있습니다.
[13, 58, 5]
```

### (2) 문자열 함수들 (ex 파일 경로명 분리하기)

[kotlinlang - text](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/)

substringBeforeLast()와 substringAfterLast()를 잘 사용하면, 정규식을 사용하지 않고 파일경로와 파일명, 확장자를 쉽게 나눌 수 있습니다.

* path : "/user/dir/folder/a<strong style="color:red">/</strong>test<strong style="color:blue">.</strong>txt"

```kotlin
// 1. "/"를 기준으로 나눔
fun parsePath(path: String) {
  var dir = path.substringBeforeLast("/")
  var fileAndType = path.substringAfterLast("/")

  // 2. "."를 기준으로 나눔
  var filename = fileAndType.substringBeforeLast(".")
  var fileType = fileAndType.substringAfterLast(".")
}
```

### 3중 문자열

```kotlin
val text = """|  ☆
             >| ☆
             >| *.
             >|   +
             >| .*"""

>>> println(text.trimMargin(">")) // trimMargin: 특별한 문자를 포함한 직전의 공백을 제거
// 출력결과는 아래와 같습니다 👍👍
|  ☆
| ☆
| *.
|   +
| .*
```


3중 문자열은 역슬래시"\"를 이용한 이스케이프를 할 필요가 없습니다. 예를 들어, 경로를 나타낼 때 "\\user\\dir\\..."과 같이 나타내야 했습니다. 하지만 3중 문자열을 사용하면 간단히 나타낼 수 있습니다. 

```kotlin
var path = """C:\Users\dir\folder\test.txt"""
// 👍👍👍👍👍
```


불필요한 코드를 줄이기 위해서 노력했다고 하던데 확실히 코드량도 그렇지만 구현하는 방법 자체가 깔끔하다는 생각이 듭니다. 조금 낯설어서 적응을 해야겠지만요.