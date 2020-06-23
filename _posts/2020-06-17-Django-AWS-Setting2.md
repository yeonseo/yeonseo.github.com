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

or

> pipenv freeze > requirements.txt
```
aws는 requirements.txt를 보고 필요한 패키지를 설치합니다. 지금까지 프로젝트를 진행하면서 설치한 모든 패키지들이 aws에도 설치되도록 requirements.txt를 만들어 줍니다.

## DB modify
DB는 이제껏 local로 운영하던 것에서 원격으로 둡니다. 저는 강좌를 따라 aws에서 제공하는 DB서비스 중에서 PostgreSQL을 사용했습니다. Elastic Beanstalk 안에 DB를 두지 않고 따로 만드는 이유는 크게 다음과 같습니다.

1. Elastic Beanstalk이 삭제되면 그 안의 DB를 손실하게 됩니다.
2. 구서버에서 신서버로 운영을 변경할 시, DB 연결 정보만으로 이전의 데이터를 이전할 수 있습니다.
3. 서버와 DB를 함께 두는 것보다 따로 관리하는 것이 보안상 더 좋습니다.


DEBUG 중인지 아닌지에 따라 DB를 달리하기 위해서 변수를 추가합니다.

``` python
# .env
DEBUG=Ture
```

DEBUG의 값을 읽어, 디버그가 아닌 상태에서는 PostgreSQL을 사용하도록 합니다.
```python
# settings.py

DEBUG = bool(os.environ.get("DEBUG"))

...

if DEBUG:

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.sqlite3",
            "NAME": os.path.join(BASE_DIR, "db.sqlite3"),
        }
    }
else:

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.postgresql",
            "NAME": os.path.join(BASE_DIR, "db.sqlite3"),
        }
    }
```

## RDS 생성
aws에서 제공하는 DB서비스를 통해 데이터베이스를 생성합니다.
https://ap-northeast-2.console.aws.amazon.com/rds/home?region=ap-northeast-2#

좌측 사이드바, 데이터 베이스 -> 데이터 베이스 생성
* Postgresql 선택
* free 선택
* username / password 입력
* 나머지는 건들지 말고 생성!

### RDS란?
Amazon Relational Database Service (Amazon RDS) makes it easy to set up, operate, and scale a relational database in the cloud. It provides cost-efficient and resizable capacity while automating time-consuming administration tasks such as hardware provisioning, database setup, patching and backups.

Amazon Relational Database Service (Amazon RDS)는 관계형 데이터베이스의 설정, 운영 및 확장을 쉽게 만들어 줍니다. 하드웨어 프로비저닝, 데이터베이스 설정, 매치 및 백업과 같은 시간소모적 관리 작업을 자동화 해보세요! 효율적인 가격과 데이터베이스 용량을 조정할 수 있습니다. :-)

프로비저닝(Provisioning) : 네트워크가 사용자에게 새로운 서비스를 제공할 수 있도록 네트워크를 준비하고 설치하는 과정. 서버 프로비저닝은 적절한 시스템, 데이터 및 소프트웨어를 사용하여 서버를 준비하고 네트워크 작업을 위해 서버를 준비하는 작업 세트입니다. 
> 출처: https://bfre.tistory.com/entry/IT-용어-풀이-프로비저닝Provisioning [IT한량]


 여기서 시작되는 <strong>'Postgresql setting'</striong> 과정에서 굉장히 힘들었습니다. 그리고 강의 댓글에서도 오류가 난다는 글들이 많이 보였구요!
 
 설치 방법에는 크게 2가지가 있습니다. 그리고 두 방법에는 다음과 같은 이유 때문에 에러가 발생할 수 있습니다.

1. requirements.txt에 패키지 적어주기

- yum 또는 apt-get이 psycopg2==2.8.4 또는  psycopg2-binary==2.8.4 을 지원하지 않음
===> <string style="color:red">Fail</string>

2. .ebextensions 폴더 안에 packages.config 만들어주기

- sudo yum install https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-6-x86_64/pgdg-centos96-9.6-3.noarch.rpm 을 실행하는 과정에서 다운로드 받을 패키지의 파일명이 달라지거나 사라져서 404 에러가 발생
===> <string style="color:red">Fail</string>

3. (+) 그 후, 커맨드를 바꿔 commit -> deploy해도 적용이 되지 않는 등의 문제가 발생할 수 있습니다. 그럴 땐 환경(EB)을 삭제한 후, 다시 시도하면 해결됬다는 분들도 계셨습니다. 

psycopg2 설치만 넘어가면 처음 셋팅에는 큰 문제가 없으니, 주의를 요합니다 :-) 그럼 시작해봅시다!


## Postgresql setting
```python
# requirements.txt
psycopg2==2.8.4 (추가)
-> psycopg2-binary==2.8.4 (저는 설치 오류가 나서, binary로 설치했습니다.)
```

📌psycopg2-binary==2.8.4는 직접 작성하여 저장해줍니다! 직접 작성한 내용이므로 freeze, pipenv_to_requirement는 하지 않습니다. aws eb는 requirements.txt 파일 안의 내용을 읽고, 패키지들을 설치합니다. 예를 들어, 

```python
# requirements.txt
django-phone-field==1.8.1
django-rest-auth==0.9.5
django-seed==0.2.2
django==3.0.7
```

🔺 위와 같은 requirements.txt 내용을 아래와 같이 실행합니다. 🔻

```
yum install django-phone-field==1.8.1
yum install django-rest-auth==0.9.5
yum install django-seed==0.2.2
yum install django==3.0.7
```

이 과정에서 많은 분들이 에러를 만납니다. 대충 아래와 같이 에러메세지가 나타납니다.

```
2020/06/19 03:08:01.699877 [ERROR] An error occurred during execution of command [app-deploy] - [InstallDependency]. Stop running the command. Error: fail to install dependenc
ies with requirements.txt file with error Command /bin/sh -c python3 -m pipenv install -r requirements.txt --skip-lock failed with error exit status 1. Stderr:Requirements fil
e provided! Importing into Pipfile…
An error occurred while installing psycopg2==2.8.4! Will try again.
```
'??? : 니가 준 requirements.txt대로 설치를 시도 해봤어. 헤이헤이,, 근데 문제가 있어,, 나 psycopg2==2.8.4 얘 때문에 오류야! 다르게 시도 해봐봐,,'

저는 psycopg2-binary==2.8.4로 바꿔 준 후, EB 환경을 삭제하고 새로 만들어서 eb deploy를 해 주었습니다.

1. yum이 설치를 지원하지 않음, 즉 yum install psycopg2 라는 커멘드가 에러 ===> <string style="color:red">Fail</string>
2.  psycopg2=='버전' 대신 psycopg2-binary=='버전' 으로 설치 시도, 역시 지원하지 않음 ===> <string style="color:red">Fail</string>
3. EB가 내가 쓴 커멘드를 실행하도록 작성함. 인터넷에 있는 해결방법에 적인 대로 아래와 같이 작성
```
commands:
  01_install_rhel_pg:
    command: "(yum repolist |grep -q pgdg96) || sudo yum install https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-6-x86_64/pgdg-centos96-9.6-3.noarch.rpm -y"
  02_install_pg_devel:
    command: "sudo yum install postgresql96-devel -y"
```
3. -1  PostgreSQL에서 제공하는 설치파일의 버전 이름이 바껴서 404 페이지 에러발생 ===> <string style="color:red">Fail</string>


생성된 데이터베이스 정보로 settings.py에 입력해줍니다.
https://ap-northeast-2.console.aws.amazon.com/rds/home?region=ap-northeast-2#database

```python
if DEBUG:

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.sqlite3",
            "NAME": os.path.join(BASE_DIR, "db.sqlite3"),
        }
    }
else:

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.postgresql",
            "HOST": os.environ.get("RDS_HOST"),
            "NAME": os.environ.get("RDS_NAME"),
            "USER": os.environ.get("RDS_USER"),
            "PASSWORD": os.environ.get("RDS_PASSWORD"),
            "PORT": "5432",
        }
    }
```

https://ap-northeast-2.console.aws.amazon.com/elasticbeanstalk/home?region=ap-northeast-2#/welcome

1. Elastic Beanstalk -> applocations 클릭
2. 어플리케이션 이름 클릭 -> 환경이름 클릭
3. 좌측 사이드바, 구성 클릭 -> 소프트웨어, 편집 클릭
4. 제일 아래 환경변수 추가
    ``` python
    RDS_HOST="your host" <- RDS에서 생성된 endpoint
    RDS_NAME="your name" <- RDS에서 비밀번호
    RDS_USER="your user" <- RDS에서 비밀번호
    RDS_PASSWORD="your pass" <- RDS만들 때 적은 비밀번호
    ```
![ex_screenshot](/post/images/2020-06-17_100043.png)

작성하고 저장하면, DB가 적용 중이라고 표시가 된다. 다 완료가 되면, requirements 등의 변경사항을 커밋하고 eb deploy를 해준다. deploy가 끝나면 eb logs를 통해 서버의 상태를 살펴볼 수 있는데, 패키지도 설치되었고 여러가지 바빴다는 것이 보인다. 오.
아직 애러가 남았다. ^_^



우선은 아래의 과정처럼 니꼬씨가 하는 것 처럼 EB가 내가 원하는 커맨드를 실행하도록 설정을 해 본다. 그래도 안된다면, 나처럼 직접 ssh 연결을 통해서 커멘드로 설치를 진행할 수도 있다. 나에게는 직접 커멘드로 이것저것 필요한 셋팅을 하는 것이 더욱 편했다.


psycopg2(PostgreSQL)를 설치하는데 많은 오류가 있는 것 같다. 'aws install psycopg2 error'의 검색결과에서 다양한 형태의 설치 에러를 만날 수 있었다. 아래는 검색 과정에서 알게 된 의식흐름,,



4. 답답했던 나는 결국 ssh 행..
위와 같이 시행착오를 겪었는데, 다들 첫 환경 설정이 어려


## packages.config 만들어주기

.ebextensions 폴더 안에 packages.config 파일을 만들어서 리눅스가 가지고 있는 프로그램을 이용할 수 있다.
yum을 이용해서 postgresql96(PostgreSQL 9.6버전)을 적어주었다. 버전은 자신이 만든 RDS의 구성을 확인하여 설치하자

 - .ebextensions
   - packages.config


```
packages:
  yum:
    postgresql96-devel: []
```

https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-2#SecurityGroups:search=sg-c4ac9aa1


하지만 


## aloow host 바꿔주기

``` python
# settings.py
# ALLOWED_HOSTS = []
ALLOWED_HOSTS = [".elasticbeanstalk.com"]
```