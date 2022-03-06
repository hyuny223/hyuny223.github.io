---
title: "[Linux Day5] bash 기초 #11-a : REGEX introduction"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-25 00:00:10
last_modified_at: 2022-02-25
---
<br>
<br>

# 0. REGEX(Regular Expression)
---
1. 배울 것들
	* Regular Expression의 약칭 REGEX
	* string pattern은 문자열의 조합되는 규칙(예약어)
	* meta character는 다른 의미를 수식하는 문자
	* grep은 정규식을 평가할 수 있는 유틸리티
		- egrep, fgrep은 grep의 특화된 버전
	* sed는 스트림 에디터
	* awk는 패턴식을 다룰 수 있는 언어툴
2. String, pattern
	* 문자열 중에는 일정한 규칙이 존재하는 경우가 있다.
		- e-mail 주소의 경우
			+ 중간에 @문자가 등장
			+ @문자의 오른쪽은 dot과 영문, 아스키코드로 이루어짐. 왼쪽은 계정명
		- Web URL의 경우
			+ http://로 시작
			+ 호스트 이름 뒤에는 URI가 붙고, 디렉토리 구조로 명명
			+ CGI 기법이 사용될 경우에 ?이 등장할 수도 있음
		- IPv4 주소의 경우
			+ 111.222.111.222의 4개의 숫자(2진)로 이루어져 있다.
3. REGEX의 예시
	* 이상해 보이지만 배우고나면 별 것 아니다
	```
	a.cdef?
	[a-zA-Z]+
	.*boy
	(caret|dollar)
	\(.*/\)[^/]*
	^Do.*\?$
	http://\([a-zA-Z0-9.-]\)/.*
	https?://.*\(.*)
	```
4. REGEX : POSIX, PCRE
	* REGEX에는 여러 변종이 있지만 가장 유명한 것은 아래 두 가지
		- POSIX REGEX
			+ UNIX 계열 표준 정규표현식
		- PCRE(Perl Compatible Regular Expression)
			+ Perl 정규표현식 호환으로 확장된 기능을 갖고 있다
5. REGEX : POSIX REGEX
	* POSIX REGEX
		- UNIX계열 표준 정규표현식 : POSIX 표준
		- BRE(Basic RE),ERE(Extended RE)가 있다.
	* 기능도 적은데 꼭 POSIX REGEX를 배워야 하나?
		- POSIX REGEX부터 배워야 다른 변종 REGEX를 접할 때 혼란을 줄일 수 있다

6. REGEX : POSIX RE : BRE, ERE
	* POSIX REGEX에서 제공되는 두가지 기법
		- BRE : Basic REGEX
			+ grep이 작동되는 기본값
		- ERE : Extended REGEX
			+ 좀 더 많은 표현식과 편의성 제고
				* 다음에 나올 meta charcter중에 ERE라고 적혀있는 것을 의미
			+ egrep의 기본값
			+ POSIX ERE를 기준으로 배워두는 것이 초반에 혼동을 줄일 수 있다.
7. REGEX : PCRE
	* Perl Compatible Regular Expression
		- Perl에서 제공되던 REGEX의 기능이 매우 훌륭하여
			+ 이를 다른 언어에서도 제공하기 위해 만들어진 기능
			+ C언어 기반으로 시작
			+ POSIX REGEX에 비해 좀 더 성능이 좋다
		- 현재는 PCRE2 버전을 사용
			+ 과거 PCRE에서 사용되던 pcregrep은 pcre2grep으로 버전업
8. REGEX and EBNF(Extended Backus-Naur Form)
	* REGEX와 EBNF 문법
		- 특히 *, +, ?, [...]는 EBNF의 영향이 크다
			+ EBNF를 포멀한 형태로 패턴화
			+ 즉 EBNF를 알고 있다면 학습이 쉬워진다
	* EBNF는 필수로 알아야 한다
		- XML같은 마크업 언어 및 대부분의 설정 포맷은 EBNF를 기준으로 작성
		- 예를 들어 sudo의 설정인 sudoers도 EBNF 포맷을 사용

# 2. grep
---
1. REGEX : command line utility
	* grep (global regular expression print)
		- 유닉스에서 가장 기본적인 REGEX 평가 유틸리티
	* sed
		- stream editor로서 REGEX 기능을 일부 탑재하고 있다
	* awk
		- REGEX + 문자열 관련의 방대한 기능을 가진 프로그래밍 언어
		- 제일 많은 기능
	* grep >>> sed >>> awk 순으로 공부하는 것이 좋다
2. grep : matcher selection
	* grep 실행시 matcher를 고를 수 있다.
		- '-G' : BRE를 사용하여 작동(디폴트)
		- '-E' : ERE를 사용하여 작동. egrep으로 작동시킨 것과 같다
		- '-P' : PCRE를 사용하여 작동. pcre2grep으로 작동시킨 것과 같다
		- '-F' : 고정길이 문자열을 탐색하는 모드로 작동. fgrep과 같다.
			+ 속도가 빠르다
3. grep : options
	* grep의 주요 옵션
		- '--color'
			+ Surround the matched (non-empty) strings
		- '-o'
			+ Print only the matched (non-empty) parts of a matching line
		- '-e PATTERN'
			+ Use PATTERN as the pattern. This can be used to specify multiple search patterns, or to protect a pattern begining with a hyphen
		- '-v, --invert-match'
			+ Invert the sense of matching, to select non-matching lines
			+ 즉, 검색에 실패한 녀석만 매칭 
		- '-c'
			+ Suppress normal output; instead print a count of matching lines ofr each input file
		- '-q, --quite'
			+ Quiet; do not write anything to standard output. Exit immediately with zero status if any match is found, even if an error was detected.
