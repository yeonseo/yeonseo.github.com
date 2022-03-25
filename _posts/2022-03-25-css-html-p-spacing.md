---
layout: post
title: HTML 텍스트 나타낼 시, p 태그 간격주기
description: ""
tags: [css], FrontEnd, TIL, Today I Leaned]
comments : true
category : [css]
---

Tiptap 에디터로 작성된 컨텐츠를 getHTML로 받아서 저장된 게시물을 표현해보니 `<p>`태그가 빈 값일 때, 줄띄움이 적용되지 않고 있어 수정해봤습니다.

![p-spacing](/post/images/2022-03-25-html-p-spacing-1.png)

<div class="space-item-3"></div>

#


##  Vue 컴포넌트

```Vue
<template>
    ...

    <div class="tiptap-view" v-html="data.detail"></div>

    ...
</template>
```

> v-html : <https://kr.vuejs.org/v2/guide/syntax.html#%EC%9B%90%EC%8B%9C-HTML>

## css 작성

min-height로 최소 높이를 주었더니, 

```css
...

&.tiptap-view {
    p {
        min-height: 1.5rem;
    }
}
...

```

깔꿈하게 성공했네욤 😆 👏👏👏

![p-spacing](/post/images/2022-03-25-html-p-spacing-2.png)

![happy-dev](/post/images/hey/happy02.gif)
