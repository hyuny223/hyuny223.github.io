---
title: "[Linux Day5] Linux Admin #8-2 - File System(makeFS)"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-25 00:00:01
last_modified_at: 2022-02-25
---
<br>
<br>

# 1. Make FS(mkfs, fsck)
---
1. partition and file system
	* 개념적 이해
		- 저장장치(memory,disk)는 아주 큰 종이로 비유하면 쉽다
			+ 가로, 세로 1000m짜리 종이를 사용하려면? 1장 통째로 쓰면 힘들다
	* Partitioning
		- 종이를 몇 개로 자르는 작업
	* Make FS
		- 파티션 된 종이를 A4 같은 규격화된 형태로 잘라서 분철하는 것과 비슷
			+ 분철하면 권수와 페이지 번호가 만들어진다 → 인덱싱 가능
			+ 분철의 방식, 밑줄, 칸의 간격 혹은 자물쇠를 설치하는 등의 세부 방식에 대한 것이 FS type이 된다.
				* ext4, xfs, ntfs, fat32 등의 파일 시스템 타입이 주로 쓰인다.

2. FS별 특징
	* Linux에서 지원하는 FS는 여러 종류
		- 각 FS별로 특징을 알아두면 어떤 FS를 사용해야 좋은지 인지
			+ 예를 들어, IoT기기에는 ext4가 유리하고, 고성능 PCIe SSD를 사용하는 경우 xfs가 유리

3. filesystem : ext4
	* Extended File System 4
		- 대부분의 Linux에서 사용하는 FS
		- ext2, ext3의 하위 버전이 존재(하위 호환성 제공)
	* 특징
		- Journaling 지원(순서대로 적는 것)
		- 1EB의 volume 지원
		- 연속된 파일의 접근 및 작은 파일들의 접근이 빠르다는 장점
		- 저성능의 I/O에서 효율이 높다 → IoT기기들에서 많이 사용
		- 삭제된 파일 복구 가능
4. filesystem : xfs
	* xfs
		- 저널링 기반의 대용량 파일 시스템(64bit)
		- 대부분의 고성능 서버형 리눅스 배포판의 기본 파일 시스템으로 사용
			+ Debian, RHEL7 (CentOS 7)
	* 특징
		- Online 상태(mounted)에서 확장이 가능
		- 대용량 파일 처리시 성능이 좋다(많은 개수의 작은 파일을 ㅓ리할 때도 나쁜 것은 아니다)
		- kernel service (xfs*)가 관리한다(micro kernel 디자인의 개념으로 만들어졌기 때문)
		- 삭제된 파일 복구 불가
		- 깨진 파일 시스템 복구를 위한 명령어로 xfs_repair 제공
5. mkfs
	* make file system
		- 실습을 하기 전에 미리 empty partition을 만들어야 한다
		- 준비과정
			+ 1. Disk 추가
				* VMware나 Virtualbox에서 Disk를 1개 더 추가
				* 최소 4G 이상의 디스크를 추가
			+ 2. Partitioning
				* Primary partition, 1G짜리 1개
				* Primary partition, 2G짜리 1개
				* 나머지는 빈 공간
6. swap공간
	* swap공간의 필요성
		- 메모리가 부족할 때, 디스크에 메모리의 일부분을 잠시 교환. 즉 메모리와 디스크의 공간을 교체하는 것
		- swap in/out을 위해서
		- swap in + swap out을 위해 RAM의 최대 2배까지 필요(실제로는 이정도까지 필요하지 않음)
			+ mkswap : 스왑 공간 작성 - 포맷
			+ swapon : 활성 - 사용
			+ swapoff : 비활성
			```
			mkswap [-L label] <device>
			swapon -a
			swapon <device | swapfile | -L file>
			swapoff -a
			swapoff <device | swapfile | -L label>
			```
	* swap공간 작성
		- 스왑 파티션에 공간 작성
		```
		# mkswap -L swapfs2 /dev/sdb1
		```
		- 일반 파일을 스왑 공간으로 사용 (temporary swapfile)
		```
		# dd if = /dev/zero of=./swapfile bs=1024 count=262144
		// if = input file, of = output file, bs = block size
		262144+0 records in
		262144+0 records out
		...
		# mkswap ./swapfile1
		Setting up swapspace version 1, size = 268431 kB
		no label, UUID = ...
		```
	* 스왑 공간 활성/비활성
		- swap공간 활성화
		```
		# swapon /dev/sdb1
		# swapon ./swapfile1
		# swapon -L swapfs2
		swapon: ./swapfile2 : Device or resource busy
		// sda1의 레이블이 swapfs2. 즉, 중복. 따라서 swap불가
		```
		- swap공간 확인
		```
		# cat /proc/swaps
		```
			+ swapoff /dev/sdb1 → swap종료
			+ swapoff -a → 전체를 swap에서 뺀다
	* swap공간과 fstab
		-fstab에 등록된 swap공간의 확인
			+ grep으로 /etc/fstab에서 swap이란 문자가 들어간 행을 추출
			```
			# grep swap /etc/fstab
			```
7. Tip
	* 간혹 몇몇 Linux 설치 프로그램이 한국어를 사용하여 설치할 때 파티션의 Label을 한글로 작성한느 버그가 있었음
	* 만일 한글로 된 레이블이 사용되어 /etc/fstab에 작성되었다면?
		- 후에 한글을 지원하지 않는 에디터로 fstab을 편집하면 문제 발생
		- 따라서 한글로 된 레이블의 경우에는 다시 영문으로 바꿔주는 것이 좋음
		- fstab에서 swap 파티션의 레이블을 swapfs1으로 수정

# 2. mount
---
1. mount
	* 파일 시스템의 탑재
		- 마운트는 root directory(/)를 기점으로 시작
			+ 모든 파일시스템을 루트로부터 시작하여 응집성있게 묶어주는 역할
	* 명령어
		- mount : 마운트를 하는 명령
		- umount : 언마운트를 하는 명령
		- findmnt : 마운트 리스트 확인
	* (옵션없이 실행하면) 마운트 테이블 보기
		- mount 명령의 실행(요즘은 findnmt 사용)
2. mount, umount 명령어
```
mount
mount [-t fstype] [-o option] [device] <directory>
umount <directory \| device>
```
	* fstype : ext4, xfs, ntfs-3g, cifs, vfat, exfat 등등(man page)
		- ext4, xfs, vfat(fat16, fat32) 같이 판별이 쉬운 FS는 자동 판단 가능
	* option : 각 파일 시스템에 따라서 조금씩 다름(man page 참조)
		- 공통 : rw, ro, remount 등
	* device : /dev/sda, /dev/sdb, ..., /dev/dvdrom
		- device naming은 뒤에
	* directory : 마운트 대상 지점 (mount point)
3. device name
	* 디바이스 파일 중 저장 장치들용

	|장치파일|설명|
	|---|---|
	|/dev/sda|SATA,SCSI,SAS,USB등 시리얼 블록 장치들 중 첫번째|
	|/dev/sdb|SATA,SCSI,SAS,USB등 시리얼 블록 장치들 중 두번째|
	|/dev/sr0|SCSI/SATAE recorder 장치 중 첫번째|
	|/dev/mapper|LVM 사용시 매퍼로 매핑된 디바이스|
	|/dev/sda1|SATA, SCSI, USB의 1번째 장치의 첫번째 파티션|
	|/dev/sdb5|SATA, SCSI, USB의 2번째 장치의 다섯번째 파티션|

4. device name : /dev/disk
	* /dev/disk 디렉토리
	```
	ls /dev/disk
	ls -l /dev/disk/by-uuid/
	```
	* EFI firmware system (GPT based)
		- GPT 디스크를 사용할 경우 by-partlabel, by-partuuid가 추가된다
5. Practice : mount : sdb2
	* /dev/sdb2를 mount 해보자
		- 어떤 순서로 해야할까
			+ 1. VMware에 sdb를 추가해두어야 한다.
			+ 2. sdb에 2개의 partition을 만들어 두었다.
			+ 3. mkfs -t ext4 /dev/sdb2
			+ 4. mkdir /media/backup
			+ 5. mount -t ext4 /dev/sdb2 /media/backup
6. Practice : umount
	* unmount를 위해 umount 명령을 내려보자
	```
	# cd /media/backup
	# pwd
	# umount /media/backup
	# cd ..
	# umount /dev/sda2
	```
		- busy가 나타나는 이유 ← umount할 device의 디렉토리에서 작업하는 프로세스가 존재
			+ 이를 해결하기 위하여 fuser <dir>로 PID를 알아낸다 → kill한다
	
7. Practice : mount point
	* umount후에는 해당 디렉토리의 목록이 달라진다.
	```
	# lsblk
	# ls /media/backup // 아무것도 남아있지 않음
	# touch /media/backup/hey_there.txt
	# ls /media/backup // hey_there.txt가 보임
	# mount /dev/sda2 /media/backup
	# ls <ALT-.>
	```
	* hey_there.txt는 어디갔을까?
		+ 위에서 묶어준 tar.gz 파일은 존재
		+ mount는 단순히 올라타는 것이기에, umount하면 다시 hey_there.txt파일이 보일 것

8. Practice : mount : option, remount
	* mount시에 옵션을 사용해보자
		- ro(readonly) 옵션으로 읽기 전용
		```
		# findmnt
		# umount /dev/sda2
		# umount -o ro /dev/sda2 /media/backup
		# findmnt
		```
	* umount, mount를 연달아 사용하여 option을 바꾸는 것보다 더 편리한 방법이 있다. 바로 remount를 이용하는 것이다.
		- mount -o remount,ro /dev/sda2
		- 보통 복구모드 사용할 때 많이 사용
10. USB memory
	* USB memory card/stick의 mount
		- USB 메모리를 장착하면 lsblk에서 장치명이 인식된다.
			+ 예를 들어, sda, sdb까지 있는 시스템에서는 USB가 sdc가 된다
		- mount 명령으로 인식시키면 된다
			+ 그러나 FStype은 알고 있어야 한다.
				* USB 메모리는 대부분 vfat, fat32, exfat등을 사용한다. 드물게 ntfs를 사용하기도 한다.
				* 정확히 파악하기 위해선 blkid명령어를 사
		- VMware usb settings
			+ VMware는 사용하는 USB장치가 2.0, 3.x인지에 따라 설정이 다르다.

11. directory binding
	* 디렉토리를 다른 위치의 디렉토리에 붙이는 기능
		- mount의 --bind 옵션으로 가능
		```
		mount --bind /usr/src/redhar/RPMS /var/www/html/rpms
		```
		- /usr/src/redhar/RPMS를 /var/www/html/rpms에 마운트
			+ 디렉토리의 일부를 특정 위치에 노출시킬 수 있음
			+ 보통 ftp나 웹서비스에서 다른 위치의 디렉토리를 노출시킬 때 편리
				* 노출시키려는 디렉토리가 다른 상위 디렉토리에 있을 때도 편리
				* 특히 binding을 해제하기만 하면 디렉토리 내용을 안 보이게 할 수도 있어서 편리
12. Tip!
	* Device is busy!!
		- 마운트한 파일시스템에서 프로세스가 실행중인 경우 발생
		- 해결책 → fuser로 해당 파일 시스템에서 구동 중인 프로세스를 체크/종료시켜야 함
		- fuser -c /media/cdrom
			+ 해당 파일 시스템 아래서 작동하는 프로세스를 체크
		- fuser -ck /media/cdrom
			+ 해당 파일 시스템 아래서 작동하는 프로세스를 체크하여 종료13. eject
	* 외부 저장 장치를 빼냄(unmount + pull)
