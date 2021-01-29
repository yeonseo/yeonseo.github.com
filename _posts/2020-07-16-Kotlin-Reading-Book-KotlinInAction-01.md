---
title: Kotlin In Action 정리 01편 - 코틀린이란? (Kotlin In Action - Dmitry Jemerov, Svetlana Isakova)
author: Yeonseo Nam
date: 2020-07-16 19:34:00 +0900
categories: [Kotlin, Tutorial]
tags: [Spring, Kotlin, Web, Backend,TIL, Today I Leaned]
comments : true
---

본 포스팅은 Kotlin In Action - Dmitry Jemerov, Svetlana Isakova 책을 읽고, 기록하기 위한 용도로 작성되었습니다. 이 책의 대상 독자는 어느 정도 자바를 알고 있는 개발자를 대상으로 합니다. 서버 개발자나 안드로이드 개발자, JVM에서 실행되는 프로젝트를 구축중인 개발자들이라면 이 책이 도움이 될 것입니다.


<img class="image fit" src="/post/images/springKotlin.png">


# 코틀린이란?

## 코틀린의 특성

### 대상 플랫폼

* 서버상의 코드(특히 웹 애플리케이션의 백앤드)
* 안드로이드 디바이스에서 실행되는 모바일 애플리케이션



### 정적타입 지정언어

정적지정 타입 : 모든 프로그램 구성 요소의 타입을 컴파일 시섬에 알 수 잇고 프로그램 안에서 객체의 필드나 메소드를 사용할 때마다 컴파일러가 타입을 점증해준다는 뜻

정적지정 장점 :

* 성능
* 신뢰성
* 유지보수성
* 도구 지원


그래서 자바와 다른 점은???

* 타입 추론 : 코틀린이 자바와 다른 점은 모든 변수의 타입을 직접 명시할 필요가 없다는 것입니다. 코틀린 컴파일러가 문맥으로부터 변수 타입을 자동으로 유추하는데, 이를 "<strong>타입 추론</strong>"이라고 부릅니다.
* 널이 될 수 있는 타입 (Nullable Type) :  <strong>Nullable Type</strong>을 사용할 수 있게 되면서 컴파일 시점에 널 포인터 에외처리에 대한 대응을 할 수 있다는 것 입니다. (이 부분에서 자세히 다루는 글들을 많이 보았는데, 뒷 장에서 다룰 때 다시 정리하겠습니다.)
* 함수 타입 (Function Type) : 자바는 1.8부터 지윈되었지만 코틀린은 처음부터 함수형 타입을 염두하고 개발된 언어입니다. 함수형 타입으로 프로그램을 짤 수 있게 지원하지만 반드시 작성해야하는 것은 아닙니다. 

* 스마트 캐스트 : 타입검사와 캐스트를 한번에 지원해줍니다. 자바에서 instanceof와 비슷하고 c#에서는 is!!


<img src="/post/images/2020-07-16-kotlin-build-process1.png">
<img src="/post/images/2020-07-16-kotlin-build-process2.png">


</br></br></br></br></br>

# 코틀린 기초

## 함수

* 함수 선언


자바와 다르게 꼭 클래스 안에서 선언할 필요없습니다.

```kotlin 
fun '함수 이름'( 파라미터들: 파라미터 타입, 파라미터들: 파라미터 타입 ) : return 타입 {
    return if ( 조건 ) 리턴값1 else 리턴값2
}
```

* 식이 본문인 함수

식이 본문인 함수라는 표현이 좀 생소했는데, 익숙하게 접했던게 "블록이 본문인 함수"라고 불리더라구요.

fun abc() : returnType { returnValue} 형태

본문이 중괄호로 둘러싸인 함수를 "블록이 본문인 함수", "블록"

fun abc() = returnValue 형태가 "식"으로 표현되었습니다.
등호와 식으로 이뤄진 함수를 "식이 본문인 함수"

```kotlin
fun '함수 이름'( a: Int , b: Int ) : Int = if ( a > b ) a else b

// 반환 타입까지도 생략하여 작성할 수 있습니다.
fun '함수 이름'( a: Int , b: Int ) = if ( a > b ) a else b
```

함수의 반환타입을 생략할 수 있는 이유는 앞에서 언급한 "타입 추론"을 통해 컴파일러가 함수의 리턴값를 파악하고 타입을 정해줍니다.



## 변수

* 변수명이 앞으로 온다.

```kotlin
// 타입 생략
var test = 30

// 타입 명시
var test: Int = 30
```


* 변경 가능한 변수와 불가능한 변수

```kotlin

/* var */
var test1 = "변경할 수 있는 변수 표현입니다. variable에서 따옴"
test1 = 20 // Error!!, 타입은 불변!

/* val */
val test2 = "변경할 수 없는 변수 표현입니다. value에서 따옴"
```

기본적으로는 val을 사용해 불변 변수로 선언하고, 필요할 때만 var 로 변경하는 것이 좋다. 경우에 따라 다른 값을 가져야 하는 경우에는 다음과 같이 작성할 수 있습니다.

```kotlin
val message: String
if ( checkSomething() ) {
    message = "Success"
} else {
    message = "Fail"
}
```


* 문자열 템플릿

```kotlin
val name = "Cat"
println("Hello~ $name!")
println("Hello~ ${name}!")
```

문자열 속에 $를 붙여 변수명을 적어주면, 문자열을 합치는 것과 같은 기능을 합니다. "" + "" 와 같은 형식보다는 훨씬 간편...!! 주의할 점은 한글에서는 오류를 일으킬 수 있기 때문에 ${}로 사용하는 것을 습관화하는 것이 좋습니다. 코드 가독성 상승은 덤!


## 클래스와 프로터피

```kotlin
class Person (
    val name: String,
    var age: Int
)
```

* 기본 접근제어자는 public이다. 위의 클래스는 public이 생략되어 있는 모습!
* val : private 필드 / public Getter 를 제공한다.
* var : private 필드 / public Getter /public Setter 를 제공한다.
* JAVA 에서는 getName()이나 getAge() 등의 Getter함수를 사용해서 값을 얻었지만, 코틀린에서는 프로퍼티 이름으로 값을 불러올 수 있습니다.

```kotlin
val person = Person("Herry Poter", 14)
println(person.name)
println(person.age)
```

## 선택 표현 처리, enum / when

* enum은 열거형을 처리할 때 사용합니다.

```kotlin
enum class CardType {
    SILVER, GOLD, PLATINUM
}
```

* when은 JAVA에서 switch에 해당하는 요소입니다. 


### 사용 예시

[Kotlin enum](https://www.baeldung.com/kotlin-enum)
[Kotlin when](https://kotlinlang.org/docs/reference/control-flow.html#when-expression)



## 스마트 캐스트

c#에서의 is, JAVA에서는 instanceof와 비슷!

```kotlin
if (value is String) // is 로 타입검사
    println(value.toUpperCase()) //
```


자바와 비교하면 정말정말정말 심플하네요..! 🎶 (약간 충격적,,) [자바 - instanceof](https://www.javatpoint.com/downcasting-with-instanceof-operator)
```java
class Animal { } 
class Dog3 extends Animal {  
  static void method(Animal a) {  
    if(a instanceof Dog3){  
       Dog3 d=(Dog3)a;//downcasting  
       System.out.println("ok downcasting performed");  
    }  
  }  
  public static void main (String [] args) {  
    Animal a=new Dog3();  
    Dog3.method(a);  
  }
}
```


## 수의 범위

자바에서 for문을 사용하기 위해서는 아래와 같이 수의 범위를 지정해야 합니다.
```java
for(i = 0 ; i >= 10 ; i = i + 2) {
  ...
}
```

Kotlin에서는 좀 더 직관적입니다.
```kotlin
for (i in 1..100) {
  ...
} // -> 반복 : 1, 2, 3, ... , 100

for (i in 100 downTo 0 step 2) {
  ...
} // -> 반복 : 100, 98, 96, ... , 0
```

범위에는 문자 타입의 값도 사용할 수 있습니다. 'A'..'F'로 사용할 수 있죠. 

그리고 in을 통해서 범위 안에 찾고자 하는 요소의 존재 여부에 따른 Boolean값을 받을 수도 있습니다. (OMG..!!!)

```kotlin
var c = 'b'
println(c in 'a'..'z' || c in 'A'..'Z') // true
println(c !in '0'..'9') // false
```

이상 코틀린 기초에 대해서 알아보았습니다. 다음 포스팅에서는 함수에 대해서 정리해 보도록 하겠습니다.