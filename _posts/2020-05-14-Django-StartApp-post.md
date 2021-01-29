---
title: Python & Django App 생성 및 설계하기
author: Yeonseo Nam
date: 2020-05-14 19:34:00 +0900
categories: [Django, Tutorial]
tags: [Python, Django, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Django]
---


본 포스팅은 https://academy.nomadcoders.co/[Nomad Coders] 의 "[풀스택] 에어비엔비 클론 코딩" 을 기초로 하여 개인 프로젝트를 진행한 과정을 담고 있습니다. 더 자세한 설명이 궁금하신 분은 노마드 코더의 강의를 들어보세요! 많은 도움을 받으실겁니다 :-)


<br/><br/><br/><br/><br/>

이전의 포스팅에서 사용자가 가입할 수 있고 게시판이 있는 사이트를 만들어본다고 했습니다. 그 구조에 대해서 생각을 해보겠습니다.


---

* Development Env.
* post date : 2020. 05. 14
* OS : macOS Catalina 64bit, Windows 10 64bit
* Python : 3.8
* Django : 2.2.5
* Tool : PyCharm 2018.3.5 (Community Edition)
* JRE : 1.8.0_152-release-1343-b28 amd64
* JVM : OpenJDK 64-Bit Server VM by 

---

<br/><br/><br/><br/><br/>

 'users' App과 'boards' App이 필요했고, 다음 명령어를 통해서 같이 추가했습니다.


``` commend
> django-admin startapp users
> django-admin startapp boards
```

## 조금더 구조 생각해보기 (App 확장하기)

하지만 앞으로 우리가 만들어갈 사이트들은 조금 더 복찹한 구조를 가지고 있고, 비슷한 성격인 듯 다른 모습을 한 App들이 많을 겁니다. 매번 App을 만들고 어떻게 기능하는지를 비슷한 작업을 반복하는 일은 피로도를 높이기만 하죠..

그래서 구조를 좀 더 생각해보아야 합니다.

추가로 App을 구상하는데 도움이 되었던 것이 있습니다. 혹시나 저처럼 첫 시작하시는 분들에게 도움이 될꺼같아서 함께 알아보도록 하겠습니다. 

공지사항과 자유게시판이 필요해서 App을 각각 만들어야 하는가 생각을 했었는데, 추상클래스를 활용해서 설계하는 방법을 배웠습니다. 하나의 App에 필요한 타입이나 내용을 추가할 수 있도록 말이죠!



이해를 돕기 위해서 공지사항과 자유게시판이 가질 정보를 간단히 나타내서 생각해 보겠습니다.

* 공지사항 게시판 - <span style="color: darkblue">작성자, 작성일, 제목, 내용, 첨부파일, 수정, 삭제</span> 긴급공지, 정보성공지
* 자유 게시판 - <span style="color: darkblue">작성자, 작성일, 제목, 내용, 첨부파일, 수정, 삭제</span>, 댓글

이처럼 두개의 게시판 모두 <span style="color: darkblue">푸른색</span>으로 쓰여진 공통의 기능이 있습니다. 공통으로 가지는 기능을 묶어서 board라는 App을 만들고 곂치지 않는 나머지 부가기능은 추상클래스로 구현합니다.

위의 게시판을 다시 나타내보겠습니다. :-)

- board : 작성자, 작성일, 제목, 내용, 첨부파일, 수정, 삭제

- board_type : 공지사항, 자유 게시판 , ...

- board_noti : 긴급공지, 정보성공지, ...

- board_reple : 댓글 기능 App (댓글 기능은 너무 커지니까 따로 App으로 구현한 다음 연결시켜주는 것이 좋겠네요~)


``` python
class AbstractItem(models.Model):

    """ Abstract Item """

    name = models.CharField(max_length=80)

    class Meta:
        abstract = True // migrate 작업 시, DB에 반영 안 하기 위한 옵션

    def __str__(self):
        return self.name


class BoardType(AbstractItem):

    """ BoardType Model Definition """

    class Meta:
        verbose_name = "게시물 종류"


class BoardNoti(AbstractItem):

    """ BoardNotification Model Definition """

    class Meta:
        verbose_name_plural = "공지 분류"


class Photo(models.Model):

    """ Photo Model Definition """

    caption = models.CharField(max_length=80)
    file = models.ImageField(upload_to="room_photos")
    room = models.ForeignKey("Room", related_name="photos", on_delete=models.CASCADE)
    user = models.ForeignKey("users.User", related_name="users", on_delete=models.CASCADE)

    def __str__(self):
        return self.caption


class Board(models.Model):

    """ Board Model Definition """
    user = models.ForeignKey(
        "users.User", related_name="boards", on_delete=models.CASCADE
    )
    title = models.CharField(max_length=140)
    content = models.TextField()
    
    ...

    board_type = models.ForeignKey(
        "BoardType", related_name="boards", on_delete=models.SET_NULL, null=True
    )
    board_noti = models.ForeignKey(
        "BoardNoti", related_name="boards", on_delete=models.SET_NULL, null=True)

    def __str__(self):
        return self.title
```

다음엔 App의 medel 작성, admin 화면에서 보기를 해보겠습니다.



