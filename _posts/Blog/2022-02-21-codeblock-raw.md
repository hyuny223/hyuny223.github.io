---
title: "[Github 블로그] 코드블럭 {% raw %}"
categories: Blog
tag: [Blog]

toc: true
toc_sticky: true
toc_label : 목차
 
date: 2022-02-16
last_modified_at: 2022-02-21
---
<br>
<br>
# 문제점
---
글을 올리면서 한 가지 난관이 생겼다.<br>
코드블럭을 이용하여 html,liquid, markdown이 섞여 있는 소스를 업로드 하려 하였다.<br>
다음과 같은 방식으로 올리니, 실제 페이지가 렌더링 되는 것이었다.<br>


{% highlight html %}
{% raw %}
```
{내용}
```
{% endraw %}
{% endhighlight %}
<br>
이를 해결하기 위해서는 다음과 같은 방법이 필요했다.<br>
<br>
# 해결방법
---
이를 해결하기 위해서는<br>
{}괄호 안에 % raw %를 선언해주고<br>
마지막에 {}괄호 안에 % endraw %를 선언해주면 된다.<br>
이름처럼 코드를 날것의 상태로 나타내주는 역할인 것 같다.<br>
<br>
# 번외
---
추가적으로, 코드블럭 안에 코드블럭을 나타내고 싶으면
`를 세번 사용하는 대신
{} 안에 % highlight html %를 선언해주고<br>
`를 사용하면 나타내줄 수 있다.<br>
종료시에는<br>
{} 안에 % endhighlight html %를 선언해준다.<br>