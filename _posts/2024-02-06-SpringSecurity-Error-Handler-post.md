---
layout: post
title: JWT 기반 Spring Security 인증 처리의 최적화 - 중복 핸들러 호출 문제 해결
description: ""
tags: [Spring Boot, Spring, Kotlin, Web, Backend, TIL, Today I Leaned]
comments : true
category : [Backend, Spring]
sitemap:
  changefreq: daily
  priority : 1.0
---


# **1. 문제 정의: 왜 두 개의 예외 핸들러가 동시에 실행될까?**  
Spring Security를 활용하여 JWT 기반의 인증 및 권한 관리를 구현할 때, **JWT 만료 시 `JwtAuthenticationEntryPoint`가 실행(401 Unauthorized)**, **접근 권한 부족 시 `JwtAccessDeniedHandler`가 실행(403 Forbidden)** 되어야 한다.  

그러나 서비스 테스트 중, **403이 발생한 후 401이 추가로 실행되는 문제가 발생**했다.

![eheh-dev](/post/images/hey/what05.gif)

<div class="space-item-3"></div>



이를 해결하지 않으면 관리자 서비스의 보안 정책의 일관성이 깨지고(로그인이 되지 않으며..), 예외 처리가 제대로 동작하지 않아 토큰 재발행 로직이 무한으로 요청되는 현상을 확인했다.

<div class="space-item-3"></div>




# **2. 원인 분석: Spring Security의 예외 흐름 이해**  

Spring Security의 인증 및 권한 처리 과정은 다음과 같이 동작한다.  

1️⃣ **인증(Authentication) 확인**  
   - `JwtAuthenticationFilter`에서 JWT가 유효한지 검증 → 인증 객체 생성  
   - 유효하지 않으면 `JwtAuthenticationEntryPoint`가 실행됨 (401 Unauthorized)  

2️⃣ **인가(Authorization) 확인**  
   - `CustomAuthorizationFilter`에서 사용자의 권한을 확인  
   - 필요한 역할이 없으면 `JwtAccessDeniedHandler`가 실행됨 (403 Forbidden)  

📌 **그러나, 특정 조건에서 403을 처리한 후에도 401이 실행되는 문제 발생!**  

✅ **분석 결과:**  
- `CustomAuthorizationFilter`에서 `response.sendError(HttpServletResponse.SC_FORBIDDEN)`을 직접 호출하여 `JwtAccessDeniedHandler`가 실행됨  
- 이후에도 필터 체인이 계속 진행되면서 기본 예외 처리 로직이 추가로 실행 → `JwtAuthenticationEntryPoint`가 401을 다시 던짐  

<div class="space-item-3"></div>





# **3. 해결 방법: 적절한 응답 종료 처리**  

🚀 **핵심 해결 전략:**  
- 403이 발생한 경우, 필터 체인의 실행을 멈추도록 수정  
- `sendError()` 대신 `response.status = SC_FORBIDDEN`을 설정하여 SecurityContext에서 추가적인 예외 처리를 막음  

## **🔹 수정 전 코드 (문제 발생 코드)**
```kotlin
class CustomAuthorizationFilter : OncePerRequestFilter() {
    override fun doFilterInternal(
        request: HttpServletRequest,
        response: HttpServletResponse,
        filterChain: FilterChain
    ) {
        val authentication = SecurityContextHolder.getContext().authentication
        if (authentication == null || !authentication.isAuthenticated) {
            response.sendError(HttpServletResponse.SC_FORBIDDEN, "User does not have required role")
            return
        }
        filterChain.doFilter(request, response)
    }
}
```
📌 `sendError()`가 호출되면서 `AccessDeniedHandler` 실행 후에도 추가적인 예외 처리가 발생할 수 있음  

## **🔹 수정 후 코드 (403에서 실행 종료)**
```kotlin
class CustomAuthorizationFilter : OncePerRequestFilter() {
    override fun doFilterInternal(
        request: HttpServletRequest,
        response: HttpServletResponse,
        filterChain: FilterChain
    ) {
        val authentication = SecurityContextHolder.getContext().authentication
        if (authentication == null || !authentication.isAuthenticated) {
            response.status = HttpServletResponse.SC_FORBIDDEN
            response.writer.write("User does not have required role")
            return
        }
        filterChain.doFilter(request, response)
    }
}
```
📌 `response.status`만 설정하고 `sendError()`를 호출하지 않음 → 추가적인 예외 처리를 막아 불필요한 `JwtAuthenticationEntryPoint` 실행 방지  

<div class="space-item-3"></div>




# **4. 기술적 인사이트: Spring Security의 예외 흐름과 필터 체인 제어**  

🚀 **Spring Security의 핵심 개념 정리**  

✅ **Spring Security의 예외 처리 흐름**
- `AuthenticationException` → 401 (`JwtAuthenticationEntryPoint`)  
- `AccessDeniedException` → 403 (`JwtAccessDeniedHandler`)  
- `sendError()` 사용 시, Spring Boot의 기본 예외 처리 핸들러가 추가 실행될 가능성이 있음  

✅ **필터 체인 내에서의 제어 방법**
- `filterChain.doFilter(request, response)` 호출 전에 `response.sendError()`를 호출하면, 기본적으로 Spring Security의 `BasicErrorController`가 추가적으로 실행될 수 있음  
- 단순히 `response.status`를 설정하면 Spring Security의 추가적인 예외 처리를 방지할 수 있음  

✅ **JWT 기반 인증 시스템을 구축할 때 고려할 점**
1️⃣ **예외 발생 위치에 따른 Security 흐름 제어**  
   - 인증(401)과 권한(403)을 명확하게 분리  
2️⃣ **필터 체인에서의 예외 흐름을 이해하고 중복 호출 방지**  
   - 불필요한 `sendError()` 호출을 줄이고, `response.status`로 응답 관리  
3️⃣ **실제 서비스 환경에서의 성능 최적화**  
   - 불필요한 예외 처리가 발생하지 않도록 필터 실행 종료 시점을 명확히 설정  

<div class="space-item-3"></div>




# **5. 결론: JWT 기반 인증 시스템을 최적화하는 방법**  

✔ **이슈 해결 후 효과**
- `JwtAccessDeniedHandler`와 `JwtAuthenticationEntryPoint`의 중복 실행 문제 해결  
- 로그 분석 및 디버깅 시, 더 명확한 예외 흐름을 유지  

📌 **Spring Security 기반의 JWT 인증 시스템을 구축할 때 고려할 점:**  
1️⃣ **예외 흐름을 명확히 설계하고 핸들러 중복 실행을 방지하자**  
2️⃣ **Spring Security의 필터 체인을 정확히 이해하고 필요 없는 추가 예외 처리를 줄이자**  
3️⃣ **프로덕션 환경에서는 불필요한 예외 로직이 추가되지 않도록 성능을 최적화하자**

🚀 **이 글이 JWT 기반 인증 시스템을 개발하는 모든 개발자에게 도움이 되길 바랍니다!**  