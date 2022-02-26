---
title: "[Linux Day5] bash 기초 #3"
categories: Linux
tag: [Linux]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-02-25
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

# 3. Parenthese
---
1. (): Back-reference, subst., alternation
	* () 괄호는 여러가지 기능으로 사용된다.
		- back-reference
		- group
			+ alternation 기능에도 group기능을 사용
	* 매칭된 결과를 다시 사용하는 패턴 (백레퍼런스)
		- ()로 묶인 패턴 매칭 부분을 "\#"의 형태로 재사용(#은 숫자가 순서대로), 0번은 전체 매칭 결과
		```
		$ egrep "^(.+):x:[0-9]+:[0-9]+:.*:/home/\1" /etc/passwd
		$ egrep -v "^(.+):x:[0-9]+:[0-9]+:.*:/home/\1" /etc/passwd
		// 일반 유저가 아닌 유저(시스템유저)
		```
		![Screenshot from 2022-02-26 18-48-35](https://user-images.githubusercontent.com/58837749/155838886-c0029c44-33b0-497f-84e4-cf67c5608f5e.png)
		- 추가설명 : 유저중에서 자신의 홈디렉토리 위치가 홈 밑에 자신의 아이디하고 같은 유저. 이를 일반유저. 시스템유저들은 홈디렉토리가 홈에 있지 않고 엉뚱한 곳에 있음. 예를 들어, root도 root디렉토리를 갖고 있음. 이와 같이 시스템 유저는 디렉토리가 다르기에,  일반유저를 골라내고자 한다면 홈디렉토리 밑에 자신의 아이디와 똑같은 유저를 골라내면 됨. 이를 만들어주는 패턴. 자신의 아이디가 달라지면6번째 필드의 디렉토리명도 달라짐. 즉, 유저명이 달라질때마다 디렉토리의 위치도 달라져야 한다는 것. 이런 것을 패턴으로 만들기 위해서는 백레퍼런스가 필요. 괄호로 묶인 지역이 아이디.\#부분은 앞에서 묶어주었던 괄호부분을 참고하는 것이기에, 앞부분이 달라지면 뒷부분도 달라짐
		- options
			+ -v : invert
			+ --color : Surround the matched (non-empty) strings
	* back-reference 응용 : tag로 감싸여진 부분 추출
	![Screenshot from 2022-02-26 19-06-19](https://user-images.githubusercontent.com/58837749/155839067-b62eaf9b-b4fa-4e8d-8a75-859d5846acf0.png)

2. Alternation
	* '()'는 alternation 용도로도 사용됨
		- '()' alternation이나 pattern group을 묶을 때도 사용된다
		![Screenshot from 2022-02-26 19-10-33](https://user-images.githubusercontent.com/58837749/155839195-63fca9f2-2e59-41c8-a90e-6aafd101b790.png)
			+ 대소문자 구별을 해주어야 한다.
		- 묶을 때 사용했어도 back-reference의 기능도 함께 갖는다.

3. Practice
	* www.naver.com 페이지에서 URL 링크를 추출해보자
		- <a ...> ... </a>로 감싸진 태그 내용을 추출해보자.
	![Screenshot from 2022-02-26 19-14-26](https://user-images.githubusercontent.com/58837749/155839345-a61bbe3c-c787-4386-9a79-894242481e69.png)
		- (a\|A) 부분이 back-reference가 되고 첫번째 소괄호이므로 \1이 된다.
			+ '(a\|A)' : a 혹은 A가 등장하는 경우
				* grep의 BRE로 사용하기 위해선?
				![Screenshot from 2022-02-26 19-19-46](https://user-images.githubusercontent.com/58837749/155839491-c5c19eb3-9869-4a32-86f7-a3617f0eea67.png)
	 	- jpg,png 확장자를 가진 파일 링크만 추출해보자
			+ 주의할 점은 확장자는 대문자일 수도 있다. 즉, JPG, jpg 모두 잡아내야 한다.
			![Screenshot from 2022-02-26 19-28-20](https://user-images.githubusercontent.com/58837749/155839795-bb4ccbfb-3574-40b8-958e-df63e5ba1851.png)
			<br>

# 4. subst.
---
1. Substitution - sed(stream ed)
	* sed, awk로 교체 작업을 하는 경우가 많다.
	* sed에서 제일 많이 쓰이는 기능이 substitution이다.
		- sed의 subst. 기능은 vim의 substitution command와 같다.
		![Screenshot from 2022-02-26 19-35-51](https://user-images.githubusercontent.com/58837749/155840043-65498369-4575-4b31-ac7f-3e66b6b3c583.png)
			+ sed의 substitution에서 separator는 slash를 많이 쓰지만, comman를 쓰기도 한다. 기계적으로 slash만 쓰는 것으로 외웠다면 지식을 업데이트 하자!
			+ vim의 substitution command는 sed의 기능이 포함된 것뿐이다. 즉, sed를 알면 vim도 알 수 있듯, UNIX는 서로 연관된 기능들이 많다.
		- sed는 BRE를 기반으로 하므로 \+로 표현되었다.
		- 하지만 -r옵션을 사용하면 ERE를 사용하게 된다. sed를 잘 쓰는 사람들은 기본이다.

2. Substitution - awk
	* awk에서도 위의 모든 기능을 구현할 수 있다.
		- 앞뒤 slash는 정규표현식이라는 의미
		![Screenshot from 2022-02-26 19-46-29](https://user-images.githubusercontent.com/58837749/155840325-b70f987f-aab3-4797-be5b-571ce15c2b55.png)
		- 공백도 처리하는 명령 포함
		- awk의 gsub(global substitution)에서 REGEX로 교체하는 방법이다.
			+ awk는 ERE를 사용하므로 + 앞에 back slash를 쓰지 않는다.
3. Practice
	* www.naver.com 페이지에서 URL 링크를 제거해보자
		- <a ...> ... </a>로 감싸진 태그 내용을 일반 메세지로 변경해보자
		![Screenshot from 2022-02-26 20-25-50](https://user-images.githubusercontent.com/58837749/155841403-736b0ec1-ada1-4850-b27b-6809b7e60879.png)
		- '()', \|, + 앞에 backslash를 쓴 이유는?
			+ sed는 BRE를 쓰기 때문
			+ ERE를 쓰기 위해서는 -r 옵션을 사용하면 된다.
