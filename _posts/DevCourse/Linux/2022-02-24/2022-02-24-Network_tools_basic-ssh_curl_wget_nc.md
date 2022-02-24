---
title: "[Linux Day4] Package Manager"
categories: Linux
tag: [Linu]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24
last_modified_at: 2022-02-24
---
<br>
<br>
ssh 주소 : 접속한 유저명이 앞에 생략
ssh username@주소 : username으로 접속
ssh - p 20022 주소 : 다른 포트로 접속

ssh-keygen -N "" : ""는 passphrase(?)를 안 쓰겠다. 공인인증서를 사용할 때 계속 비밀번호를 요구하는 것과 같은 역할

ssh -t username@주소 w : 주소에서 이루어진 결과물을 나한테 알려줘라(다른 주소임). 예를 들어 지금 주소가 1이면, 1번 주소에서 적혀진 주소인 2번 주소에 접속해서 명령을 내리고 다시 1번 주소에 결과값을 알려달라는 것
