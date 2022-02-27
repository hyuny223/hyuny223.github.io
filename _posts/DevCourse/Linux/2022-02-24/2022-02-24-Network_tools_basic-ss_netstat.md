---
title: "[Linux Day4] Package Manager"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:05
last_modified_at: 2022-02-24
---
<br>
<br>
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

ss -n 'src 192.168.110.134' → 이쪽 주소(192)에서만 접속하는 경우만 살펴본다

src 주소 and src 주소 → 결합
ss -n 'src 192.168.110.0/24' → 범위 연산
ss -n 'dport = :ssh or sport = :shh' → 포트번호 검색
ss -n '( dport = :ssh or sport = :shh ) and  src 주소'  → 결합
