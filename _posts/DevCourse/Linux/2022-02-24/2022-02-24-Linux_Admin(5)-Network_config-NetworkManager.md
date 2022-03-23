---
title: "[Linux Day4] Linux_Admin #5 - Network Configuration: NetworkManager"
categories: dev_Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:04
last_modified_at: 2022-02-24
---
<br>
<br>

# 1. NetworkManager 설정
---
1. NetworkManager
	* NetworkManager의 장점
		- NetworkManager는 daemon으로 작동하면서 network configuration을 수행하고, 자동으로 network connection을 관리(연결 감지, 해제, 재시도)를 수행한다.
		- Dbus기반으로 동적 상태를 감지할 수 있기 때문에 다른 애플리케이션이나 daemon들에게 네트워크 정보를 제공하거나, 관리를 위한 권한을 줄 수도 있다.
		- 통일된 명령어를 사용하여 systemd 기반의 다른 Linux distributions에게도 동일한 방식의 경험을 제공
		- Ethernet, Wi-Fi, Mobile broadband 등 다양한 기능들에게 플랫폼을 제공하므로, 네트워크 연결 관리가 좀 더 쉬워진다.

2. legacy: ifconfig, route, ip, nmcli
	* UNIX standard command (POSIX)
		- ifconfig
			+ interface config. : query/control
		- route
			+ routing table : query/control
	* Non-standard command (Linux specific)
		- ip : net. commands on EL6
		- nmcli : new commands on EL7 (NetworkManager CLI)
		- ethtool, pifconfig & pethtool(python-ethtool)
		<br>

# 2. nmcli
---
1. nmcli
	* nmcli : Network Manager CLI tool
		- 네트워크에 관련된 대부분의 기능을 갖고 있다
		- 조회 및 설정 가능
	* nmcli g[eneral]
		- STATE : connected / asleep
		- CONNECTIVITY : full / none

2. nmcli : networking
	* networking 상태 조회
		- nmcli n
	* nmcli n[etworking]
		- nmcli n on
		- nmcli n off
		- 간혹 networking이 off로 되어 있어서 설정이 안 되는 경우도 있다. 따라서 항상 networking 상태부터 확인

3. NIC : nmcli
	* net. device 확인
		- nmcli (Command-Line-tool for NetworkManager)
		```
		# nmcli dev
		```
4. net. device naming
	* eth#[:n]
		- eth0, eth1, eth0:0, eth0:1
			+ old style (EL6 이전에 주로 쓰이던 명령어)
		- 보드에 네트워크 카드를 꽂을 때, 슬롯이 2개 이상인 경우, 카드를 살펴볼 필요가 있을 때 무엇이 eth0, eth1인지 모르는 경우가 발생 → 이를 위해 nmcli탄생
	* Consistent Network Device Naming(new naming)
		- convection for naming Ethernet adapters in Linux
		- Stable, predictable, reliable naming of all network interfaces by default, with on-board tools

5. Consisten Network Device Naming
	* prefix
		- en : ethernet
		- wl : wireless lan
		- ww : wireless wan
	* following device name
	
	|o<index>|on-board device index number|
	|s<slot>[f<function>][d<dev_id>]|hotplug slot index nuber|
	|x<MAC>|MAC address|
	|p<bus>s<slot>[f<fucntion>][d<dev_id>]|PCI geographical location|
	|p<bus>s<slot>[f<fucntion>][d<dev_id>][..][c<config>][i<interface>]|USB port number chain|

6. Consistent Net. Dev. Naming : BIOSDEVNAME
	* new naming(BIOSDEVNAME) convection vs old name
		- package name : biosdevname
			+ debian, RH

			|Device|Old name(x)|New name|
			|Embedded network interface|eht[0123...]|em[1234....]|
			|PCI card network interface|eth[0123...]|p<slot>p<ethernet port>|
			|Virtual function|eth[0123...]|p<slot>p<ethernet port>_<virtual interface>|

7. NIC : nmcli : command
	* nmcli <g \| n \| r \| c \| d>
		- general, networking, radio, connection, device
		- help가 제공된다
		```
		# nmcli g
		# nmcli n
		# nmcli r
		# nmcli c
		```
8. nmcli : con : show
	- nmcli c[onnection] s[how]
	```
	# nmcli connection show
	# nmcli c s
	```
	- nmcli c[onnection] s[how] <connection name>
	```
	# nmcli c s ens33 // 네트워크 이름
	```
	- ipv[46].* : 설정된 값 (offline + online)
	- IP[46].* : 할당된 값 (online)
		+ 외부에서 다른 툴로 설정 → 설정된 값과 할당된 값이 다를 수 있다.
		+ 혹은 설정을 바꿈 & not reload → 예전 값이 들어가 있을 수 있음
		+ 결국 설정된 값과 할당된 값이 다를 수 있다.
		+ IP4.* → 복수 개 설정 가능하다는 의미
9. nmcli : con : property
	* 주요 속성
		- ipv4.method → ip주소를 할당받을 때 어떠한 방법을 쓸 것인가
			+ auto \| manual
			+ auto = dhcp
			+ manual = static ip
		- ipv4.addr
			+ IPv4 address CIDR 표기법 = 192.168.110.50/24
		- ipv4.gateway
			+ Gateway IP
		- ipv4.dns
			+ DNS server IP
	* optional prefix of property
		- '+'
			+ appending items instread of overwriting the whole value
		- '-'
			+ removing selected items instead of the whole value
		- none
			+ replace the whole value
10. Practice : nmcli : con : down/up
	* nmcli로 connection을 down시키고 show로 확인
	```
	# nmcli d
	# nmcli con down "유선 연결 1"
	# nmcli c s "유선 연결 1"  // IP4.* 부분 출력X
	# nmcli con up "유선 연결 1"
	# nmcli c s "유선 연결 1"  // IP4.* 부분 출력O
	```
11. Practice : nmcli : con : modify
	* nmcli 속성을 변경
		- NIC의 id가 한글인 "유선 연결 1"로 되어있는 경우가 존재
			+ CONNECTION ID를 device name과 통일시켜 보자.
			```
			# nmcli d
			# nmcli con modify "유선 연결 1" connection.id ens33
			# nmcli c
			```
	* IP 주소를 변경
		- IP 변경 전에 현재 시스템의 IP부터 메모해두자.
		```
		# nmcli c s
		# nmcli c s ens33
		// auto = DHCP
		// DHCP 서버에서 받은 주소 중 IP, Gateway만 기억하면 된다
		```
	* 앞의 nmcli : con : modify(IP)
	```
	# nmcli c mod ens33 ipv4.method manual ipv4.addr(ess) 192.198.xxx.110/24\
	  ip4.gateway 192.168.xxx.2 +ipv4.dns 8.8.8.8  // xxx는 자신의 IP 번호를 작성해준다
	# nmcli c s ens33  // IP4의 값을 확인해보자
	# nmcli c down ens33 && nmcli c ip ens33  // routing table 변경 없이 IP변경 정도를 반영하는 경우는 up만 다시 하면 된다.
	```
	* virtual IP 추가
	```
	# nmcli c mod ens33 +ipv4.addresses 192.168.110.181/24
	# nmcli c mod ens33 +ipv4.addresses 192.168.110.182/24
	# nmcli c up ens33
	# nmcli c s ens33
	>> 예전 버전에서는 ip 명령어로도 virtual IP를 설정할 수 있었다.
	# ip address add 192.168.110.181/24 dev eth0
	# ip address add 192.168.110.182/24 dev eht0
	```

	* virtual IP 삭제
	```
	# nmcli c mod ens33 -ipv4.addresses 192.168.110.181/24
	# nmcli c mod ens33 -ipv4.addresses 192.168.110.182/24
	# nmcli c s ens33
	```
12. Practice : nmcli : con : del / add
	* 기존의 설정을 삭제했다가 새로 만들어보자
	```
	# nmcli c del ens33
	# nmcli c s
	# nmcli d s
	# nmcli c add con-name ens33 ifname ens33 type ethernet ip4 192.168.110.161/24
	# nmcli c s
	```
		- ens33에 dns 서버를 추가하고 싶다면?
	* DNS 추가
	```
	# nmcli c mod ens33 +ip4.dns 8.8.8.8
	# nmcli c s ens33 | egrep '(ipv4.addr|ipv4.gateway|ipv4.dns)'
	```
		- +ipv4.dns 대신에 ipv4.dns로 쓰면 뭐가 다를까?
13. nmcli : dev
	* device 자체가 올라오지 않는 경우에는?
		- nmcli dev connect <device name>
			+ 장치가 자동으로 생성된다
			```
			# nmcli dev s // STATUS disconnected
			# nmcli dev connect ens33
			# nmcli con m ens33 ... 생략 ...
			```
	* device connect가 실패하는 경우?
		- nmcli c화면에서 DEVICE란이 --로 비어있다
			+ nmcli networking을 검사해서 off인 경우라면 on을 해주자
			```
			# nmcli c
			# nmcli g // STATE asleep
			# nmcli device connect ens33
			# nmcli networking
			disabled
			# nmcli networking on
			# nmcli g
			```
			+ 이렇게 해도 안 되는 경우라면, lshw, lspci, lsusb, dmesg등으로 하드웨어가 인식되는 상태인지 검사필요
