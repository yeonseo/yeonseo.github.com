---
title: NodeJS Routing 모듈화 및 리팩토링
author: Yeonseo Nam
date: 2020-06-28 19:34:00 +0900
categories: [NodeJS, Tutorial]
tags: [NodeJS, Web, Backend,TIL, Today I Leaned]
comments : true
---

페이지 별로 관리하기 위해서 Routing 모듈화를 합니다.

<br/><br/><br/><br/><br/>

---

* Development Env.
* post date : 2020. 06. 28
* OS : macOS Catalina 64bit, Windows 10 64bit
* Nodeserver : 1.0.0
* Express : 4.17.1
* Nodemon : 2.0.4

---


![nodejs_logo](/post/images/nodejs_logo.jpg)

# NodeJS Routing 모듈화
[expressjs - router](http://expressjs.com/ko/api.html#router)


```js
// app.js
var exp = require('express')
var app = exp()
var bodyParser = require('body-parser');

const express = require('express');
var cors = require('cors')

var mainRouter = require('./routers/router_main')
var userRouter = require('./routers/router_user')

app.listen(3000, function() {
    console.log("start! express server on port 3000");
})

app.use(express.static('public'))
app.use(express.static('assets'))
app.use(bodyParser.json())
app.use(bodyParser.urlencoded ({
    extended:true
}))
app.set('view engine', 'ets')
app.use(cors()) // Use this after the variable declaration

app.use('/main', mainRouter)
app.use('/user', userRouter)

app.get('/', function(req, res) {
    res.sendFile(__dirname + "/public/main.html")
})
```

```js
// routers/router_main.js
var exports = require('express')
var router = exports.Router()
var path = require('path')

//url routing
router.get('/', function(req, res, next) {
    console.log('main')
    res.sendFile(path.join(__dirname, "../public/main.html"))
})
module.exports = router;
```

이런식으로 모듈화를 하면, app.js를 심플하게 관리할 수 있습니다.
[github - commit](https://github.com/yeonseo/node-server/commit/3131cd90a6cd34f83c0b567cf32fbbe4f068bb96)



# NodeJS Routing 리팩토링

라우터 하위에 index.js에서 하위의 라우터를 관리하도록 수정하겠습니다.


```
다음과 같은 구조로 작성합니다.
│  index.js
│  
├─main
│      router_main.js
│      
└─user
        router_user.js

```

[github - commit](https://github.com/yeonseo/node-server/commit/278b0209bb83d9c241c67faa11423e3e8d1af22e)

