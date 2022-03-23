---
layout: post
title: 스프링부트(Spring Boot)에서 Mongo DB 사용하기
description: ""
tags: [Sptring Boot, Spring, Kotlin, Web, Backend, Mongo DB, TIL, Today I Leaned]
comments : true
category : [Sptring, Mongo DB]
---

> <https://jsonobject.tistory.com/559> Spring Boot, Spring Data MongoDB, Querydsl로 타입 세이프 쿼리 작성하기

RDBMS의 경우 Spring Boot, Spring Data JPA, Querydsl 조합을 이용하면 직관적이고 높은 생산성으로 개발에 집중할 수 있다.


<details>
<summary>레플리카셋</summary>
<div markdown="1">

Pod은 우리의 서비스 또는 서버의 동작을 보장해주지 않습니다.
실행된 노드의 리소스가 부족하거나, 동작에 실패하거나 등등의 여러가지 이유로 인해 Pod는 죽을 수 있습니다.
그리고, 누군가 이를 수정하거나 고쳐주기 전까진 '멍청하게도' 죽은 그 상태로 남아있죠.
이를 해결하기 위해 등장한 것이 바로! 레플리카셋입니다!

레플리카셋을 간단히 설명하자면,
"지정한 pod 갯수만큼 항상 실행될 수 있도록 관리해주는 Controller"
정도로 말할 수 있겠네요.

다시 말하면, 특정 Pod을 5개로 항상 유지하도록 하는 레플리카셋을 만든다면
Pod의 갯수를 모니터링 하다가, 임의의 이유로 인해 Pod가 삭제될 경우
자동으로 새로운 Pod 1개를 생성하여 5개를 유지해줍니다.

이를 통해,
명시된 Pod의 실행을 항상 안정적으로 유지하고,
Pod의 가용성을 보증하는 역할입니다.

> <https://artist-developer.tistory.com/34> [Kubernetes] 쿠버네티스 ReplicaSet
</div>
</details>


<br/>
<br/>
<br/>
<br/>


> <https://csy7792.tistory.com/346> [Spring] Kotlin 을 이용하여 MongoDB Query를 Type-safe 하게 작성하기

```
Query()
    .addCriteria(
        Criteria.where("id").isEqualTo(1L)
    )
```


크게 2가지로 나누어볼 수 있을 것이다.

Type-safe 하지 않다.
만약 id가 Long 타입이 아니라 다른타입이라면 ? 바로 오류로 이어질 것이다.
오타로 인해 오류가 발생할 수 있다.
id를 잘못하여 di 로 적는순간 오류로 이어질 것이다.
id는 짧기에 이런일이 없겠지만 필드명이 길어지는 순간 위험도는 증가한다.

> Kotlin을 이용하여 Spring data mongo 를 이용한다면 이러한 문제점을 해결할 수 있다.

`org.springframework.data.mongodb.core.query.TypedCriteriaExtensions` 파일을 확인해보면 아주 멋진 방법으로 이러한 문제를 해결해 놓았다.

바로 아래와같이 infix fun 을 이용하여 해결했다.

```
fun <T> KProperty<T>.nin(vararg o: Any): Criteria =
        Criteria(asString(this)).nin(*o)
```

그렇다면 이것을 응용하여 어떻게 Type-safe 하고 오타에 안전하게 개발 할 수 있을까 ?

바로 아래와 같이 작성하면 된다.

```
Query()
    .addCriteria(
        User::id isEqualTo 1L
    )
```

Spring + MongoDB 의 조합을 이용하고 계시다면 Kotlin 을 도입하는것을 고려해볼만한 이유가 될 것 같다.

