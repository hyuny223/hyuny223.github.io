---
title: "[Linux Day4] Linux network system"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:02
last_modified_at: 2022-02-24
---
<br>
<br>

# 1. 네트워크 용어
---
## 1. Terminology : network system
* Network 설정에 필요한 기초 용어
	- Linux에 한정된 용어가 아니라 국제 표준 문서에서 통용되는 용어
		+ hotname : primary hostname, FQDN
		+ TCP/IP : IP address(IPv4, IPv6), subnet mask, gateway
		+ NIC : Network Interface Card == 랜카드
		+ Wired Network(Wired connection) : 유선 네트워크(유선연결)
		+ Wireless Network (Wireless connection) : 무선 네트워크(무선 연결)
		+ LAN : Local Area Network
		+ WAN : Wide Area Network

## 2. 네트워크 이름 : hostname
* 사람의 이름과 컴퓨터의 이름 비교
    - 사람의 이름 : Steven Jobs
    - 컴퓨터의 이름 : access.redhat.com
* 호스트이름(hostname)
    - 사람의 이름(given name + first name)에 해당하는 것
    - 때에 따라 given name에 한정되는 의미로 말하기도 함
* 도메인주소(domain address)
    - 사람의 성(family name)에 해당하는 것
    - e.g.) Jobs = redhat.com

## 3. FQDN, hostname
* hostname에는 중의적 의미가 있다.
    - 1) domain을 제외한 호스트 이름(Steven)
    - 2) domain을 포함한 FQDN(Steven Jobs)
* FQDN : Fully Qualifed Domain Name
    - DNS 서버 → FQND 설정
    - fedora.redhat.com = hostname = FQDN
    - 도메인 주소는 체계적인 구조를 지님(e.g. devel.fclinux.or.kr)
    	+ kr : 한국의 주소(korea)
    	+ or : 단체(organization)
    	+ fclinux : 단체의 이름
    	+ devel : 단체내에서 유일한 컴퓨터 이름 
* special hostname
    - localhost
    	+ 항상 자기 자신을 의미하는 주소와 매핑된다.
    		* IPv4 = 127.0.0.1
    		* IPv6 = ::1
## 4. IP address
* IPv4
    - version 4
    - 32bit 주소 체계(2^32) : 8bit씩 끊어서 xxx.ooo.xxx.ooo의 식으로 읽음
* IPv6
    - version 6
    - 128bit 주소 체계(2^128)

## 5. IPv6 address
* 128bit address
    - IPv4-mapped IPv6
    	+ ::ffff:IPv4_addr
    	+ e.g. 58.232.1.100 = ::ffff:58.232.1.100
    - Link-local Unicast
    	+ FE80::/10
    - Documentation IPv6 Address Prefix
    	+ 2001:DB8::/32
    	+ http://tools.ietf.org/html/rfc3849
## 6. dual stack
* IPv4 + IPv6
* 지원되는 OS
    - 대부분의 Modern OS는 dual stack을 지원한다
    - 하지만 window XP는 제외
* 과도기에 자주 겪는 문제
    - IPv4, IPv6 dual stack system
    	+ IPv4용으로 짜여진 프로그램과 IPv4, IPv6 가능 프로그램의 혼용
    		* Server:IPv6 vs Client:IPv4인 경우
    		* e.g.) 리스너 소켓이 IPv6에 붙어버린 경우
    	+ 방화벽 문제
    		* IPv6, IPv4용 방화벽은 따로 관리된다.
    		* IPv6로 통신하는데 IPv4용 방화벽만 열어준 경우 : 통신이 실패할 수 있다.
    		* IPv4-mapped-IPv6 주소는 IPv6 스택을 경유하는가?

## 7. IP address (con't)
* IPv4의 클래스 (지금은 쓰이지 않는 구식 기법)
    - A,B,C,D,E 클래스 : D,E는 특수한 목적
    - A,B,C 클래스 범위

       |클래스|상위 8bit 범위(2진수)|상위 8bit 범위(10진수)|
       |---|---|---|
       |A|00000000~01111111|0~127(127은 로컬루프백)|
       |B|10000000~10111111|128~191|
       |C|11000000~11011111|192~223|

    - 127.0.0.1과 같이 특수하게 예약된 주소에 대한 문서
    	+ RFC-3330 Special use IPv4 addresses
* A,B,C 클래스 별 Net ID/Host ID
    - Host ID의 bit 자릿수가 하나의 네트워크 내에 묶을 수 있는 호스트 개수
    - C classt는 8bit의 Host ID를 가지므로, 2^8 = 256개의 호스트가 동일 네트워크에 묶을 수 있음!

* CIDR (Classless Inter-Domain Routing)
    - IP 클래스와 상관없이 subnet을 지정하여 자르는 것을 의미
    	+ xxx.ooo.xxx.ooo/##으로 표기
    	+ ##에는 서브넷 마스크의 on 비트의 갯수를 표기

    	|예1|192.168.100.0/24|192.168.100.0/255.255.255.0|
    	|예2|192.168.100.0/25|192.168.100.0/255.255.255.128| 
    	|예3|192.168.100.128/26|192.168.100.0/255.255.255.192|

    - 1993년도 이후로는 CIDR에 의해 IP class개념은 대체되었다.
    	+ RFC-1518, RFC-1519

* public IP / private IP
    - public IP : 공인 주소(인터넷에서 유일한 주소)
    - private IP : 사설 주소(인터넷에 직접 연결되지 않은 유일하지 않은 주소)
* NIC
    - Network Interface Card = LAN 카드

# 2. SELinux와 네트워크 보안
---
## 1. SELinux와 네트워크 서비스
* Security Enhanced Linux
    - 커널 레벨에서의 중앙 집중식 보안 기능
    	+ 대부분의 리눅스는 default 설치, Ubuntu는 설치되지 않음
* SELinux의 보안레벨
    - enforcing(강제) : SELinux를 이용. 보안설정에 걸리면 강제로 막는다
    - permissive(허가) : SELinux를 이용. 보안설정에 걸려도 허용하되, 로그 출력
    - disabled(비활성) : SELinux를 사용하지 않음
* 서버 구성시에는 SELinux에 대한 이해가 필요
    - 당장 실습할 때는 enforcing에서는 안 되는 것이 너무 많음
    - 따라서 실습할 때는 일단 permissive레벨로 내리는 것이 좋다.



