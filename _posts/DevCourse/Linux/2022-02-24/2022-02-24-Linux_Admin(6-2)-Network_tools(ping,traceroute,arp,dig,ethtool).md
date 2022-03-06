---
title: "[Linux Day4] Linux_Admin #6-2 -  Network tools(ping,traeroute,arp,dig,ethtool)"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:06
last_modified_at: 2022-03-06
---
<br>
<br>

# 1. querying
---
## 1. ping
* 상대 호스트의 응답 확인 (호스트 존재여부 확인인
    ```bash
    $ ping [-c count] [-i interval] [-s size] [-t ttl] target
    $ ping -c 3 192.168.0.x
    ```
* 0.2sec 간격으로 2000번의 테스트
    ```bash
    $ ping -c 2000 -i -0.2 -s 1000 192.168.0.x
    ```
    - 대다수의 시스템들은 방화벽으로 막기 때문에 응답하지 않는 경우가 대다수

## 2. traceroute
* 패킷의 도달 경로를 확인
    ```bash
    $ traceroute -n 192.168.0.x
    traceroute to 192.168.0.x (192.168.0.x), 30 hops max, 60 byte packets
    1  192.168.0.x  0.871 ms  0.754 ms  0.701 ms
    ```

## 3. arp
* Arp  manipulates or displays the kernel's IPv4 network neighbour cache. It can add entries to the table, delete one or display the current content.

* ARP  stands  for Address Resolution Protocol, which is used to find the media access control address of a network neighbour for  a  given  IPv4 ddress.

* ARP 테이블 : IP와 MAC주소의 매칭 테이블
    - Address Resolution Protocol
        ```bash
        # arp
        Address                  HWtype  HWaddress           Flags Mask            Iface
        _gateway                 ether   00:23:xx:xx:xx:xx   C                     wlp1s0
        17.xxx.xxx.xx.bc.google          (incomplete)                              wlp1s0
        ```
    - arp -s \<MAC address> \<IP address>
        + static arp address를 사용하는 경우 
    - L3까지는 IP주소로 오지만 L2로 오기 위해서 IP주소로 통신하는 것이 아니다. MAC address로 통신하기에,MAC adrress로 변환해주는 매칭테이블이 필요(매칭테이블 → MAC address로 통신(in L2) 
* Tip : NIC 교체 후 통신이 실패하는 경우?
    - 고정 IP주소를 사용하는 기관이나 회사의 경우, 보안이나 IP주소 관리를 위해서 고정 ARP테이블을 사용하는 경우가 있음
    - 점검 방법
        + ARP 테이블을 확인하여 IP와 MAC주소가 제대로 매칭되는지 확인
        + 네트워크 관리자에게 교체한 NIC의 MAC주소와 IP를 새로 알려주어야 함

## 4. resolver : name service
* IP address 혹은 hostname (FQDN, alias)를 해석
    - nameserver를 의미
    - /etc/resolv.conf에 지정됨
        + /etc/nsswitch.conf에 해석기 및 우선순위 지정
        + 하지만 NetworkManager의 관리하에 있는 경우는 수동 편집을 금지!
* resolver client
    - nslookup : deprecated
    - dig

## 5. nslookup, dig
* 네임서버에 질의하는 유틸리티
    ```bash
    $ nslookup www.naver.com
    Server:		127.0.0.53
    Address:	127.0.0.53#53

    Non-authoritative answer:
    www.naver.com	canonical name = www.naver.com.nheos.com.
    Name:	www.naver.com.nheos.com
    Address: 223.130.200.104
    Name:	www.naver.com.nheos.com
    Address: 223.130.200.107
    ```

## 6. dig
* dig [@server] target
    ```bash
    $ dig www.naver.com

    ; <<>> DiG 9.11.3-1ubuntu1.16-Ubuntu <<>> www.naver.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17491
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 65494
    ;; QUESTION SECTION:
    ;www.naver.com.			IN	A

    ;; ANSWER SECTION:
    www.naver.com.		7050	IN	CNAME	www.naver.com.nheos.com.
    www.naver.com.nheos.com. 29	IN	A	223.130.200.104
    www.naver.com.nheos.com. 29	IN	A	223.130.200.107

    ;; Query time: 0 msec
    ;; SERVER: 127.0.0.53#53(127.0.0.53)
    ;; WHEN: Sun Mar 06 21:27:15 KST 2022
    ;; MSG SIZE  rcvd: 108
    ```
* /etc/resolv.conf에 DNS를 설정하여 다음과 같이 작성할 수도 있다.
    ```bash
    $ dig @8.8.8.8 www.naver.com
    ```
* 유명한 nameserver
    - 1.1.1.1 : Cloudflare DNS
    - 208.67.222.222, 208.67.200.200 : CISCO openDNS
    - 8.8.8.8 : google DNS

# 2. net. dev. utils
---
## 1. ethtool
* query or control network driver and hardware settings, particularly for wired Ethernet devices.

* devname is the name of the network device on which ethtool should operate.
    ```bash
    $ ethtool devname
    ```
    ![Screenshot from 2022-03-06 21-36-22](https://user-images.githubusercontent.com/58837749/156923517-d5ffc431-9af1-4528-8436-906ebe64f79d.png)

* network 카드에 대해서 질의하거나 설정하는 ethtool

## 2. ethtool : flow control : spped/duplex
* 간혹 시스템이 느려지는 경우는 duplex 문제일 가능성이 크다.
    ![Screenshot from 2022-03-06 21-36-34](https://user-images.githubusercontent.com/58837749/156923518-38033d37-ce52-4d97-97b1-ab2b8a44a059.png)

* 외부에서 프로그램을 설치하여 사용하다가 강제로 speed와 duplex를 바꾸는 경우가 존재 → ethtool -s 주소 speed 1000 duplex full

## 3. ethtool : flow control : nego
* ethernet auto-negociation
    - -a : --show-pause
    - -A : --pause (change the pause param)
        ```bash
        # ethtool -s devname speed 1000 duplex full autoneg off
        ``` 

        ![Screenshot from 2022-03-06 21-38-12](https://user-images.githubusercontent.com/58837749/156923565-5242bb6d-c83e-476e-9655-5e49c4979f88.png)


## 4. ethtool : flow control : pause
* ethtool -A|--pause devname [autonet on|off] [rx on|off] [tx on|off]
    ```bash
    # ethtool -A enp0s31f6 autoneg off rx on tx on
    ```

## 5. ethtool : statistics
```
# ethtool -S enp5s2
```

## 6. ethtool : offloading
* -k \<dev> : show offloading
* -k \<dev> <param> <on|off>
    ```bash
    # ethtool -k enp0s31f6
    ```



