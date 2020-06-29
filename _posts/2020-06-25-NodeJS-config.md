---
layout: post
title: NodeJS 시작하기
description: "NodeJS를 백앤드로 사용하기"
tags: [NodeJS, Web, Backend,TIL, Today I Leaned]
comments : true
category : [NodeJS]
---



프로젝트에서 NodeJS를 이용해서 진행하게 되었다. 안드로이드, ios(아이폰), 웹에서 동시에 구동되도록 개발한다고 들었다.
NodeJS / Cordoba / 프론트앤드는 아직 미정

<p></p>
<p></p>
<p></p>
<p></p>
<p></p>

---
* Development Env.
* post date : 2020. 06. 25
* OS : macOS Catalina 64bit, Windows 10 64bit
* 
* JVM : OpenJDK 64-Bit Server VM by 
---

![nodejs_logo](/post/images/nodejs_logo.jpg)
# NodeJS


> Node.js는 확장성 있는 네트워크 애플리케이션(특히 서버 사이드) 개발에 사용되는 소프트웨어 플랫폼이다. 작성 언어로 자바스크립트를 활용하며 Non-blocking I/O와 단일 스레드 이벤트 루프를 통한 높은 처리 성능을 가지고 있다.
[출처](https://ko.wikipedia.org/wiki/Node.js)

# npm

> npm 은 자바스크립트 프로그래밍 언어를 위한 패키지 관리자이다. 자바스크립트 런타임 환경 Node.js의 기본 패키지 관리자이다. 명령 줄 클라이언트, 그리고 공개 패키지와 지불 방식의 개인 패키지의 온라인 데이터베이스로 이루어져 있다. [출처](https://ko.wikipedia.org/wiki/Npm_(%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4))

코드를 공유하고 재생산하기 위해서 만들어진 것

 
 ## npm init (project create)

 ```
  > mkdir nodeServerVimeo


    디렉터리: C:\Users\PC\Documents\git\yeonseo


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----     2020-06-25   오후 4:16                nodeServerVimeo


 > cd .\nodeServerVimeo\
 \nodeServerVimeo > ls
 \nodeServerVimeo > npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (nodeservervimeo) nodeserver
version: (1.0.0)
description: nodeserver test
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)
About to write to C:\경로\nodeServerVimeo\package.json:

{
  "name": "nodeserver",
  "version": "1.0.0",
  "description": "nodeserver test",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}


Is this OK? (yes) yes


   ╭────────────────────────────────────────────────────────────────╮
   │                                                                │
   │      New minor version of npm available! 6.13.4 -> 6.14.5      │
   │   Changelog: https://github.com/npm/cli/releases/tag/v6.14.5   │
   │               Run npm install -g npm to update!                │
   │                                                                │
   ╰────────────────────────────────────────────────────────────────╯

 \nodeServerVimeo >
```
package.json, key와 value로 이루어진 프로젝트 정보 파일이 생성됨
--save : package.json에 설치한 라이브러리를 저장하도록 하는 명령어

