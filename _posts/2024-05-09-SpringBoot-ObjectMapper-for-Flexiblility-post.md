---
layout: post
title: Spring Boot에서 ObjectMapper 최적화 - JSON 파싱 에러 해결 및 성능 개선
description: ""
tags: [Spring Boot, Spring, Kotlin, Web, Backend, TIL, Today I Leaned]
comments : true
category : [Backend, Spring]
sitemap:
  changefreq: daily
  priority : 1.0
---

# 1. 문제 정의: JSON 구조 변화와 400 Bad Request 발생# **Spring Boot에서 ObjectMapper 최적화: JSON 파싱 오류 해결 및 역할별 구성 전략**  

## **1. 문제 정의: JSON 구조 변화와 400 Bad Request 발생**  

Spring Boot의 `ObjectMapper`는 JSON 데이터를 DTO로 변환하는 중요한 역할을 한다. 그러나 최근 API 개발 과정에서 다음과 같은 문제가 발생했다.  

📌 **최근 경험한 주요 이슈:**  
- **DTO에 정의되지 않은 필드가 포함된 요청을 받을 경우 400 Bad Request 발생**  
- **ObjectMapper 설정이 변경되면서 JSON 구조가 예상과 다르게 변형됨** (예뻤던 JSON이 클래스로...)  
- **상황에 따라 적절한 ObjectMapper가 적용되지 않아 JSON 직렬화/역직렬화가 혼란스러움**  


🚀 **이슈 발생 배경:**  
- Spring Boot의 기본 설정에서는 DTO에 없는 필드가 포함된 JSON 요청을 허용하지 않음.  
- ObjectMapper의 `activateDefaultTyping` 설정이 JSON 구조를 예상과 다르게 변형함.  
- 역할에 따라 ObjectMapper가 다르게 동작해야 하지만, 전역 설정으로 인해 동일한 방식으로 적용됨.  

👉 **결론:** ObjectMapper 설정을 최적화하여 **유연한 JSON 처리, DTO 구조 유지, 역할별 ObjectMapper 적용**이 필요했다.

![strong-dev](/post/images/hey/strong02.gif)

~~공통 서비스를 변경할 때는.. 주요 서비스가 아녀도 한 번쯤 확인해주라...~~

<div class="space-item-3"></div>




## **2. 원인 분석: ObjectMapper의 기본 동작과 예상치 못한 영향**  

### 🔎 **400 Bad Request 발생 원인**  
**Jackson의 기본 설정**에서는 DTO에 정의되지 않은 필드가 포함되면 예외를 발생시킨다.  
이유는 `DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES` 설정이 기본적으로 `true`이기 때문.  

**📌 ObjectMapper의 기본 동작**
1️⃣ JSON 요청을 DTO로 변환할 때, DTO에 없는 필드가 포함되면 예외 발생  
2️⃣ Spring Boot의 기본 `ObjectMapper` 설정이 `FAIL_ON_UNKNOWN_PROPERTIES = true`이므로 엄격한 검증 수행  
3️⃣ API 확장 시, 새로운 필드가 추가되면 DTO 변경이 필요 → **유지보수 부담 증가**  

### 🔎 **activateDefaultTyping이 JSON 구조를 변형한 이유**  
기존 설정에서 `activateDefaultTyping(DefaultTyping.EVERYTHING)`을 활성화했을 때, JSON 데이터가 예상과 다르게 변경되었다.  
- **원인**: Jackson이 다형성 지원을 위해 JSON 객체에 추가적인 타입 정보를 포함시킴.  
- **결과**: JSON이 배열 또는 클래스로 변환되어, 클라이언트가 예상한 포맷과 다르게 출력됨.  

**📌 예상과 다르게 변형된 JSON 예시**  
🚨 기존 JSON (기대했던 모습)
```json
{
    "name": "John",
    "email": "john.doe@example.com"
}
```
🚨 activateDefaultTyping 적용 후 (예상치 못한 변화)
```json
["com.bcg.sto.issue.backofficeapi.member.dto.AdminInvestInformDto$InvestQualSearchOption", {
    "name": "John",
    "email": "john.doe@example.com"
}]
```
**→ JSON 구조가 바뀌면서 API 응답이 깨짐 😭**  

📌 **이슈 요약:**  
- **DTO 필드와 JSON 불일치로 인한 400 Bad Request 발생**  
- **activateDefaultTyping으로 인해 JSON 구조가 배열/클래스로 변환됨**  
- **API 요청마다 적절한 ObjectMapper를 적용할 필요성 증가**  

<div class="space-item-3"></div>




## **3. 해결 방법: ObjectMapper 설정 최적화 및 역할별 구성**  

🚀 **핵심 해결 전략:**  
- **DTO에 없는 필드를 무시하도록 설정하여 API 요청의 유연성을 높임**  
- **activateDefaultTyping을 제거하고, JSON 구조를 변형시키지 않도록 설정**  
- **웹 요청/내부 데이터 직렬화 등 역할에 따라 다른 ObjectMapper를 사용하도록 구성**  

### **🔹 문제 해결을 위한 새로운 ObjectMapper 설정**
```kotlin
fun webObjectMapper(): ObjectMapper {
    return ObjectMapper()
        .registerModule(JavaTimeModule()) // Java 8 Date/Time 지원
        .registerModule(kotlinModule { enable(KotlinFeature.SingletonSupport) }) // Kotlin 지원 강화
        .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false) // DTO에 없는 필드 무시
}
```
📌 **변경된 사항:**  
✅ `DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES = false` → **DTO에 없는 필드가 있어도 예외 발생 안 함**  
✅ `activateDefaultTyping` 제거 → **JSON 구조가 예상대로 유지됨**  
✅ `KotlinFeature.SingletonSupport` 활성화 → **Kotlin 객체 직렬화/역직렬화 안정성 강화**  

<div class="space-item-3"></div>




### **🔹 역할별 ObjectMapper 구성**
```kotlin
@Bean
@Primary
fun defaultObjectMapper(): ObjectMapper {
    return ObjectMapper()
        .registerModule(JavaTimeModule())
        .registerKotlinModule()
}

@Bean
@Qualifier("webObjectMapper")
fun webObjectMapper(): ObjectMapper {
    return ObjectMapper()
        .registerModule(JavaTimeModule())
        .registerModule(kotlinModule { enable(KotlinFeature.SingletonSupport) })
        .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
}
```
📌 **왜 역할별로 ObjectMapper를 분리했을까?**  
1️⃣ **웹 요청 처리용 (`webObjectMapper`)**  
   - DTO 필드에 없는 JSON 데이터가 포함되어도 API가 정상적으로 동작하도록 유연성 확보  
   - Kotlin 싱글톤 지원 활성화  
   
2️⃣ **내부 데이터 변환용 (`defaultObjectMapper`)**  
   - 보다 엄격한 데이터 직렬화/역직렬화 적용 가능  
   - JSON 데이터의 무결성을 유지하면서도, 일부 DTO에서는 유연성을 확보  

💡 **결과:** API 요청별로 적절한 ObjectMapper가 적용되어 **예상치 못한 JSON 변환을 방지하고, DTO 확장성을 확보**할 수 있게 됨.  

<div class="space-item-3"></div>




## **4. 기술적 인사이트: ObjectMapper의 유연성과 안정성 확보**  

🚀 **Spring Boot에서 JSON 처리를 최적화하는 핵심 개념 정리**  

✅ **DTO와 JSON 구조가 불일치할 때 예외 발생을 방지하려면?**  
- `DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES = false` 설정으로 DTO에 없는 필드 무시  
- API가 확장될 때 유지보수 비용을 최소화할 수 있음  

✅ **activateDefaultTyping을 신중하게 사용해야 하는 이유**  
- JSON 객체의 구조를 변경시키므로, 예상치 못한 API 응답 문제 발생 가능  
- 다형성을 고려해야 하는 경우 `@JsonTypeInfo`를 DTO 개별 필드에 적용하는 것이 더 안전함  

✅ **API 요청별로 다른 ObjectMapper를 사용해야 하는 이유**  
- 웹 API에서는 DTO의 유연성을 높이고, 내부 데이터 직렬화는 더 엄격하게 관리 가능  
- ObjectMapper를 `@Qualifier`로 분리하여 역할에 맞는 설정을 적용할 수 있음  

<div class="space-item-3"></div>




## **5. 결론: ObjectMapper 최적화로 API 안정성 및 확장성 확보**  

✔ **이슈 해결 후 효과**  
- JSON 요청에서 **DTO에 없는 필드가 있어도 API가 정상적으로 동작**  
- `activateDefaultTyping` 제거로 **JSON 구조가 깨지지 않음**  
- 역할별 ObjectMapper 적용으로 **유연성과 데이터 무결성을 동시에 확보**  

📌 **Spring Boot 기반 API 개발 시 고려할 점:**  
1️⃣ **DTO의 엄격한 검증과 API 확장성을 어떻게 균형 맞출 것인가?**  
2️⃣ **JSON 변환 로직이 API의 일관성을 해치지 않도록 설정을 신중하게 구성하자**  
3️⃣ **역할별 ObjectMapper 설정을 분리하여 유지보수성과 확장성을 극대화하자**  

🚀 **이 글이 ObjectMapper 설정을 고민하는 모든 개발자에게 도움이 되길 바랍니다!**  
