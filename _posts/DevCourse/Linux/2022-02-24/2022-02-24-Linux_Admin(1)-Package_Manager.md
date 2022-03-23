---
title: "[Linux Day4] Linux_Admin #1 - Package Manager"
categories: dev_Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:00
last_modified_at: 2022-02-24
---
<br>
<br>

# 1. Package system 종류
---
## 1. Linux Package System
* RedHat Package System
  - rpm database
  - yum
  - dnf
* Debian Package System
  - dpkg, dselect
  - apt-get, apt-cache
  - aptitude
  - apt

# 2. Package란?
---
## 1. Package
* 시스템을 구성하는 파일의 묶음
    - 패키지 관리자(or 설치 프로그램)에 의해서 읽혀지는 pre-built 파일들
    	+ 과거에는 소스코드를 다운로드하여 빌드하면서 설치하는 경우도 있었다
    - 관리(설치, 삭제, 업그레이드, 질의)의 편리함을 제공
* 리눅스 패키지 방식
    - RPM : 레드햇 계열(레드햇,페도라,센토스 ...)
    - DEB : 데비안 계열(데비안, 우분투, 민트 ...)
## 2. Linux Package Manager
* Debian 계열 주요 명령어
    - dpkg(debian package manager) - 기본명령
    - apt(advanced package tools) - 네트워크, 의존성 설치 지원툴
* RedHat 계열 주요 명령어
    - rpm(Red Hat Package Manager) - 기본명령
    - yum(yellow-dog updator modifier) - 네트워크, 의존성 설치 지원툴
    - dnf
* Debian계열은 apt, RH계열은 yum
    - yum은 차세대 버전에서 dnf로 변경되나 내부만 변경, 명령어는 유지
* dpkg - debian package manager
* apt - advanced package tool
* aptitude - Text GUI (ncurse based) tool
* dselect - front-end to dpkg
* synaptic - GUI(X window)
* tasksel
* 모든 것들이 다 사용되진 않는다.
    - 2020년 기준으로 보면 대부분 apt, aptitude를 주로 사용
    	+ 간혹 문제가 생긴 경우, 관리 목적으로 dpkg를 사용하는 경우도 존재

## 3. dpkg
* dpkg는 초기 debian에서부터 유래
    - dpkg, dselect가 초기에 사용됨
    	+ dependancy를 제댈 해결하기 어려움
    	+ 검색이 어려움
    	+ 네트워크 설치를 제대로 지원 못 함
    -상기 문제점으로 인해 APT가 등장
    - 따라서 현재는 dpkg의 모든 기능을 배울 필요는 없다.
    	+ 약간의 유용한 기능만 알고 있으면 된다.
* Debian Package manager : UNIX의 pkg에서 유래
    - extension - deb

	```
	strace_4.5.20_2.3_amd64.deb
	// package name / version & release / arch.
	```

* dpkg : query : list
    - strace, gcc 패키지 리스트 확인
	![Screenshot from 2022-02-24 09-11-19](https://user-images.githubusercontent.com/58837749/155473963-dd36fca1-b5f8-449b-8570-67781e389b13.png)

	![Screenshot from 2022-02-24 09-11-44](https://user-images.githubusercontent.com/58837749/155474080-dfa073ce-d21c-45f5-aa75-38c63e2e95dd.png)

* dpkg : query : search
```
dpkg -S <pattern ...>
```

    - -S : (대문자) 패키지 검색 : 파일명
	```
	dpkg -S /bin/ls
	```
	![Screenshot from 2022-02-24 09-13-13](https://user-images.githubusercontent.com/58837749/155474510-9f553562-fc1f-47f1-9e09-eb720fc8bec9.png)

* Practice : dpkg : unpack & configure (3)
    - audit
	```
	# dpkg --audit
	```
