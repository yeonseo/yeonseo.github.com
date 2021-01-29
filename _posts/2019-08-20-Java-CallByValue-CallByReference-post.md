---
title: call by value와 call by reference의 차이점을 주소값 출력으로 알아보기
author: Yeonseo Nam
date: 2019-08-20 19:34:00 +0900
categories: [Java, Tutorial]
tags: [call by value, call by reference, TIL, Today I Leaned]
comments : true
---
* Development Env.
* post date : 2019. 08. 20
* OS : macOS Majave 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Eclipse : 2019-06 (4.12.0)


# call by value와 call by reference
<div>call by의 종류에는 아래와 같이 4가지가 있다.</div>

1. call by value
2. call by reference
3. call by name
4. call by address
<div>이 중에서 all by value와 call by reference의 경우를 살펴보겠다.</div>

## call by value
<div>: 값을 넘겨주는 경우</div>

```java
String a = "Hello";
String b = a;
System.out.println("\n------ a & b -----");
System.out.println("hashcode a : "+a.hashCode());
System.out.println("hashcode b : "+b.hashCode());
System.out.println("identityHashCode a : "+System.identityHashCode(a));
System.out.println("identityHashCode b : "+System.identityHashCode(b));
```
<div>이 경우 참조변수 a가 참조하는 "Hello"에 대한 주소값을 참조변수 b도 참조하게 된다. 이 경우에는 call by value인 경우라고 할 수 있다. 각 참조변수 a, b의 hashcode() 값과 System.identityHashcode() 값을 출력해 각각 참조하고 있는 주소값을 확인해보자.</div>

```java
------ a & b -----
hashcode a : 69609650
hashcode b : 69609650
identityHashCode a : 1142020464
identityHashCode b : 1142020464
```
<div>참조변수 a와 b의 출력결과를 보면 모두 같은 값을 가지고 있다는 것을 확인 할 수 있다. 그렇다면 b의 값이 달라진다면 b의 hashcode()와 System.identityHashcode()는 어떻게 보여질까? </div>

```java
System.out.println("\n------ a & b +\"JAVA\"-----");
b = a + "JAVA";
System.out.println("hashcode a : "+a.hashCode());
System.out.println("hashcode b : "+b.hashCode());
System.out.println("identityHashCode a : "+System.identityHashCode(a));
System.out.println("identityHashCode b : "+System.identityHashCode(b));
```
<div>이번에는 b에 "JVAV"라는 문자열을 더한 후에 값을 출력해보았다.</div>

```java
------ a & b +"JAVA"-----
hashcode a : 69609650
hashcode b : -1094639148
identityHashCode a : 1142020464
identityHashCode b : 1682092198
```
<div>a와 b의 hashcode()와 System.identityHashcode() 출력 결과값이 서로 달라진 것을 확인 할 수 있다. Java는 a와 b에게 "Hello"의 주소값을 참조하도록 하다 b가 "JAVA"라는 문자열도 참조해야함을 알고 "HelloJAVA"라는 값을 새로 저장한 후, b가 "HelloJAVA"의 주소를 참조하도록 바꿔주었다.</div>

```java
------ a & b +"JAVA"+"JAVA"-----
hashcode a : 69609650
hashcode b : -1094639148
identityHashCode a : 1142020464
identityHashCode b : 1626877848
```
<div>이후 b에 "JAVA"를 한번 더 더해주었다. 내 예상엔 hashcode()와 System.identityHashcode()가 모두 변할 것 같았다. 하지만 hashcode()값은 위와 같았고 System.identityHashCode() 값만 변하는 것을 확인 할 수 있었다. 시스템의 메모리 상에서 또다른 주소로 "HelloJAVAJAVA"가 저장이 되고 b가 참조하는 주소가 바뀌었다. 어쩌면 당연하다고 생각되겠지만, 이 3단계 과정 중에 a의 hashcode()와 System.identityHashcode()값은 변하지 않았다. `b=a;`를 해주었을 때, call by reference가 아닌 call by value가 일어났기 때문이다.</div>
<div>
그렇다면 call by reference는 무엇이고, 이 경우엔 어떻게 될까?</div>

## call by reference
<div>: 주소로 가서 값을 사용하는 경우

call by reference는 변수가 저장된 주소로 가서 행위를 한다. 값을 변화시키거나 불러오거나 등등의 행위를 하는데, 위에서 확인했듯이 call by vlaue의 경우엔 a와 b(a로부터 주소값을 참조받아 변화된 b)가 각각 주소값을 가지고 서로 각자 변화한 것과는 달리 call by reference의 경우엔 변화가 서로에게 반영된다고도 표현할 수 있고, 값을 변경하는 행위를 했을 때 메소드가 종료되었음에도 값이 유지가 된다고도 표현할 수 있다. c언어에선 포인터의 개념이라고 할 수 있고, JAVA에선 클래스(객체) 개념이라고 할 수 있다.</div>

## Constructor
<div>함수명을 하나만 쓰고, 매개변수 조건만 달리해서 사용한다. </div>
<div>ex) add method</div>

## overloading condition
* return-
* mathod name-
* parameter-
