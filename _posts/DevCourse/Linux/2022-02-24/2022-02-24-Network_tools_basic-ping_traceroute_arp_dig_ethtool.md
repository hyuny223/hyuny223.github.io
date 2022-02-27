---
title: "[Linux Day4] Package Manager"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:06
last_modified_at: 2022-02-24
---
<br>
<br>

L3까지는 IP주소로 오지만 L2로 오기 위해서 IP주소로 통신하는 것이 아니다. MAC address로 통신하기에,MAC adrress로 변환해주는 매칭테이블이 필요
(매칭테이블 → MAC address로 통신(in L2)

 resolver : IP address나 호스트네임을 해석해줌

 이를 이용하여 특정 서버의 이름을 질의(?)할 때 사용하는 명령어
 → nslookup(deprecated), dig → 뒤에다 호스트 주소명

 network 카드에 대해서 질의하거나 설정하는 ethtool

 외부에서 프로그램을 설치하여 사용하다가 강제로 speed와 duplex를 바꾸는 경우가 존재 → ethtool -s 주소 speed 1000 duplex full
