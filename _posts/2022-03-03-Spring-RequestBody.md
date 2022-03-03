---
layout: post
title: [IntelliJ] 인텔리제이를 더욱 효과적으로 사용하기 위한 간단 셋팅 (5분)
description: ""
tags: [Tool, IntelliJ, TIL, Today I Leaned]
comments : true
category : [Tool]
---

# Get 에서는 Body가 담기지 않음



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
        console.log(">>>>>>>>>>>>>>>>>>>>");
        //
        // let form = new FormData();
        // form.append("pageable", encodeURI(JSON.stringify(pageable)));
        // form.append("searchOptionValue", encodeURI(JSON.stringify(searchOptionValue)));

        const dto = {...pageable, ...searchOptionValue};
        console.log(dto);

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






# query
https://blog.leocat.kr/notes/2019/11/14/querydsl-result-handling-projection


// 회원 조회
fun getMemberCount(searchOptionValue: AdminMemberDto.MemberSearchOption, pageable: PageRequest): Long {
    var baseQuery = query.select(Projections.constructor(AdminMemberDto.MemberSearchOption::class.java)).from(member, accountInfo)

    baseQuery = memberSearch(baseQuery, searchOptionValue, pageable)

    return baseQuery.fetchCount()
}

