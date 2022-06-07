---
layout: post
title: Converter사용으로 간편하게 타입 변환하기
description: ""
tags:  [Spring Boot, Spring, Kotlin, Web, Backend, Mongo DB, TIL, Today I Leaned]
comments : true
category : [Spring]
sitemap:
  changefreq: daily
  priority : 1.0
---


# @Converter 란?
> https://www.baeldung.com/spring-type-conversions

> 컨버터란 말 그대로 '변환기'의 역할을 한다. 예를 들어, String을 Integer로 바꾼다던가, Bool을 String으로 바꾸는 일이 가능하다. 소스 코드 내에서도 이러한 일들은 가능하지만, 모든 컬럼을 모든 서비스에서 사용할 때마다 적용해주기란,,
<strike> 10개의 컬럼이 있고, 10개의 서비스가 있다면, 10개를 바꿔주는 일을 10번,,, </strike>

@Converter는 이 작업을 쉽게 도와준다. :)


<div class="space-item-3"></div>

# 적용하기
Boolean에 Nullable 인 경우를 적용했다.

## BooleanToNullYNConverter 클래스 생성
```kt
import javax.persistence.AttributeConverter
import javax.persistence.Converter

@Converter
class BooleanToNullYNConverter : AttributeConverter<Boolean?, String> {
    override fun convertToDatabaseColumn(attribute: Boolean?): String? {
        if (attribute == null) return null
        return if (attribute) "Y" else "N"
    }

    override fun convertToEntityAttribute(dbData: String?): Boolean? {
        if (dbData == null) return null
        return ("Y" == dbData);
    }
}
```

## 해당하는 컬럼에 적용

```kt
@Column(nullable = true, columnDefinition = "VARCHAR(1)")
@Convert(converter = BooleanToNullYNConverter::class)
var hopeYn: Boolean? = null // nullable 적용

@Column(nullable = true, columnDefinition = "VARCHAR(1)")
@Convert(converter = BooleanToNullYNConverter::class)
var offerYn: Boolean? = null // nullable 적용
```

오늘도 이렇게 배워감 🤗

![happy-dev](/post/images/hey/happy02.gif)
