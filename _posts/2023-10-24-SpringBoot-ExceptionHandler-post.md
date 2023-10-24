---
layout: post
title: Spring Boot의 업그레이드 후 Exception Handler 이슈
description: ""
tags: [Spring Boot, Spring, Kotlin, Web, Backend, TIL, Today I Leaned]
comments : true
category : [Spring]
sitemap:
  changefreq: daily
  priority : 1.0
---


스프링 부트는 빠른 개발 속도와 확장성으로 인해 많은 프로젝트에서 선호되는 프레임워크 중 하나이다. 그런데 이런 장점에도 불구하고, 버전을 업그레이드 할 때 여러 가지 문제에 직면하게 되는데, 그 중 예외처리와 관련해 최근에 회사 프로젝트에서도 이러한 경험을 하게 되었다. 그 경험을 공유 및 기억하고자 기록합니다. 🤖

<div class="space-item-5"></div>

# 문제 발생
팀 전체가 기존 프로젝트의 스프링 부트의 버전 업그레이드를 진행하고 나서 배포를 한 후, API 테스트를 진행하던 중 이상한 현상을 발견했다. 기존에 잘 동작하던 ExceptionHandler가 작동하지 않아, 예외 발생 시 항상 401 상태 코드만을 반환하고 있는 것... 하필 Server-to-Server 작업을 내가 맡아 아주 억울한 상황이었다. 그리고 프론트에선 서버로부터 코드와 에러메세지를 받아 보여주고 있기 때문에 (배포한다면) 이는 사용자에게 정확한 에러 메시지를 전달하지 못하는 문제가 발생하는 최악의 상황이었다.


# 원인 파악
먼저, ExceptionHandler의 로직에 문제가 있는지 확인하기 위해 코드를 상세히 살펴보았다. 레거시 프로젝트였고, 기존에 잘 돌아가던 코드였기 때문에 역시나 문제는 발견할 수 없었다. 코드는 클린 그 자체.

![sad-dev](/post/images/hey/crying01.gif)


<div class="space-item-3"></div>


이후, 스프링 부트의 버전 업그레이드가 슬슬 의심되기 시작했고, 변경 로그와 문서를 찾아보면서 버전 업그레이드에 따른 변화점을 확인했다.

그 결과, 스프링 부트의 버전 업그레이드에 따라 ExceptionHandler의 작동 방식에 변화가 생겼음을 알게되었다. 특히, 특정 예외 처리가 내부적으로 핸들링되면서 401 상태 코드가 반환되도록 변경되었던 것..!


> 💡 스프링 부트 2.3 (Spring 기준 3.2) 이후로 오류 처리에 대한 주요 변경 사항
>
> ErrorAttributes 및 관련 인터페이스의 주요 변경 사항과 함께 새로운 ErrorController 구현이 도입되었습니다.
> 
> 🤖 에러 핸들러 사용:
> Spring Boot에서는 @ControllerAdvice와 @ExceptionHandler 어노테이션을 사용하여 전역 에러 핸들러를 구현할 수 있습니다.


<div class="space-item-3"></div>

** 참고 문서 **

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html

https://www.baeldung.com/exception-handling-for-rest-with-spring


<div class="space-item-3"></div>

<div class="space-item-3"></div>

# 해결 과정
할 수 있는 해결 방법은 두 가지였습니다.

1. 예외 처리 방식을 스프링 부트의 새로운 버전에 맞게 수정
2. 기존의 ExceptionHandler 방식을 유지하도록 설정 변경


현재 스프링 부트의 버전에 맞게 수정되어야 하는 것이 앞으로의 유지보수에도 좋고, 보다 사용자에게 명확한 에러 메시지를 전달하기 위해 첫 번째 방법을 선택하고 작업을 진행했다.  `GlobalExceptionHandler`를 적용하기 위해 `@ControllerAdvice`와 `@ExceptionHandler`를 사용하여 구현했다.


```kt
class ResponseException(val statusCode: Int, val errorMessage: String) : RuntimeException("$statusCode: $errorMessage")

@ControllerAdvice
class GlobalExceptionHandler {
    @ExceptionHandler(ResponseException::class)
    fun handleResponseException(ex: ResponseException): ResponseEntity<String> {
        val headers = HttpHeaders()
        headers.contentType = MediaType.parseMediaType("text/plain; charset=UTF-8")
        return ResponseEntity(ex.errorMessage, headers, HttpStatus.valueOf(ex.statusCode))
    }
}

```


### ResponseException 클래스:

ResponseException은 커스텀 예외 클래스로, statusCode와 errorMessage 두 개의 필드를 포함 (실제 코드에서는 예외 발생 케이스를 Enum으로 관리하고 있다)

statusCode: 해당 예외에 대한 HTTP 상태 코드

errorMessage: 사용자에게 전달할 에러 메시지

## GlobalExceptionHandler 클래스:

`@ControllerAdvice` 어노테이션을 통해 전역적인 예외 핸들러로 동작

`handleResponseException` 메서드에서는 `ResponseException`이 발생할 경우 해당 메서드로 예외를 핸들링한다. 예외 정보를 기반으로 HTTP 헤더와 함께 응답을 return한다.


<div class="space-item-3"></div>

<div class="space-item-3"></div>

# 마치며
이 경험을 통해 스프링 부트와 같은 프레임워크의 버전 업그레이드는 항상 주의 깊게 진행해야 함을 다시 한번 깨달았다. 특히, 변경 로그와 Spring 문서를 평소에도 읽어두는 습관에 대한 필요성을 느꼈다. 그랬다면 ExceptionHandler의 작동 방식의 변화를 인지하고 좀 더 빠르게 해결로 갔을 지도.. 이번 문제 해결 과정을 통해 업그레이드에 대응하는 방법을 배웠고, 이를 다음에 유사한 상황이 발생할 때 빠르게 대처할 수 있는 통찰력을 얻었다. 앞으로도 이러한 경험을 바탕으로 더욱 성장하고, 항상 변경사항에 대비하는 개발자로 성장하기 위해 노력하겠다.



<details>
<summary>정상 응답 확인 후기</summary>
<div markdown="1">

![thanks-for-check](/post/images/2023-10-23-reply.png)

바로 확인해주신 동료님..! 감사함미동..

</div>
</details>



<div class="space-item-3"></div>

### 예외처리 관련 공식 페이지
> https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html
> 
> https://www.baeldung.com/exception-handling-for-rest-with-spring