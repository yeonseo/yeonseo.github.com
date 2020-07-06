---
layout: post
title: Spring Boot and Kotlin
description: "NuxtJS를 프론트앤드로 사용하기"
tags: [Spring, Kotlin, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Spring, Kotlin]
---

백앤드로 Spring Boot + Kotlin 조합으로 개발하게 되었습니다. 


<br/><br/><br/><br/><br/>

3. 
![Spring boot & Kotlin](/post/images/springKotlin.png)


---

* Development Env.
* post date : 2020. 07. 02
* OS : macOS Catalina 64bit, Windows 10 64bit
* Nodeserver : 1.0.0
* NuxtJS : 2.13.2

---


# Building web applications with Spring Boot and Kotlin

[Spring공식 kotlin tutorials](https://spring.io/guides/tutorials/spring-boot-kotlin/)

[Spring kotlin - github](https://github.com/spring-guides/tut-spring-boot-kotlin)


프로젝트를 생성하는데 4가지 방법으로 만들 수 있습니다. 

* Using the Initializr Website [https://start.spring.io/](https://start.spring.io/)
* Using command line
* Using IntelliJ IDEA
* Using command line


저는 웹사이트를 통해서 만들어주었고, Kotlin과 Gradle을 선택했습니다.

[Maven vs Gradle](https://bkim.tistory.com/13)을 참고해서 Gradle로 결정을 했습니다. 짧게 말하자면, "Gradle=Ant와 Maven의 장점 모음집" 라고 말할 수 있겠네요. 링크를 타고 들어가면 그래프와 효율적인 측면에서 비교를 잘 해놓으셨으니 궁금하신 분은 참고바랍니다. :-)


## Gradle Build config

1. Plugins 설정해주기

자동으로 생성해 주었으니, 기본적인 설정은 되어 있을겁니다. 제 경우엔 

```kts
kotlin("plugin.jpa") version "1.3.61" 
```

가 없어서 추가 적용했습니다. 


```kts
// build.gradle.kts에 적용된 모습
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
  kotlin("plugin.jpa") version "1.3.61"
  id("org.springframework.boot") version "2.2.2.RELEASE"
  id("io.spring.dependency-management") version "1.0.8.RELEASE"
  kotlin("jvm") version "1.3.61"
  kotlin("plugin.spring") version "1.3.61"
}

```

2. Compiler options 설정해주기

이 부분은 튜토리얼과 동일하게 설정되어 있어서 넘어갔습니다.

```kts
// build.gradle.kts 부분 확인해보기

tasks.withType<KotlinCompile> {
  kotlinOptions {
    freeCompilerArgs = listOf("-Xjsr305=strict")
    jvmTarget = "1.8"
  }
}
```

## Controller 및 templates 추가하기

[Writing your first Kotlin controller](https://github.com/spring-guides/tut-spring-boot-kotlin#writing-your-first-kotlin-controller)


## 실행해 보기

1. settings.gradle.kts 에 rootProject.buildFileName 추가하기

```kts
// settings.gradle.kts
rootProject.name = "blog"
rootProject.buildFileName = "build.gradle.kts"
```

2. Load Gradle Change 눌러주고 기다리기

3. main 함수가 있는 kt 파일로 가서, run 클릭
![Spring boot & Kotlin Run](/post/images/2020-07-06-springKotlin1.png)

4. [http://localhost:8080/](http://localhost:8080/) 에 접속해서 실행되는지 확인하기
![Spring boot & Kotlin Run](/post/images/2020-07-06-springKotlin2.png)


위에서 작성한 코드는 아래의 커밋에서 확인하실 수 있습니다.
[springKotlinBackendTest - 기초 환경 설정 및 기본 컨트롤러 추가](https://github.com/yeonseo/springKotlinBackendTest/commit/f635bac6c5f0907635f61d6fbffc4a275da4024b)