---
layout: post
title: AWS 환경설정 2
description: "Django & angular를 배포해보자!"
tags: [Python, Django, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Django, DjangoAngualr]
---
* Development Env.
* post date : 2020. 06. 17
* OS : macOS Catalina 64bit, Windows 10 64bit
* Python : 3.8
* Django : 2.2.5
* Tool : PyCharm 2018.3.5 (Community Edition)
* JRE : 1.8.0_152-release-1343-b28 amd64
* JVM : OpenJDK 64-Bit Server VM by 

본 포스팅은 https://academy.nomadcoders.co/[Nomad Coders] 의 "[풀스택] 에어비엔비 클론 코딩" 을 기초로 하여 개인 프로젝트를 진행한 과정을 담고 있습니다. 더 자세한 설명이 궁금하신 분은 노마드 코더의 강의를 들어보세요! 많은 도움을 받으실겁니다 :-)



# AWS CLI

## requirements.txt 생성

```
> pipenv run pipenv_to_requirements -f
```
