---
title: "[Linux Day4] Ubuntu Network config - legacy"
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

# 1. Debian 계열 network configuration
---
1. Debian net. config : Legacy
	* Network 설정은 Legacy방식과 NetworkManager가 있다.
		- Debian/RH계열은 서로 설정 방식과 config파일 포맷이 다르다
			+ 현재는 NetworkManager로 통일
		- Debian legacy config : /etc/network/interface
		- RH legacy config : /etc/sysconfig/network-scripts/*
		<br>

2. Debian 계열 : legacy net. conf.
	* 혹시 legacy 설정을 사용하는 커스텀 리눅스를 사용하는 경우
	* legacy를 쓰면 안되는데, 인터넷 검색을 잘못하여 legacy 방식으로 강제 설정한 경우 → 각종 오류의 원인
	<br>

3. NIC : /etc/network/interfaces
	* Debian network interface config
		- eth0을 사용하는 것은 전부 옛날 버전
	```
	auto lo
	iface lo inet loopback
	...
	auto eth0
	iface eth0 inet dhcp
	```
	* RH계열 legacy
		- /etc/sysconfig/network-script/ifcfg-*
	* NetworkManager로 현재는 통일
	<br>

4. NIC : /etc/network/interfaces
	* iface <NIC> inet <static \| dhcp \| bootp \| tunnel \| ppp \| loopback>
	* address <IP address>
		- dotted quad
	* netmask <network mask>
		- dotted quad
	* gateway <IP address>
	* dns-nameservers <IP address>
	* dns-search <domain>
	* hwaddress <MAC address>
	* metric <metric value>
	<br>

5. restart networking : init
	```
	# /etc/init.d/networking
	Usage: /etc/init.d/networking {start|stop|reload|restart|force-reload}
	# /etc/init.d/networking restart
	[....] Running /etc/init.d/networking restart is deprecated because it may not re-enable some interfaces ... (warning).
	[ ok ] Reconfiguring network interfaces...done.
	```
	* systemd로 대체
	* /etc/init.d/networking == service networking
	<br>

6. restart networking : ifup, ifdown
	* ifup, ifdown을 사용해도
	```
	# ifdown -a && ifup -a
	```
	* /etc/init.d/networking의 내부는 ifdown, ifup을 사용
	* /etc/init.d/networking script는 구식
		- upstart init system을 사용하는 시스템에서 문제가 있다.
	* 차후 systemd로 통합되면 systemctl 명령어가 사용될 것이다.
	<br>

7. /etc/default/networking
	* Networking default config
	```
	#CONFIGURE_INTERFACES=yes
	#EXCLUDE_INTERFACES=
	#VERBOSE=no
	```
		- /etc/init.d/networking 스크립트에서 사용하는 환경설정
		- CONFIGURE_INTERFACES=no이면 부팅시 자동 활성화 되지 않음
		- VERBOSE=yes로 하면 자세한 출력을 보여준다.

8. 지금까지는 legacy. 사용하지 않을 것!!
