---
title: Spring Boot and Kotlin 웹 튜토리얼 2
author: Yeonseo Nam
date: 2020-07-06 21:34:00 +0900
categories: [Spring, Kotlin, Tutorial]
tags: [Spring, Kotlin, Web, Backend,TIL, Today I Leaned]
comments : true
---

Spring Boot + Kotlin 조합으로 진행되는 튜토리얼을 학습하는 과정을 담았습니다. Testing with JUnit 5 / Creating your own extensions / Persistence with JPA / Implementing the blog engine / Exposing HTTP API / Configuration properties

<br/><br/><br/><br/><br/>

<img class="image fit" src="/post/images/springKotlin.png">


---

* Development Env.
* post date : 2020. 07. 06
* OS : macOS Catalina 64bit, Windows 10 64bit
* IntelliJ IDEA 2020.1.2 (Community Edition)
* Spring Boot : 2.3.1
* kotlin("jvm") version "1.3.72"
*	kotlin("plugin.spring") version "1.3.72"
*	kotlin("plugin.jpa") version "1.3.61"
* jvmTarget(JAVA) : "1.8"

---


# Understanding the generated Application


[Spring공식 kotlin tutorials](https://spring.io/guides/tutorials/spring-boot-kotlin/)

[Spring kotlin - github](https://github.com/spring-guides/tut-spring-boot-kotlin)


## Testing with JUnit 5 / Creating your own extensions / Persistence with JPA / Implementing the blog engine 진행

<br/><br/><br/>

### 1. Entity클래스 (Post 클래스)


build.gradle.kts에 설정 추가
```
plugins {
  ...
  kotlin("plugin.allopen") version "1.3.61"
}

allOpen {
  annotation("javax.persistence.Entity")
  annotation("javax.persistence.Embeddable")
  annotation("javax.persistence.MappedSuperclass")
}
```

Kotlin [primary constructor concise syntax](https://kotlinlang.org/docs/reference/classes.html#constructors) 을 사용하여 모델을 생성하고 동시에 속성과 생성자 변수를 동시에 선언할 수 있습니다. 아래의 코드를 보면, User와 Article 모델에 대한 정의와 그 둘의 관계(@ManyToOne var author: User)까지 정의되어 있습니다. 쿼리문을 통해 DB를 관리하지 않고, 이처럼 클래스를 통해서 DB를 관리하게 됩니다.

src/main/kotlin/com/example/blog/Entities.kt
```
@Entity
class Article(
    var title: String,
    var headline: String,
    var content: String,
    @ManyToOne var author: User,
    var slug: String = title.toSlug(),
    var addedAt: LocalDateTime = LocalDateTime.now(),
    @Id @GeneratedValue var id: Long? = null)

@Entity
class User(
    var login: String,
    var firstname: String,
    var lastname: String,
    var description: String? = null,
    @Id @GeneratedValue var id: Long? = null)
```
[코틀린(Kotlin) data class를 JPA에서 사용시 주의할 점](https://anomie7.tistory.com/64)


### 2. url 처리 밎 파라미터를 전달 (@GetMapping, @PathVariable)

src/main/kotlin/com/Project명/blog/HtmlController.kt
```
@GetMapping("/article/{slug}")
  fun article(@PathVariable slug: String, model: Model): String {
    val article = repository
            .findBySlug(slug)
            ?.render()
            ?: throw ResponseStatusException(HttpStatus.NOT_FOUND, "This article does not exist")
    model["title"] = article.title
    model["article"] = article
    return "article"
  }
```

url에 파라미터를 전달하는 경우 2가지 방법으로 전달 할 수 있습니다. 위의 예제에서는 @PathVariable 방식을 통해서 /article/{slug} 형태의 url로 처리를 하고 있습니다. [@GetMapping, @PathVariable, @RequestParam](https://elfinlas.github.io/2018/02/18/spring-parameter/) 여기를 참고해서 다른 어노테이션의 사용법도 알아두면 좋을 것 같습니다.



```kt
  @GetMapping("/article/{slug}")
  fun article(@PathVariable slug: String, model: Model): String {
    val article = repository
            .findBySlug(slug)
            ?.render()
            ?: throw ResponseStatusException(HttpStatus.NOT_FOUND, "This article does not exist")
    model["title"] = article.title
    model["article"] = article
    return "article"
  }
```

return "article"로 templates에 "article.mustache" 파일을 찾아 랜더링 해줍니다. 진행하다가 templates에서 article을 artcle로 잘못적는 바람에 404 에러를 만났습니다. ㅠ 이유를 알지 못했다가, return 해주는 값과 templates의 값이 같아야 한다는 글을 보고 오류를 잡을 수 있었습니다. ㅠㅠㅠㅠ 조심조심

[Git Commit - 기사 페이지를 get방식으로 요청 시, 기사제목으로 데이터 찾은 후 render](https://github.com/yeonseo/springKotlinBackendTest/commit/c79deacfa6769fab728c3bb1c380a2d422f00338)



## Exposing HTTP API

<br/><br/><br/>

### @RestController (vs @Controller)

앞의 HtmlController 에서는 @Controller 를 사용하여 @GetMapping 을 적용한 모습을 볼 수 있습니다. HttpControllers에서는 @RestController를 사용하였는데, 이 둘의 차이를 알아보도록 하겠습니다.

[@Controller와 @RestController](https://mangkyu.tistory.com/49)

* @Controller : View 반환

* @RestController : Json/Xml 형태로 객체 데이터를 반환

@Controller는 기본적으로 뷰를 반환합니다. 데이터를 반환하게 하기 위해서는 @ResponseBody 어노테이션을 함께 사용해야 했습니다. @RestController 어노테이션은 스프링 4점대 버전부터 지원하는 어노테이션으로, 

<strong>@RestController = @Controller + @ResponseBody</strong>

즉, @RestController 만으로 문자열과 JSON 등을 전송할 수 있습니다.

```
@RestController
@RequestMapping("/api/article")
class ArticleController(private val repository: ArticleRepository) {

    @GetMapping("/")
    fun findAll() = repository.findAllByOrderByAddedAtDesc()

    @GetMapping("/{slug}")
    fun findOne(@PathVariable slug: String) =
            repository.findBySlug(slug) ?: throw ResponseStatusException(NOT_FOUND, "This article does not exist")

}
```

위의 코드를 보면 [http://localhost:8080/api/article/reactor-aluminium-has-landed](http://localhost:8080/api/article/reactor-aluminium-has-landed) 로 접근할 때 JSON 형태로 데이터를 전송합니다.

<img class="image fit" src="/post/images/2020-07-06-springKotlin3.png">

저는 Spring Boot+Kotlin 을 백앤드로 사용할 예정이라서, @RestController를 많이 사용할 것 같네요 :-) 함께 쓰이는 [@RequestMapping](https://joont92.github.io/spring/@RequestMapping/)도 학습해두면 좋을 것 같습니다.

## Configuration properties

생성자를 기반한 속성값 연동을 가능하게 해줍니다. application.properties 파일에서 속성값을 읽어 바인딩 할 수 있습니다.

```
blog.title=Blog
blog.banner.title=Spring Boot + Kotlin
blog.banner.content=https://github.com/spring-guides/tut-spring-boot-kotlin
```


클래스에 @ConfigurationProperties 가 선언되어 있거나 생성자에서 @ConstructorBinding을 선언하여 사용합니다.

BlogProperties.kt
```
package com.springBackendTest.blog

import org.springframework.boot.context.properties.ConfigurationProperties
import org.springframework.boot.context.properties.ConstructorBinding

@ConstructorBinding
@ConfigurationProperties("blog")
data class BlogProperties(var title: String, val banner: Banner) {
    data class Banner(val title: String? = null, val content: String)
}
```


따라한 튜토리얼에서는 간단한 사용 예시만 있어서 조금 개념잡기가 어려웠는데, 아래의 포스팅을 살펴보니 validate도 적용해서 사용하네요. 나중에 참고해서 사용해야겠습니다.

[A Guide to Spring Boot ConfigurationProperties for Kotlin Data Class](https://towardsdatascience.com/a-guide-to-use-spring-boots-configurationproperties-annotation-in-kotlin-s-dataclass-1341c63110f4)


[docs.spring.io - Annotation Type ConfigurationProperties](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/context/properties/ConfigurationProperties.html)

[Official Spring Boot documentation on @ConfigurationProperties](https://docs.spring.io/spring-boot/docs/2.2.1.RELEASE/reference/html/spring-boot-features.html#boot-features-kotlin-configuration-properties)

<br/><br/>

위에서 작성한 코드는 아래의 커밋에서 확인하실 수 있습니다.
[Git Commit - Exposing HTTP API / Configuration properties](https://github.com/yeonseo/springKotlinBackendTest/commit/c72c799dd94fec3fb474c19fa0a8bb41674278c5)


<br/><br/>

Spring을 공부해보셨다면, 아마도 아실텐데 펫클리닉 프로젝트가 코들린 버전으로도 있더라구요. [펫클리닉 Kotlin 버전](https://github.com/spring-petclinic/spring-petclinic-kotlin)

# Pet Clinic 이란?

> A legendary and official webapp
> The Spring PetClinic is a sample application designed to show how the Spring stack can be used to build simple, but powerful database-oriented applications.
[The Spring PetClinic Community](https://spring-petclinic.github.io/)

아주 유명한 공식적인 웹 앱!
Spring PetClinic은 스프링을 이용해서 만들어진 웹 어플리케이션으로, 간단한 디자인을 보여줍니다. 자주 쓰이는 어노테이션도 적용되어 있고, 테스트 모델도 만들어져 있어서 처음에 공부하기 좋습니다. 이걸로 강의를 찍으시는 분들도 있을 정도,,!

코틀린은 안드로이드 앱과 관련한 코드나 튜토리얼이 많은 편입니다. 스프링을 검색하면 스프링 + 자바 조합으로 굉장히 커뮤니케이션이 잘 발달되어 있지만, 스프링 + 코틀린 튜토리얼이 굉장히 빈약한 편,, ㅠㅠ 그 와중에 펫 클리닉을 코틀린으로도 제공받을 수 있다는 건 얼마나 감사한지! ㅎㅎㅎ 더 확장된 웹 기능을 담고있는 코드가 궁금하신 분들은 한번 참고 해보시길 바랍니다.