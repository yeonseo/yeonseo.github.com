---
layout: post
title: Vue - infinite redirection 문제
description: ""
tags: [vue, Web, ERROR, Frontend, TIL, Today I Leaned]
comments : true
category : [Frontend, Vue]
---

# 에러 기록
<img class="image fit" src="/post/images/2022-02-28-infinite-redirection.png">

```
vue-router.esm-bundler.js:72 [Vue Router warn]: Detected an infinite redirection in a navigation guard when going from "/" to "/login". Aborting to avoid a Stack Overflow. This will break in production if not fixed.

[Vue Router warn]: Unexpected error when starting the router: Error: Infinite redirect in navigation guard
    at vue-router.esm-bundler.js:3045:47
```


아래의 문제 코드를 보면서 설명하겠담


```
router.beforeEach((to, from, next) => {
    const noQuery = from.params.init;
    const token = store.state.token.token;

    console.log(">>>>>>>>>>>>>>>>>>>>> to !");
    console.log(to);

    // 무조건 인증 체크 후, 페이지 이동
    if (token) {
        if (to.path === '/') {
            return next({ path: "/" });
        } else {
            next();
        }
    } else {
        console.log(">>>>>>>>>>>>>>>>>>>>> need login infor !");
        return next({path: "/login"});
    }


});
export default router;

```

<img class="image fit" src="/post/images/2022-02-28-infinite-redirection2.png"> 



## 이유
토큰 정보가 없어, login 으로 가려고 이동하려고 함. 이때도 토큰 정보가 없어 beforeEach 안에서 또 router 연결이 이뤄짐 ( return next({path: "/login"});)

이때 infinite redirection 발생으로 beforeEach 가드안에서 탈출하지 못 하고 에러가 발생함.

 -> memeber 진입
 -> 토근이 없어 login으로 넘겨짐
 -> login patt를 가지고 진입
 -> 다시 토큰이 없어 login으로 넘겨짐 (무한반복 에러)




### 수정

```
router.beforeEach((to, from, next) => {
    const noQuery = from.params.init;
    const token = store.state.token.token;

    console.log(">>>>>>>>>>>>>>>>>>>>> to !");
    console.log(to);
    
    if (to.path === '/login') {
        next();
    } else if (token) {
        if (to.path === '/') {
            return next({ path: "/" });
        } else {
            next();
        }
    } else {
        console.log(">>>>>>>>>>>>>>>>>>>>> need login !");
        return next({path: "/login"});
    }
});
export default router;

```


라우팅이 다시 일어날 수 있는 조건 (현재는 login으로 연결되는 부분) 을 미리 체크해주어 next()로 빠져나갈 수 있도록 수정함.