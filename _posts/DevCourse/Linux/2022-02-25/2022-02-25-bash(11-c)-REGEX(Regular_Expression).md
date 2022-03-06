---
title: "[Linux Day5] bash 기초 #3 : REGEX(regular expression)"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-25 00:00:12
last_modified_at: 2022-02-26
---
<br>
<br>

# 1. REGEX: greedy
---
1. Greedy matching
	* Greedy matching이란
	```
	$ var2 = "It's gonna be <b>real</b>It's gonna <i>change everything</i> I feel"
	$ echo $var2 | egrep -o "<.+>"
	```
		- pattern은 최대한 많은 수의 매칭을 하려고 하는 성질이 있다.
			+ <.+>이 <b>이 아니라 <b>real ...</i>까지 매칭된 결과 = greedy matching
			+ greedy matching 후 result set의 범위를 줄여나가면서 정확한 표현식을 완성하도록 해야 한다.
		- \<br>real ...</i>의 result sets에서 \<b>만 뽑아내려면?
			+ 표현식을 더 정확하게 작성해야 함
	
	* <.+>에서 임의의 문자인 .를 [^<>]표현으로 변경하면?
	```
	$ var2="It's gonna be <b>real</b>It's gonna <i>change everything</i> I feel"
	$ echo $var2 | egrep -o "<.+>"
	$ echo $var2 | egrep -o "<[^<>]+>"
	```
2. Non-greedy matching
	* 최소 매칭 기능 == greedy matching의 반대 개념
		- 앞서 <.+> 대신에 <[^<>]+>을 사용하면 최소 매칭이 되는데
			+ POSIX RE에서는 패턴을 수정하여 non-greedy matching 효과와 같은 결과의 패턴을 만든다.
		- POSIX RE에서는 non-greedy matching 수량자를 제공하지는 않는다.
			+ 따라서 패턴을 변경하여 non-greedy matching과 유사한 결과를 만든다.
				* 하지만 PCRE는 non-greedy matching을 쉽게 할 수 있는 quantifier를 제공한다. 이를 lazy quantifier라고 부른다.
			+ vim은 POSIX BRE를 사용하지만 non-greedy 수량자인 \{-}를 제공한다
3. Non-greedy matching : vim
	* vim 에디터 실행 후 아래와 같은 문자열을 타이핑하자.
	```
	It's gonna be <b>real</b>It's gonna <i>change everythong </i> I feel
	```
	* 타이핑 후 /<.\+>로 검색해보자(greedy matching)
	* 이번에는 /<.\{-}>으로 검색해보자
4. Non-greedy matching : lazy quantifier
	* Lazy quantifier (PCRE 기능)
		- lazy quantifier 사용시 non-greedy mathing을 쉽게할 수 있다.
			+ 단지 quantifier에 suffix로 ?를 더하면 된다.
			+ PCRE에서만 지원되므로 POSIX REGEX모드에서는 사용할 수 없다
			```
			$ echo $var2 | grep -P -o "<.+?>"
			```
				* PCRE의 lazy quantifier를 이용하여 non-greedy matching을 간단하게 표현할 수 있다.
				* grep -P : PCRE matcher를 사용하도록 한다. pcre2grep을 사용해도 된다.
	* On POSIX RE : It dosen't supprot non-greedy matching
	```
	$ echo $var2 | egrep -o "<[^<>]>"
	```
	* On PCRE : It can be non-greedy matching by lazy quantifier
	```
	$ echo $var2 | grep -P -o "<.+?>"
	```
	<br>

# 2. backslash
---
1. Back-slash
	* meta char.의 의미를 없앤다. 아래 두가지 패턴의 차이는?
		- 1번 패턴 : c.b
			+ cab,cbb,ccb,cdb,c1b,c2b 등등
		- 2번 패턴 : c\.b
			+ c.b : dot이 메타의 의미가 아닌 진짜 일반 문자 '.'를 의미하게 된다.
	* BRE에서 ERE의 일부 기능을 표현할 때 사용한다.
		- 예시
			+ ERE의 {m,n}을 BRE로 표현할 때 \{m,n\}으로 사용
			+ ERE의 ?를 BRE로 표현할 때 \?으로 사용
		- ?,+,{},\|,()에 대해 back-slash를 사용한다(BRE인 경우에만)
			+ BRE에서 \를 ERE pattern으로 해석하도록 하는 용도로 사용하는 경우는 가끔 헷갈릴 수 있으므로, 웬만하면 ERE만 사용하는 것이 좋다.
			+ 즉, egrep을 사용하는 경우에는 \가 필요 없다.
	* BRE에서 ERE의 ?,{},+,\|,() 패턴 사용시 앞에 사용
	* back-slash를 문자 앞에 두는 행동을 escape 시킨다고 표현
		+ \.으로 적으면 ".을 이스케이프 시켰다"고 표현
		+ Parser의 의미를 생각해보면 escape라고 표현하는 이유를 알 수 있을 것
	
2. Practice : back-slash

	* BRE vs ERE : 앞서 패턴에서 +를 추가했더니 실패
	* 해법은? ERE에서 사용되는 메타 문자 앞에 back-slash 추가

	* ERE를 사용하는 egrep에서는 굳이 \가 없어도 된다.

	* www.naver.com 페이지에서 URL 링크를 추출해보자
		- curl : 터미널에서 웹페이지를 접속하는 유틸
		
		- 중복되는 링크를 제거해보자

		- img 태그만 추출해보자

3. Tip! BRE vs ERE
	* 어떤 툴이 BRE를 사용하는지 ERE를 사용하는지 알아두면 언제 \를 붙여야 하는지 쉽게 판단 가능
		- BRE를 사용하는 유틸
			+ grep, vim, sed ...
		- ERE를 사용하는 유틸
			+ egrep, awk
	* PCRE는 별개의 문제지만 기본적으로 ERE를 베이스로 한다.
	<br>

