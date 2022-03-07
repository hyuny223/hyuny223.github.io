---
title: "[Linux Day4] Linux_Admin #6-3 - Network tools(ssh,curl,wget,nc)"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-24 00:00:07
last_modified_at: 2022-03-07
---
<br>
<br>

# 1. ssh server
---
## 1. ssh?
* ssh(secure shell)는 통신 구간을 암호화
    - 암호화 되지 않은 통신을 사용하던 telnet 서비스는 deprecated
        + telnet이 하던 프로토콜 테스트 기능은 nc, curl로 대체 
    - Linux의 ssh는 openssh를 사용한다.

## 2. ssh server, ssh client
* sshd
    - ssh daemon, 즉 ssh server를 의미한다
* ssh
    - ssh client이며, ssh 명령어가 바로 ssh client CLI 유틸리티
        + Linux나 UNIX 계열에서는 따로 ssh client GUI가 제공되는 것이 아니라, 터미널에서 ssh CLI 명령어로 실행한다
        + MS 계열에서 접속하려면 putty 같은 툴을 이용

## 3. sshd : prerequisite
* sshd 서버의 준비 작업
    1. sshd 서버의 설치 여부 확인
        + sshd 서버는 기본 설치시 mandatory 항목이라서 자동 설치되는 경우가 많다. 하지만 그래도 만일을 위해서 항상 확인하자.
            ```bash
            # apt list openssh*
            # rpm -qa openssh-server
            # yum -y install openssh-server
            ``` 
    2. sshd 서비스가 실행 중인지 확인
        + LISTEN 상태도 확인 : ss -nlt or ss nltp
        + systemd 기반이라면 systemctl로 확인
            * systemctl status sshd
                ![Screenshot from 2022-03-07 13-17-43](https://user-images.githubusercontent.com/58837749/156967259-4a9274fd-db7a-4844-8db0-e69b805e8cfa.png)  
        + init 기반이라면 다음의 명령으로 확인
            * service sshd status
        + sshd 서비스가 정지된 경우
            * systemd 기반이라면 systemctl로 실행
            * init 기반 이라면 service 명령으로 확인
        + 부팅할 때 sshd 서비스가 실행되도록 하고 싶다면  
            * systemd 기반
                - systemctl enable sshd
                - enable에 --now 옵션을 사용하면 start 기능까지 수행
            * init 기반
                - RedHat : chkconfig sshd on
                - Debian : update-rc.d sshd default
    3. ssh port(22/tcp)가 방화벽에 허용되어 있는지 확인
        + ssh port(22/tcp) LISTEN 상태 확인
             ![Screenshot from 2022-03-07 13-25-25](https://user-images.githubusercontent.com/58837749/156967889-8ecd219c-b085-4ad6-b41e-d55a9255f84a.png)

             * 위는 IPv4, 아래는 IPv6
        + ssh port(22/tcp)가 방화벽에 허용되어 있는지 확인 필요
             ![Screenshot from 2022-03-07 13-29-10](https://user-images.githubusercontent.com/58837749/156968241-98090fba-9ce0-4f36-b6fd-d96cdc1f46e9.png)

             * 혹은 firewall-cmd, ufw를 이용
                    ```bash
                    # ufw -h
                    # ufw status
                    # ufw enable
                    # ufw status verbose
                    ```

# 2. ssh client
---
## 1. sshd : prerequisite
* ufw(ubuntu)
    - allow <port number | port symbol> [/protocol]
         ![Screenshot from 2022-03-07 13-32-38](https://user-images.githubusercontent.com/58837749/156968492-162f685d-c8ae-4195-951c-03cb336f6df7.png)

## 2. ssh : client
* Linux 혹은 UNIX like 시스템은 ssh 사용
    - ssh [-p port] [username@]<host address>
        + ssh 192.168.52.110
            * 접속한 유저명이 앞에 생략 
        + ssh linuxer@192.168.52.110
            * username으로 접속 
        + ssh -p 20022 192.168.52.110
            * 다른 포트로 접속 

## 3. ssh-keygen
* -N : new passphrase
    - 개인용에서는 password를 쓰지 않지만, 기업에서는 꼭 ""대신에 패스워드 사용
    - key 위치 : ~/.ssh
    - ssh-keygen -N "" : ""는 passphrase(?)를 안 쓰겠다. 공인인증서를 사용할 때 계속 비밀번호를 요구하는 것과 같은 역할
* -p
    - 암호변경
    - key 위치 : ~/.ssh
* -R <host>
    - known_host에서 remote host의 키 제거 : 기본 위치(~/.ssh)

## 4. ssh : pubkey 기반 연결
* pubkey 기반 연결 (높은 신뢰성)
    ```bash
    $ ssh-kegen -N ""
    $ ssh-copy-id username@ip
    $ ssh username@ip
    ```

    - ssh-copy-id는 remote의 ~/.ssh/authorized_keys에 복사하는 과정
        ```bash
        $ ssh -t username@ip w
        ```

        + ssh -t username@주소 w : 주소에서 이루어진 결과물을 나한테 알려달라(다른 주소임). 예를 들어 지금 주소가 1이면, 1번 주소에서, 적혀진 주소인 2번 주소에 접속해서 명령을 내리고 다시 1번 주소에 결과값을 알려달라는 것
        + ${HOME}의 권한이 700이 아니라면, 키 기반 연결이 거부된다(보안).


# 3. HTTP/others utils
---
## 1. curl
* URL을 기반으로 통신하는 기능 제공
    - CLI tool과 library를 제공
    - URL기반의 다양한 프로토콜 지원
        + DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS,  IMAP, IMAPS,  LDAP,  LDAPS,  POP3,  POP3S,  RTMP, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP
        + 기능이 굉장히 많기 때문에 man page 일독 필요
    - libcurl 라이브러리 제공 : 다양한 프로그래밍 언어에서 프로그래밍 가능
* 사용법
    ```bash
    $ curl [options] <URL>
    ```
* curl : HTTP
    - curl <URL>
        ```bash
        $ curl http://www.naver.com
        $ curl https://www.naver.com // 나오는 정보의 양질이 다르질
        $ curl -O https://www.naver.com/manual.html //manual.html 파일로 저장
        $ curl -o naver.html https://www.naver.com // URI에 파일명이 없으므로 -o 옵셕으로 직접 지정
        ```
* curl : download
    - curl -C - -O <URL>
        ```bash
        $ curl -C - -O http://blah.org/blah.iso // -C <offset> : continue-at <offset>
        ```
* curl : HTTP APT
    - curl <API URL>
        ```bash
        $ curl v2.wttr.in/Seoul
        ``` 
        
        + alias로 잡아서 쓰는 사랍이 많다
        
        ```bash
        $ curl https://api.exchangeratesapi.io/latest?base=USD
        ```

## 2. wget
* wget <URL>
    - wget과 curl은 대부분의 기능이 비슷하나, curl이 더 많은 기능을 갖는다.
        + 그러나 파일 다운로드에 특화된 기능이 존재한다 : mirroring 기능(web site 복제)

         ```bash
         $ wget http:// ... /
         ```

## 3. nc (netcat)
* network 기능이 가능한 cat
* server, client의 양쪽 기능이 가능
    - 간단한 간이 서버, 클라이언트로 사용 가능
    - 바이너리 통신 가능
        ```bash
        $ nc -k -l 5000
        $ echo Hello | nc 127.0.0.1 5000
        $ nc 127.0.0.` 5000
        ```
    ![Screenshot from 2022-03-07 14-21-34](https://user-images.githubusercontent.com/58837749/156972847-d50ed86f-112a-41ab-9615-d47c67723721.png)


---

ssh-keygen generates, manages and converts authentication keys for ssh(1).  ssh-keygen can create keys for use by SSH protocol version 2.

The type of key to be generated is specified with the -t option. If invoked without any arguments, ssh-keygen will generate an RSA key.

ssh-keygen is also used to generate groups for use in Diffie-Hellman group exchange (DH-GEX).  See the MODULI GENERATION section for details.

Finally, ssh-keygen can be used to generate and update Key Revocation Lists, and to test whether given keys have been revoked by one.  See the KEY REVOCATION LISTS section for details.

Normally each user wishing to use SSH with public key authentication runs this once to create the authentication key in home/.ssh/id_dsa, home/.ssh/id_ecdsa, home/.ssh/id_ed25519 or home/.ssh/id_rsa.  Additionally, the system administrator may use this to generate host keys.
Normally this program generates the key and asks for a file in which to store the private key.  The public key is stored in a file with the same name but “.pub” appended.  The program also asks for a passphrase.  The passphrase may be empty to indicate no passphrase (host keys must have an empty passphrase), or it may be a string of arbitrary length.  A passphrase is similar to a password, except it can be a phrase with a series of words, punctuation, numbers, whitespace, or any string of characters you want.  Good passphrases are 10-30 characters long, are not simple sentences or otherwise easily guessable (English prose has only 1-2 bits of entropy per character, and provides very bad passphrases), and contain a mix of upper and lowercase letters, numbers, and non-
alphanumeric characters.  The passphrase can be changed later by using the -p option.

There is no way to recover a lost passphrase.  If the passphrase is lost or forgotten, a new key must be generated and the corresponding public key copied to other machines.
For keys stored in the newer OpenSSH format, there is also a comment field in the key file that is only for convenience to the user to help identify the key.  The comment can tell what the key is for, or whatever is useful.  The comment is initialized to “user@host” when the key is created, but can be changed using the -c option.

After a key is generated, instructions below detail where the keys should be placed to be activated.

---

curl  is  a tool to transfer data from or to a server, using one of the supported protocols (DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS,  IMAP, IMAPS,  LDAP,  LDAPS,  POP3,  POP3S,  RTMP, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP). The command is designed to work  without user interaction.

curl offers a busload of useful tricks like proxy support, user authentication, FTP upload, HTTP post, SSL connections, cookies, file  transfer  resume,  Metalink,  and more. As you will see below, the number of features will make your head spin!

curl is powered by  libcurl  for  all  transfer-related  features.  See libcurl(3) for details.
