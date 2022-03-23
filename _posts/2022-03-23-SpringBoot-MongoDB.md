---
layout: post
title: 스프링부트(Spring Boot)에서 Mongo DB 사용하기
description: ""
tags: [Sptring Boot, Spring, Kotlin, Web, Backend, Mongo DB, TIL, Today I Leaned]
comments : true
category : [Sptring, Mongo DB]
---

# NoSQL DB와 Mongo DB

Mongo DB란 NoSQL의 종류 중 하나이다.
로그성 데이터를 저장하는데에는 NoSQL이 유리하다

> https://meetup.toast.com/posts/274

> 관계형 데이터베이스가 기존 데이터베이스의 문제점에서 출발했듯이 관계형 데이터베이스 역시 한계점을 노출하게 되었으며 이로 인해 새로운 데이터베이스인 NoSQL이 탄생하게 되었습니다. 
> NoSQL은 초기엔 주로 탈 RDBMS를 의미하는 표준화된 구조적 질의 언어가 없는 데이터베이스(No SQL) 또는 관계를 갖지 않는 데이터베이스(Nonrelational Operational Database SQL)로 정의하였지만 현재는 앞의 두 가지 의미 외에도 SQL뿐만 아니라 여러 가지 기능을 제공(Not Only SQL)한다는 의미로 일반화되었습니다.

## NoSQL DB 특징

유연성 : 스키마 선언 없이 필드의 추가 및 삭제가 자유로운 Schema-less 구조입니다.
확장성 : 스케일 아웃에 의한 서버 확장이 용이합니다.
고성능 : 대용량 데이터를 처리하는 성능이 뛰어납니다.
가용성 : 여러 대의 백업 서버 구성이 가능하여 장애 발생 시에도 무중단 서비스가 가능합니다.


<p class="space-item-3"/>

 <font size="5">그럼 이제 Spring Boot에 Mongo DB를 사용해보자</font>

![lets_get_start](/post/images/hey/lets_get_start.gif)

# 인텔리제이로 Spring Boot 프로젝트 만들기

언어 : kotlin
자바 : 11
구성 : gradle

Initialize를 통해 프로젝트 만들기를 시작한다

![spring-boot-kotlin-mongo1](/post/images/2022-03-23-kotlin-mongo1.png)


프로젝트의 이름과 타입등을 정해준다 

![spring-boot-kotlin-mongo2](/post/images/2022-03-23-kotlin-mongo2.png)


의존성을 추가한다.
나는 JOOQ라는 것도 함께 테스트 해보고 싶어서 이번 프로젝스 생성에 함께 넣었다.

![spring-boot-kotlin-mongo3](/post/images/2022-03-23-kotlin-mongo3.png)


FINISH로 끝!

![spring-boot-kotlin-mongo4](/post/images/2022-03-23-kotlin-mongo4.png)



<div class="space-item-3"></div>

### 문제 발생
cause: org/springframework/aot/gradle/springaotgradleplugin has been compiled by a more recent version of the java runtime (class file version 55.0), this version of the java runtime only recognizes class file versions up to 52.0

![spring-boot-error1](/post/images/2022-03-23-kotlin-mongo5-1.png)
위와 같은 에러가 발생 했습니다. 원인이 뭔지 봅시다.

![spring-boot-error2](/post/images/2022-03-23-kotlin-mongo5.png)
가장 의심스러운 JAVA 버전을 확인하러 갑니다.
현재 프로젝트에서 사용중인 버전으로 바꿔주세요.


![spring-boot-error3](/post/images/2022-03-23-kotlin-mongo5-2.png)
그리고 Gradle에서도 JAVA 버전의 설정이 중요하니 확인하러 Gradle로 가줍니다. 역시 잘못되어 있어서 11버전으로 바꿔주었는데요. 그렇다면 확인해봐야 할 곳이 또 남았습니다.

![spring-boot-error4](/post/images/2022-03-23-kotlin-mongo5-3.png)
여기서 여러가지 버전이 간혹 올라와서 문제를 자주 일으키더라구요. 저는 JAVA버전이 여러개 설치 되어 있어서 항상 이 곳을 먼저 확인해주는 편입니다. 이 프로젝트에서는 11 버전을 사용하고 있으니, 이외의 버전은 모두 '-'를 눌러 삭제해 주었습니다.


<div class="space-item-3"></div>

# Monogo BD 사용시 주의할 점

> <https://jsonobject.tistory.com/559> Spring Boot, Spring Data MongoDB, Querydsl로 타입 세이프 쿼리 작성하기

MongoDB 로컬 인스턴스 실행
아래는 예제를 실행하기 위한 목적의 MongoDB 로컬 인스턴스를 실행하는 예이다. (자신이 선호하는 다른 방법으로 실행해도 무방하다.)

MongoDB 도커 컨테이너를 실행
```
$ docker run -d --name mongodb -p 27017:27017 mongo
```
만약, Spring Data MongoDB가 제공하는 @Transactional을 사용하려면 인스턴스가 레플리카 셋으로 구성되어야 한다. Docker Compose를 이용하여 레플리카 셋을 구성하는 방법은 본 블로그의 이 글을 참고한다.



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


<div class="space-item-3"></div>


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
