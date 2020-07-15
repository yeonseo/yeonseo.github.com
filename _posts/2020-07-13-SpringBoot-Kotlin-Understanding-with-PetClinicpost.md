---
layout: post
title: Spring Boot and Kotlin - 펫 클리닉으로 이해하기 1
description: "Spring Boot and Kotlin 펫 클리닉 프로젝트 파해치기"
tags: [Spring, Kotlin, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Spring, Kotlin]
---

Spring Boot + Kotlin 조합으로 진행되는 튜토리얼을 학습하는 과정을 담았습니다. Testing with JUnit 5 / Creating your own extensions / Persistence with JPA / Implementing the blog engine / Exposing HTTP API / Configuration properties

<br/><br/><br/><br/><br/>

<img class="image fit" src="/post/images/springKotlin.png">


---

* Development Env.
* post date : 2020. 07. 13
* OS : macOS Catalina 64bit, Windows 10 64bit
* IntelliJ IDEA 2020.1.2 (Community Edition)
* Spring Boot : 2.3.1
* kotlin("jvm") version "1.3.72"
*	kotlin("plugin.spring") version "1.3.72"
*	kotlin("plugin.jpa") version "1.3.61"
* jvmTarget(JAVA) : "1.8"

---


# Pet Clinic 이란?

> A legendary and official webapp
> The Spring PetClinic is a sample application designed to show how the Spring stack can be used to build simple, but powerful database-oriented applications.
[The Spring PetClinic Community](https://spring-petclinic.github.io/)

아주 유명한 공식적인 웹 앱!
Spring PetClinic은 스프링을 이용해서 만들어진 웹 어플리케이션으로, 간단한 디자인을 보여줍니다. 자주 쓰이는 어노테이션도 적용되어 있고, 테스트 모델도 만들어져 있어서 처음에 공부하기 좋습니다. 이걸로 강의를 찍으시는 분들도 있을 정도,,!

코틀린은 안드로이드 앱과 관련한 코드나 튜토리얼이 많은 편입니다. 스프링을 검색하면 스프링 + 자바 조합으로 굉장히 커뮤니케이션이 잘 발달되어 있지만, 스프링 + 코틀린 튜토리얼이 굉장히 빈약한 편,, ㅠㅠ 그 와중에 펫 클리닉을 코틀린으로도 제공받을 수 있다는 건 얼마나 감사한지!


<img src="/post/images/lets_get_start.gif">


# Spring 이란?

토비의 스프링에서 다음과 같이 정의합니다.

* 애플리케이션 프레임워크
* 경량급
* 자바 엔터프라이즈 개발을 편하게
* 오픈소스

많은 글이나 강의를 보았는데 잘 이해가 가지 않았던 부분이 아래의 동영상 목록을 보고 이해할 수 있었습니다. 뉴렉처라는 분이 올리신 강의에서 Spring이 어떤 작동원리를 가지고 있고, 왜 사용하고 있는지 잘 알 수 있습니다. 
[스프링 프레임워크 강좌/강의](https://www.youtube.com/watch?v=XtXHIDnzS9c&list=PLq8wAnVUcTFUHYMzoV2RoFoY2HDTKru3T)




# Kotlin version of the Spring PetClinic Sample 가져오기

[GitHub - Kotlin version of the Spring PetClinic Sample Application](https://github.com/spring-petclinic/spring-petclinic-kotlin)

위의 주소에서 파일을 받거나 아래의 커멘트로 프로젝트를 clone 하고, Gradle 설치를 진행하신 후 프로젝트를 실행해 봅니다.

```
git clone https://github.com/spring-petclinic/spring-petclinic-kotlin.git
cd spring-petclinic-kotlin
./gradlew bootRun
```
