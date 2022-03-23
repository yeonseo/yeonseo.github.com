---
layout: post
title: title
description: ""
tags: [Sptring Boot, Spring, Kotlin, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Sptring, Spring Data JPA]
---


# 키체인 발급

[Github Personal Access Token 토큰 발급, 키체인 설정]

<https://hyeon9mak.github.io/github-personal-access-token/>

<br/>
<br/>
<br/>

# source Tree

1. 소스트리 상단 오른편에 '설정'에 진입
2. 원격 > origin 클릭
3. 편집 클릭해서 편집화면 진입

![source-tree-url-change](/post/images/2022-03-22-git-url-change3.png)

1. 변경하려는 URL을 입력한다.
2. 확인 클릭

![source-tree-url-change](/post/images/2022-03-22-git-url-change4.png)

<br class="space-item-3"/>

# Fork 에서 Git origin url 변경

사실 제대로 방법을 찾지 못해서, 아래의 커멘드로 변경하는 방법을 이용해 변경시도를 했다.

<br class="space-item-3"/>

# Git Bash로 Git remote U 바꾸기

```Console
// 현재 설정 확인
$ git config --list

// 새로운 URL로 변경
$ git remote set-url origin https://yeonseo:변경변경test입니다@github.com/블라블라_변경된/깃.git

```

변경변경test입니다 -> 아까 git에서 발급받은 Key 값을 넣는다.

/블라블라_변경된/깃 -> 저장소위치 넣는다.

<br class="space-item-3"/>

![source-tree-url-change](/post/images/2022-03-22-git-url-change1.png)

<br class="space-item-3"/>

▽▽▽▽ 변경된 URL 확인 ▽▽▽▽

![source-tree-url-change](/post/images/2022-03-22-git-url-change2.png)
