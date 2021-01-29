---
title: NodeJS Mysql, NoSql 연동
author: Yeonseo Nam
date: 2020-06-27 19:34:00 +0900
categories: [NodeJS, Tutorial]
tags: [NodeJS, Web, Backend,TIL, Today I Leaned]
comments : true
---

DB는 RDS와 Nosql을 사용하기로 했습니다. RDB는 Mysql을 사용하고, Nosql은 MongoDB를 사용해 연동을 합니다. 

<br/><br/><br/><br/><br/>

---

* Development Env.
* post date : 2020. 06. 27
* OS : macOS Catalina 64bit, Windows 10 64bit
* Nodeserver : 1.0.0
* Express : 4.17.1
* Nodemon : 2.0.4

---


![nodejs_logo](/post/images/nodejs_logo.jpg)

# NodeJS에서 Mysql 연동하기 
[expressjs - mysql](http://expressjs.com/ko/guide/database-integration.html#mysql)

```
> npm install mysql --save
```

```sql
-- mysql에서 작업
create database testdb_node;
use testdb_node;
create table users (
id int auto_increment primary key,
email varchar(100))
DEFAULT CHARACTER SET=utf8;

insert into users(email) values('test1@aaa.com');
insert into users(email) values('test2@aaa.com');
insert into users(email) values('test3@aaa.com');
insert into users(email) values('test4@aaa.com');
insert into users(email) values('test5@aaa.com');

select * from users;
```

```js
// app.js에 추가
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : 'localhost',
  user     : 'dbuser',
  password : 's3kreee7'
});

connection.connect();
connection.query('SELECT * from users', (error, rows, fields) => {
  if (error) throw error;
  console.log('User info is: ', rows);
});
connection.end();
```

```
<!-- 실행화면 -->
start! express server on port 3000
User info is:  [ RowDataPacket { id: 1, email: 'test1@aaa.com' },
  RowDataPacket { id: 2, email: 'test1@aaa.com' },
  RowDataPacket { id: 3, email: 'test2@aaa.com' },
  RowDataPacket { id: 4, email: 'test3@aaa.com' },
  RowDataPacket { id: 5, email: 'test4@aaa.com' },
  RowDataPacket { id: 6, email: 'test5@aaa.com' } ]
```

## post 요청에 대한 결과 구현

```js
// app.js
...
app.post('/ajax_send_email', function(req, res) {
    console.log(req.body)
    var email = req.body.email
    // var responeData = {'result' : 'ok', 'email' : req.body.email}
    var responeData = {}

    var query = connection.query('select email from users where email="' + email + '"'
    , function(err, rows) {
        if(err) throw err;
        if(rows[0]) {
            responeData.result = "ok"
            responeData.email = rows[0].email
            console.log(rows[0].email)
        } else {
            responeData.result = "none"
            responeData.email = ""
        }
        res.json(responeData)
    })
})
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
        var resultDiv = document.querySelector(".result");

        if(result.result !== "ok") resultDiv.innerHTML = "이메일을 찾을 수 없습니다.";
        else resultDiv.innerHTML = result.email;
    })
}
```
