---
title: "[Linux Day2] Unix command(6)"
categories: Linux
tag: [Linux,command]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-22 00:00:06
last_modified_at: 2022-02-23
---
<br>
<br>

# 1. ps, pgrep
---
## 1. ps
* ps : process status
![Screenshot from 2022-02-23 08-55-45](https://user-images.githubusercontent.com/58837749/155239778-ef3067b3-bede-4829-a508-7dc5604d4ad6.png)
    - ps는 기본적으로 현재 세션의 프로세스를 보여준다.
        + PID : 프로세스 ID
        + TTY : terminal ID
        + TIME : CPU 시간 (누적 시간) = CPU를 점유했던 시간의 누적값
            * 현실 세계의 시간이 아니다. 현실 세계의 시간은 ETIME이란 다른 항목이 존재 
        + CMD : command = 프로세스 이름(argv[0]를 의미)
            * ps : all processes
    ![Screenshot from 2022-02-23 09-01-03](https://user-images.githubusercontent.com/58837749/155240256-5e60758b-321c-47cb-af40-5223cc5dfbc0.png)
    - -e : select all processes
    - -a : select all processes except both session leaders and processes not associated with a terminal 
* ps : full-format
![Screenshot from 2022-02-23 09-02-30](https://user-images.githubusercontent.com/58837749/155240447-ba834887-1e8b-4eaa-936e-378697aef4c4.png)
    - UID : 해당 프로세스의 소유권자 ID (User ID)
        + 숫자인 경우에는 UID, 심볼인 경우에는 Username
    - PID : 프로세스 ID
    - PPID : 부모 프로세스 ID (Parent PID)
    - C : CPU 사용량
    - STIME : 프로세스를 시작한 시간 (시:분)
    - TTY : terminal ID
    - TIME : CPU 시간 (누적 시간) = CPU를 점유했던 시간의 누적값
* ps : long-format
![Screenshot from 2022-02-23 09-07-09](https://user-images.githubusercontent.com/58837749/155240896-b96b147d-a116-40d1-a396-38431a038d89.png)
    - F : 프로세스 플래그
    - S : 상태 코드(state code)
    - PRI : 실시간 우선순위(realtime priority)
        +  Realtime scheduling에서 사용하는 우선순위
    - NI : 나이스 우선 순위(nice)
        + 사용 가능 범위 : -20 ~ 19 (음수일수록 우선 순위가 높다. default = 0)
    - SZ : 사용되는 프로세스 코어 이미지의 메모리 크기 (KB)
    - C : CPU Usages → 현재 사용량이 아닌 누적된 수치의 퍼센트
        + (CPU time / Elapsed time) * 100
            * ps : all, long, full
    - ps -el : all, long
    - ps -ef : all, full
    - ps -ej : all, jobs (pid,pgid, sid, tty) 
    - ps -eo (중급자)
* ps : BSD, SysV style
    - ps는 UNIX 표준화 이후 두가지 옵션을 지원
        + ps axf : BSD
        + ps -e --forest : UNIX SysV 
    - man ps 명령으로 확인 가능 
    ![Screenshot from 2022-02-23 09-13-17](https://user-images.githubusercontent.com/58837749/155241480-80855ad3-614d-478d-a965-2ba95643c84c.png)

* Practice : ps
![Screenshot from 2022-02-23 09-15-30](https://user-images.githubusercontent.com/58837749/155241754-0aff5636-e49b-433f-84c9-32ddb978575b.png)
    - grep : 어떤 단어가 들어간 행만 뽑아줘
    - -bash : login shell
    - bash : non-login shell
        + X windowㅔ서 terminal 창으로 열은 경우는 non-login shell이다.  


# 2. Process control #1
---
## 1. kill
* kill 명령어(or 함수)는 프로세스를 죽이는 명령인가?
    - 실제로는 프로세스에 시그널을 "send"하는 기능
    - 시그널에 따라서 프로세스를 continue하거나 stop등 다양한 기능이 존재
    - 사용 가능한 signal 종류는 kill -l 명령으로 확인 가능
    ![Screenshot from 2022-02-23 09-21-56](https://user-images.githubusercontent.com/58837749/155242239-177a031a-3cb9-4679-881a-38f4f7bf62b2.png)

* kill UNIX signals
    - SIGHUP : Hang Up (연결이 끊어졌을 때 발생)
    - SIGINT : Interrupt <CTRL-C>
    - SIGQUIT : Quit <CTRL-\> (문제발생 → Core Dump → Error 해결)
    - SIGKILL : kill (강제로 죽임, 최후의 수단)
    - SIGSEGV : Segment violation
    - SIGTERM : Terminate (Killed 하라고 요청)
    - SIGSTP : Temporary Stop <CTRL-Z> (잠시 멈출 필요가 있을 때(다른 일 할 때))

    |명령어 예|설명|
    |---|---|
    |kill 13011|PID 13011 프로세스에 SIGTERM 시그널을 보냄|
    |kill -QUIT 13013|PID 13013 프로세스에 SIGOUT 시그널을 보냄|
    |kill -9 13012|PID 13012 프로세스에 9번 시그널(SIGKILL)을 보냄|

* Practice : ps, kill
    ![Screenshot from 2022-02-23 09-32-05](https://user-images.githubusercontent.com/58837749/155243192-8e5ab57c-0616-4a1c-82c7-fb82fbfbf9ec.png)
    ![Screenshot from 2022-02-23 09-32-45](https://user-images.githubusercontent.com/58837749/155243194-53472821-3e68-4c83-928e-2a4a32b3a379.png)


# 3. Process control #2
---
## 1. Job control : fore/back-ground process
* foreground process
    - 현재 session에서 제어터미널(controlling terminal)을 가진 프로세스
* background process
    - 현재 session에서 제어터미널(controlling terminal)을 잃은 프로세스
* CTRL-Z
    - SIGTSTP(Signal - Temporary Stop) 시그널을 foreground 프로세스에 전달
    - 작동: 잠시 정지 → background에 stopped 상태로 내려가게 됨
    - fore/back-ground process 설명시 session, controlling terminal 단어가 언급되어야 함

## 2. job control : session,controlling terminal
* 세션(session)
    - 세션은 멀티유저 시스템에서 통신 객체(seat or remote)를 구별하기 위함
        + 세션은 제어 터미널(Contolling terminal을 가질 수 있다)
        + session(필) → Controlling terminal(충)
        + Terminal 작업 공간(session) get → Terminal 작업 → System 접근
    - SID(Session ID) == PID가 같은 프로세스는 Session Leader
        + 세션 리더는 Process Group leader를 겸한다
        + 세션 리더로부터 파생된 자식 프로세스(child process)는 모두 같은 세션을 갖는다
            * 즉, 자식 프로세스의 필요조건은 부모 프로세스 
* session에 속한 controlling terminal을 소유할 수 있다는 의미 == foreground process
* logout시 세션이 파괴되면서 세션에 속한 프로세스들은 모두 종료 
    - 참고로 POSIX C API의 setsid 함수로 new session을 열 수 있다
* Controlling terminal
    - 제어터미널(controlling terminal)
        + 사용자의 제어(e.g. 키보드 입력)를 받는 터미널 장치
        + controlling terminal → CUI(console User Interface)에서 멀티 태스킹(여러 입력) 제어
        + 제어터미널을 소유한 프로세스는 키보드 입력을 가진다.
            * 이런 프로세스를 fore-ground process라고 부른다
        +  1session 1fore-ground process  
    -  제어터미널 규격
        + ps 명령어 출력에 TTY(terminal ID) 필드 부분을 보면 두가지 방식으로 출력
            * pts/# : UNIX98 Pseudo terminal system (가짜 터미널)
            * tty# : console terminal (진짜 터미널)
    - Session에서 제어 터미널을 갖지 않는 경우
        + ps의 TTY 필드에 ?로 나타난다.
        + ps -e \| less
        ![Screenshot from 2022-02-23 10-00-51](https://user-images.githubusercontent.com/58837749/155245564-c5786608-cbac-4724-b3d8-291ab50cea88.png)

    - [내부에서 만들어진 공간 → background process] & session → ~제어터미널 → 격리 / 분리된 공간

* Process Group
    - Process Group → 파생된 process 챶기
    - Process group leader : ProcessGroup ID == PID
        + \* 프로세스 그룹 리더는 시그널 전파 기능을 갖는다.
        + kill에서 음수의 PID 번호는 프로세스 그룹에 시그널을 전파하는 것
        + e.g. kill -USRI -14901
    * 프로세스 그룹 리더 : -14901 → 자식 프로세스도 -14901

* Daemon → 백그라운드에서 시스템 관련 작업
    - Orphan process(and Session leader)
    - stdio를 모두 /dev/null로 리다이렉션
        + 즉, stdio를 비활성화. 입력을 차단
        + 힌트 : SIGTTIN, SIGTTOU
    - control terminal을 갖지 않는(TTY: ?)

## 3. interrupt key
* CTRL-C
    - SIGINT(Interrupt Signal) 시그널을 foreground 프로세스에 전달
    - 작동 : SIGINT를 받는 프로세스가 프로세스 그룹 리더라면 프로세스 그룹에 속한 모든 프로세스에게 시그널이 전파됨 = 파생된(forked) 자식 프로세스도 전부 종류
* CTRL-\\\
    - SIGQUIT(Quit Signal) 시그널을 foreground 프로세스에 전달
    - 작동 : CTRL-C와 같으나 core dump가 생성됨
    - 프로세스 그룹 리더에게 전달되면 전파 
* CTRL-Z

## 4. command
* jobs : stopped, background process의 작업 리스트 출력
* fg %# : background의 지정된 프로세스를 foreground로 불러올 때
    - #에는 jobs의 작업번호, #는 생략가능
* bg %# : 백그라운드로 넘어갈 때 정지된 상태로 넘어감 → bg → 다시 running 상태로
* command & : command를 background에서 running 상태로 실행

## 5. Practice
![Screenshot from 2022-02-23 10-26-04](https://user-images.githubusercontent.com/58837749/155247529-ea97a20b-ccc5-4173-b2c0-faa805a31cd4.png)

    - bg %3을 사용하면 foreground로 나오지 않아도 계속 sh파일을 running
    - foreground로 데려와서 종료시키거나 kill을 사용해야 종료 가능