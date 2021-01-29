---
title: Python & Django 설치 및 프로젝트 생성하기
author: Yeonseo Nam
date: 2020-05-13 19:34:00 +0900
categories: [Django, Tutorial]
tags: [Python, Django, Web, Backend,TIL, Today I Leaned]
comments : true
---
* Development Env.
* post date : 2020. 05. 13
* OS : macOS Catalina 64bit, Windows 10 64bit
* Python : 3.8
* Django : 2.2.5
* Tool : PyCharm 2018.3.5 (Community Edition)
* JRE : 1.8.0_152-release-1343-b28 amd64
* JVM : OpenJDK 64-Bit Server VM by 

본 포스팅은 https://academy.nomadcoders.co/[Nomad Coders] 의 "[풀스택] 에어비엔비 클론 코딩" 을 기초로 하여 개인 프로젝트를 진행한 과정을 담고 있습니다. 더 자세한 설명이 궁금하신 분은 노마드 코더의 강의를 들어보세요! 많은 도움을 받으실겁니다 :-)



# Python  설치하기
많이 포스팅 되어있으니 생략하겠습니다.

# Django 설치하기
많이 포스팅 되어있으니 생략하겠습니다.

# Django Project
``` commend
> pip install pipenv
> pipenv shell
> pipenv install Django
> mkdir MyWeb
> cd MyWeb
> django-admin startproject config
```
위의 명령어를 작성하면 다음과 같이 나타난 파일을 확인할 수 있습니다. 이를 다음 4단계에 걸쳐 수정을 조금 한 후, 본격적으로 프로젝트를 진행합니다.

---
1. 단계 : 가장 상위 /config 이름 바꾸기
- MyWeb
- - <span style="color:red">config (-> Rename 'Aconfig')</span>
- - - config
- - - - ...py files
- - - manage. py

---
2. 단계 : 붉은 색의 파일들을 상위로 이동
- MyWeb
- - Aconfig
- - - <span style="color:red">config</span>
- - - - <span style="color:red">...py files</span>
- - - <span style="color:red">manage.py</span>

---
3. 단계 : /Aconfig 삭제하기
- MyWeb
- - <span style="color:red">Aconfig (-> Remove!)</span>
- - config
- - - ...py files
- - manage. py

---
4. 단계 : 최종모습! 이렇게 만들기!
- MyWeb
- - config
- - - ...py files
- - manage. py

---


잘 설치되었는지 확인하고 싶다면,
``` commend
> python manage.py runserver
```
명령어 입력 후, 나타나는 주소로 접속합니다. 다른 옵션을 주지 않았다면 http://127.0.0.1:8000 와 같이 로컬주소를 얻을 수 있습니다. 인터넷 주소창에 로컬주소를 입력하면 귀여운 로켓이 날고 있는 django 화면이 나타납니다. :-)

그 후에, 자신이 만들고자 하는 웹의 기능과 화면에 따라서 App들을 추가하며 프로젝트를 진행합니다.

``` commend
> django-admin startapp 'API 이름'
```
예를 들어, 사용자가 가입할 수 있고 게시판이 있는 사이트를 만든다고 한다면 다음과 같이 입력하면 됩니다.
- 'users'
- 'boards'
``` commend
> django-admin startapp users
> django-admin startapp boards
```


다음 포스팅에서 본격적으로 App을 추가하고 설계해보겠습니다.