---
title: "[Linux Day5] bash 기초 #11-b : REGEX(regular expression)"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-25 00:00:11
last_modified_at: 2022-02-26
---
<br>
<br>

# 1. REGEX syntax
---
## 1. POSIX REGEX : meta char.

|문자지정|.|임의의 문자 한 개를 의미|
|반복지정|?|선행문자패턴이 0개 혹은 1개 나타납니다 - ERE|
||+|선행문자패턴이 1개 이상 반복됩니다 - ERE|
||*|선행문자패턴이 0개 이상 반복됩니다|
||{m,n}|(interval) 반복수를 직접 지정할 수 있습니다 - ERE|
|||M이나 n중 하나를 생략할 수 있다|
|||예) {3}:3번 반복, {,7}:7번 이하, {2,5}: 2-5번 반복|
|위치지정|^|라인의 앞부분을 의미|
||$|라인의 끝부분을 의미|
|그룹지정|[...]|안에 지정된 문자들 그룹 중에 한 문자를 지정|
||[^...]|안에 지정된 그룹의 문자를 제외한 나머지(여집합)를 지정|
|기타|backslash|(escape) 메타의 의미를 없애줌|
||\||(alternation,choie) OR연산을 함 - ERE|
||()|괄호는 패턴을 그룹화 및 백레퍼런스의 작동 - ERE|

* POSIX RE - IEEE std 1003.1 (Internation standar
* ERE - Extended Regular Expression(ERE를 사용하면 약간의 속도 저하가 발생하는 플랫폼도 있으나 큰 차이는 없다)

## 2. Any single character
* dot/preiod : . - any single character
	- c.b : cab, cbb, ccb, cdb, clb, c2b 등
	- a..b : axyb, a12b, ax0b, a#-b 등
	- a...........b : 이런 방식으로는 쓰지 않음 → quantifier, interval
		![Screenshot from 2022-02-26 09-27-05](https://user-images.githubusercontent.com/58837749/156980993-b848dbd2-4bec-43bf-9261-f0f9c51217f8.png)
        + var3이라는 변수에 위와 같은 값을 대입
        + 예제는 bash shell prompt에서 실행하는 것으로 가정
        + 예제 타이핑시 맨 앞의 $문자는 프롬프트이므로 타이핑하지 않는다.
        + \는 escape
## 3. Quantifier(수량자)
* ?, +, *, {m,n} - 수량자의 종류는 총 네가지
	- 수량자는 선행문자패턴(or atom이라고도 함)을 수식하는 기능을 가진다
* 아래 패턴의 의미를 생각해보자
	- X?ML : atom - X
	- can* : atom - n
	- can+ : atom - n
	- http.* : atom - dot
		+ ?: question mark
		+ *: asterisk. star
		+ +: plus sign
		+ {}: (curly) braces
	- X?ML : XML or ML (?수량자가 수식하는 atom은 X)
		+ ?앞 문자인 X가 0개 혹은 1개 존재
	- can* : ca, can, cann, cannn, ...
		+ 앞 문자인 n이 0개 이상 존재
	- can+ : can, cann, cannn, cannnn, ...
		+ 앞 문자인 n이 1개 이상 존재
	- http.* : http://, httpd, https, http1234 ...
		+ "http"뒤에 어떤 문자도 붙을 수 혹은 붙지 않을 수 있다
* 참고로 *수량자만 BRE다. 나머지는 ERE
* {m,n} - interval expression
	- abc{2,5} : abcc, abccc, abcccc, abccccc
	- {n}, {n,}, {n,m}은 표준이고, {,m}은 GNU extension
	- 연습 : ?, *, +를 { }로 표현하면?
		+ {0,}는 무엇일까?
		+ {,1}은 무엇일까?
		+ {1,}는 무엇일까?


## 4. Quantifiter : BRE vs ERE
* grep은 기본값으로 BRE로 작동
    - \* 수량자만 바로 사용 가능(얘만 BRE)
    - +, ?, {} 패턴은 \를 앞에 더해주어야 한다(얘는 ERE)
    - BRE tool & backslash → ERE(BRE tool에서 ERE를 사용하기 위해서는 \가 필요조건)
    - ERE에서는 BRE든, ERE든 그냥 사용하면 됨
* egrep은 기본값으로 ERE로 작동


## 5. Anchor
* ^,$ - 패턴의 위치를 지정하는 패턴
    - ^ftp: "ftp"로 시작하는 행
    - ^$ : 비어있는 행(행의 시작과 끝에 아무런 문자도 없다)
    - \<BR>$ : \<BR>로 끝나는 경우
* 간혹 라인 단위 처리를 하지 않을 경우, 즉 개행문자(newline)단위로 처리하지 않을 경우에는, $는 문서의 끝을 의미
    - ^: caret  
    - $: doolar sign

## 6. Character set
* \[  \]. [^  ] - character class
    - [abcd] : a,b,c,d 중에 하나
    - [0-9] : 0,1,2,3, ... ,9
    - [a-zA-Z0-9] : 대소문자 알파벳과 숫자
    	+ ASCII 기준
    - [^0-9] : [0-9]를 제외한 나머지
    	+ ^은 여집합
    - ^ 문자 자체를 그룹에 넣기 위해선?
    	+ ^이 [ 바로 뒤에만 오지 않으면 된다.
    		* 즉, 맨앞 금지
    		* [0-9^]
    	+ 혹은 escape

## 7. Practice : #1-a : POSIX BRE
* REGEX로 단어를 검색해보자
	```bash
	$ grep --color "p[abcd]\+n" /usr/share/dict/words
	```
	- BRE이기에 + 수량자를 사용할 경우 back-slash 필요
	![Screenshot from 2022-02-26 10-12-11](https://user-images.githubusercontent.com/58837749/156982650-9c8f31a8-d6fd-40da-a620-f580033695f6.png)

* 패턴 해석
	- p가 등장하고
	- [a-d]중 적어도 1번 이상 등장
	- n이 등장


## 8. Practice : #1-b : POSIX BRE
* anchor를 이용하여 패턴을 수정
	![Screenshot from 2022-02-26 10-11-46](https://user-images.githubusercontent.com/58837749/156982649-a805bf16-90cd-42d5-9b5d-f8143a528a60.png)

* 패턴 해석
    - p가 등장
    - [a-d]중 적어도 1번 이상 등장
    - n이 매칭의 끝부분에 등장

## 9. Tip! - grep: line controsl options
* -A NUM, --after-context=NUM
	- Print NUM lines of trailing context after matching lines
* -B NUM, --before-context=NUM
	- Print NUM lines of leading context before matching lines
* -C NUM, -NUM, --context=NUM
	- Print NUM lines of output context
		+ e.g. -C 1 equal to -A 1 -B 1 
* --group-separator=SEP
	- Use SEP as a group separator. By default SEP is double hyphen (--).
* --no-group-separator
	- Use empty string as a group separator