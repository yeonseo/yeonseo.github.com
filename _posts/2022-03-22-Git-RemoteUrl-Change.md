---
layout: post
title: Git origin url 변경하기 (소스트리, Fork, Git Bash)
description: ""
tags: [Git, TIL, Today I Leaned]
comments : true
category : [Git]
---

깃을 사용하다가, 키체인이 만료되었을 경우 이를 암호키 설정을 바꿔줘야하는데 3대 이상의 장비로 자주 작업을 바꾸다보니 이 과정도 꽤나 피로도가 쌓이는 작업이 되버렸습니다. 그래서 Git origin URL을 변경하는 방법으로 정착했는데 꽤 편리하더라구요.

<em>분명 나는 키체인 발급할 때 무기한으로 했는데 왜 자꾸 만료되는가?</em>

<div class="space-item-6"></div>


# 준비물

Git URL을 사용하기 위해서는 다음과 같이 준비물이 필요합니다.

1. Git 계정
2. Git Personal access tokens

위의 재료들을 아래와 같이 조합해서 사용합니다.

```Console
╔════════════════════════════════════════════════════════════╗
╠═ ${git_id}        : Git id                                ═╣
╠═ ${access_tokens} : Git Personal access tokens            ═╣
╠═ ${git_repository_dir}/${git_repository}.git : source     ═╣
╚════════════════════════════════════════════════════════════╝

https://${git_id}:${access_tokens}@github.com/${git_repository_dir}/${git_repository}.git

// 예시
https://yeonseo:Test1234!@#$Test1234!@#$@github.com/yeonseo/test_git_url.git
```

<div class="space-item-6"></div>

# Git Personal access tokens 발급

[Github Personal Access Token 토큰 발급, 키체인 설정]

<https://hyeon9mak.github.io/github-personal-access-token/>

<div class="space-item-3"></div>

# Source Tree에서 Git origin url 변경

1. 소스트리 상단 오른편에 '설정'에 진입
2. 원격 > origin 클릭
3. 편집 클릭해서 편집화면 진입

![source-tree-url-change](/post/images/2022-03-22-git-url-change3.png)

1. 변경하려는 URL을 입력한다.
2. 확인 클릭

![source-tree-url-change](/post/images/2022-03-22-git-url-change4.png)

<div class="space-item-3"></div>

# Fork 에서 Git origin url 변경

사실 제대로 방법을 찾지 못해서, 아래의 커멘드로 변경하는 방법을 이용해 변경시도를 했다.

<div class="space-item-3"></div>

# Git Bash로 Git remote Url 바꾸기

```Console
// 현재 설정 확인
$ git config --list

// 새로운 URL로 변경
$ git remote set-url origin https://yeonseo:변경변경test입니다@github.com/블라블라_변경된/깃.git

```

변경변경test입니다 -> 아까 git에서 발급받은 Key 값을 넣는다.

/블라블라_변경된/깃 -> 저장소위치 넣는다.

<div class="space-item-3"></div>

![source-tree-url-change](/post/images/2022-03-22-git-url-change1.png)

<div class="space-item-3"></div>

▽▽▽▽ 변경된 URL 확인 ▽▽▽▽

![source-tree-url-change](/post/images/2022-03-22-git-url-change2.png)


<div class="space-item-6"></div>

이렇게 설정을 해두면, Personal access token이 만료될 때까지 유효합니다. 만료되면 또 이 과정을 반복해야하지만, MacOS와 Window를 동시에 사용하고 있어서 그때마다 헷갈리는 과정을 블로그 찾아가며 적용하는게 너무 번거롭더라구요. 간략하게 URL만 바꿔 적용하면 되니 편리편리 :-)


![happy-dev](/post/images/hey/happy01.gif)

<strike>자꾸 만료되지 말구♪ 이제 제발 그만 변경하자♬</strike>