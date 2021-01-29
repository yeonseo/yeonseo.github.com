---
title: REST API란?
author: Yeonseo Nam
date: 2020-06-29 19:34:00 +0900
categories: [Web, Tutorial]
tags: [REST, Web, TIL, Today I Leaned]
comments : true
---


REST는 로이 필딩(Roy Fielding)의 2000년 박사학위 논문에서 소개되었는데, 분산 시스템 설계를 위한 아키텍처 스타일입니다. 음, 웹사이트를 설계하는데 활용하면 좋은 가이드라인이라고 생각합니다. 필딩의 REST 원리를 따르는 시스템은 종종 RESTful이란 용어로 지칭되는데 Django에서는 Framework로 아예 설치해서 사용할 수 있습니다.

# REST API

REST(REpresentational State Transfer)란, "웹에 존재하는 모든 자원(이미지, 동영상, DB 자원)에 고유한 URI를 부여해 활용"하는 것입니다. 우선 3가지 영역에 나누어 URI를 나타낼 수 있는데 자원 / 행위 / 표현을 두고 URI를 부여합니다.

예를 들어,

board
    - localhost/board/create
    - localhost/board/list
    - localhost/board/1/delete
    - localhost/board/1/detail

게시판의 자원 / 행위 / 표현에 대해서 위와 같이 URI를 부여합니다.



## REST 특징 (6가지 조건)
1. 클라이언트/서버 구조: 일관적인 인터페이스로 분리되어야 한다
2. 무상태(Stateless): 각 요청 간 클라이언트의 콘텍스트가 서버에 저장되어서는 안 된다
3. 캐시 처리 가능(Cacheable): WWW에서와 같이 클라이언트는 응답을 캐싱할 수 있어야 한다.
잘 관리되는 캐싱은 클라이언트-서버 간 상호작용을 부분적으로 또는 완전하게 제거하여 scalability와 성능을 향상시킨다.
4. 계층화(Layered System): 클라이언트는 보통 대상 서버에 직접 연결되었는지, 또는 중간 서버를 통해 연결되었는지를 알 수 없다. 중간 서버는 로드 밸런싱 기능이나 공유 캐시 기능을 제공함으로써 시스템 규모 확장성을 향상시키는 데 유용하다.
5. Code on demand (optional) - 자바 애플릿이나 자바스크립트의 제공을 통해 서버가 클라이언트가 실행시킬 수 있는 로직을 전송하여 기능을 확장시킬 수 있다.
6. 인터페이스 일관성: 아키텍처를 단순화시키고 작은 단위로 분리(decouple)함으로써 클라이언트-서버의 각 파트가 독립적으로 개선될 수 있도록 해준다.

## REST의 주요 목표
* 구성 요소 상호작용의 규모 확장성(scalability of component interactions)
* 인터페이스의 범용성 (Generality of interfaces)
* 구성 요소의 독립적인 배포(Independent deployment of components)
* 중간적 구성요소를 이용해 응답 지연 감소, 보안을 강화, 레거시 시스템을 인캡슐레이션 (Intermediary components to reduce latency, enforce security and encapsulate legacy systems)


## REST 설계 지침
1. 소문자를 사용한다.
대소문자에 차이를 두기 떄문에(foo.com과 FOo.com은 서로 다르다) 혼란을 줄 수 있기 때문에 지양하는 것이 좋다.

2. 하이픈( - )을 사용한다.

3. 확장자를 사용하지 않는다.
http://foo.com/world.txt
http://foo.com/world.png
위와 같이 했을 때, 확장자에 때른 url을 만들어야 하기 떄문에 비효울적일 수 있다.

4. 밑줄( __ )은 사용하지 않는다.

## 행위에 대한 HTTP Method
* <strong>GET</strong> : 리소스를 조회
* <strong>POST</strong> : URL를 요청 시, 리소스(ex, 게시물 리스트)를 생성
* <strong>PUT</strong> : 리소스를 수정
* <strong>DELETE</strong> : 해당 리소스를 삭제

위의 board 예시로 본다면,
board
    - localhost/board/create <strong>(POST)</strong>
    - localhost/board/list <strong>(GET)</strong>
    - localhost/board/1/delete <strong>(DELETE)</strong>
    - localhost/board/1/detail <strong>(GET)</strong>
    - localhost/board/1/modify <strong>(PUT)</strong>

자원 / 행위 / 표현에 대해서 계층적 구조로 설계가 되기 때문에, 굉장히 확장성도 좋고 클라이언트와 서버간의 통신을 주고 받을 때도 직관적으로 필요한 자원이 어디에 있을지 예측가능합니다.


> 출처
> [위키백과 - REST](https://ko.wikipedia.org/wiki/REST)
> [기본기를 쌓는 정아마추어 코딩블로그](https://jeong-pro.tistory.com/180)
