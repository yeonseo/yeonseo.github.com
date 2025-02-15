---
layout: post
title: Spring @Converter -데이터 변환을 더 깔끔하게!
description: "JPA @Converter를 활용한 Boolean ↔ Y/N 변환기 적용기"
tags:  [Spring Boot, Spring, Kotlin, Web, Backend, Mongo DB, TIL, Today I Leaned]
comments : true
category : [Backend, Spring]
sitemap:
  changefreq: daily
  priority : 1.0
---

# **1. @Converter란? 왜 필요할까?**  

> 📌 `@Converter`는 **JPA에서 특정 데이터 타입을 다른 타입으로 변환하는 기능을 제공**하는 어노테이션이다.  

예를 들어, **Boolean 값을 데이터베이스의 Y/N 형식으로 변환**하고 싶다면?  
각 컬럼마다 변환 로직을 직접 적용하는 것은 비효율적이다.  

✅ **기존 방식의 문제점**  
- 서비스에서 데이터를 주고받을 때, 매번 Boolean ↔ String 변환을 수동으로 수행해야 함  
- 변환 로직이 여기저기 퍼져서 유지보수가 어려워짐  

✅ **@Converter 사용 시 장점**  
- 한 번 구현하면 **여러 엔터티에서 재사용 가능**  
- JPA가 자동으로 변환을 수행하여 **가독성이 좋아지고 코드가 깔끔해짐**  

📌 **한마디로, 변환 작업을 쉽게 도와주는 JPA의 강력한 기능이다!**  


<div class="space-item-3"></div>



# **2. @Converter 적용하기**  

이번 프로젝트에서는 **Boolean 값을 데이터베이스의 Y/N 형식으로 변환**하는 작업을 진행했다.  
이때, `@Converter`를 활용하여 변환기를 구현하면 훨씬 간결하게 적용할 수 있다.  

---

### **🔹 1️⃣ Boolean ↔ Y/N 변환기 생성**  

📌 **Boolean이 `Y/N` 형식으로 변환되도록 JPA Converter 구현**  

```kotlin
import javax.persistence.AttributeConverter
import javax.persistence.Converter

@Converter(autoApply = true) // 모든 Boolean? 타입에 자동 적용 가능
class BooleanToNullYNConverter : AttributeConverter<Boolean?, String> {

    override fun convertToDatabaseColumn(attribute: Boolean?): String? {
        return when (attribute) {
            true -> "Y"
            false -> "N"
            else -> null // null 값 처리
        }
    }

    override fun convertToEntityAttribute(dbData: String?): Boolean? {
        return when (dbData) {
            "Y" -> true
            "N" -> false
            else -> null
        }
    }
}
```

✅ **핵심 포인트**  
1️⃣ `convertToDatabaseColumn(attribute: Boolean?)` → Boolean 값을 `Y/N` 형식으로 변환  
2️⃣ `convertToEntityAttribute(dbData: String?)` → `Y/N` 값을 다시 Boolean으로 변환  
3️⃣ `@Converter(autoApply = true)` → 해당 타입이 있는 모든 컬럼에 자동 적용 가능  

📌 **즉, @Convert를 따로 명시하지 않아도 Boolean 컬럼에 자동 적용 가능!**  

---

### **🔹 2️⃣ 엔터티에 적용하기**  

📌 **이제 `@Convert`를 적용하여 JPA 엔터티에서 Boolean ↔ Y/N 변환을 자동 처리**  

```kotlin
@Column(nullable = true, columnDefinition = "VARCHAR(1)")
@Convert(converter = BooleanToNullYNConverter::class)
var hopeYn: Boolean? = null // nullable 가능

@Column(nullable = true, columnDefinition = "VARCHAR(1)")
@Convert(converter = BooleanToNullYNConverter::class)
var offerYn: Boolean? = null // nullable 가능
```

✅ **변환기 적용 후 효과**  
- hopeYn과 offerYn 컬럼이 **자동으로 Y/N 형식으로 변환되어 저장됨**  
- null 값도 정상적으로 처리됨  

📌 **이제 엔터티에서 별도 변환 없이 Boolean 값을 그대로 사용하면 됨!**  

<div class="space-item-3"></div>



# **3. 기술적 인사이트: JPA @Converter의 동작 방식**  

🚀 **JPA @Converter의 핵심 개념 정리**  

✅ **1️⃣ 자동 변환 적용 (`@Converter(autoApply = true)`)**  
- `autoApply = true`를 설정하면, **해당 변환기가 Boolean? 타입 컬럼에 자동 적용**됨  
- 단, 모든 Boolean 컬럼에 Y/N 변환이 필요하지 않다면, 명시적으로 `@Convert`를 붙이는 것이 더 적절할 수도 있음  

✅ **2️⃣ 명시적 변환 (`@Convert`)**  
- `@Convert(converter = BooleanToNullYNConverter::class)`를 사용하면  
  **특정 필드에만 변환기 적용 가능**  
- autoApply를 사용하지 않을 경우, 필드마다 `@Convert`를 적용해야 함  

✅ **3️⃣ JPA가 자동으로 변환을 수행**  
- JPA가 `@Converter`를 감지하여, **DB 저장 시 변환을 수행하고, 조회 시 다시 원래 타입으로 변환**  
- 개발자는 **Boolean 값을 그대로 사용하면 됨** (변환 로직을 신경 쓰지 않아도 됨)  

📌 **결론:**  
✅ **JPA의 강력한 기능을 활용하면 데이터 변환을 더 깔끔하게 처리할 수 있다!**  

<div class="space-item-3"></div>



# **4. 결론: JPA @Converter를 적극 활용하자!**  

✔ **@Converter 도입 후 효과**  
- **Boolean ↔ Y/N 변환이 자동으로 처리됨**  
- **코드의 가독성이 좋아지고 유지보수가 쉬워짐**  
- **재사용 가능한 변환기를 만들어 다양한 엔터티에서 활용 가능**  

📌 **Spring Boot + JPA를 사용할 때, 데이터 변환이 필요하다면 `@Converter`를 적극 활용하자!**  

<div class="space-item-3"></div>



# **5. 마치며: 개발 생산성을 높이는 작은 변화**  

이번 작업을 통해 **JPA @Converter를 활용하면 데이터 변환을 훨씬 쉽게 처리할 수 있음**을 다시 한 번 느꼈다.  

특히, **데이터 변환 로직을 분리하면 유지보수성과 확장성이 극대화**된다는 점에서 큰 의미가 있었다.  
앞으로도 **더 효율적인 데이터 처리 방법을 고민하면서 개발 생산성을 높이는 방법을 탐구**하고자 한다. 🚀  

<div class="space-item-3"></div>



📌 **더 깊이 있는 내용이 궁금하다면?**  
✅ Baeldung: [Spring Type Conversions](https://www.baeldung.com/spring-type-conversions)  
✅ Spring 공식 문서: [JPA @Converter 사용법](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.entity-persistence)  


오늘도 이렇게 배워감 🤗

![happy-dev](/post/images/hey/happy02.gif)
