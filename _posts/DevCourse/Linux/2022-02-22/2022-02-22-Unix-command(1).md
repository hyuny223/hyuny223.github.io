---
title: "[Linux Day2] Unix command(1)"
categories: dev_Linux
tag: [Linux,command]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-22 00:00:01
last_modified_at: 2022-02-24
---
<br>
<br>
# 1. Command completion
## 1. Linux(UNIX) 필수 명령어
* 익숙해지기 위한 필요조건으로 억지로 외우는 것은 필요 없다. 
	- 자주사용 & man 페이지 → command 능숙

## 2. Command completion(auto-comp)
* double tap command → completion

# 2. 국제화 규격 
## 1. i18n이란?
*  i(nternationalizatio)n: 18개
	- 국제화설계 → 제품 자체가 여러 환경을 지원할 수 있도록 제품을 설계
		+ 지역화 → 제품을 각 환경에 대해 지원하는 것
	- UTF-8이 기본문자세트로 사용
	- i18n → unix → LANG 환경변수 설정의 영향을 받는다.
<br>

# 3. man page

## 0.  commands #0
```
$ man -k (apropos), man -f (whatis)
```
* 정보찾기

# 4. 기초 명령어

## 1.  commands #1
```
path: pwd, cd
file data / meta
조회 : ls, file, stat, which, find
데이터 변경 : cp, mv, rm, mkdir/rmdirm ln, readlink** 
메타 변경 : chmod, chown
```

## 2. commands #2
```
파일 묶음(archive) : tar, cpio
압축(compress) : gzip, xz, zstd***
```

## 3. commands #3
```
Txt 관련
editor : vim(vi), nano, emacs
filter : cat(tac), head, tail, less/more, sort
regex : grep(egrep,fgrep), sed, awk
Job Control : jobs, fg, bg
Process control : kill, pkill,pgrep
tracing : strace
```

## 4. commands #4
```
Networking : nc(net cat), curl, wget, w(who)
Disk : df(disk free), du(disk usage) (x)
```


# 5. 관리자 명령어

## 1. Linux(UNIX) admin
* commands #0
```
System
uptime : free(/proc/meminfo)
process summary : top
process status : ps
stat : vmstat, pidstat
hardware : lshw, lspci, lsusb
```
	-  관리자 영역

* commands #1
```
Package
RedHat : rpm, yum
Debian : dpkg, apt, aptitude
```

* commands #2
```
Network
status : ss, netstat(x)
config : nmcli, ip
dig, nslookup(x)
ssh
packet : tcpdump, wireshark, tshark
```

* commands #3
```
Files
open files : lsof
Kernel : sysctl
Firewall : 
```

* commands #4
```
Disks
fdisk,cfdisk
parted
mkfs,fsck
mount,lsblk,blkid
gruddy
udiskctl
```

* commands #5
```
Security
ulimit
User
useradd,groupadd, usermod
passwd, chpasswd
```

* commands #6
```
Service: systemctl
Performance: tuned-adm
Locale : locale, localectl
```

* commands #7
```
Alternatives : update-alternatives
```

## 2. Linux(UNIX) dev.
* commands #0
```
Compiler : gcc, g++, clang
Debugging, tracing : gdb
Tools : make, git
```

* commands #1
```
Python : python2, pip / python3, pip3
```
	- 두 버전 호환의 문제  

## 3. Obsolete 
* commands #1
```
exit, logout → ^D
clear → ^L
netstat → ss
nslookup → dig
```

* commands #2
```
fdisk → parted
mount → udiskctl
nohup → systemd-run
at, cron → anacron → systemd.timer
```


# 6. 필수 명령어, 개념

* 명령어 : pwd, cd, ls, cp, mv, rm, mkdir, ln, find, chmod, ssh, vim
* 개념
	- UNIX accout 
    - UNIX ownership : user, group
    - UNIX file mode : 권한(rwx), numeric/symbolic
    - Link : hard link, symbolic link
    - Path : absolute path, relative path, CWD(current working directory)
    - Shell
