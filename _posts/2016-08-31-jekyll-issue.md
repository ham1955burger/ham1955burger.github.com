---
layout: post
title:  "Jekyll 현재 날짜와 시간으로 Post안되는 현상"
date:   2016-08-31 10:22:00 +09
categories: Study
---
<h4> 문제 </h4>
*[2016-08-29자 jekyll post](https://ham1955burger.github.io/study/2016/08/29/jekyll.html) 참고*

    <!-- 2016-08-28-ham-1955-burger-first-post.md -->

    ---
    layout:  post
    title:  "ham 1955 burger first post!"
    date:   2016-08-28 13:00:00
    categories: Study
    ---

    FIRST POST!

현재시간이 `2016-08-28 13:00:00` 이라고 가정하고
상단과 같이 .md파일 작성 후 게시글을 보려할때,
.md파일이 분명히 존재함에도 불구하고 게시글이 보여지지 않는 현상이 있었다.

---

<h4> 해결 </h4>

원인은 template에 date항목 때문이었는데,
date가 **UTC 표준시**로 설정되어 있기 때문에, 한국시간으로 바꿔줘야 하므로 **+09**을 붙여주어 수정하니 문제가 없었다.

    date:   2016-08-28 13:00:00 +09

---

<h4>참고</h4>

[한국 표준시 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/한국_표준시)
