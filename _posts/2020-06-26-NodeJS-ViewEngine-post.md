---
title: NodeJS View Engine 처리
author: Yeonseo Nam
date: 2020-06-26 19:34:00 +0900
categories: [NodeJS, Tutorial]
tags: [NodeJS, Web, Backend,TIL, Today I Leaned]
comments : true
---

View engine을 활용한 응답처리
[https://www.inflearn.com/course/node-js-%EC%9B%B9%EA%B0%9C%EB%B0%9C/lecture/6124](https://www.inflearn.com/course/node-js-%EC%9B%B9%EA%B0%9C%EB%B0%9C/lecture/6124)


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
# Node 

## EJS

```ejs
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>email ejs templete</title>
    </head>
    <body>
        <h1> Welcome <%= email %> </h1>
    </body>
</html>
```

```js
// app.js
app.post('/email_post', function(req, res) {
    console.log(req.body)
    // res.send("<h1>Hi! " + req.body.email + "</h1>")
    res.render('email.ejs', {'email' : req.body.email})
})
```




JSON 활용한 Ajax처리
[https://www.inflearn.com/course/node-js-%EC%9B%B9%EA%B0%9C%EB%B0%9C/lecture/6125](https://www.inflearn.com/course/node-js-%EC%9B%B9%EA%B0%9C%EB%B0%9C/lecture/6125)

## Ajax처리

```html
<!-- /public/form.html -->
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="http://code.jquery.com/jquery-latest.js"></script>
    <title>Document</title>
</head>

<body>
    <form action="/email_post" method="post">
        email : <input type="text" name="email"><br>
        <input type="submit">
    </form>

    <button class="ajaxsend">Ajax Button</button>

    <p class="result"></p>
    <script src="/main.js"></script>
</body>

</html>
```

```js
// /assets/main.js
document.querySelector('.ajaxsend').addEventListener('click', function () {
    var inputdata = document.forms[0].elements[0].value;
    sendAjax('http://127.0.0.1:3000/ajax_send_email', inputdata);
})

function sendAjax(url, data) {
    var data = { 'email': data };
    data = JSON.stringify(data);
    var xhr = new XMLHttpRequest();
    xhr.open('POST', url);
    xhr.setRequestHeader('Content-Type', "application/json");
    xhr.send(data);
    xhr.addEventListener('load', function () {
        var result = JSON.parse(xhr.responseText);
        if(result.result !== "ok") return;
        document.querySelector(".result").innerHTML = result.email;
    })
}
```


```js
// app.js
var exp = require('express')
var app = exp()
var bodyParser = require('body-parser');

const express = require('express');
var cors = require('cors')

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

//url routing
app.get('/', function(req, res) {
    res.sendFile(__dirname + "/public/main.html")
})
app.get('/email_post', function(req, res) {
    res.sendFile(__dirname + "/public/form.html")
})
app.post('/email_post', function(req, res) {
    console.log(req.body)
    // res.send("<h1>Hi! " + req.body.email + "</h1>")
    res.render('email.ejs', {'email' : req.body.email})
})
app.post('/ajax_send_email', function(req, res) {
    console.log(req.body)
    var responeData = {'result' : 'ok', 'email' : req.body.email}
    res.json(responeData)
})
```

## 크로스 도메인 문제 발생 시,

has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.

```
> npm install cors --save
```
서버를 다시 실행합니다.
[stackoverflow - No 'Access-Control-Allow-Origin' header is present on the requested resource—when trying to get data from a REST API](https://stackoverflow.com/questions/43871637/no-access-control-allow-origin-header-is-present-on-the-requested-resource-whe)