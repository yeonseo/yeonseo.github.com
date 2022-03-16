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






# 20220308

https://jh3786.tistory.com/19

'_' 잘하면,, 내가 FCM 할 것 같아서,,,




# 20220310

https://gingerkang.tistory.com/127

드래그 앤 드롭으로 '배너' 기능에서 사용될 예정



[Vue.js] 라이브러리 없이 Drag & Drop 가능한 파일 업로드 만들기
https://velog.io/@hgoguma_124/Vue.js-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EC%97%86%EC%9D%B4-Drag-Drop-%EA%B0%80%EB%8A%A5%ED%95%9C-%ED%8C%8C%EC%9D%BC-%EC%97%85%EB%A1%9C%EB%93%9C-%EB%A7%8C%EB%93%A4%EA%B8%B0

파일 업로드도 드래그 앤 드롭이라서 '_'



# 20220311
좋아 가보자고!!



내가 짠 나이스 인증관련해서 문제 발생함

```
ERROR 22-03-11 10:52:59[http-nio-8888-exec-1] [[dispatcherServlet]:175] - Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is java.security.InvalidKeyException: No installed provider supports this key: javax.crypto.spec.SecretKeySpec] with root cause
java.security.InvalidKeyException: No installed provider supports this key: javax.crypto.spec.SecretKeySpec
	at java.base/javax.crypto.Cipher.chooseProvider(Cipher.java:930)
	at java.base/javax.crypto.Cipher.init(Cipher.java:1433)
	at java.base/javax.crypto.Cipher.init(Cipher.java:1364)
	at com.bcg.funble.userapi.common.AES256Service.encryptedAES256(AES256Service.kt:34)
	at com.bcg.funble.userapi.common.APIService.getSymmetricKey(APIService.kt:154)
	at com.bcg.funble.userapi.common.APIService.certCheck(APIService.kt:47)
	at com.bcg.funble.userapi.member.controller.MemberController.certCheck(MemberController.kt:43)
	at com.bcg.funble.userapi.member.controller.MemberController$$FastClassBySpringCGLIB$$b2e1b9f0.invoke(<generated>)
	at org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:218)
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:688)
	at com.bcg.funble.userapi.member.controller.MemberController$$EnhancerBySpringCGLIB$$4a75e0d5.certCheck(<generated>)
	at jdk.internal.reflect.GeneratedMethodAccessor316.invoke(Unknown Source)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
```

내가 생각하는 이유는
* 대표님이 다른 사람의 정보로 인증요청을 했었음
* 그 정보가 기기에 남았고
* 다시 대표님이 인증을 요청할 때 정보가 충돌됨


팀장님은 우선 내 코드에서 싱글톤인데 전역변수를 사용하는 코드는 좋지 못하다고 알려주심

* 문제의 이유가 싱글톤 때문인거라고 추측하시는듯
왜 발생했는지 모르겠지만


* service 빈 생성 => 싱글톤
* 전역변수를 사용하면, 서비스 호출 과정에서 문제가 생길 수 있음.
* 완벽하게 제어하기가 힘드므로 이를 없애는 것을 제안해주셨음


## 싱글톤에 대해서 알아보자

- 싱글톤 패턴(Singleton pattern)을 쓰는 이유와 문제점
https://jeong-pro.tistory.com/86

싱글톤 패턴의 문제점

싱글톤 인스턴스가 너무 많은 일을 하거나 많은 데이터를 공유시킬 경우 다른 클래스의 인스턴스들 간에 결합도가 높아져 "개방-폐쇄 원칙" 을 위배하게 된다. (=객체 지향 설계 원칙에 어긋남)

따라서 수정이 어려워지고 테스트하기 어려워진다.

또한 멀티쓰레드환경에서 동기화처리를 안하면 인스턴스가 두개가 생성된다든지 하는 경우가 발생할 수 있음

개발을 할때 항상 들어온 goto는 쓰면 안돼! 전역 객체는 안 좋은거야! 라는 말 처럼 꼭 필요한 경우아니면 지양해야함. // 적절히 잘 쓰면 아주 좋음, (그게 어렵지)

https://alwayspr.tistory.com/11
스프링 빈은 Thread-safe 할까?

이번에는 상태를 가지는(전역변수가 있는) 싱글톤 객체를 통해 덧셈을 하는 프로그램을 만들어보겠습니다.

public class Singleton {

    int num;


    private static Singleton singleton = new Singleton();

    private Singleton(){}

    public static Singleton getInstance(){
        return singleton;
    }

    public int add(){
        return ++ num;
    }

}

class AddTest{
    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();

        int[] array = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19 };

        for(int i : array) {

            new Thread(() -> {
                System.out.println(singleton.add());
            }).start();

        }
    }
}

쓰레드를 통해 객체의 내부 상태를 변경시키고, 출력을 합니다.





여기서 주목할 점은 데이터에 문제가 발생했습니다. 1 ~ 20이 나와야 하는데 1이 두번 나오고 20이 빠졌습니다.

하나의 공유자원을 놓고 여러 개의 쓰레드가 읽기/쓰기를 하면서 데이터 조작 중 문제가 발생한 거죠. (Race Condition)

이 경우는 Thread-safe 하지 못합니다.



본인이 짠 코드를 한번 생각해보면 스프링 빈(@Controller, @Service, @Repository, @Component 어노테이션이 달린 객체 등)의 전역변수에는 주로 스프링 빈과 같은 불변 객체들이 있지 VO, DTO, Map 같은 가변 객체가 존재하지 않을 겁니다. 만약 있다면 synchronized 키워드나 concurrent 패키지의 클래스들을 사용하여 동시성 문제를 해결했을 것입니다. 그리고 스프링 빈 사이의 데이터를 주고받을 때에는 스프링빈의 상태를 변경 시키는 것이 아니라 메소드의 파라미터를 이용했을 것이고요. 자신도 모르게 관행에 따라 개발을 하다 보니 Thread-safe 하게 개발 한 것 같습니다.



결론은 스프링 빈을 상태를 변경할 수 있게 만든다면 Thread-safe 하지 않습니다.





Spring Singleton
https://dahye-jeong.gitbook.io/spring/spring/2021-01-19-spring-singleton
Singleton Registry / Singleton Pattern 주의점
객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지 (stateful)하게 설계하면 안된다. 무상태(stateless)로 설계해야 한다!
특정 클라이언트에 의존적인 필드가 있으면 안된다.
특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다!
가급적 읽기만 가능해야 한다.
필드 대신에 자바에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.







## 그럼 어떻게 짜는게 좋은가

1. class entity로 넘겨주자




코틀린(Kotlin)의 Companion object는 단순히 자바(Java)의 static 키워드를 대체하기 위해서 탄생했을까요? 이 갑작스러운 질문은 코틀린에서 왜 static을 안 쓰게 되었는지 이해하는 데 큰 도움이 될 수 있습니다.
https://www.bsidesoft.com/8187






# 20220314

## 추상 data class는 toDto  리턴값으로 사용될 수 없다


C:\Users\PC\Documents\git\project\FUNBLE\funble_backend\module-admin-api\build\tmp\kapt3\stubs\main\com\bcg\funble\moduleadminapi\member\mapper\AdminMemberMapper.java:18: error: The return type AdminMemberDto.MemberBase is an abstract class or interface. Provide a non abstract / non interface result type or a factory method.
    public abstract com.bcg.funble.moduleadminapi.member.dto.AdminMemberDto.MemberBase toDto(@org.jetbrains.annotations.NotNull()


```
@Mapper(componentModel = "spring", unmappedTargetPolicy = ReportingPolicy.IGNORE)
interface AdminMemberMapper: EntityMapper<AdminMemberDto.MemberList, Member> {
    override fun toEntity(dto: AdminMemberDto.MemberList): Member
    @Mappings(
        Mapping(target="age", expression="java(entity.getAge())"),
        Mapping(target="userType", expression="java(entity.getUserType())"),
        Mapping(source="investPersonCod", target = "invest", qualifiedByName = ["invest"]),
//        Mapping(source="accountInfo", target = "accountNumber", qualifiedByName = ["accountNumber"]),
        Mapping(source="customerTendencyEvalCod", target = "customerTendency", qualifiedByName = ["customerTendency"])
    )
    override fun toDto(entity: Member): AdminMemberDto.MemberList
    //     override fun toDto(entity: Member): AdminMemberDto.MemberBase


    fun listToMemberDto(list: List<Member>): List<AdminMemberDto.MemberList>
    fun listToCorpMemberDto(list: List<Member>): List<AdminMemberDto.CorpMemberList>

    
    @Mappings(
            Mapping(source="investPersonCod", target = "invest", qualifiedByName = ["invest"]),
            Mapping(source="customerTendencyEvalCod", target = "customerTendency", qualifiedByName = ["customerTendency"])
    )
    fun toMemberDetailResponseDto(entity: Member): AdminMemberDto.MemberDetailResponse?

    companion object {
        @JvmStatic
        @Named("invest")
        fun invest(investPersonCod: InvestPersonCode?): List<String> {
            val result = mutableListOf<String>()
            result.add(investPersonCod.toString())
            investPersonCod?.let { result.add(it.description) }
            return result
        }

        @JvmStatic
        @Named("customerTendency")
        fun customerTendency(customerTendencyEvalCod: CustomerTendencyEvalCode?): List<String> {
            val result = mutableListOf<String>()
            result.add(customerTendencyEvalCod.toString())
            customerTendencyEvalCod?.let { result.add(it.description) }
            return result
        }

        @JvmStatic
        @Named("accountNumber")
        fun accountNumber(accountInfo: AccountInfo?): String? {
            return accountInfo?.accountNo
        }
    }
}

@Mapper(componentModel = "spring", unmappedTargetPolicy = ReportingPolicy.IGNORE)
interface AdminSubscribeHistoryMapper: EntityMapper<AdminMemberDto.DetailResponse, SubscribeHistory> {
    override fun toEntity(dto: AdminMemberDto.DetailResponse): SubscribeHistory

    @Mapping(source="subscribeInfo", target = "status", qualifiedByName = ["subscribeStatus"])
    override fun toDto(entity: SubscribeHistory): AdminMemberDto.DetailResponse

    fun subscribeHistoryListToSubscribeHistoryListDto(entity: List<SubscribeHistory>?)
            : List<AdminMemberDto.DetailResponse>?

    companion object {
        @JvmStatic
        @Named("subscribeStatus")
        fun subscribeStatus(subscribeInfo: SubscribeInfo): String {
            return subscribeInfo.getStatus(true)
        }
    }
}

```




## Mapping
@Mappings(
            Mapping(source="investPersonCod", target = "invest", qualifiedByName = ["invest"]),
            Mapping(source="customerTendencyEvalCod", target = "customerTendency", qualifiedByName = ["customerTendency"])
    )
    fun toMemberDetailResponseDto(entity: Member): AdminMemberDto.MemberDetailResponse?

    companion object {
        @JvmStatic
        @Named("invest")
        fun invest(investPersonCod: InvestPersonCode?): List<String> {
            val result = mutableListOf<String>()
            result.add(investPersonCod.toString())
            investPersonCod?.let { result.add(it.description) }
            return result
        }

        @JvmStatic
        @Named("customerTendency")
        fun customerTendency(customerTendencyEvalCod: CustomerTendencyEvalCode?): List<String> {
            val result = mutableListOf<String>()
            result.add(customerTendencyEvalCod.toString())
            customerTendencyEvalCod?.let { result.add(it.description) }
            return result
        }

        @JvmStatic
        @Named("accountNumber")
        fun accountNumber(accountInfo: AccountInfo?): String? {
            return accountInfo?.accountNo
        }
    }



```
class AdminMemberDto {
    abstract class MemberBase(
            var investPersonUniqueNo: String?,
            var individualCorpCod: String?,
            var htsId: String? = null,
            var name: String? = null,
            var invest:  List<String>? = null,
            var customerTendency: List<String>? = null, // 투자 성향
            var memberOpenYn: String? = null,
            var createdAt: String? = null
    )

    data class MemberList (
            var accountInfo: AccountInfoAdminListDto? = null,
            var phoneNo: String?,
            var age: Int? = null, // 회원 만 연령 나이
            var userType: String? = null, // 회원 타입
            var accountNoOpenYn: String? = null, // 계좌 개설 여부
            var allianceAccountNoRegisterYn: String? = null, // 제휴 계좌 연동 유무
            var investLimitAmt: String? = null, // 연간 투자 한도
    ): MemberBase(null,null,null,null,null,null,null,null)



    data class MemberDetailResponse (
            var accountInfo: AccountInfoAdminListDto? = null,
            var phoneNo: String?,

            var accountNoOpenYn: String? = null, // 계좌 개설 여부
            var allianceAccountNoRegisterYn: String? = null, // 제휴 계좌 연동 유무
            var certificationRegisterYn: String? = null, // 인증서등록여부 - Y.등록,N.미등록

            var investTendencyDiagnosisYn: String? = null, // 투자성향진단여부 - Y.진단 N.미진단 (KYC)
            var investLimitAmt: String? = null, // 연간 투자 한도

            var pushReceiveAgreeYn: String? = null, // PUSH수신동의여부
            var textReceiveAgreeYn: String? = null, // 문자수신동의여부
            var emailReceiveAgreeYn: String? = null, // 이메일수신동의여부
//
            var subscribeHistory: List<DetailResponse>? = null,
            var totalSubscribeAmt: BigDecimal? = null,

            /**
             *
             * SKS와는 무관한 법인회원을 등록하는 과정
            1. 법인관리자가 SKS 지점을 방문하여 계좌개설(공동인증서, HTS-ID 등을 발급 받음)
            2. 법인관리자가 SKS HTS에 접속해서 ID를 초기화 하고 공동인증서를 등록하는 등 온라인거래 가능한 상태를 만듬
            3. 펀블앱에 접속해서 기본적인 법인회원정보를 입력, 사업자등록증을 업로드하여 회원가입 신청(신청 상태)
            4. 펀블관리자가 입력한 회원가입정보를 확인하고 수작업으로 등록을 진행(등록 상태)
            5. 법인관리자에게 회원등록 완료를 카카오톡이나 이메일 등을 통해 통보하면 법인관리자가 앱에 접속하여 공동인증서로 로그인을 실행
             *
             */
    ): MemberBase(null,null,null,null,null,null,null,null)
}
```

