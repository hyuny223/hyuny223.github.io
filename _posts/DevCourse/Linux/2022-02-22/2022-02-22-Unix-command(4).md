---
title: "[Linux Day2] Unix command(4)"
categories: Linux
tag: [Linux,command]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-22 00:00:04
last_modified_at: 2022-02-22
---
<br>
<br>

# 1. stdio
---
## 1. stdio(standard input/output)
* stdio (standard Input/Output)
    - file channel : file에 입출력하기 위한 통로 : 표준화된 입출력 방식(stdio) → 가상화 레이어 → file 입출력 → C언어의 I/O 인터페이스의 심플함을 가능하게 함
* 파일 채널(file channel)
    - 메타 정보를 가지는 객체(file channel) → 파일에 입출력 
    - 프로세스 scope에서 유효 → 프로세스 종료와 함께 휘발
* 파일서술자(file descriptor) == 파일기술자
    - 파일 채널들에게 붙여진 유일한 식별자(idenrifier), 숫자
    - 양수 0번부터 시작하여 증가
    - 예약된 파일서술자
        +  0 : stdin(standard input, 표준입력) - I
        +  1 : stdout(standard output, 표준출력) - O
        +  2 : stderr(standard error, 표준에러) - O
    - [Process → fd:num → terminal or File ← fd:num ← Process]
        +  이때 "fd:num"이 file channel을 의미한다.
        +  file channel의 식별자는 오름차순으로 증가한다
        +  복수채널 → 동일파일 가능
        +  먼저 반납된 file을 후에 할당된 file channel이 연결 가능

## 2. pipe, redirection
* pipe
    - 프로세스 사이에 통신으로 사용
    - pipe의 종류 두가지
        + anonymous pipe : temporary
        + named pipe : persistency
    - 프로세스들의 직렬 연결 → ?(어떤 목적?)
    - 명령행에서 Vertical bar (\|)로 사용 → single thread와 같은 방식
    ```
    $ find ~ | wc -l  // anonymous pipe의 용례 → stdout과 stdin과 연결
    $ find ~ > tmp.txt; wc -l < tmp.txt; rm tmp.txt // anonymous pipe를 사용하지 않을 때 사용하는 방법
    ```
    - wc
        + word count : 워드, 바이트, 라인 수등 다양한 기능을 제공
        + -l 옵션 사용시 line 수를 카운트
    - anonymous pipe(nameless pipe, unnamed pipe)
        + 임시로 생성되었다가 소멸되는 파이프()= 익명파이프)
    - named pipe
        + path+filename을 명명 → 명명된 파이프
* redirection(방향재지정)
    - 채널의 방향을 다른 곳으로 연결
        + A > B : A의 stdout을 파일 B로 연결 (저장)
        ```
        $ ls -a > ls.txt  // ls -a가 출력하는 정보를 ls.txt로 만들라는 의미
        ```
        + A < B : A의 stdin을 파일 B로 연결 (적용?)
        ```
        $ sort < names.txt  // sort라는 입력을 ls.txt에 적용
        ```
        + A >> B : 방향은 >과 같고, 추가하는 모드 (append)
        ```
        $ strace ls 2> strace.txt  // 2>는 2번 파일서술자를 파일로 연결하는 명령. 즉, fd2인 stderr의 출력을 파일로 저장하는 것
        ```
* cat
    - stdout과 파일을 자유롭게 연결해주는 기본 필터
    - cat → 파일의 내용을 stdout으로 출력
    - stdin → redirection → stdout
    ```
    $ cat ~/.bashrc
    $ cat > hello.txt
    Hello world
    ^D
    ```

# 2. archiving and compression
---
## 1.  archive, compress
* UNIX는 archive, compress이 분리되어 있음
* 아카이브 유틸 : tar(tape archive), cipo
    - archive → 단순히 파일을 묶는 작업
    - UNIX의 계열때문에 tar와 cpio가 분리되어 있음
* 압축 유틸 : gzip, bzip2, xz, zstd, lz4
    - 압축, 압축해제
    - 압축률은 xz > bzip2, zstd > gzip > lz4

## 2.  아카이빙 : tar
* tar [ctxv] [f archive-file] files...
    - c(create) : 아카이브를 생성
    - t(test) : 아카이브를 테스트
    - x(extract) : 아카이브로부터 파일을 풀어냄
    - v(verbose) : 상세한 정보 출력(실무사용X) → 디버깅이나 확인 용도로만 사용(쓰지 마셈)
    - f archive-file : 입출력할 아카이브 파일명
    - --exclude file : 특정파일 배제
    ```
    $ tar c *.c > arc_c.tar  
    // create → c언어 파일을 모두 아카이빙 → arc_c.tar라는 파일로 redirection
    $ tar cf arc_c.tar *.c
    // create file arc_c.tar ← *.c를 아카이빙
    // 이 두 명령은 동일한 결과물을 생성
    ```

## 3.  gzip, xz, bzip2, zstd
* 압축 프로그램의 발전

| 프로그램 | 확장자 | 설명 |
|---|---|---|
| compress | Z | 초기 유닉스에서나 쓰임 |
| gzip | gz | GNU에서 만든 zip |
| bzip2 | bz2 | 과거에 텍스트 압축에 유리 |
| xz | xz | 텍스트 압축에 압도적으로 강하나, 느리다 |
| lz4 | lz4 | 빠르고 무난한 성능(zstd로 인해 입지 약해짐) |
| zstd | zst | 멀티스레드 지원, 빠르고 중상의 압축률 |

* compress, gzip, bz2, zip → xz, zstd
* gzip
    - gzip [-cdflrv] \<file ...\> 
        + -d(decompress) : 압축해제
        + -c(stdout) : 표준 출력(stdout)으로 결과물을 보냄
        + -l, -9 : (fast, better) 압축 레벨 지정
    - e.g. tar와 gzip을 함께 사용하는 고전적 방법
    ```
    $ tar c /etc/*.conf | gzip -c > etc.tar.gz(압축)
    $ gzip -cd etc.tar.gz | tar x  // tar xv(verbose) 사용하지 않기
    ```
* bzip2, xz
    - bzip2 [-cdfv] \<file ...\>
        + 옵션은 gzip과 동일
    - xz도 gzip, bzip2와 옵션 및 사용법이 같다
        + -T# : 멀티스레드 사용 (#는 개수, 0은 자동 판단)
    - compress : ncompress 패키지에 포함됨
        + 유닉스의 오래된 압축 유틸리티(사용 X)
* zstd
    - zstd [OPTIONS] [-\|input-file] [-o output-file]
        + 옵션은 xz와 동일 (멀티스레드 옵션까지)
        + 추가옵션
            * -# : compression level [1-19] (default: 3)