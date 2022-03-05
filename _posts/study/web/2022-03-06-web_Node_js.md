---
layout: post
title:  "[Node_JS] Node Js에 대해 그리고 설치"
subtitle:  
date: 2022-03-06 09:01:38 +0900
categories: study
tags: web_&_frontend
comments: true
related_posts:

---

## 노드JS란? 그리고 설치하기 <br/>
<br/>

[NodeJs 윈도우 설치링크](https://nodejs.org/ko/)
<br/>

노드 JS란 JavsScript를 기반으로 한 서버 관리 플랫폼이다.<br/>
노드 JS를 계기로 프론트엔드 분야에서만 쓰이던 JavaScript가 백엔드 영역까지 확장 되었다고 볼 수 있다.<br/>

16.14.0 등 짝수로 표기되어있는 LTS버전은 안정적으로 작동하는 버전이고, 17.. 등으로 표기된 버전은 최신형 라이브러리를 사용할 수 있으나 일부 라이브러리가 불안정적으로 작동할 수 있다.<br/>

따라서, 대부분은 짝수버전 즉 LTS버전을 사용하는 것을 선호한다.<br/>

위에 있는 링크에 들어가서 노드 js를 설치해주자.
<br/>

그 후에 파워쉘(PowerShell)에서 node -v를 입력해주면 노드 js의 버전을 확인할 수 있다.<br/>

![버전확인](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/web_and_frontend/2022-03-06_1.jpg?raw=true)

필자의 경우 16.14.0 LTS 버전으로 잘 설치되었음을 확인할 수 있다.<br/>
MacOS나 리눅스 버전은 [여기](https://nodejs.org/ko/download/)에서 다운로드가 가능하다.<br/>
<br/>

## NVM에 대해서<br/>


[NVM 설치링크](https://github.com/coreybutler/nvm-windows/releases)<br/>

NVM(Node Version Manager)는 윈도우에서 파워쉘을 통해서 노드 js의 버전을 관리할 수 있게 만들어주는 모듈이다.

위에 있는 깃허브 링크에서 설치할 수 있다.<br/>

필자는 윈도우 OS를 사용하고 있어서 윈도우 OS 기준으로 작성하면 위에있는 링크에 들어가서 nvm-setup.zip파일을 다운로드 하고 압축을 푼 뒤 설치파일을 실행하면 된다.<br/>

## NPM에 대해서<br/>

대부분 Node JS와 함께 설치되는 Package Manager로, 파이썬에서의 pip와 같은 포지션을 담당한다고 보면 된다.<br/>

파이썬에서 외부 라이브러리를 아나콘다에서 가상환경을 설정하거나, Package Manager로 설치한 뒤에 import를 실행하는 것 처럼 NPM도 Javascript에서 같은 포지션을 차지한다고 보면 된다.<br/>

예시로, 깃허브 블로그를 사용하는 사람이라면

- package.json
- package-lock.json

이 두 파일이 존재하는데, 이 두 파일이 관련되어 있다고 보면 된다.<br/>
사실 필자도 이 때는 처음 접하는 것이라 자세히는 모르지만, 위와 같은 내용이라고 파악이 된다.<br/>



