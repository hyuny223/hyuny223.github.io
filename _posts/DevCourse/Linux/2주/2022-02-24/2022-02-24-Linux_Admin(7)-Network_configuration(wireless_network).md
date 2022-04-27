---
title: "[Linux Day4] Linux_Admin #7 - Network Configuration(wireless network)"
categories: dev_Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:08
last_modified_at: 2022-03-07
---
<br>
<br>

# 1. Wireless net
---
## 1. NetworkManager : WiFi (wireless)
* NetworkManager(NM) 도입 이후
    - wireless 부분도 NM으로 통합 (wicd는 deprecated)

## 2. nmcli : wifi
* radio wifi \[on | off\] <br>
![Screenshot from 2022-03-07 14-35-37](https://user-images.githubusercontent.com/58837749/156974201-a47991c8-354c-4151-813e-a440cf12b304.png)

## 3. nmcli : wifi : disabled
* WIFI disabled?
    ```bash
    $ nmcli r
    WIFI-HW  WIFI      WWAN-HW  WWAN
    enabled  disabled  enabled  enabled
    ```

    - WIFI가 disabled인 원인은 다양하지만 우선 blocked 되었는지부터 살펴보자
        ```bash
        $ rfkill list
        0: hci0: Bluetooth
            Soft blocked: no
            Hard blocked: no
        1: phy0: Wireless LAN
            Soft blocked: no
            Hard blocked: yes

        // rfkill block 1 : 1번 장치 soft block
        // rfkill unblock 1 : 1번 장치 soft unblock
        // rfkill unblock wifi : wifi soft unblock (wlan으로 대신 써도 됨)
        ```
        
        + soft blocked가 yes인 경우는 rfkill unblock \[# \| all\] 으로 해제 가능. 하지만 hard blocked인 경우는 BIOS나 장치 firmware기능에서 막힌 것이므로 불가!


## 4. nmcli : wifi : soft/hard blocked
* soft blocked
    - 소프트웨어에서 wifi를 비활성화 시키는 기능
* hard blocked
    - embedded 되어 있는 WIFI 칩을 BIOS에서 disabled 시킨 경우
    - 랩탑의 경우 Fn + F5 같은 기능에 WIFI를 on/off할 수 있는데, 이 기능으로 인해 disabled되면 hard blocked로 나타난다.
        + sleep모드로 진입할 때 배터리 절약을 위하여 이 기능이 기본적으로 켜지는 경우 존재

## 5. wicd vs nmcli
* NetworkManager 사용지 wicd는 사용할 수 없다

## 6. nmcli : wifi : device
* dev [list]
    ```bash
    $ nmcli dev
    DEVICE  TYPE      STATE      CONNECTION           
    wlp1s0  wifi      connected  SK_WiFiGIGAC193_2.4G 
    vmnet1  ethernet  unmanaged  --                   
    vmnet8  ethernet  unmanaged  --                   
    lo      loopback  unmanaged  --   
    ```
    - list 아큐먼트는 생략이 가능하다

* dev wifi [list]
    ```bash
    $ nmcli dev wifi
    IN-USE  SSID                  MODE   CHAN  RATE        SIGNAL  BARS  SECURITY  
    \*       SK_WiFiGIGAC193_2.4G  Infra  3     130 Mbit/s  52      ▂▄__  WPA1 WPA2 
    ```

* dev wifi rescan
    ```bash
    $ nmcli dev sifi rescan
    $ nmcli dev sifi list
    ```

    - refresh previous wifi list

## 7. nmcli : wifi : connect
* dev wifi connect <SSID | BSSID> [password <pass>]
    ```bash
    $ nmcli devwifi connect Dev_wifi4 password qwer01234
    ```

    - open AP인 경우에는 password 부분을 지정하지 않아도 된다.

* dev disconnect <ifname>
    ```bash
    $ nmcli d disconnect wlan0
    ```

# 2. wifi
---
# 1. nmcli : wifi : AP(hotspot) mode
* wifi hotspot
    ![Screenshot from 2022-03-07 14-54-21](https://user-images.githubusercontent.com/58837749/156976092-ed32f8f2-069f-4cfb-a6f0-762d9de34316.png)<br>
    (프로그래머스 강의 중, 사용금지)
* wifi hotspot 정보
    - nmcli -p c s hotspotname
        + IP4.ADDRESS[1]이 AP의 IP주소 범위가 된다

* wifi hotspot 정지/실행
    ```bash
    $ nmcli c down hotspotname
    $ nmcli c up hotspotname
    ```

# 2. hostapd
* AP로 구동을 위한 기능 제공 daemon
    - NetworkManager보다 더 많은 기능을 사용하려면 hostapd를 사용해야
        + IEEE 802.11 2.4GHz, 5GHz의 다양한 모드 지원
        + 5GHz AP구동시 regulatory domain rule에 의해 region 설정시 일부 채널이 블록되거나 passive로 작동될 수 있다.
            * 이를 해결하기 위해서는 kernel module 수정이 필요 