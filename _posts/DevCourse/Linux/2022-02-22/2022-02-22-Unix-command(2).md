---
title: "[Linux Day2] Unix command(2)"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차

 
date: 2022-02-22
last_modified_at: 2022-02-22
---
<br>
<br>
# 1. Path
---

1. Path: pwd, cd
  * prw
  → print working directory

  * cd
  → change directory
```
cd [경로]
→ cd : 홈 디렉토리
→ cd / : 루트 디렉토리
→ cd ~ : 홈 디렉토리
→ cd - : 이전경로
```
<br>

2. Directory : 절대/상대 경로
  * absolute path : abs-path<br>
  → root directory(/)를 시작으로 하는 경로<br>
  → e.g. /usr/local/bin<br>

  * relative path<br>
  → 현재 디렉터리(.)를 시작으로 하는 경로(dot은 생략 가능)<br>
  → e.g. ../../tmp == ./../../tmp<br>
  → e.g. work/day1 == ./work/day1<br>

# 2. File
---

1. ls
→ list file<br>
→ ls [-altriRr] [파일명 ...]<br>
→ e.g. **ls -al**, ls -ltr, ls -i...<br>
<br>

2. file type : [-dbclps]
```
- : regular file
d : directory
l : symbolic link
```
<br>
<br>
3. ls 명령어의 예
```
a : all
l : long
t : sort by mtime(newest first)
r : reverse
----
$ ls -a
$ ls -l
$ ls -a -l == ls -al
$ ls - lt
$ ls - ltr
$ ls - li
```
<br>
<br>
4. file : UNIX file mode
  * file mode bit : UNIX의 파일 권한을 나타내는 3+9 bit 체계
  * 3+9 bit의 UNIX file mode
    - 3bit → SetUID, SetGID, Sticky bit
    - 9bit → owner, group, others
  * 표기방법
    - Symbolic mode : rmx
    - Octal mode : 8진수(실무)
<br>
<br>
5. octal mode
  * Octal mode : 8진수로 표기되는 UNIX file mode
  * rwxrwxrwx : 3칸씩 각각의 rwx = owner, group, others
    - r == 2^2(4) : readable
    - w == 2^1(2) : writable
    - x == 2^0((1)) : executable
<br>
<br>
6. UNIX file mode bit
  * directory인 경우 : readable file list, writable file, accessible
  * 파일 생성시 기본값
    - 기본 mode값은 umask값을 뺀 나머지가 된다.
    - umask가 022라면, 디렉터리는 777-022=755가 생성시 기본 mode가 된다. 
    - 파일은 666-022 = 644가 생성시 기본 mode가 된다.
  * r권한(필) → dir의 목록을 읽을 수 있다(open)
  * x권한(필) → 목록에 있는 링크에 access(access)
<br>
<br>
7. cp, mv, rm
  * cp : copy
  * mv : move, rename
  * rm : remove

  ```
  $ cp ~/.bashrc ~/bashrc_example
  $ cd; ls
  $ mv ~/bashrc_example ~./old_bashrc
  $ ll !$
  $ rm ~/old_bashrc
  ```
<br>

8. chown, chgrp
  * chown, chgrp - change owner/group
    - root 유저만 가능
```
$ chown root helloworld; ls -l helloworld
```
  * chattr - change attribute
    - 리눅스 파일 시스템의 커스텀 속성 변경
<br>

# 3. Directory
---
1. mkdir
  * make directory
  * mkdir [-p] <directory name>
  * p → 디렉토리를 여러개 만들 수 있다

2. rmdir
  * remove directory
  * 디렉토리 비어있다(팔) → rmdir [-p] <directory name>
  * 상관 없이 → rm -rf → 파일+디렉토리 삭제
    - r : recursively
    - f : force