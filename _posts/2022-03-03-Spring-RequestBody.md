---
layout: post
title: 스프링부트 (Spring) Get 사용 시 Body관련
description: ""
tags: [Spring, Kotlin, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Spring, Spring Data JPA]
---

# Get 에서는 Body가 담기지 않음


> https://www.baeldung.com/spring-mvc-send-json-parameters

>대리님, 저번에 말씀드린 GET 에서 requestBody가 안 되는 경우를 생각해봤는데욤
>지금상황에서 다 post로 보내야하기때문에,, get과 혼용이 우려될수있으니가욤.!
>공식 문서에서는 parms를 string으로 해결하기도 해서, 저렇게 보내는건 어떨지욤 '_'




Does Spring @RequestBody support the GET method?
Asked 6 years, 1 month ago
Active 6 months ago
Viewed 32k times

26


3
I am trying to carry JSON data in an HTTP GET request message, but my Spring MVC server can't seem to retrieve the JSON data from the GET request body.

spring-mvc
get
Share
Follow
edited Jan 22, 2016 at 23:26

Mario Cervera
65111 gold badge88 silver badges1919 bronze badges
asked Jan 22, 2016 at 22:20

Hongbo Tian
27311 gold badge33 silver badges55 bronze badges
you'll have to use the query string, or POST/PATCH/PUT/DELETE – 
Neil McGuigan
 Jan 23, 2016 at 0:21
Add a comment
2 Answers
Sorted by:

Highest Score (default)

31

HTTP's GET method does not include a request body as part of the spec. Spring MVC respects the HTTP specs. Specifically, servers are allowed to discard the body. The request URI should contain everything needed to formulate the response.

If you need a request body, change the request type to POST, which does include the request body.

```
getMemberList(pageable, searchOptionValue) {
    // let form = new FormData();
    // form.append("pageable", encodeURI(JSON.stringify(pageable)));
    // form.append("searchOptionValue", encodeURI(JSON.stringify(searchOptionValue)));

    const dto = {...pageable, ...searchOptionValue};

    return axios.post(`/v3/member/list`, dto);
    // return axios.get(`/v3/member/list`, form);
}
```

```
@ApiOperation(value="회원관리 > 회원조회")
@PostMapping("/list")
fun getMemberList(
        @RequestBody dto: AdminMemberDto.MemberSearchOption
): ResponseEntity<MutableMap<String, Any?>> = ResponseEntity(
        adminMemberService.getMemberList(dto, dto.of()), HttpStatus.OK)
```




