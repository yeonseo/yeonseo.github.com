---
title: NodeJS URL 요청처리
author: Yeonseo Nam
date: 2020-06-25 19:34:00 +0900
categories: [NodeJS, Tutorial]
tags: [NodeJS, Web, Backend,TIL, Today I Leaned]
comments : true
---

URL Routing 처리
[https://www.inflearn.com/course/node-js-%EC%9B%B9%EA%B0%9C%EB%B0%9C/lecture/6120](https://www.inflearn.com/course/node-js-%EC%9B%B9%EA%B0%9C%EB%B0%9C/lecture/6120)


<br/><br/><br/><br/><br/>

---

* Development Env.
* post date : 2020. 06. 26
* OS : macOS Catalina 64bit, Windows 10 64bit
* Nodeserver : 1.0.0
* Express : 4.17.1
* Nodemon : 2.0.4

---


![nodejs_logo](/post/images/nodejs_logo.jpg)
# Node URL 라우팅 처리

## Get 방식

```html
<!-- /public/main.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./assets/style.css">
    <title>Main</title>
</head>
<body>
    <h1>Main Page</h1>

    <h3>2020년 신규 선발 일정 변경 안내(7/6 공고 예정)</h3>
    <p>
        안녕하세요? 서울시복지재단 희망두배 청년통장 선발 담당자입니다.

        2020년 신규 선발은 6월 중 예정이었으나, 주민센터의 재난지원금 신청 및 지급 등의 업무로 인해,
        7월 중 공고 및 접수로 일정이 변경되었습니다.
        신청접수기간은 7월 초 예정이며, 서울시 및 자치구 홈페이지, 서울시복지재단 및 희망두배청년통장 홈페이지 등에 모집 공고문이 게시됩니다.
        2019년 신청자격 및 방법은 공지사항 40번을 통해 확인하실 수 있습니다.
        서울시 희망두배 청년통장에 대한 관심에 진심으로 감사드립니다.
    </p>

    <script src="./assets/main.ts"></script>
</body>
</html>
```

```ts
// /assets/main.ts
// 입력
```

```css
/* /assets/main.css */
/* 입력 */
```

```js
// app.js
var exp = require('express')
var app = exp()
const express = require('express');
app.listen(3000, function() {
    console.log("start! express server on port 3000");
})
app.use(express.static('public'))
app.get('/', function(req, res) {
    res.sendFile(__dirname + "/public/main.html")
})
```

## Post 방식

```html
<!-- /public/email_post.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <form action="/email_post" method="post">
        email : <input type="text" name="email"><br>
        <input type="submit">
    </form>
</body>
</html>
```


post 데이터를 읽기 위해서는 parser가 필요하므로 install합니다.

```
> npm install body-parser --save
+ body-parser@1.19.0
updated 1 package and audited 51 packages in 0.678s
found 0 vulnerabilities
```


```js
// app.js
var exp = require('express')
var app = exp()
var bodyParser = require('body-parser');

const express = require('express');

app.listen(3000, function() {
    console.log("start! express server on port 3000");
})
app.use(express.static('public'))
app.use(bodyParser.json())
app.use(bodyParser.urlencoded ({
    extended:true
}))

//url routing
app.get('/', function(req, res) {
    res.sendFile(__dirname + "/public/main.html")
})
app.get('/email_post', function(req, res) {
    res.sendFile(__dirname + "/public/form.html")
})
app.post('/email_post', function(req, res) {
    console.log(req.body)
    res.send("<h1>Hi! " + req.body.email + "</h1>")
})
```