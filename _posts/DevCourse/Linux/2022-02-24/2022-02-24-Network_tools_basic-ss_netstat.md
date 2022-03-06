---
title: "[Linux Day4] Linux Admin #6(1/3) : Network tools(basic)"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:05
last_modified_at: 2022-03-06
---
<br>
<br>

# 1. ss(socket status)
---
## 1. net tools : netstat
* network stat
    - obsolete program
    - Replacement for netstat is ss
    - Replacement for netstat -r is ip route
    - Replacement for netstat -i is ip -s link
    - Replacement for netstat -g is ip maddr

## 2. netstat : obsolete command
* network status
    - 네트워크의 상태를 확인한느 유틸리티
        ```bash
        netstat -nr
        netstat [-nc] [-A socktype] [-t] [-u] [-p]
        netstat [-n] [-lt]
        netstat -s
        // 사용하지 않기!!!!
        ``` 
    - socktype : inet, unix
    - tcp connections
        ```bash
        netstat -nt
        ```
    - tcp listen ports
        ```bash
        netstat -ntl
        ``` 

## 3. ss
* socket
    - 프로토콜 & ip address & port → socekt
    - HTTP가 단방향 소통이라면, socket은 양방향 소통이다.
        + HTTP의 client와 server의 관계는 service식이라면, socket은 publisher, subscriber와 비슷 
    - 통신을 위한 인터페이스(즉 구조체) → 데이터 전달(통신)
* socket statistics

    ```bash
    ss [options] [FILTER]
    ```
    - options
        + -n : numeric
        + -a : all
        + -l : listening
        + -e : extended
        + -o : options
        + -m : memory
        + -p : processes 
        + -i : info
        + -s : summary
        + -4 : ipv4
        + -6 : ipv6
        + -t : tcp
        + -u : udp
        + -x : unix
        + -f FAMILY : --family=FAMILY

## 4. ss : filter
* FILTER에는 state, address를 지정할 수 있다
    - FILTER := [ state TCP-STATE ] [ EXPRESSION ]
    - TCP-STATE

      |TCP state|Desc.|
      |---|---|
      |established|TCP state transition에 따른 표기|
      |syn-sent||
      |syn-recv||
      |fin-wait-1||
      |fin-wait-2||
      |time-wait||
      |closed||
      |close-wait||
      |last-ack||
      |closing||
      |all|All of the tcp states|
      |connected|All the states except for listen and closed|
      |synchronized|All the connected states except for syn-sent|
      |bucket|Show states, which are maintained as minisockets|
      |big|Opposite to bucket state|

## 5. TCP State Transition Diagram
* flow
    ![Screenshot from 2022-02-24 13-11-22](https://user-images.githubusercontent.com/58837749/156921927-9e3a3ff0-5f6b-4518-835b-d787206377d1.png)
    (무단 사용 금지, 프로그래머스 리눅스 시스템 프로그래밍 강의 중)

    ```bash
    TCP ①echo ②akc
    3 ways handshaking - SYN(C) → SYN & ACK(S) → ACK(C)
    4 ways handshaking - FIN(C) → ACK(S)
    FIN ← close()함수 CALL(필)
    ACK ← OS(필)
    따라서ACK가 더 빠르게 작동
    FIN_WAIT1은 ACK를 받음
    FIN_WAIT2는 FIN을 받음
    PASSIVE 측은 FIN을 받게 되면 CLOSE_WAIT(close()함수를 기다린다는 것)으로(FIN → CLOSE_WAIT)
    PASSIVE 측 close() 함수는 프로그래머가 따로 넣어주어야 함(OS에서 자동처리X)
    TIME_WAIT : 60초
    Package copy가 일어날 수 있다 → FIN이 또 ACTIVE쪽으로 날라올 수 있음 → TIME_WAIT을 통해 잉여 FIN을 버려줌
    orphaned : 소켓이 실질적으로 연결되지 않고, 생성만 된 상태
    synrecv : sync를 몇개 받았는지
    ```
* Three-way handshaking
    - TCP 접속을 만드는 과정
        + 3번 왔다갔다 하기 때문에 붙여진 이름
        + 문제가 생기는 일이 드물다
* Four-way handshaking
    - TCP 접속을 해제하는 과정
        + 아주 드물게 동시에 접속이 해제되는 경우에는 3번만에 끝나기도 한다.
        + 문제가 종종 생긴다. 특히 Passive close하는 측에서 문제 발생(close_wait)

## 6. ss : tcp connection
* ss -nt
    ![Screenshot from 2022-03-06 20-16-42](https://user-images.githubusercontent.com/58837749/156920728-cf36f29d-3134-492e-96fd-2f9120f8aad2.png)

* ss -ntm
    - mem
        + r : the read (inbound) buffer
        + w : thr write (outbound) buffer
        + f : forward allocated memory(memory available to the socket)
        + t : the transmit queue(stuff waiting to be sent or waiting on an ACK)
            ![Screenshot from 2022-03-06 20-20-10](https://user-images.githubusercontent.com/58837749/156920831-9fdd7075-f156-416e-ba13-a66924312767.png)

## 7. ss : tcp state
* ss state <tcp state> ..
    - established 상태를 보려면?
        ![Screenshot from 2022-03-06 20-22-02](https://user-images.githubusercontent.com/58837749/156920891-23309e53-85fb-4c0c-b742-8582dda056a5.png)

* close-wait와 fin-wait-2는 심각한 문제
    - close-wait가 발생한다면 진짜 심각한 문제(시한 폭탄 수준!)
        ![Screenshot from 2022-03-06 20-26-29](https://user-images.githubusercontent.com/58837749/156921024-e0869d6e-4269-4371-8d4d-8e7e7e7d5e62.png)
        ![Screenshot from 2022-03-06 20-26-12](https://user-images.githubusercontent.com/58837749/156921026-866d7107-b7ca-4241-9e4f-6a65f6ff4452.png) 


## 8. ss : address filter
* address filter
    - IP address, CIDR, Port number
        + IP, CIDR 표기법 : dst,src
        + Port : dport, sport 
    - operators
        + symbolic, literal 방식을 지원 

            |symbolic|literal|
            |---|---|
            |>|gt|| 
            |<|lt|| 
            |>=|ge|| 
            |<=||le| 
            |==|e|| 
            |!=|ne|| 
            ||and|| 
            ||or|| 

    ![Screenshot from 2022-03-06 20-33-49](https://user-images.githubusercontent.com/58837749/156921217-466655ea-e075-4db3-a4ef-beeb437fe089.png)

## 9. ss : statistics
* statistics
    ![Screenshot from 2022-03-06 20-35-18](https://user-images.githubusercontent.com/58837749/156921284-29183cd8-1e52-45e4-b713-dd117e53a51e.png)

## 10. ss : process info
* -p : Show process using socket
    - root 유저의 권한을 필요로 할 수 있다(→ socket meta정보를 읽기 위하여) 
        ![Screenshot from 2022-03-06 20-37-27](https://user-images.githubusercontent.com/58837749/156921355-8bb2a793-d619-466b-b98c-fd54d1c16e0f.png)

## 11. ss: filter : addr/port
* adv. filter : address and port
    ```bash
    // ip address에는 실제 ip주소를 삽입
    ss -n 'src ip address'
    ss -n 'src ip address and src ip address'
    ss -n 'src 192.168.0.x/24'
    ss -n 'dport = :ssh or sport = :ssh'
    ss -n ' ( dport = :ssh or sport = :ssh ) and src ip address'
    ss -nt 'dst :22 or dst :80'
    ss -nt 'dport = :22 or dport = :80'

    ss -n 'src 192.168.110.134' → 이쪽 주소(192)에서만 접속하는 경우만 살펴본다
    src 주소 and src 주소 → 결합
    ss -n 'src 192.168.110.0/24' → 범위 연산
    ss -n 'dport = :ssh or sport = :shh' → 포트번호 검색
    ss -n '( dport = :ssh or sport = :shh ) and  src 주소'  → 결합
    ```
* adv. filter : greater / less
    ```bash
    ss -n 'dport > :8000'
    ss -n 'dport > :8000 and dport < :20000'
    ss -nlt 'sport le 1023'
    ```

## 12. ss : tcp : internal TCP info
* ss -nt -i
    ![Screenshot from 2022-03-06 20-44-33](https://user-images.githubusercontent.com/58837749/156921575-65e61f0f-aa49-4220-b538-e50d5f1de7b0.png)

* Field TCP info

    |Field|Desc.|
    |---|---|
    |ts|TCP timestamp (FRC-1323)|
    |sack|selective ack|
    |ecn|explicit congestion notification|
    |fsatopen|Fast open|
    |rto:\<icsk_rto\>|tcp re-transmission timeout in millisecond|
    |cubic|congestion algorithm|
    |rto|205 msec(retans. timeout)|
    |rtt|4.176/5.973(average round trip time/mean deviation of rtt|
    |ato(ack timeout)|40|
    |mss|1460|
    |cwnd|10|
    |send|28.0Mbps|
    |lastsnd|31561(how long time since the last packet sent, the unit is millisecond|
    |lastrcv|31549(how long time since the last packet received, the unit is millisecond|
    |lastack|31561(how long time since the last ak received, the unit is millisecond|
    |rcv_space|31759(a helper variable for TCP internal auto tuning socket receive buffer|

## 13. ss와 같이 쓰이는 명령어
* lsof, fuser
    - 열린 파일을 검색하거나 액션을 행할 수 있는 기능을 지님
        + 특정 소켓 주소를 점유하고 있는 프로세스를 찾아낼 때 사용
        + 프로세스를 추적하는 용도로 자주 쓰이는 명령어로 중급 이상의 실력자들이 많이 사용하는 명령어
