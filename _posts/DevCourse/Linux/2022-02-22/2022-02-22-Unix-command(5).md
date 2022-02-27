---
title: "[Linux Day2] Unix command(5)"
categories: Linux
tag: [Linux,command]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-22 00:00:05
last_modified_at: 2022-02-22
---
<br>
<br>

# 1. Link : Hard Link, Symbolic Link
---
## 1. file : ln(link)
* ln - make link
    - 하드 링크(hard link) : File #1과 File #2는 분리된 파일처럼 보이지만, 실제로는 hard link로서 같은 i-node를 가리키는 동일 파일 (reference 느낌)
    - 심볼릭 링크(symbolic link) == symlink(-s 옵션 사용) : symlink는 완전히 다른 파일로, 단지 특정 파일의 위치를 가리키는 기능을 하고 있음 (pointer 느낌)
    - 심볼릭 링크 → 하드 링크 → 숫자 관리 → 매개체 관리(i-node) → 특정 저장 공간 접근

# 2. i-node
---
## 1. i-node
* 파일의 메타 정보 및 관리용 객체 → 파일 접근
    - 파일은 고유의 i-node를 1개 갖고 있다.
    - i-node는 disk partition(or volume)내에서 유일한 식별자(identifier)
    - 동일 partition or 동일 volume → i-node number
* file meta info

## 2. ln (link) : hard vs symbolic
* hard link 생성
    - 동일 파티션 내(필) → 동일 i-node
    - regular file(필) → hard link
        + regular : mp4, pdf, txt 등의 파일
        + non-regular : directory나 device file 등 → hard link 생성 X
        + 실체를 가진 파일
* symbolic link 생성 (abbrebiation : symlink)
    - 위치(path)만 가리키므로 다른 파티션, 모든 종류의 file에 만들 수 있다.
    - 가리키는 대상의 UNIX file mode = symlink UNIX file mode(777)

# 3. which
---
## 1. which
* PATH에 존재하는 파일을 검색
```
$ whick find
/usr/bin/find
$ which bash
/bin/bash
$ which tcpdump
/bin/sbin/tcpdump
```

# 4. readlink, canonical path
---
## 1. Symlink가 여러 단계로 가리키는 파일이 있을 수 있다.
* symlink_A가 symlink_B를 가리키고, symlink_B가 symlink_C를 가리키고, 결국 마지막 대상은 누군지 애매할 수 있다
* 이를 해결하기 위한 방법으로 symlink의 canonical path를 따라가는 기능이 있다.
    - readlink -f <symlink> : 1 ~ 10이라면, 9까지는 존재해야
    - readlink -e <symlink> : 1 ~ 10이라면, 10까지 모두 존재해야
* canonicalization
    - 대용적, 상대적 표현 & canonicalization → official 의미 결정
