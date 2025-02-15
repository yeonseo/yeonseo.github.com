---
layout: post
title: 스프링부트 (Spring Boot) Get 사용 시 Body관련
description: ""
tags: [Spring Boot, Kotlin, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Backend, Spring Data JPA]
sitemap:
  changefreq: daily
  priority : 1.0
---

Spring Boot를 사용해 웹 프로젝트를 진행하던 중, Get 요청에 Body를 담아 구현하려던 일이 있었다. 검색결과 리스트를 요청하는 서비스였는데 검색 조건이 복잡함에 따라 DTO Class로 관리하려다 보니, Get 요청안에 검색조건을 Json 형태로 보내게 되었다. Post Man에서는 정상적으로 동작하던 API 서비스가 프론트엔드를 통해 http로 요청하니 body에 담긴 데이터를 받지 못해 에러가 발생했다. 


## 우선, Get 에서는 Body가 담기지 않음


<https://www.baeldung.com/spring-mvc-send-json-parameters> JSON Parameters with Spring MVC


위의 공식문에서 살펴보면, 

> Spring MVC offers @RequestParam to extract query parameters from GET requests. However, unlike @RequestBody, the @RequestParam annotation supports only simple data types such as int and String.
>
> So, to send JSON, we'll need to define our JSON parameter as a simple string.

Spring에선 Get requests인 경우에 @RequestParam를 사용할 수 있다. @RequestBody는 노노.. 그렇기 때문에 Json형태의 Object(body)를 굳이 보내고 싶다면, 문자열로 보내야하는데 그 구조가 복잡할 경우였다. 내가 오타를 내든, 다른사람이 에러를 내든, 타입 에러가 발생할 위험을 감수하고 싶지 않다.





### 깃헙에도 오라온 질문

<https://stackoverflow.com/questions/34956899/does-spring-requestbody-support-the-get-method>

>>I am trying to carry JSON data in an HTTP GET request message, but my Spring MVC server can't seem to retrieve the JSON data from the GET request body.
>>
>> `-Mario Cervera`
>
> HTTP's GET method does not include a request body as part of the spec. Spring MVC respects the HTTP specs. Specifically, servers are allowed to discard the body. The request URI should contain everything needed to formulate the response.
>
>If you need a request body, change the request type to POST, which does include the request body.
>
> `-Leland Barton`




## 코드 적용
```Vue
getMemberList(pageable, searchOptionValue) {
    let form = new FormData();
    form.append("pageable", encodeURI(JSON.stringify(pageable)));
    form.append("searchOptionValue", encodeURI(JSON.stringify(searchOptionValue)));
    
    return axios.get(`/v3/member/list`, form);
}

╔════════════════════════════════════════════════════════════╗
╠═ ↓↓↓↓↓                    CHANGE                    ↓↓↓↓↓ ═╣
╚════════════════════════════════════════════════════════════╝

getMemberList(pageable, searchOptionValue) {
    const dto = {...pageable, ...searchOptionValue};

    return axios.post(`/v3/member/list`, dto);
}
```

```
@ApiOperation(value="회원관리 > 회원조회")
@GetMapping("/list")
fun getMemberList(
        @RequestBody dto: AdminMemberDto.MemberSearchOption
): ResponseEntity<MutableMap<String, Any?>> = ResponseEntity(
        adminMemberService.getMemberList(dto, dto.of()), HttpStatus.OK)

╔════════════════════════════════════════════════════════════╗
╠═ ↓↓↓↓↓                    CHANGE                    ↓↓↓↓↓ ═╣
╚════════════════════════════════════════════════════════════╝

@ApiOperation(value="회원관리 > 회원조회")
@PostMapping("/list")
fun getMemberList(
        @RequestBody dto: AdminMemberDto.MemberSearchOption
): ResponseEntity<MutableMap<String, Any?>> = ResponseEntity(
        adminMemberService.getMemberList(dto, dto.of()), HttpStatus.OK)

```




