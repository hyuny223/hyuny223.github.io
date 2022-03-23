---
title: "[Linux Day2] Unix command(3)"
categories: dev_Linux
tag: [Linux,command]

toc: true
toc_sticky: true
toc_label : 목차

 
date: 2022-02-22 00:00:03
last_modified_at: 2022-02-22
---
<br>
<br>

# 1. file, stat
---
## 1. file
* file \<file\>
* file → 파일의 타입 확인
    - 파일의 고유 표식 → 파일 종류 분류
    - 이에 UNIX 계열에서 파일의 확장자는 중요하지 않다.
    - magic 데이터 : /usr/share/file/magic

## 2. stat
* stat [option] \<file\>
* status of file
    - file의 meta data를 출력한다.
    - meta date : 내용이 아닌 수식하는 정보. 예를 들어, 파일이름, 생성시간, 권한 등등
    - mtime(modification time) : file의 data(내용)가 변경된 시간
    - ctime(change time) : file의 meta-data(형식)가 변경된 시간
* 예시
```
$ cp ~/.bashrc ~/old_bashrc
$ stat <ALT-.> //이게 왜 안먹지?
$ mv ~/old_bashrc ~/old_bashrc2
```

# 2. touch
---
## 1. touch
* touch \<file\>
    - 파일의 메타 정보 업데이트 : 주로 시간 관련 정보를 업데이트
    - file이 존재하지 않는 경우 빈 파일의 생성

# 3. find
---
## 1. find
* find directory [expression]
```
$ mkdir /tmp; cd !&
# for i in {8,21}; do dd bs=100000 count=$i \
if=/dev/zero of =./${i}00k.dat; done
...
$ find . -name "[89]*k.dat"  // .은 home directory, -name은 이름 지정 8 또는 9, *는 모든 이름
...
$ find . -name "*k.dat" -a -size 1M  // 여기서 -a(생략가능)는 AND, -o(생략불가)는 OR
./800k.dat
./900k.dat
./1000k.dat
```
* find 사용 예시
```
$ find . -name "*.dat" [-a] -size +1500k
./1600k.dat
./1700k.dat
./1800k.dat
./1900k.dat
./2000k.dat
./2100k.dat
$ find . -name "*k.dat" -size +1500k -size -1800k
./1600k.dat
./1700k.dat
./1800k.dat
$ find . -mtime -l -size +1M
```
* invalid expression
```
$ find . -name *.txt  //쉘의 wildcard 해석 기능으로 인해 해석 변경
find: paths must precede expression
Usage: find [-H] [-L] [-P] [Path...] [expression]
```
따라서 다음과 같이 나타내준다.
```
$ find . -name "*.txt"  //single or double quotation
```
* 검색 후 작업지시
```
find ... -exec 명령어 \; → 반복마다 실행 → (+) 제한X & (-) 효율성↓
find ... -exec 명령어 \; → 수집 후 실행 → (+) 효율성↑ & (-) 제한O
...
$ find . -name "*.tmp" -exec rm {} \;  // 괄호에 find한 파일들을 위치시킨다
$ find . -name "*.tmp" -exec rm -rf {} \;  // 디렉토리까지 함깨 지우기 위해서 rm -rf 사용
// rm a.tmp; rm b.tmp; rm.c.tmp와 같은 방식으로 작동
$ find . -name "*.tmp" -exec rm -rf {} \+
// rm a.tmp b.tmp c.tmp;
```
* maxdepth와 같은 조건은 항상 맨 앞에 나와야한다.
```
find ./ -maxdepth 3 ... -exec cp {} ~/backup \;
```
<br>