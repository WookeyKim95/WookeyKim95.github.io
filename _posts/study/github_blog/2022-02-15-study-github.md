---
layout: post
title:  "[GitHub] GitHub에 파일 올리기 및 용어 정리"
subtitle:   
date: 2022-02-15 09:52:23 +0900
categories: study
tags: github
comments: true
related_posts:

---

# [GitHub] GitHub에 파일 올리기 및 용어 정리<br/>

## Git 용어 모음<br/>

참고 링크 : [유튜브_코딩알려주는누나](https://www.youtube.com/watch?v=lelVripbt2M)

### Commit<br/>

```
git commit -m "대충 업데이트 내용"
```

프로젝트 폴더 내에서 파일의 생성, 수정, 삭제 등 변경사항이 있다면 어떤 내용이 변경되었는지 적는다. 대충 업데이트 내용 자리에 영어로 업데이트 내용을 적어주면 된다.

commit을 완료하면 origin 로컬저장소에 변경내용이 반영된다.
이 commit을 해야만 업데이트를 진행할 수 있다.<br/>

### Push<br/>

```
git push -u origin main
```
맨 처음에 push를 진행할 때에만 -u 표시가 들어가고

```
git push origin main
```
이후 부터는 이렇게 입력해서 push를 진행해주면 된다.

여기서 origin은 로컬저장소의 이름이고, main이 깃허브 원격저장소의 branch의 이름이라고 보면 된다.<br/>
처음에 우리가 저장소를 만들 때 origin과 main으로 명명했기 때문에 저렇게 입력을 해주어야 하는 것이다.<br/>

![git브랜치](https://github.com/wookeykim95/wookeykim95.github.io/blob/main/assets/img/study/github/2022-02-15_github_1.jpg?raw=true)

만약 깃허브의 branch이름이 master라면

```
git push origin master
```
이렇게 해주면 된다.<br/>
<br/>


### Pull<br/>

반대로, 원격 저장소에 있는 내용을 로컬 저장소에 가져와 동기화할 수 있는데 이것을 pull이라고한다.<br/>

```
git pull main origin
```

이렇게 가져올 원격저장소 - 로컬저장소 순으로 이름을 입력해주면된다. 그러면 merge(동기화)가 진행된다.<br/>
<br/>

### Checkout<br/>

참고 링크: [누구나 쉽게 이해할 수 있는 Git 입문](https://backlog.com/git-tutorial/kr/stepup/stepup2_3.html)

```
git checkout "branch name"
```
원격 저장소의 대상 브랜치를 전환한다. 예를들어, 현재 대상으로 main branch를 가리키고 있다고 하면

```
git checkout "pya"
```
원격 저장소에 pya 라는 브랜치가 있는 경우 pya 브랜치로 전환된다.<br/>

```
git checkout -b "pya"
```
-b 까지 같이 쓰면 생성과 동시에 대상이 해당 브랜치로 전환된다.<br/>
<br/>

### status<br/>

```
git status
```
현재 무슨 파일이 수정되었는지, 커밋할 파일이 무엇이 있는지, 그리고 원격저장소의 어떤 branch로 연결되어 있는지 확인할 수 있다.<br/>
<br/>

### config<br/>

#### 계정 로그인하기<br/>

이전 맨 위의 로그인 작업은 포스트에도 적었던 내용이지만 여기에다가도 다시 적겠다.<br/>

```
git config --global user.name "your_name"
git config --global user.email "your_email"
```
로컬 저장소와 원격 저장소의 연동을 위해서 깃허브에 로그인하는 작업이다.<br/>

#### 계정 로그아웃, 변경하기<br/>

참고링크 : [스택오버플로우](https://stackoverflow.com/questions/11868447/how-can-i-remove-an-entry-in-global-configuration-with-git-config/11868676)

```
git config --global --unset user.name
git config --global --unset user.email
```
반대로 이 로컬 컴퓨터에서 로그아웃을 하는 과정이다.<br/>
이 이후에 다시 위의 코드로 로그인을 해주면 된다.<br/>

```
git config --global replace-all user.name "new_user.name"
git config --global replace-all user.email "new_user.email"
```
혹은 위 코드를 사용할 수 있다.<br/>

```
git config --list
```
현재 로그인 상태 등을 조회할 수 있다.<br/>
<br/>

## VSCode에서 Git 기능 활용하기<br/>

사실 Git을 설치했고 VScode를 쓰고 있다면 VSCode의 터미널이나 왼쪽 창에서도 Git 기능을 사용할 수 있다.<br/>

### 1. 터미널에서 사용하기

![VSCode 터미널](https://github.com/wookeykim95/wookeykim95.github.io/blob/main/assets/img/study/github/2022-02-15_github_2.jpg?raw=true)

Git Bash에서 하는 것처럼 해주면 된다!

### 2. VSCode 제어창에서 사용하기

왼쪽에 제어창이 있다. 이때 가지모양 아이콘이 있는데 이것을 클릭하면 어떤 내용이 수정되었는지 반영된다.

![VSCode 터미널](https://github.com/wookeykim95/wookeykim95.github.io/blob/main/assets/img/study/github/2022-02-15_github_3.jpg?raw=true)

- 초록색 U는 새로 추가된 파일내역
- 주황색 M은 내용이 수정된 파일내역
- 빨간색 D는 삭제된 내역

을 가리킨다.<br/>

![VSCode 터미널](https://github.com/wookeykim95/wookeykim95.github.io/blob/main/assets/img/study/github/2022-02-15_github_4.jpg?raw=true)

오른편에 살포시 올려주면 십자모양이 나타나는데 변경 사항을 반영하여 커밋을 준비하는 과정이다.<br/>

이것을 **스테이징(Staging)** 이라 한다.<br/>

이것을 해준 다음에

![VSCode 터미널](https://github.com/wookeykim95/wookeykim95.github.io/blob/main/assets/img/study/github/2022-02-15_github_5.jpg?raw=true)

위와 같이 해주면 커밋을 할 수 있다!<br/>
위의 스테이징된 항목 커밋을 눌러주면 12시 방향에 메세지를 입력하라는 창이 뜨는데

```
git commit -m "hello!"
```
이 과정과 똑같다!

저기 -m 뒤에 있는 부분에 메세지를 넣어주는 작업이다!

그렇게 하면 파란색으로 동기화 버튼이 나오며, 누르면 push가 완료된다.<br/>