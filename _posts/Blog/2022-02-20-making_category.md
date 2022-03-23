---
title: "[Github 블로그] Category의 post layout"
categories: Blog
tag: [Blog]
toc: true
toc_sticky: true
toc_label : 목차

 
date: 2022-02-20
last_modified_at: 2022-02-20
---
<br><br>


기본적으로 블로그를 만드는 방법은 다음의 링크에서 참고하였다.<br>
https://ansohxxn.github.io/blog/category/<br>
정말 감사하다는 인사를 드리고 싶다.<br>
<br>
그런데 문제점이 발생하였다.<br>
우측 상단의 Category를 누르면, 사이드바에서 카테고리를 누르는 것과는 다른 방식이 나타났다.<br>
<br>
이를 해결하기 위하여 이것저것 만져보았다.<br>
<br>
_layouts 폴더에 있는 categories.html을 바꿔주면 되는 것이다.<br>
<br>
이 중에서 맨 아래 부분이 categories의 게시글 post 형식을 정해주는 것이다.<br>
지워보면 그 부분만 사라지는 것을 알 수 있다.<br>
이 중, 중간에 있는 archive-single.html을 archive-single2.html로 바꿔준다.<br>
(이 html 파일은 위에서 언급한 링크에서 생성한 것이다)<br>
