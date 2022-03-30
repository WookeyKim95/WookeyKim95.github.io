---
layout: post
title:  "[Github blog] 블로그 테마 업그레이드하기_5"
subtitle: 
date: 2022-03-29 09:22:58 +0900
categories: project
tags:
comments: true
related_posts:

---

# [Github blog] 블로그 테마 업그레이드하기_5

## 문제의 발생<br/>
<br/>

문제가 발생하였다.<br/>

일단 Merge가 되는 것도 확인하였는데, 수정내용이 인터넷 홈페이지에 반영되지 않는다.<br/>

무슨 문제인가 싶어서 리소스 폴더를 백업하고, 파일을 모두 지운뒤,<br/>

수정한 버전의 파일로 모두 붙여넣기도 해보았다.<br/>

그리고 커밋을 했지만 아래와 같은 상태가 계속 지속되고 있다.<br/>

![현 상태](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-29-project_1.jpg?raw=true)

28일에 위 과정을 진행했는데, 29일 오전 9시 기준 아직도 저 상태이다.<br/>

구글에 반영이 되지 않는 이유를 검색해야겠다.<br/>

문제를 해결한 뒤에 이 글을 업데이트 하도록 해야겠다.<br/>

참고로, 로컬서버 구동시, 수정내용은 정상적으로 반영이 되며, 원격 저장소에 있는 파일도<br/>

로컬 저장소에 있는 파일과 동일하다.<br/>
<br/>

## 경과의 진행

![경과](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-29-project_2.jpg?raw=true)

아하하.. 이걸보니까 오히려 기쁜 마음이 들었다..<br/>

몇 시간 뒤 내 깃허브를 다시 확인해보니 Page build Failure가 떴다.<br/>

드디어 어떻게 해결을 해야할지 구글링을 할 지 실마리를 잡을 수 있는 것이다!!<br/>

그리고 그 다음날에 해결하였다. 그 과정은 정리해서 올리도록 하겠다.<br/>
