---
title: "[Linux Day4] Linux_Admin #2 - APT"
categories: dev_Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:01
last_modified_at: 2022-02-24
---
<br>
<br>

# 1. advanced package tool
---
## 1. apt
* debian의 dpkg를 wrapping한 front-end tool
	- 네트워크 설치 지원 (mirror 탐색 가능)
	- dpkg → 의존성문제 → apt → 의존성 해결
* binary(명령어)
	- apt-get : install / remove / upgrade...
	- apt-cache : query
* binary-extensions
	- apt-file
* new binary
	- apt
## 2. apt vs apt-get
* apt-get, apt-cache, apt-file ...
	- 통일성 부족, 관리 어려움 → apt 권장
	- 따라서 apt-get을 사용하는 문서 → 이전에 작성된 문서


# 2. sources.list
---
## 1. apt : source list
* source list
    - apt가 package를 가져오는 곳(package 주머니?)
    	+ apt가 package file을 다운받는 곳의 정보
    	+ CD-ROM, Disk같은 로컬 주소이거나, URL일 수 있다.
* /etc/apt/sources.list
    - source.list를 직접 편집해도 되지만, 추가할 경우에 /etc/apt/sources.list.d/에 *.list 파일명으로 추가하는 편이 좋다.
* 편집시 apt edit-sources 명령어로 가능
    - 직접 vim으로 열어서 편집할 수도 있지만, 되도록이면 apt edit-sources 명령을 통해 편집하도록 하자

![Screenshot from 2022-02-24 09-21-49](https://user-images.githubusercontent.com/58837749/155477982-a665675f-37e4-4b5f-8309-946d5486b025.png)

## 2. apt : source list : ubuntu
* sources.list format
	```
	deb [ option1=value1 option2=value2 ] uri suite [component1] [component2] [...]
	deb-src [ option1=value1 option2=value2 ] uri suite [component1] [component2] [...]
	```
	- deb : 패키지
	- deb-src : 소스
	- uri : DEB 패키지를 제공하는 사이트의 URI
	- suite : distribution codename 디렉토리 이름(=dist.version). 즉, 버전의 코드 네임
		+ 16.04 = xenial, 18.04 = bionic ...
		+ https://wiki.ubuntu.com/Releases
	- component : suite의 구성 요소 및 라이센스 종류별 분류
		+ 최소 1개 이상이 컴포넌트를 지정해야 한다
		+ main : officially supported(maintained) sofrtware(ubuntu에서 packaging), free(즉, 우분투에서 관리하는 패키지)
		+ restricted : supported SW, restricted free license
		+ universe : community-maintained, not officially supported SW
		+ multiverse : non-free
		+ security : Important security updates
		+ updates : recommended updates
		+ proposed : proposed updates (i,e beta version)
		+ backports : unsupported
## 3. apt : source list : debian
* /etc/apt/source.list
    - debian 7.x의 sources.list
    ```
    # deb-src cdrom:[Debian GNU/Linux 7.5.0 _Wheezy_ ...생략 ]/ wheezy main
    # deb cdrom:[Debian GUN/Linux 7.5.0 _Wheezy_ ...생략 ]/ wheezy main

    deb http://security.debian.org/ wheezy/updates main
    deb-src http://security.debian.org/ wheezy/updates mai
    ```
    - CD-ROM을 사용하지 않는다면 comment 처리 or 삭제
## 4. apt : source list : debian : append
* source list에 URL을 추가하기 위해서는?
	- /etc/apt/sources.list를 직접 수정해도 되지만, 좋은 방법은 아니다

# 3. mirror site
---
## 1. apt : sources.list.d : debian
* kakao mirror 설정하기
    - 편집할 파일 :  /etc/apt/sources.list.d/kr.list
* 먼저 할 일
    - sudo select-editor를 실행하여 기본 에디터를 vim으로 변경
* kakao mirror 설정하기 : apt edit-sources kakao.list
    - 실제 편집되는 파일 : /etc/apt/sources.list.d/kakao.list
    ```
    $ sudo apt edit-sources kakao.list
    ...
    # kakao mirror : ubuntu 18.04 LTS
    deb http://mirror.kakao.com/ubuntu/ bionic main restricted universe
    deb http://mirror.kakao.com/ubuntu/ bionic-updates main restricted universe
    deb http://mirror.kakao.com/ubuntu/ bionic-security main restricted universe
    ```
## 2. apt : source list : update
* apt update (apt-get 사용시에도 동일)
    - source list 갱신
    	+ source list 추가/편집 or 오랜만에 작동시키는 경우
    	+ update를 하지 않으면 목록 갱신이 안 되어 제대로 작동하지 않는다.
## 3. apt : list
* apt list [options] [package pattern]
    - 패키지 목록 출력
    - options :
    	+ 옵션이 지정되지 않으면 해당 버전에서 제공되는 패키지의 최신 목록 출력
    		* 동일 패키지 중에서는 가장 최신의 패키지만 출력
    	+ --installed : 설치된 패키지 리스트
    	+ --upgradable : 업그레이드가 가능한 패키지 리스트
    	+ --all-versions : 모든 버전의 패키지 리스트
## 4. apt : search
* apt search [-n] <regex>
    - 패키지를 키워드로 검색, 키워드는 REGEX로 입력한다.
    - options:
    	+ -n : 검색 대상을 name 필드로 한정한다.
    	```
    	# apt search bash  
    	// ...name이 아닌 description에 bash가 들어간 경우까지 검색
    	# apt search -n bash
    	// ...name의 중간에 bash가 있어도 검색
    	# apt search -n '^bash'
    	// ...name의 시작 부분에 bash가 있는 경우에만 검색
    	```
## 5. apt : show
* apt show \<package name>\[=version]
    - 패키지 정보 출력
    ```		
    # apt show bash
    # apt list --all-versions bash
    # apt show bash=x.x.x-2ubuntux
    ```
## 6. apt : remove, purge, autoremove
* apt\<remove\|purge\|autoremove> \<package>\[=version]
    - remove: 패키지만 삭제 (config 파일은 남겨둔다)
    - purge : 패키지 삭제 (config도 삭제)
    	+ 간혹 설정이 꼬이는 경우는 재설치해도 config가 남아서 문제가 된다. 그럴 경우 purge로 삭제하고 재설치하는 것이 좋다.
    - autoremove : 의존성이 깨지거나 버전 관리로 인해 쓰이지 않는 패키지를 자동 제거

## 7. apt : install : unmet dependency
* 일반적으로 apt의 sources.list 설정에서 components에 universe를 빼먹는 경우.
* downgrade를 해야하는 경우
```
# apt list --all-versions libcurl3-gnutls
# apt install curl libcurl3-gbutls=x.xx.x-1ubuntux
```

# 4. aptitude
---
## 1. aptitude
* ncurses based tool
* apt-cache, apt-get 명령을 포함
## 2. aptitude : menu
* C-T : ^T : menu on/off
## 3. aptitude : upgrade
* Actions > Mark Upgradable (U)


-y 는 yes or no 물어보지 말고 그냥 할 것다
