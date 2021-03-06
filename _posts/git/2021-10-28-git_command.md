---
title: "[git] 소스트리 git 사용법 정리 "
tags:
- git
- 소스트리
categories:
- git
date: '2021-10-22 09:00:00'
classes: wide
last_modified_at: '2021-11-03 11:14:00 -0700'
---

# git add / commit / push
- 소스트리에서는 파일 내용이 변경시 바로바로 감지되어 커밋하지 않은 변경사항이라고 표시된다. 

<br/>

# git add
- 소스트리에서 모두 스테이지에 올리기 / 선택내용 스테이지에 올리기는 git add 파일 명령어와 동일하다.

- 스테이지에 올라가지 않을 파일 리스트 옆에 (+)를 눌러도 동일하게 git add 가 적용되어 스테이지에 올라간다.

- commit 을 위한 후보를 선택하는 것으로 보면된다. 아직까지 commit 은 안되었고 후보를 선택했다.

# git commit
- 스테이지에 올라간 파일들은 소스트리 하단의 텍스트창에서 commit 메세지를 작성할수 있다. 

- \[커밋] 버튼을 누르면 git commit -m (메세지) 와 동일하게 커밋된다.

- commit을 완료하면 소스트리 좌측 사이드바의 History 메뉴에서 추가된 커밋 크래프를 확인할 수 있다.

## origin / master
- 소스트리 History 에서 보면 origin과 master , 혹은 origin/master 라는 꼬리표가 붙은것을 확인할수 있는데 의미는 다음과 같다.

- 소스트리의 history 그래프의 origin / master를 요약하면 아무것도 붙지않은 \[master]는 내 컴퓨터 로컬 저장소 버전을 나타내고, \[origin/master]는 github의 원격저장소 버전을 가리킨다.

### origin
- 현재 프로젝트의 파일들과 원격으로 연결되있는 github 원격저장소의 닉네임
- 따로 원격저장소 네임을 설정 안했으면 디폴트로 origin 으로 설정되는듯하다.
- 요약하면 원격저장소의 현재 버전 상태를 가리키는 커밋에 origin 이라는 꼬리표가 붙어있다.

### master
- 우리가 커밋을 올리는 브랜치의 이름
- 따로 브랜치를 생성하지 않으면 git은 master라는 기본 줄기에 커밋을 한다.

<br/>

# git push
- 소스트리 상단 메뉴에서 push를 누르면 push 설정창이 뜬다.
- push 설정창에서 각 브랜치들을 체크박스로 설정할 수 있다.
- 체크박스를 설정하고 push를 진행하면 체크박스를 선택한 브랜치들에게 push가 진행된다.
- 만약 master 브랜치를 선택하고 push를 했으면 git -push origin master와 동일하다.
- push 가 진행되면 history 그래프창에 commit 목록의 꼬리표가 origin/mster 로 변경된다. 이는 정상적으로 push가 되어졌다는 뜻이다.

<br/>
<br/>

# git과 svn(subVersion)의 차이점
- svn은 소스코드에서 변경된 점만 저장한다.
- git은 파일 전체(스냅샷)을저장한다.
- svn은 소스코드에서 변경된 점을 계산해야되서 리소스가 많이 필요하다.
- git은 애초에 파일 전체(스냅샷)을 저장하기때문에 계산이 필요없다.


# git Snapshot 과정
- file1, file2, file3 있을시 git add file1
  - file1 만 스테이지로 올라간다
- git commit -m "asdf"
  - file1 이 commit 된다.
- git add file2, file3
  - file2,3 이 스테이지로 올라간다
- git commit -m "asdf"
  - file1,2,3 이 모두 커밋된다.
- file2, file3 수정후 add 및 commit
  - file1, 2(수정됨), 3(수정됨) 이 모두 스냅샷이 찍힌다. 

## git add
- git add 를 하는순간 로컬 저장소(.git폴더)로 해당 파일들이 올라간다.
- 수정사항이 없는 파일들은 스테이지로 올릴수가 없다.

## git commit
- git commit 을 하는순간 로컬저장소에 올라간 파일들이 하나의 스냅샷 즉 버전으로 만들어진다.
- 이전 파일들이 찍힌 스냅샷과, 현재 파일들이 찍힌 스냅샷으로 로컬에서 버전 관리를 할수 있다.

## 로컬에서만 버전관리를 할때는 add . 와 commit 으로 버전관리를 할수 있다.
- 다른 사람들과 협업 및 원격으로 저장해서 파일을 공유하려면 push 명령어를 수행해야 된다.

## git push
- git push를 하는 순간 commit으로 로컬저장소에 찍힌 스냅샷이 원격 저장소에 올라간다.
- git commit 으로 새로운 스냅샷을 만들고, git push를 다시 하게되면 원격 저장소에서는 그 스냅샷이 또 저장된다.
- 예) 스냅샷1(아까 commit한거), 스냅샷2(지금 commit한거) .... 스냅샷n
- 원격 저장소는 스냅샷들의 저장소인데 원격저장소라고 생각하면 된다.

## git으로 관리하는 파일은 총 4가지 상태를 가지게 된다.
- untracked
  - 추적 안되는 상태.
- tracked
  - 수정없음
  - 수정함
  - 스테이지됨

<br/>
<br/>

# 원격 저장소에서 같은 소스파일을 협업하는 방법
## 브랜치(Branch)
- 시간순으로 스냅샷(커밋)들이 커밋1(10.01), 커밋2(10.02), 커밋3(10.03)이 원격저장소에 있다고 가정
- user1 이 커밋3을 기준으로 수정하고 push 하고
- user2 이 커밋3을 기준으로 수정하고 push 한다고 가정하면 오류가 난다.
- user2 는 커밋4에 푸시를해서 커밋5를 만들어야 한다.

## Master
- git이 제공하는 기본 브랜치
- 브랜치는 항상 이전버전 커밋(스냅샷)을 가리킨다.  
- 브랜치는 커밋을 가리키는 포인터일 뿐이다.

예) 순서대로 커밋1 커밋2 커밋3 이 올라왔다고 가정하면  
커밋1 <- 커밋2 <- 커밋3 <- (Master 브랜치) 순으로 포인터로 가리키게 된다.

- 여기서 user1 브랜치를 새로 만들면
커밋1 <- 커밋2 <- 커밋3 <- (Master 브랜치, user1 브랜치) 가된다.

- 만약 브랜치가 물리적인 길을 가졌다면 Master 브랜치, user1 브랜치 둘다 커밋3을 새로 올려야 됬을것이다.

- 하지만 브랜치는 그저 포인터이므로 커밋을 가리키는것만으로 분기를 만들 수 있다.

- 분기를 만들려면 프로젝트를 통째로 복사해야해서 무겁고 시간이 많이 걸리는 SVN과 같은 버전 관리 툴과는 달리 git 은 가볍고 빠르다.

<br/>
<br/>

## 브랜치가 갈라지는 프로세스
커밋1 <- 커밋2 <- 커밋3 <- (Master, 고양이 브랜치)
- 고양이 브랜치에 커밋
커밋1 <- 커밋2 <- 커밋3 (<- master브랜치) <- 커밋4 <- 고양이 브랜치
- master 브랜치로 이동해서 master 브랜치로 커밋
- 커밋1 <- 커밋2 <- 커밋3
  - <- 커밋4(고양이 브랜치)
  - <- 커밋5(Master 브랜치)

## 브랜치를 마음대로 이동, 갈아타는 방법 (Checkout)
- \<HEAD> 포인터 이용
- \<HEAD> 포인터로 master 브랜치, 고양이 브랜치 혹은 이전 커밋(예를들면 커밋2) 으로 갈아 탈수 있다.

## Checkout
- 체크아웃을 하면 브렌치를 갈아탈 수 있는데, 그 브렌치의 소스내용으로 실시간 반영된다.

- 즉 이전 소스내용으로 돌아가고 싶다면, 이전 소스내용의 브렌치를 Checkout 하면 이전 내용의 소스로 롤백이 가능하다.


<br/>
<br/>
<br/>
<br/>

# 소스트리에서 브랜치 생성하는 방법
- 여러사람이 작업하는 원격 저장소에서는 미리 서로 브랜치 규칙을 만들어서 약속을 하는것이 일반적이다.
## 브랜치 규칙 예제
- Master 브랜치에는 직접 커밋을 올리지 않는다 (동시에 작업하다 버전이 꼬일 가능성)
- 기능 개발을 하기 전에 \[Master] 브랜치를 기준으로 새로운 브랜치를 만든다.
- 새로운 브랜치의 이름은 \[feature/기능이름] 형식으로 하고 한 명만 커밋을 올린다.
- \[feature/기능이름] 브랜치에서 기능 개발이 끝나면 \[Master] 브랜치에 이를 합친다.

## 소스트리에서 브랜치 생성방법
- 소스트리 상단에 브랜치 버튼클릭
- 브랜치 생성창이 뜨는데 브랜치 이름을 feature/detail-page 로 한다. 이슈 수정에서는 보통 #이슈번호 로 브랜치를 생성한다
- 현재 브랜치가 Master인것을 확인한다.
  - 이 브랜치가 가리키는 커밋을 기준으로 새로운 브랜치를 만든다는 뜻이다.

- 새 브랜치 체크아웃 옵션 : 브랜치를 만듦과 동시에 그 브랜치로 이동을 한다.
  - 체크아웃 : 브랜치를 이동하는 명령어
  - 소스트리에서는 왼쪽 브랜치항목에 더블클릭으로 브랜치를 이동할수 있다. 동그라미가 있는 브랜치가 현재 이동된 브랜치다.

- 새 브랜치를 만들고 그 브랜치에 체크아웃된 상태에서 커밋을 하게 되면 그 브렌치에 커밋과 푸시가 이어진다.

- 즉 origin 에는 아직 합쳐지지 않았고 브랜치가 갈라져서 기능만 추가되어진 상태

- 이 상태에서 git 관리하는 사람이 추가한 브랜치를 origin에 합친다.

<br/>
<br/>
<br/>

# 소스트리에서 브랜치 생성시 <span style="color:red">**주의해야 할점**</span>
- 브랜치를 생성하면 소스트리 History 그래프에서는 이전에 commit push 한 항목옆에 브랜치 태그가 보인다.

<br/>

![git_branch.png](/assets\image\posts_image\git_branch.png)

- 비록 History 에는 새로 생성한 브랜치가 이전에 Commit Push 한 커밋(snapshot)을 가리키고 있지만 당황하지 않는다.

- 가리키고만 있을뿐이지 이전에 commit push 한 것이 그 브랜치로 들어간것이 아니다. 그림으로 요약하면 다음과 같은 상태가 된것이다.

![new_branch.png](/assets\image\posts_image\new_branch.png)

<br/>
<br/>

- 변경사항을 커밋하면 새로 생성한 브랜치는 그 변경사항을 가리키게 된다.


![new_branch_commit.png](/assets\image\posts_image/new_branch_commit.png)

- 아직 push를 하지 않은상태라 origin/testBranch라고 안뜬다.

- push 를 진행하게되면 (testBranch) (origin/testBranch) 라고 변경된다.

![new_branch_push.png](/assets\image\posts_image/new_branch_push.png)

- testBranch가 origin/testBranch Commit 을 가리키고 있다는 뜻이다.

- History에는 master 브랜치에 일자로 커밋 푸시된것처럼 보이지만 아직 merge를 하지 않아서 그렇다.

<br/>
<br/>

- 그림으로 요약하면 다음과 같은 상태가 된것이다.

![new_branch_summary.png](/assets\image\posts_image/new_branch_summary.png)

- 갈라진 분기의 commit 인 #2021-11-03 commit 테스트를 testBranch가 가리키는 형태다.

- **Branch는 포인터일 뿐 commit 이 아니다.**

- Branch를 포인터라 생각하면 편하다.

- origin 뜻은 원격에 commit 이 push 되었다는 상태.

<br/>
<br/>

# Master가 각 브랜치들 수정사항 merge 되었는지 확인하는 방법
- Master 혹은 Develop 브랜치에서 merge 가 완료되면 소스트리에서 다음과 같이 표시가 된다.

<br/>

![gitlab_merged_ex.png](/assets\image\posts_image\gitlab_merged_ex.png)

- merge가 완료되면 master 브랜치에서 pull 받으면 병합된 수정사항들이 모두 적용이 된다.
  - master에서 merge 하기 전에 pull 받으면 수정사항들이 모두 날라간다.