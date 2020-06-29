---
layout: post
title: NodeJS Routing 모듈화
description: "NodeJS를 백앤드로 사용하기"
tags: [NodeJS, Web, Backend,TIL, Today I Leaned]
comments : true
category : [NodeJS]
---

페이지 별로 관리하기 위해서 Routing 모듈화를 합니다.

<p></p>
<p></p>
<p></p>
<p></p>
<p></p>

---
* Development Env.
* post date : 2020. 06. 27
* OS : macOS Catalina 64bit, Windows 10 64bit
* Nodeserver : 1.0.0
* express : 4.17.1
* nodemon : 2.0.4
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