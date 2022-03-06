---
title: "[Linux Day5] bash 기초 #11-e : Etc"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-25 00:00:07
last_modified_at: 2022-02-26
---
<br>
<br>

# 1. Etc#1
---
## 1. Boundary - ERE
* word 경계 검색에 사용

|\b|boundary가 맞는 표현식만 찾는다(단어 경계면 검색)|
|\B|boundary에 맞지 않는 표현식만 찾는다(단어 경계면이 아닌 경우만 검색|

![Screenshot from 2022-02-26 20-57-13](https://user-images.githubusercontent.com/58837749/155842284-cf02bb5c-8038-4e57-93db-e1c59968411e.png)

## 2. Predefined character class

|클래스|설명|PCRE Set|
|---|---|---|
|a|a|a|

- cntrl 은 pcre에서만 쓰이기에 반드시 외워놓을 
- [...]안에 조합 가능
![Screenshot from 2022-02-26 21-06-37](https://user-images.githubusercontent.com/58837749/155842562-4e322323-3cb0-43ea-a39a-297c5c9ecbcb.png)


## 3. REGEX and PCRE
* POSIX REGEX
	- 간단한 패턴 매칭에 사용
	- 패턴의 복잡함↑ → 성능↓
	- 처음엔 POSIX REGEX부터 학습 필요(Standard)
* PCRE 
	- Perl에서 파생된 확장된 정규표현식
	- 매우 빠른 속도, 확장된 표현식
	- C, C++, 기타 대부분의 언어가 지원(추가 라이브러리로 제공)
	- 실무라면 PCRE를 사용하는 편이 낫다

## 4. Practice
* IPv4 address를 REGEX로 표현하면?
```
[0-9]{,3}\.[0-9]{,3}\.[0-9]{,3}\.[0-9]{,3}
// 틀린 케이스
// 333.469.789.1의 경우도 매칭하기 때문
```
* IPv4주소는 각 요소가 0~255(0xff)사이의 값만을 가져야 한다
* 두자리 이상의 숫자 범위에 대해 정의하려면?
	- Hint: 0~63까지 표현하기 위해선
	```
	(6[0-3]|[5-1][0-9]|[0-9])
	// 63~60 또는 59~10 또는 9~0
	```
<br>

## 5. Practice : Hangul, Hanja
* i18n을 만족하는 Linux는 UTF-8 한글 처리도 가능
* 추후작성

## 6. Practice : Hangul class
* 추후 작성

## 7. REGEX tester
* Web-based REGEX tester
	- https://regexr.com/
	- https://www.regextester.com/
	- https://regex101.com/
	<br>

# 2. Etc#2
---
## 1. Glob
* UNIX 혹은 linux 명령행에서 파일명 패턴에 쓰이는 문자열
	- wildcard를 사용하는 파일명이 glob에 해당
	- https://en.m.wikipedia.org/wiki/Glob(programming)
* REGEX와 비슷하지만 REGEX는 아니다
	- REGEX와 Glob를 혼동하는 경우가 많으니 주의
* man 7 glob에서 확인
* glob tester
	- http://www.globtester.com/

## 2. backtracking
* Greedy matching 속성으로 인해 역탐색을 하는 행위
	- Greedy matching → backtracking → 성능↓
	- 따라서 성능이 매우 중요하고 filter같이 빈번하게 regex test를 하는 구조라면 backtracking을 제거하는 것이 좋다(즉 greedy matching의 최소화)
* Log 메세지를 이용한 backtracking 확인
	- regex101.com에 접속
		+ REGEX에 아래 문자열을 삽입
		```
		\(.*\) \(.*\) \(.*\) \(.*\) \(.*\): \(.*\)
		```
		+ Test string에는 아래 문자열을 넣는다.
		```
		Aug 24 02:22:13 localhost.localdomain kernel: IPv6: ADDRCONF(NETDEV-UP): wlp4s0: link is not ready
		```
* 즉, 백트래킹의 최소화를 위해선 여러 방법이 존재
	- .\* 혹은 .\+과 같이 greedy한 표현식을 남발하면 안 된다.
		+ 특히 dot은 되도록이면 쓰지 않거나, 꼭 써야 한다면 lazy quantifier를 사용하는 것이 좋다
			* i.e. : .*? or .+?
	- 공백을 반복할 수 있는 표현식을 피한다
* backtracking으로 손실되는 성능은?
	- 복잡도에 따라 다르지만 일반적으로 non-greedy한 표현식보다 100~1000%의 성능 하락이 있다.
	
