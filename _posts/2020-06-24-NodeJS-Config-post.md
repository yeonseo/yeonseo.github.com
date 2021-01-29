---
title: NodeJS 시작하기
author: Yeonseo Nam
date: 2020-06-24 19:34:00 +0900
categories: [NodeJS, Tutorial]
tags: [NodeJS, Web, Backend,TIL, Today I Leaned]
comments : true
---



프로젝트에서 NodeJS를 이용해서 진행하게 되었다. 안드로이드, ios(아이폰), 웹에서 동시에 구동되도록 개발한다고 들었다.
NodeJS / Cordoba / 프론트앤드는 아직 미정

NPM Project 시작하기
[https://www.inflearn.com/course/node-js-%EC%9B%B9%EA%B0%9C%EB%B0%9C/lecture/6118](https://www.inflearn.com/course/node-js-%EC%9B%B9%EA%B0%9C%EB%B0%9C/lecture/6118)

<br/><br/><br/><br/><br/>

---

* Development Env.
* post date : 2020. 06. 25
* OS : macOS Catalina 64bit, Windows 10 64bit
* Nodeserver : 1.0.0
* Express : 4.17.1
* Nodemon : 2.0.4

---


![nodejs_logo](/post/images/nodejs_logo.jpg)

# NodeJS

> Node.js는 확장성 있는 네트워크 애플리케이션(특히 서버 사이드) 개발에 사용되는 소프트웨어 플랫폼이다. 작성 언어로 자바스크립트를 활용하며 Non-blocking I/O와 단일 스레드 이벤트 루프를 통한 높은 처리 성능을 가지고 있다.
[출처](https://ko.wikipedia.org/wiki/Node.js)

# npm

> npm 은 자바스크립트 프로그래밍 언어를 위한 <strong>패키지 관리자</strong>이다. 자바스크립트 런타임 환경 Node.js의 기본 패키지 관리자이다. 명령 줄 클라이언트, 그리고 공개 패키지와 지불 방식의 개인 패키지의 온라인 데이터베이스로 이루어져 있다. [출처](https://ko.wikipedia.org/wiki/Npm_(%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4))

개발자들이 코드를 공유하고 재생산하기 위해서 만들어진 것!

 
## npm init (node project create)

```
 > mkdir nodeServerVimeo
 > cd .\nodeServerVimeo\
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

```
\nodeServerVimeo > npm install express --save
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN nodeserver@1.0.0 No repository field.

+ express@4.17.1
added 50 packages from 37 contributors and audited 50 packages in 1.612s
found 0 vulnerabilities

\nodeServerVimeo > vi .\package.json

{
  "name": "nodeserver",
  "version": "1.0.0",
  "description": "nodeserver test",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1"
  }
}
```

dependencies에 express가 추가된 것을 볼 수 있습니다.

## express 사용하여 웹서버 구동

```
nodeServerVimeo> node app.js
end of server code...
start! express server on port 3000
```

```js
// app.js
var exp = require('express')
var app = exp()
app.listen(3000, function() {
    console.log("start! express server on port 3000");
})

console.log ("end of server code...")
```

localhost:3000 으로 인터넷 주소창에 서버에 접속을 해볼 수 있습니다. 노드는 함수들이 비동기방식으로 작동되기 때문에 end of server code...가 먼저 나오고 그 후에 express 함수가 작동되는 것을 볼 수 있습니다.


## nodemon 설치& 사용

Nodemon은 파일이 바뀌는 것을 감지해서 자동으로 프로그램을 재시작해주는 개발 도구입니다. node라는 명령어 대신 nodemon으로 실행하면 됩니다. 짱짱맨!

```
> npm install -g nodemon

// 설치 완료 후,

> nodemon app.js // node대신 nodemon 사용
```

 이후, app.js 파일을 수정하면 프로그램이 자동으로 다시 시작되는 것을 볼 수 있습니다. :-)