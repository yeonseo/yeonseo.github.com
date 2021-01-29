---
title: NuxtJS 설치 밎 환경 구성
author: Yeonseo Nam
date: 2020-07-01 19:34:00 +0900
categories: [NuxtJS, Tutorial]
tags: [NuxtJS, Web, Frontend,TIL, Today I Leaned]
comments : true
---

<br/><br/><br/><br/><br/>

---

* Development Env.
* post date : 2020. 07. 01
* OS : macOS Catalina 64bit, Windows 10 64bit
* Nodeserver : 1.0.0
* NuxtJS : 2.13.2

---


![nuxtjs_logo](/post/images/NuxtJS_Logo.png)

# NuxtJS 설치
[NuxtJS - installation](https://ko.nuxtjs.org/guide/installation/)

NodeJS가 설치되어 있다면, 아래 사이트를 따라합니다.

[NuxtJS - 처음부터 nuxt 시작하기](https://ko.nuxtjs.org/guide/installation#%EC%B2%98%EC%9D%8C%EB%B6%80%ED%84%B0-nuxt-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0) 


그 후, [create-nuxt-app 사용하기](https://ko.nuxtjs.org/guide/installation#-code-create-nuxt-app-code-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0) 를 통해서 빠르게 nuxt 프로젝트를 생성했습니다.

```
> npx create-nuxt-app nuxtClientTest

```

```
create-nuxt-app v3.1.0
✨  Generating Nuxt.js project in nuxtClientTest
? Project name: nuxtClientTest
? Programming language: TypeScript
? Package manager: Npm
? UI framework: None
? Nuxt.js modules: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Linting tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Testing framework: None
? Rendering mode: Universal (SSR / SSG)
? Deployment target: Server (Node.js hosting)
? Development tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)

...

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!


🎉  Successfully created project nuxtClientTest

  To get started:

        cd nuxtClientTest
        npm run dev

  To build & start for production:

        cd nuxtClientTest
        npm run build
        npm run start


  For TypeScript users.

  See : https://typescript.nuxtjs.org/cookbook/components/
```

```
> cd .\nuxtClientTest\
nuxtClientTest> npm run build
```

```
> nuxtClientTest@1.0.0 build C:\Users\PC\Documents\git\yeonseo\nuxtClientTest
> nuxt-ts build

i Production build                                                                                                          09:45:37
i Bundling for server and client side                                                                                       09:45:37
i Target: server                                                                                                            09:45:37
√ Builder initialized                                                                                                       09:45:37
√ Nuxt files generated                                                                                                      09:45:37
i Starting type checking service...                                                                         nuxt:typescript 09:45:41

√ Client
  Compiled successfully in 8.26s

√ Server
  Compiled successfully in 535.28ms


Hash: 71025251c6962d8bda9a
Version: webpack 4.43.0
Time: 8259ms
Built at: 2020-07-01 9:45:50 AM
                         Asset       Size  Chunks                         Chunk Names
../server/client.manifest.json   6.68 KiB          [emitted]
                      LICENSES  389 bytes          [emitted]
                app.57dbd67.js   52.8 KiB       0  [emitted] [immutable]  app
        commons.app.7871ae3.js    153 KiB       1  [emitted] [immutable]  commons.app
        pages/index.ad64b17.js   2.99 KiB       2  [emitted] [immutable]  pages/index
            runtime.90d5c68.js   2.32 KiB       3  [emitted] [immutable]  runtime
 + 2 hidden assets
Entrypoint app = runtime.90d5c68.js commons.app.7871ae3.js app.57dbd67.js

Hash: 5d359b434b64da019f2b
Version: webpack 4.43.0
Time: 536ms
Built at: 2020-07-01 9:45:50 AM
               Asset       Size  Chunks             Chunk Names
      pages/index.js   12.3 KiB       1  [emitted]  pages/index
           server.js   80.4 KiB       0  [emitted]  app
server.manifest.json  207 bytes          [emitted]
 + 2 hidden assets
Entrypoint app = server.js server.js.map
i Ready to run nuxt start
```

이제 서버를 로딩하여 [localhost:3000](localhost:3000)에 Nuxt 프로젝트를 시작할 수 있습니다. Nuxt 명령어는 [NuxtJS - 명령어 리스트](https://ko.nuxtjs.org/guide/commands/) 에서 확인해보시길 바랍니다.

```
nuxtClientTest> npm run start
```

```
> nuxtClientTest@1.0.0 start C:\Users\PC\Documents\git\yeonseo\nuxtClientTest
> nuxt-ts start


   ╭─────────────────────────────────────────╮
   │                                         │
   │   Nuxt.js @ v2.13.2                     │
   │                                         │
   │   ▸ Environment: production             │
   │   ▸ Rendering:   server-side            │
   │   ▸ Target:      server                 │
   │                                         │
   │   Memory usage: 77.6 MB (RSS: 130 MB)   │
   │                                         │
   │   Listening: http://localhost:3000/     │
   │                                         │
   ╰─────────────────────────────────────────╯
```


[NuxtJS 구조](https://ko.nuxtjs.org/guide/directory-structure)

