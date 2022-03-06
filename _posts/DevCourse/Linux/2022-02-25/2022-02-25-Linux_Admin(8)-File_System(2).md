---
title: "[Linux Day5] Linux Admin #8 - File System(2)"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-25 00:00:07
last_modified_at: 2022-02-26
---
<br>
<br>

# 1. fstab
---
## 1. fstab
* fstab : filesystem table
	- /etc/fstab
	- 부팅시 파일 시스템을 자동 마운트 하기 위한 정보를 담고 있다 → 이를 이용하여 부팅
		+ fstab에 잘못된 설정이 들어가 있다면 부팅이 안 된다.
	- mount 명령을 단축시킬 수 있는 정보를 가짐 : 6개 필드로 구성
	```
	UUID=xxx : device(장치파일)
	\/home : mount point(마운트 포인터)
	xfs : fstype(파일 시스템 타입)
	options : defaults(옵션)
	0 0 : dump,fsck(덤프 여부, 부팅시 체크 순서)
	```
	<br>

## 2. fstab : 1번 항목
* /etc/fstab의 1번 항목 : FS device 파일
	- mount할 device 장치 : 다양한 형태로 사용될 수 있다.
		+ 방식1) /dev/sd* : 실제 디바이스 장치로 설정하는 방식
		+ 방식2) /LABEL=name : 레이블 이름으로 검색하여 장치를 찾아냄
		+ 방식3) UUID=uuid : UUID값으로 검색해서 장치를 찾아냄
	- 방식 1의 단점
		+ serial device(sd*)는 port를 순서대로 연결하지 않는 경우 장치명의 변경이나 역전될 가능성이 있다.
			* port0, 3, 4번에 장치가 연결된 경우 0(sda), 3(sdb), 4(sdc)가 된다.
			* 새로운 장치가 port 2번에 설치되면, 0(sda)는 유지되지만, 2(sdb), 3(sdc), 4(sdd)가 되면서 3,4번 장치가 밀리게 된다.
			* 이 문제를 해결하기 위해 LABEL, UUID를 사용하게 된 것
	
## 3. fstab: 1번 항목 : LABEL/UUID
* 장치명에 레이블 지정
	- LABEL=... 혹은 UUID=... 으로 지정가능
		+ tune2fs, e2label을 사용
* LABEL
	- 식별 가능한 문자열을 FS의 label부분에 넣어서 찾는 방법
	- 문제점
		+ LABEL이 완료된 상태에서 mount된 특정 부분이 고장 → 다른 장치에 옮겨야 한다 &  mount한 파일이 그대로 → LABEL이 중복되는 문제
		
* UUID
	- UUID(Universally Unique Identifier)를 FS에 넣어 찾는 방법
		+ 더욱 세련된 방식
		+ UUID를 변경하기 위해서는 uuidgen으로 생성한 다음에 e2label, xfs_admin으로 한다.

## 4. fstab: 4번 항목
* 파일 시스템 주요 옵션 : mount의 옵션과 동일
	- defaults : 기본값 사용(기본값에선 부팅시 자동으로 마운트)
	- noauto : 부팅시 자동으로 마운트 하지 않음
	- rw : 읽기/쓰기 가능
	- ro : 읽기만 가능
	- user : 일반 유저도 마운트 가능
	- nouser : 일반 유저는 마운트할 수 없음(수퍼유저만 가능)
	- noexec : 실행 파일의 사용 금지
* Linux FS (ext4, xfs, ...) 관련 옵션
	- relatime : FS에 접근 시간 기록 → atime을 특정 경우에만 업데이트
		+ dir 탐색 혹은 잦은 읽기에 대한 성능 향상을 목적으로 한다(default after CentOS6)
		+ atime 업데이트 조건을 느슨하게 함 → 24시간 이상 경과된 경우 or ctime,mtime보다 작은 경우
	- noatime : atime 자체를 사용하지 않도록 함
		+ log및 매우 많은 depth를 가지는 디렉토리 및 파일 시스템에서 유리

## 5. fstab : systemd
* systemd를 사용하는 시스템의 fstab(사실 별다른 건 없다)
![Screenshot from 2022-02-26 22-40-19](https://user-images.githubusercontent.com/58837749/155845372-11ebad65-8705-4295-a1a9-6fae0062bb2d.png)

## 6. FS(ext4) : LABEL, UUID
* Utilities
```
e2label <device> [new-label] //설정
tune2fs -U uuid_value <device> //설정
findfs LABEL=label //query
findfs UUID=uuid //query
```
	- e2label : ext4 리눅스 파일 시스템의 레이블을 확인 및 편집
	- tune2fs : ext4 파일 시스템의 다양한 설정 조정
	- xfs\_admin : XFS 파일 시스템의 레이블/UUID 확인 및 편집
		+ label, UUID를 사용하는 방식이 오류를 방지할 수 있다.
	- findfs : 레이블을 검색

* tune2fs
	- -U <clrea\|random\|time\|UUID_value>
		+ random, time을 이용해서 생성하는 방법이 보편적
		+ 직접 생성해서 넣을 때는 UUID_value를 넣어주면 된다(e.g. uuidgen)
* UUID
	- uuidgen : UUID 생성 유틸
		+ 명령어 대신에 /proc/sys/kernel/random

## 7. FS: LABEL, UUID : ext3, ext4
* 파일시스템 레이블 편집 예
```
# e2label /dev/sda2
/boot  // 경로명이 아니라 label임
# e2label /dev/sda2 MYBoot
# e2label /dev/sda2
MYBoot
# e2label /dev/sda2 /boot
# e2label /dev/sda2
/boot
```
	- 잘 안 쓰기에 이런 것이 있다 정도만 학습
	- 부트 파티션의 레이블을 편집하는 경우
		+ /etc/grub.conf의 kernel 파라미터의 조정이 필요
		+ /etc/fstab 파일에서 장치명 대신 LABEL을 사용하는 경우 조정 필요

## 8. FS: LABEL, UUID : findfs
* findfs를 이용한 LABEL로 Device 찾기
```
# findfs LABEL=/boot
/dev/sda2
```

## 9. Pratice: sdb2를 fstab에 등록
* sdb2를 fstab에 등록해보자
	- 등록하면 장점
		+ 부팅시 자동마운트 가능(물론 수동도 가능)
		+ 수동 mount시 device파일이나 mount point중 한 개만 입력해도 됨
			* e.g. mount /dev/sdb2 혹은 mount /media/backup
* 주의
	- fstab 수정시에는 특히 주의. 오타 → 부팅 멈춤
		+ 해결책 → remount
		```
		root password
		mount -o remount,rw /
		vi /etc/fstab에서 재설정
		```
* vi로 /etc/fstab을 연다
	- 저장한 뒤에 mount /media/backup(mount point)으로 명령해보자. 그 후 findmnt로 mount table을 확인해보자. 그리고 umount도 해보자
* 그러나 실무에서는 이런 방법을 잘 안 쓴다.
	- sdb2가 sdc2 혹은 sdd2가 될 수도 있기 때문이다.
* 따라서 실무에서 사용하는 LABEL 혹은 UUID 방식으로 수정해보자

## 10. 실습(추후 추가)

# 2. 디렉토리 구조
---
## 1. 디렉토리 구조
* SUS 표준에 근거한 디렉토리 구조
	- \/ : root 디렉토리(root 유저의 home디렉토리와는 다름)
		+ root유저의 home 디렉토리는 /root
	- /dev : 장치 파일들 (device file)
	- /tmp : 임시 파일 저장용 디렉토리(temprorary directory)
	- /dev/null : 널 디바이스
	- /dev/console : 시스템 콘솔 장치
* 기본만으로는 부족하고 추가적인 디렉토리 구조가 필요 → 원할한 구동
	- /boot : 부팅을 위한 커널 관련 이미지
	- /bin : 기본 실행 명령어 파일들(binary, 실행 가능한 파일)
		+ /usr/bin을 가리키는 심볼릭 링크(RHEL7)
	- /sbin : 수퍼유저용 시스템 관리용 명령어 파일들
		+ /usr/sbin을 가리키는 심볼릭 링크(RHEL7)
	- /etc : 시스템 설정 파일 및 관련 스크립트
	- /lib : 시스템 라이브러리
		+ /usr/lib를 가리키는 심볼링 링크(RHEL7)
	- /lib64 : 시스템 라이브러리 62bit 전용
		+ /usr/lib64를 가리키는 심볼링 링크(RHEL7)
	- /usr : 응용 프로그램 관련 파일들(기본 실행 파일들이 아니라)
		+ windows의 c:\Program Files와 비슷
		+ 하지만 최근에는, 기본 실행 파일과, 응용 프로그램 파일간의 경계가 희미해졌기 때문에, /bin의 경우 위에서 살펴본 바와 같이 /usr/bin으로 심볼릭 링크가 걸려있는 경우가 있음
	- /usr/bin : 응용 프로그램 파일들
	- /usr/sbin : 관리자용 응요 프로그램 실행 파일
	- /usr/lib : 라이브러리
	- /usr/lib64 : 라이브러리 64bit 전용
	- /usr/include : C언어용 헤더 파일
	- /usr/share : 응용 프로그램이 공유하는 파일
		+ 폰트, 아이콘, 매뉴얼, 그림파일 등
	- /usr/src : 소스코드 (커널, 패키지)
	- /usr/local : 패키지가 아닌 임의로 설치되는 응용 프로그램 파일들
		+ 따라서 버전관리가 안 된다. local에 다운받는 것은 비추천
* 리눅스의 추가적인 디렉토리 구조
	- /var : 각종 잡다한 파일들이 존재
	- /var/log : 로그파일
	- /media : 마운트용 디렉토리(구버전에서는 /mnt)
* 사용자들이 주로 만들어서 사용하는 디렉토리
	- /export : 외부 저장 장치
		+ 혹은 /expt로 명명하는 경우도 있다.
	- /u00, /u01, /u02 ... : 오라클 DB 디렉토리

## 2.  디렉토리 구조의 변화
* 최근 리눅스에서의 변화(i.e. RHEL 7)
	- 다음 디렉토리들은 /usr 밑의 동명의 디렉토리에 심볼릭 링크화 되었다.
		+ /bin
		+ /sbin
		+ /lib
		+ /lib64

