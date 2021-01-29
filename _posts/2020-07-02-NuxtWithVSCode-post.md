---
title: Visual Studio Code로 NuxtJS 디버깅하기
author: Yeonseo Nam
date: 2020-05-13 19:34:00 +0900
categories: [NuxtJS, etc]
tags: [NuxtJS, Web, Frontend,TIL, Today I Leaned]
comments : true
---

Visual Studio Code에서 Nuxt JS를 디버깅 할 수 있도록 환경설정을 해보겠습니다. 다른 Tool도 많지만, WebStorm에서는 코드 디버깅이 제대로 되지 않더라구요. 해결방법이 있는 것을 봤지만, VS는 기본적으로 vue와 nuxt코드를 이해했습니다. 집에서는 데스크탑과 MacOS를 사용하고 있었고, 회사에서는 데스크탑을 사용하고 있어서 chrome과 remote만 하면되는 VS로 선택했습니다. 😁


<br/><br/><br/><br/><br/>


---

* Development Env.
* post date : 2020. 07. 02
* OS : macOS Catalina 64bit, Windows 10 64bit
* Nodeserver : 1.0.0
* NuxtJS : 2.13.2

---


![nuxtjs_logo](/post/images/NuxtJS_Logo.png)

# Debugging with Chrome 🐣


## 1. debugger for chrome 설치

![install debugger for chrome](/post/images/2020-07-02-nuxtdebugwithvs.png)



## 2. configurations 추가

윈도우 : f5
<br/>
맥 : ⌘⇧D
<br/><br/>
chrome 선택, .vscode/launch.json 생성되면 아래와 같이 launch.json 내용 수정 합니다.

```
// launch.json

{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "attach",
      "name": "client: chrome",
      "port": 9222,
      // "url": "http://localhost:3000",
      "webRoot": "${workspaceFolder}",
      "sourceMapPathOverrides": {
          "webpack:///*": "${workspaceRoot}/*"
      }
    },
    {
      "type": "node",
      "request": "launch",
      "name": "server: nuxt",
      "args": ["dev"],
      "osx": {
      "program": "${workspaceFolder}/node_modules/.bin/nuxt"
      },
      "linux": {
      "program": "${workspaceFolder}/node_modules/.bin/nuxt"
      },
      "windows": {
      "program": "${workspaceFolder}/node_modules/nuxt/bin/nuxt.js"
      }
    }
  ],
  "compounds": [
    {
      "name": "fullstack: nuxt",
      "configurations": ["server: nuxt", "client: chrome"]
    }
  ]
}
```

nuxt.config.js에 아래의 내용을 추가합니다.
```
// nuxt.config.js

export default {
  build: {
    extend(config, ctx) {
      if (ctx.isDev) {
        config.devtool = ctx.isClient ? 'source-map' : 'inline-source-map'
      }
    }
  }
}
```


## 3. fullstack 으로 디버깅 실행

![install debugger for chrome](/post/images/2020-07-02-nuxtdebugwithvs2.png)

크롬창으로 가서 [http://127.0.0.1:3000/](http://127.0.0.1:3000/) 으로 접속해보면, build되고 있는 모습을 볼 수 있습니다. Wow!

![install debugger for chrome](/post/images/2020-07-02-nuxtdebugwithvs3.png)




## 4. (번외) timeout message 발생 시, 크롬 속성에 debugging 정보 추가

![install debugger for chrome](/post/images/2020-07-02-nuxtdebugwithvs4.png)

Chrome 속성 > 바로가기 > 대상
아래의 명령어를 추가
```
 --remote-debugging-port=9222 --"%1"
```

[여기를 참고하세요!](https://liftcodeplay.com/2019/12/25/how-to-debug-nuxt-js-with-vs-code/)



저는 위와 같은 방식으로 디버깅 구동이 되었습니다.  ¯\_(ツ)_/¯
VS Code를 통해 디버깅 환경 구축하시는 분들에게 도움이 되셨기를!



> 참고 출처
>
> [Debugging Nuxt.js with Visual Studio Code](https://codeburst.io/debugging-nuxt-js-with-visual-studio-code-724920140b8f)
> [run the debugging, timeout message](https://liftcodeplay.com/2019/12/25/how-to-debug-nuxt-js-with-vs-code/) 
timeout
> [Debugging Nuxt.js with VS Code](https://medium.com/js-dojo/debugging-nuxt-js-with-vs-code-60a1a9e75cf6
fullstack) 
