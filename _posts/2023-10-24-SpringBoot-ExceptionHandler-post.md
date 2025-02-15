---
layout: post
title: Spring Boot 버전 업그레이드 후 예외 처리 방식 변화 대응기
description: "Spring Boot의 예외 처리 방식이 변화하면서 발생한 문제와 해결 과정 공유"
tags: [Spring Boot, Spring, Kotlin, Web, Backend, TIL, Today I Leaned]
comments : true
category : [Backend, Spring]
sitemap:
  changefreq: daily
  priority : 1.0
---


# **1. 문제 정의: 예외 핸들러가 무력화되다?!**  

Spring Boot는 빠른 개발 속도와 확장성 덕분에 많은 프로젝트에서 널리 사용된다. 그러나 이러한 장점에도 불구하고, **버전 업그레이드 과정에서 기존 코드가 예상치 못한 방식으로 동작하는 경우**가 발생할 수 있다.  

이번 프로젝트에서도 **Spring Boot 버전 업그레이드 후 기존 예외 핸들러가 제대로 동작하지 않는 문제**를 경험했다.  

<div class="space-item-3"></div>




# **2. 문제 발생: 예외 발생 시 무조건 401 반환**  

Spring Boot를 최신 버전으로 업그레이드한 후 배포를 진행했고, API 테스트를 수행하는 과정에서 이상한 현상이 발견되었다.  

✅ **기존 동작:**  
- `@ExceptionHandler`가 정상적으로 예외를 처리하고, 적절한 HTTP 상태 코드 및 메시지를 반환  

❌ **문제 발생 후:**  
- 예외 발생 시 무조건 **401 Unauthorized** 응답이 반환됨  
- `@ExceptionHandler`가 무시되고, 기본 Spring Security 예외 처리 흐름이 적용됨  

> 📌 **Server-to-Server API 개발을 담당하고 있었기에, 이 문제가 더욱 치명적으로 다가왔다.**  
> 클라이언트는 서버에서 내려오는 HTTP 상태 코드와 메시지를 기반으로 오류를 처리하는데,  
> **모든 오류가 401로 반환되면서 프론트엔드와의 통신에도 혼란이 발생**했다.  

![crying-dev](/post/images/hey/crying01.gif)  

<div class="space-item-3"></div>




# **3. 원인 분석: Spring Boot 예외 처리 방식의 변화**  

🚀 **1️⃣ 예외 처리 로직 확인**  
먼저, 기존 `@ExceptionHandler`의 코드가 정상적으로 동작하는지 검토했다.  
- ✅ 예외 핸들러 코드 자체에는 문제 없음  
- ✅ 예외 발생 시, `@ExceptionHandler`가 실행되지 않음  

🚀 **2️⃣ Spring Boot 변경 사항 조사**  
Spring Boot의 공식 문서를 확인하면서, **버전 업그레이드 과정에서 예외 처리 방식에 변화가 있었음을 발견**했다.  

📌 **Spring Boot 2.3 (Spring 5.2) 이후 주요 변경 사항**  
- `ErrorAttributes` 및 관련 인터페이스 변경  
- 새로운 `ErrorController` 구현 도입  
- 일부 인증 관련 예외는 `Spring Security`에서 자동으로 처리하도록 변경  

📌 **결론:**  
✅ **Spring Boot의 예외 처리 방식이 변화하면서, 기존 `@ExceptionHandler`가 동작하지 않게 된 것!**  
✅ **특정 인증 관련 예외가 내부적으로 핸들링되면서, 401 상태 코드가 자동으로 반환됨!**  

> ⚠ **기존 `@ExceptionHandler`가 동작하지 않는 이유:**  
> - Spring Security에서 특정 예외 (`AuthenticationException`, `AccessDeniedException`)를 자동으로 처리  
> - 기존 예외 핸들러가 호출되기 전에 Security 필터에서 응답을 가로챔  

<div class="space-item-3"></div>




# **4. 해결 방법: Spring Boot 최신 예외 처리 방식 적용**  

🚀 **해결 방법은 두 가지였다.**  
1️⃣ **Spring Boot 최신 예외 처리 방식에 맞게 코드 수정**  
2️⃣ **기존 방식(`@ExceptionHandler`)을 유지할 수 있도록 설정 변경**  

📌 유지보수성과 확장성을 고려했을 때,  
✅ **첫 번째 방법**(Spring Boot의 최신 방식에 맞춰 예외 처리 방식을 수정) 을 선택하여 해결했다.  



### **🔹 해결 코드: `GlobalExceptionHandler` 적용**  

📌 **Spring Boot 최신 버전에 맞게 예외 처리를 적용한 코드**  

```kotlin
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

### **🔹 `ResponseException` 클래스**
- **커스텀 예외 클래스**  
- `statusCode`: HTTP 상태 코드  
- `errorMessage`: 사용자에게 전달할 메시지  

### **🔹 `GlobalExceptionHandler` 클래스**
- **`@ControllerAdvice`**: 전역 예외 처리를 담당  
- **`@ExceptionHandler(ResponseException::class)`**: `ResponseException` 발생 시 처리  
- **HTTP 상태 코드와 에러 메시지를 함께 반환**  

<div class="space-item-3"></div>



# **5. 기술적 인사이트: Spring Boot의 최신 예외 처리 흐름**  

🚀 **Spring Boot 최신 예외 처리 방식 정리**  

✅ **Spring Boot 2.3 이후 주요 변경 사항**  
1️⃣ **기본 예외 처리 로직 변경**  
   - `ErrorAttributes` 및 `ErrorController`가 내부적으로 동작 방식 수정  
   - 일부 예외는 `Spring Security`에서 자동 처리  
   
2️⃣ **Spring Security와 예외 처리 충돌 문제 해결**  
   - Security 필터에서 예외가 먼저 처리되므로, `@ExceptionHandler`가 무시될 가능성 있음  
   - `@ControllerAdvice`에서 명확하게 예외를 처리하도록 변경  

3️⃣ **전역 예외 처리의 중요성**  
   - 단순한 API 오류뿐만 아니라, 인증 및 권한 관련 예외도 직접 처리할 필요가 있음  
   - 보안 정책을 명확히 정의하고, 불필요한 Security 예외 핸들링을 줄이는 것이 핵심  

📌 **결론:**  
✅ **Spring Boot의 예외 처리 방식을 정확히 이해하고 대응해야 한다.**  
✅ **특히, 보안과 관련된 예외는 Security 필터에서 먼저 처리됨을 고려해야 한다.**  

<div class="space-item-3"></div>




# **6. 결론: 버전 업그레이드 대응 전략**  

✔ **이슈 해결 후 효과**  
- 예외 처리 로직이 정상적으로 동작하도록 복구됨  
- 로그 분석 및 디버깅이 쉬워짐  
- API 클라이언트(프론트엔드)와의 예외 처리 통신이 안정적으로 이루어짐  

📌 **Spring Boot 기반 애플리케이션에서 예외 처리를 설계할 때 고려할 점**  
1️⃣ **Spring Boot 버전 업그레이드 시 변경 로그를 꼭 확인하자.**  
2️⃣ **예외 처리 방식이 Security 필터와 충돌하는지 점검하자.**  
3️⃣ **`@ExceptionHandler`만으로 해결되지 않는 경우, Security 예외 처리 흐름을 확인하자.**  

<div class="space-item-3"></div>



# **7. 마치며: 버전 업그레이드는 예외 처리까지 고려하자!**  

이 경험을 통해 **Spring Boot와 같은 프레임워크의 버전 업그레이드는 단순히 코드 변경만이 아니라, 예외 처리 방식까지 고려해야 한다**는 점을 다시 한번 깨달았다.  

특히, **변경 로그(Changelog)와 공식 문서를 미리 확인하는 습관이 중요하다.**  
그랬다면 이번 문제도 더 빠르게 해결할 수 있었을지도…  

이번 문제 해결 과정을 통해,  
✅ **버전 업그레이드 대응 능력**  
✅ **Spring Security와 예외 처리의 관계 이해**  
✅ **전역 예외 처리 패턴 적용 능력**  
을 모두 배울 수 있었다.  

앞으로도 이러한 경험을 바탕으로 **더욱 성장하는 개발자**가 되고자 한다. 🚀  

---

### **🔍 예외 처리 관련 공식 문서**  
📌 https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html  
📌 https://www.baeldung.com/exception-handling-for-rest-with-spring  
