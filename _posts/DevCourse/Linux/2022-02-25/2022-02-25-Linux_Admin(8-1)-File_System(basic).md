---
title: "[Linux Day5] Linux Admin #8-1 - File System(basic)"
categories: dev_Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-25 00:00:00
last_modified_at: 2022-02-25
---
<br>
<br>

# 1. FS(basic)
---
1. FS(File System)
	* FS는 OS의 매우 큰 부분을 차지한다.
		- computer라는 시스템은 큰 틀에서 read, evaluate, write의 작업을 진행하는데, 그 중에서 메모리의 역할 중 일부분을 FS가 담당(메모리와 비슷. 즉, read와 write 담당)
	* FS는 넓게 보면 데이터베이스의 일종
		- 무엇을 저장
			+ 파일 (계층적 구조) 
	* 파일 시스템 타입
		- Linux : xfs, ext4, zfs, Btrfs
		- Windows : ntfs, exfat, fat32, fat16
		<br>

# 2. FS의 종류
---
1. File system : FS type list
	* ext4 : 리눅스 고유 파일 시스템
	* ext3, ext2 : ext4의 이전 버전
	* xfs : 실리콘 그래픽스의 저널링 파일 시스템(RHEL7부터 기본 파일 시스템
	* zfs : 솔라리스의 기본 파일 시스템, Cow 지원
		- CoW(Copy on Write)
	* Btrfs : zfs와 비슷한 컨셉으로 개발
	* ntfs : 윈도우즈 NT계열에서 사용하는 파일 시스템
	* fat32 : FAT 32비트의 버전
	* exfat : FAT 32비티의 확장 버전
	* fat15(vfat) : windows 9x 계열
	<br>

# 3. 새로운 FS를 만드는 방법
---
1. 새로운 FS 만드는 법
	* Partitioning
		- command : fdisk, parted	
	* File System
		- command : mkfs(==format), fsck or xfs_*
	* Mount
		- command : mount / umount
		- /etc/fstab : information file about the file system
			+ mount를 자동화 하기 위한 설정파일
			+ device file(old fashioned)
			+ LABEL / UUID
			<br>

# 4. Partition(disk label / partition type)
---
1. Partition
	* Physical 혹은 Logical 구획
		- 과거에는 physical partition만 존재
	* 최근의 modern OS에는 logical partition을 선호
		- 대표적으로는 Logical Volume(LVM)을 사용하는 방법
			+ 좀 더 시스템에서 작은 개체로 만들어서 묶어서 논리적인 파티션의 크기를 만드려는 시도
		- Logical partition을 사용하면 물리적 한계를 가진 디스크를 묶어서 보다 큰 용량의 파티션을 만들 수 있다
			+ 10TB*10 = 100TB

2. Partition : disklabel type
	* Physical disk의 disklabel type은 두가지
		- DOS방식 (혹은 msdos방식) : 고적적
			+ 2TB의 제한
			+ tool : fdisk를 사용
		- GPT방식 : DOS레이블의 문제를 개선한 방식
			+ 용량 제한이 없다
			+ tool : parted / gparted or gdisk를 사용

3. Partition : DOS labeled disk (MBR)
	* DOS partition의 종류
		- Primary partition
		- Extended partition
		- Logical drive
	* Primary partition(주파티션)
		- 4 partitions per disk
	* Extended partition(확장파티션)
		- 4개 이상의 파티션이 필요할 때 primary partition대신에 한개를 만들 수 있다.
		- extended partition은 다시 여러 개의 logical drive로 나눌 수 있다.
			+ extended partition을 자르는 것을 logial drive라고 한다
		- 결과적으로 4개 이상의 partition이 필요할 때 사용

	* DOS label disk의 예 두가지 : msdos 형식(2TB 이하)
		- 주파티션 1개, 확장 파티션에 논리드라이브 2개, 빈공간 일부
			+ 주1, 확1, 논2 = 4개
		- 확장 파티션에 논리드라이브 2개
			+ 확1, 논2 = 3개

4. Partition : GPT
	* 뒤에서 다룬다
	<br>

# 5. fdisk(partition 만들기)
---
1. fdisk : legacy command
```
fdisk -l
fdisk <block device>
```
	* fdisk -l
		- 현재 FS 리스트를 출력, 모든 device를 검색할 때 유용
		- 최근의 Linux는 fdisk -l 대신에 lsblk를 더 많이 사용
	* block device
		- 저장 장치를 이르는 용어
			+ /dev/sd[abcd....]
				* SCSI disk(스커지(scuzzy)  디스크)
				* Serial type : SATA, USB
			+ /dev/hd[abcd]
				* 고대의 유물 : IDE disk
2. fdisk : command
	* a : 부트 활성 플래그를 지정(DOS, Windows 계열의 boot 드라이브 지정)
	* d : 파티션 삭제
	* l : 알려진 파티션 ID(파티션 타입)
	* n : 새로운 파티션을 생성
	* p : 현재 파티션 상태를 출력
	* t : 파티션 ID(타입)을 변경
	* q : 변경된 상태를 저장하지 않고 종료
	* w : 변경된 상태를 저장하고 종료
		- 중요! 변경된 상태를 저장하는 w키를 입력하기 전에는 실제로 저장되지는 않는다.
		- 따라서 연습하는 경우에는 파티션을 편집한 뒤에 꼭 q로 종료

3. fdisk : new partition
	* fdisk /dev/sda
		- 파티션 편집은 저장하지만 않으면 되므로 실습하는데 문제 X - q 사용!
		- 연습할 때는 기존 파티션은 d 명령으로 지우고 연습하면 된다
			+ 실제로 지워지는 것이 아니다
	* 시나리오 0 : 모든 파티션 삭제
	![Screenshot from 2022-02-25 10-10-29](https://user-images.githubusercontent.com/58837749/155634527-1d362071-4532-4320-8afb-fc60e9558696.png)
	* 시나리오 1 : 주파티션 생성
	* 시나리오 2 : 확장파티션 생성(번호는 4번, 나머지 용량 전부)
	* 시나리오 3 : 논리 드라이브 만들기
	![Screenshot from 2022-02-25 10-15-38](https://user-images.githubusercontent.com/58837749/155635248-813025a9-5a75-4bf2-8a8e-2db76ddc2113.png)
	![Screenshot from 2022-02-25 10-17-33](https://user-images.githubusercontent.com/58837749/155635251-ff48f2ab-61fc-4911-8280-984a00371f0f.png)

		- 확장파티션, 논리드라이브 모두 만들 수 있도록 나오는데(p,e,l 명령어), 나는 nvme를 사용해서 그런지 나오지 않는다.
	* 시나리오 4: 논리 드라이브 파티션 ID 타입 변경 (82번으로 변경)
	![Screenshot from 2022-02-25 10-23-46](https://user-images.githubusercontent.com/58837749/155635803-e626afb8-69f3-42d3-bfcf-7235ed1298e8.png)

