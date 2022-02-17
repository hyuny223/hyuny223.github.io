---
layout: single
title:  "깃허브 사용법"
categories: how_to_use
tag: [github,command]
---
<br/><br/>
github 사용이 처음이라 생소한 점이 많다.<br/><br/>
따라서 자주 사용하는 것 같은 명령어를 기록해 두어야겠다.<br/><br/>

---
<br/><br/>

```c++
git init
```
→ 해당 디렉토리에 .git을 만듦<br/><br/>

```c++
rm -r .git
```
→ 해당 디렉토리에 .git을 삭제<br/><br/>

```c++
git add .
```
→ 모든 변화(.)를 add<br/><br/>


```c++
git commit -m "message"
ex) git commit -m "first commit"
```
→ commit한 기록을 관리<br/><br/>


```c++
git branch <브랜치 이름>
ex) git branch sub01
```
→ local 저장소에 브랜치를 만듦<br/><br/>


```c++
git checkout <브랜치 이름>
ex) git checkout sub01
```
→ local 저장소를 해당 브랜치로 바꿈<br/><br/>


```c++
git branch -m <이전 브랜치> <바꿀 브랜치>
ex) git branch -m master main
```
→ 브랜치의 이름을 바꿈<br/><br/>


```c++
git remote add origin <저장소 url>
ex) git remote add origin https://github.com/hyuny223/hyuny223.github.io.git
```
→ local 저장소와 remote 저장소의 연결고리<br/>
  checkout을 main branch에서 할 것<br/><br/>


```c++
git push origin <브랜치 이름>
ex) git push origin sub01
```
→ 생성한 local branch를 remote branch에 추가(혹은 붙여넣기)<br/><br/>


```c++
git branch -a
```
→ 모든 local, remote branch를 확인<br/><br/>

```c++
git branch -d <브랜치 이름>
ex) git branch -d sub01
```
→ 해당 local 브랜치를 삭제<br/><br/>

```c++
git push origin --delete <브랜치 이름>
ex) git push origin --delete sub01
```
→ 해당 remote 브랜치를 삭체<br/><br/>


```c++
git clone <저장소 url>
ex) git clone https://github.com/hyuny223/git_practice.git
```
→ url에 있는 저장소의 내용을 복사<br/><br/>


```c++
git clone -b <branch name> --single-branch <저장소 url>
ex) git clone -b week1-3/chl --single-branch https://github.com/hyuny223/git_practice.git
```
→ url에 있는 저장소에 있는 특정 브랜치를 복사<br/><br/>

```c++
예시

git add .
git commit -m "first commit"
git push origin main

git checkout week1-1/chl
git commit -m "another commit"
git push origin week1-1/chl
```
→ main 브랜치와 week1-1/chl 브랜치에 push하는 예제<br/><br/>