---
layout: post
title:  "[Github Blog] 깃허브 블로그 포스트 날짜에 관한 일지"
subtitle:   "개요"
date: 2022-01-21 16:19:32 +0900
tags: github
categories: study
comments: true
related_posts:

---

# [Github Blog] 깃허브 블로그 포스트 날짜에 관한 일지<br/>

## 어라.. 난 이글을 21일에 썼는데..?<br/>

내 블로그를 쭉 둘러보고 있었는데, 이상한 점을 발견했다.<br/>
![이미지1](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-21_1.jpg?raw=true)<br/>
난 저 글을 21일 오전에 작성했는데 블로그 상 날짜에는 20일로 나타나는 것이 아닌가;;<br/>
<br/>


![이미지2](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-21_2.jpg?raw=true)<br/>
md파일로 들어가서 다시 한번 날짜를 확인해보았다.<br/>
그렇지만 분명 21일이 맞았다..<br/>
뭐가 잘못된 것이지 생각하던 순간 바로 한 가지 생각이 떠올랐다.<br/>
<br/>

## 9시 이전이라서 그런가..?<br/>

<br/>

한국 시간으로는 오전 9시지만 그리니치 천문대 표준시, 즉 세계 표준시는 이때서야 자정이 되면서 날짜가 바뀐다.<br/>
또 한가지 더 든 생각으로는 내가 이 블로그 리소스 파일의 html파일의 언어를 한국어로 안 바꿔 놓았다..<br/>

![이미지3](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-21_5.jpg?raw=true)<br/>
어.. 그렇다.. F12를 눌러보니 html lang='en'으로 되어있었다.<br/>
<br/>

![이미지4](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-21_3.jpg?raw=true)<br/>
우선 문제의 포스트의 시간을 1시간 뒤, 즉 9시 너머로 설정해보았다.<br/>
<br/>

![이미지5](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-21_4.jpg?raw=true)<br/>
그랬더니 21일로 잘 바뀌었다.<br/>
<br/>
<br/>

다시 원래대로 돌려놓고, 이번엔 html의 기본 언어를 바꿔보기로했다.<br/>
내가 사용하는 Jekyll테마는 **hydejack 무료버전**이다.<br/>
**정확히 말하자면, 아래 따라쟁이님 블로그에서 샘플테마를 받아서 커스텀을 진행하였다.**<br/>
출처 - [따라쟁이님 블로그, 하루만에 만드는 깃허브 블로그](https://khw11044.github.io/blog/githubpages/2020-12-26-making-blog-02/)<br/>
<br/>
![이미지5](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-21_6.jpg?raw=true)<br/>
이 블로그 리소스의 경우 기본언어 설정하는 곳은 **_layout 폴더의 compress.html에 있었다.**<br/>
<br/>

그리고 사진에는 없지만 크롬에서 한국어로 페이지 번역하기를 해보니까 내 블로그가 와장창 깨졌다..<br/>
저기서 **default:'en'**을 **default:'ko'**로 바꿔보았다. <br/>

![이미지5](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-21_7.jpg?raw=true)<br/>
그리고 우선 VSCode에서 bundle exec jekyll serve로 시뮬레이션해보니 날짜도 원하는 대로 나오고 언어도 ko로 잘 반영되었다.<br/>

자 이제 남은건.. github에다 푸시해서 크롬 등 웹 상에서도 잘 반영되는지 확인하는 것이다..<br/>
<br/>
<br/>

![이미지6](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-21_8.jpg?raw=true)<br/>
한 5분 정도 기다린 뒤에 마이크로소프트 엣지로 내 블로그에 와서 F12를 눌러보았다.<br/>
하지만 언어설정은 한국어로 잘 되었지만 날짜는 여전했다..<br/>
<br/>
<br/>
이건 내 개인적인 의견인데..<br/>
VSCode에서 bundle exec jekyll serve로 시뮬레이션해서 접속하면 한국날짜가 반영되지만<br/>
이 블로그는 깃허브 서버를 쓰다보니까 세계 표준시가 반영되는 것 같다..<br/>

**물론 코알못 개발알못이 개인적으로 생각하는 것인지라 헛소리일수도 있습니다**<br/>

<br/>
이런 현상이 발생한다는 것이 신기하다는 생각이 들면서도 지금 내 능력으로 여기서 더 잘못 건들면 홈페이지 깨질 것 같아서 우선 세계 표준시로 보는 것으로 해야겠다..<br/>
그래도 업로드는 지금까지 업로드하던 시간 형식으로 해야지.<br/>
<br/>

## 2022-03-20 추가 작성내용<br/>
<br/>

새로운 사실을 발견했다.<br/>

__세계 표준시 문제가 맞는 것 같다.__<br/>

자신이 같은 날에 깃허브에 커밋을 했더라도, 9시 이전에 커밋했을 경우 자신의 깃허브에 커밋했다는 표시가 블록에 반영이 되지 않는다.<br/>

![이미지7](https://github.com/wookeykim95/wookeykim95.github.io/blob/main/assets/img/study/github/2022-03-20_github_1.jpg?raw=true)<br/>


위의 사진을 캡쳐한 뒤, 오전 8시 42분에 커밋을 했고, 9시 이후에 다시 새로고침을 해보니 커밋 횟수가 반영되었다.<br/>

![이미지8](https://github.com/wookeykim95/wookeykim95.github.io/blob/main/assets/img/study/github/2022-03-20_github_2.jpg?raw=true)<br/>

<br/>

깃허브가 전 세계에서 운용되어 세계 표준시를 사용하다보니까 이런 현상이 발생하는 것 같다.<br/>

**신기해.**