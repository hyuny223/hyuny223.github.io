---
title: "[Github 블로그] toc 설정"
categories: Blog
tag: [Blog, category]
toc: true
toc_sticky: true
toc_label : 목차

 
date: 2022-02-21
last_modified_at: 2022-02-21
---
<br><br>
toc는 목차 메뉴라고 생각하면 된다.<br>
다음과 같은 형식으로 정해주면 된다.<br>
<br>
```html
---

중략

toc: true
toc_sticky: true
toc_label : 목차

후략

---

# 목차

```
"toc: true"는 toc를 실행시켜준다.<br>
"toc_sticky: true"는 toc를 고정시켜주는 역할을 한다.<br>
"toc_label: 목차"는 "on this page"로 고정되어 있는 label을 "목차"로 바꾸어 준다.<br>
"# 목차"를 사용하면 목차를 사용할 수 있다.<br>
<br>

# 마지막!