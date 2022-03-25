---
layout: post
title: Axios errorHandler에서 요청 다시 보내기 (feat. Refreash Token을 받아보자)
description: ""
tags: [Vue3, Web, FrontEnd, TIL, Today I Leaned]
comments : true
category : [FrontEnd, Vue, axios]
---


로그인 Refreash Token을 적용하기 위해서 Axios errorHandler를 구현하게 되었습니다. interceptors 안에서 error 발생 시, API 서버에서 날려주는 에러 status값에 따라 대응하도록 코딩했는데, 몰랐던 개념들이 많아서 정리겸 포스팅을 합니다. 🤟

# axios 파일 구성

axios
 - interceptors : Axios 실행 전, 가로채기 => 요청/응답 전 작업
    - request : 요청시 처리부
    - response : 응답시 처리부



## 기본 구조
```js
const _axios = axios.create({
    headers: {
        'Content-Type': 'application/json',
    },
});


// interceptors 

// interceptors > request
// >>>>>> 요청 전, 가로채기
_axios.interceptors.request.use(
    // 구성을 변경
    (config) => {
        return config;
    },

    // 오류 요청에 대한 작업
    (error) => {
        return Promise.reject(error);
    }
);

// interceptors > response
// >>>>>> 응답 전, 가로채기
_axios.interceptors.response.use(
    (response) => {
        return response.data;
    },
    (error) => {
        return Promise.reject(error);
    }
);
```

<div class="space-item-3"></div>


* 요청 시, 헤더에 토큰을 담아 보낼 때는 아래와 같이 작성하면 됩니다.

```js
const _axios = axios.create({
    headers: {
        'Content-Type': 'application/json',
    },
});

_axios.interceptors.request.use(
    // 구성을 변경
    (config) => {
        // token 추가
        if(store.state.token) {
            const token = store.state.token;
            config.headers.authorization = "Bearer " + token;
            
            // 이런식으로 추가할 수 있습니다
            config.headers.userId = state.userId;
        }
        return config;
    },

    (error) => {
        return Promise.reject(error);
    }
);

_axios.interceptors.response.use(
    (response) => {
        return response.data;
    },
    (error) => {
        return Promise.reject(error);
    }
);
```

## Promise.reject 란??


[Promise.reject 설명](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)

> Promise.reject는 주어진 이유(reason)로 거부된 Promise 객체를 반환합니다.

즉, Axios 에러발생 -> error에 담긴 정보로 에러 발생을 출력해주는 작업을 해줍니다.

```Console
Error message: Error from : /v1/member/list
status : 500 
message: Internal Server Error
from : mounted hook
```
<strike>정말 보기 싫은 에러</strike>


![error-noooooo](/post/images/hey/hate04.gif)

<div class="space-item-6"></div>

# Refreash Token (토큰 재발급)
그렇다면, refreash token 요청 작업이 어떻게 이뤄지는지 봅니다.

우선, 토큰값이 만료되었기 때문에 API에서 에러코드가 떨어지게 됩니다. 제가 작업 중인 프로젝트에서는 토큰 값이 이상이 있을 경우 401, 403을 보내주고 있는데요.

```Console
401 Unauthorized => 신분증 내라
403 Forbidden => 여긴 STAFF 온리야
```

오류가 먼저 발생되고, 그 후에 처리를 해야 된다는 점을 이해해주시면 감사하겠습니다.

## 로직 순서

1. 토큰 만료 등으로 오류발생
2. 기존 토큰으로 인증 재요청
    - (1) 오류응답 발생 => 처리부분에서 가로채기!
    - (2) 가로챈 부분에서 토큰 재발행 요청
    - (2) 토큰 인증 및 재발행 요청 성공 시, 원래 하려던 작업 다시 실행

<div class="space-item-3"></div>

<details>
<summary>Axios 구현부 전체 코드 접기/펼치기 버튼</summary>
<div markdown="1">

```js
import { axios } from '@bundled-es-modules/axios';
import { store } from '@/store';
import UserService from "@/services/api/user.service";

// 에러를 처리부. 길어지므로, 함수로 빼서 관리
const errorHandler = async (error) => {
    if (error.response.status === 401 || error.response.status === 403) {
        // accessToken 과 refreshToken으로 사용자 토큰값 검증 요청 API
        const res = await UserService.checkAdminToken()

        // 토큰 정상 발생시 응답값으로 확인
        if(res === 'ok') {
            // 이전 요청 다시 제개
            return instance.request(error.config);
        }
    }

    if( error.response.status === 400) {
        return await Promise.reject(error.response.data.message);
    }

    const errorMsg = `Error from : ${error.config.url}\n`
        + `status : ${error.response.status} \n`
        + `message: ${error.response.statusText}`;
    return Promise.reject(errorMsg)
}

const instance = axios.create({
    headers: {
        'Content-Type': 'application/json',
    },
});

instance.interceptors.request.use(
    (config) => {
        if(store.state.token) {
            const token = store.state.token;
            config.headers.authorization = "Bearer " + token;
        }
        return config;
    },
    (error) => {
        return Promise.reject(error);
    }
);

instance.interceptors.response.use(
    (response) => {
        return response.data;
    },
    error => errorHandler({...error})
);

export default instance;
```
</div>
</details>


<div class="space-item-6"></div>

위의 흐름대로 적용해주시면 토큰 재발생 후, 다시 요청을 보냅니다. 그러면 사용자 입장에서는 만료된 토큰으로 api 요청을 보냈지만 에러메세지나 멈춤없이 화면이 로딩됩니다.

<div class="space-item-2"></div>

![error-noooooo](/post/images/hey/hate04.gif)

<strike>다시 한번, 에러 꺼져 ㅠ </strike>