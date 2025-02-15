---
layout: post
title: Spring 6 HTTP Interface 도입기
description: "Spring 6에서 도입된 HTTP Interface를 활용한 REST API 호출 방식 개선과 최적화"
tags: [Spring Boot, Spring, Kotlin, Web, Backend, TIL, Today I Leaned]
comments : true
category : [Backend, Spring]
sitemap:
  changefreq: daily
  priority : 1.0
---

# **1. 문제 정의: RestTemplate과 WebClient, 정말 최선일까?**  
Spring Boot에서 외부 API를 호출할 때, 전통적으로 **RestTemplate** 또는 **WebClient**를 사용해왔다.  
프로젝트를 WebClient를 주로 사용했고, 다음과 같은 문제를 경험했다.

❌ **WebClient의 복잡성**  
- Reactive 기반이라 일반적인 동기식 요청에도 비동기 개념이 필요  
- 학습 부담이 크고, 단순한 API 호출에는 오버엔지니어링이 될 가능성이 높음  

📌 **이런 고민 끝에, 더 나은 대안을 찾기 위해 Spring 6에서 새롭게 도입된 HTTP Interface를 고려해보기로 함.**  

<div class="space-item-3"></div>




# **2. HTTP Interface란 무엇인가?**  

Spring 6에서는 **HTTP Interface**라는 새로운 기능이 추가되었다.  
이는 **FeignClient와 유사한 방식**으로, **인터페이스만 정의하면 Spring이 자동으로 구현체를 생성**해주는 방식이다.  

기존 방식과 비교하면 다음과 같다.  

| 방식           | 특징                          | 코드량 | 학습 곡선 | 비동기 지원  |
| -------------- | ----------------------------- | ------ | --------- | ------------ |
| RestTemplate   | 직관적이지만 deprecated 예정  | 많음   | 낮음      | ❌            |
| WebClient      | 비동기 처리를 지원하지만 복잡 | 많음   | 높음      | ✅            |
| HTTP Interface | 간결한 인터페이스 기반        | 적음   | 낮음      | ❌(동기 방식) |

📌 **기존 방식보다 훨씬 간결한 코드로 유지보수가 쉬운 API 클라이언트를 만들 수 있다.**  

<div class="space-item-3"></div>




# **3. HTTP Interface 도입 및 적용 과정**  

🚀 **핵심 도입 전략:**  
- 기존 `RestTemplate` 사용 코드를 `HTTP Interface` 기반으로 변경  
- Bean 등록 방식이 아닌, `@HttpExchange` 어노테이션을 활용하여 인터페이스 정의  
- `WebClient`보다 직관적이며 유지보수가 용이한 구조로 개선  


## **🔹 기존 코드 (RestTemplate 방식)**
```kotlin
import org.springframework.stereotype.Service
import org.springframework.web.client.RestTemplate

@Service
class UserService(private val restTemplate: RestTemplate) {

    fun getUser(userId: String): UserResponse? {
        val url = "https://api.example.com/users/$userId"
        return restTemplate.getForObject(url, UserResponse::class.java)
    }
}
```
📌 `RestTemplate`을 직접 사용하므로, URL을 직접 다뤄야 하고 코드가 길어짐  


## **🔹 Spring 6 HTTP Interface 적용**
```kotlin
import org.springframework.web.service.annotation.GetExchange
import org.springframework.web.service.annotation.HttpExchange

@HttpExchange(url = "https://api.example.com/users")
interface UserClient {

    @GetExchange("/{userId}")
    fun getUser(userId: String): UserResponse
}
```
📌 **인터페이스만 정의하면, Spring이 구현체를 자동으로 생성**  
📌 **비즈니스 로직에서 API 호출과 관련된 복잡한 코드가 사라짐**  

<div class="space-item-3"></div>




## **4. 기술적 인사이트: Spring HTTP Interface의 동작 원리**  

🚀 **Spring HTTP Interface의 핵심 개념 정리**  

✅ **1️⃣ @HttpExchange 기반으로 REST API 정의**  
- `@HttpExchange`는 API의 기본 URL을 설정하고, 각 메서드별 HTTP 메서드를 지정할 수 있다.  
- 기존 `FeignClient`와 비슷한 방식으로 동작하지만, **Spring이 직접 제공하는 기능이므로 추가 라이브러리가 필요 없음**  

✅ **2️⃣ Spring이 자동으로 구현체를 생성**  
- `RestTemplate`이나 `WebClient`를 직접 사용하지 않아도, 인터페이스 기반으로 API 호출을 추상화 가능  
- 내부적으로 `RestClient`를 사용하여 HTTP 요청을 처리  

✅ **3️⃣ 유지보수 및 확장성 측면에서 유리**  
- API 변경 시 **인터페이스만 수정하면 되므로 유지보수 비용이 절감**  
- `@RequestParam`, `@RequestHeader` 등 다양한 Spring MVC 어노테이션과 함께 사용할 수 있음  

<div class="space-item-3"></div>




## **5. 결론: HTTP Interface를 통한 API 호출 최적화**  

✔ **이슈 해결 후 효과**
- `RestTemplate` 대비 코드량이 줄어 유지보수성이 증가  
- `WebClient` 대비 간결하고 직관적인 API 호출 방식 제공  
- Spring 6의 공식 기능이므로 향후 지원이 보장됨  

📌 **Spring 6에서 새롭게 도입된 HTTP Interface는 API 호출 방식을 획기적으로 개선할 수 있는 기능이다.**  


<div class="space-item-3"></div>




### **🚀 Spring 6 HTTP Interface 도입을 고민하고 있다면?**  
✅ `RestTemplate`을 대체할 깔끔한 대안을 찾고 있다면 적극 고려해볼 것  
✅ `WebClient`의 복잡성이 부담된다면 HTTP Interface로 간편하게 API 호출을 구현할 수 있음  
✅ Spring 6 이상을 사용하는 프로젝트라면 바로 적용해볼 수 있음

<div class="space-item-3"></div>




👀 참고 사이트

[spring-HTTP Interface](https://docs.spring.io/spring-framework/reference/integration/rest-clients.html#rest-http-interface)

[baeldung-HTTP Interface in Spring](https://www.baeldung.com/spring-6-http-interface)