---
layout: post
title:  "블로그 작성에 이미지 삽입하기."
subtitle:   "개요"
date: 2022-01-20 09:10:32 +0900
categories: study
tags: github
comments: true
related_posts:

---


## 링크를 통해 블로그에 이미지 넣기.

깃허브 블로그에다 글을 작성하면서 이미지도 올릴 필요가 있다.
그런데 깃허브 블로그는 우리가 늘 접했던 블로그에 글을 쓰는 방식과 많이 다른 것 같다.
MD파일을 생성하고, MD문법에 맞추어 글을 작성하고 이미지 첨부도 **웹 링크**를 연결해야한다.
아니면, 원래 근본적으로 웹에 글을 올리는 방식이 이랬는데, 포털 사이트들이 파일 생성, 업로드하는 과정을 많이 축소 시켜 놓았던 것일까?

---
## 그래서 이번엔 이미지 업로드 도전.

![이미지1](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-20_1.jpg?raw=true)

![이미지2](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-20_2.jpg?raw=true)

카테고리, 그리고 포스트 마다 다른 이미지를 사용하므로 나중에 이미지가 많아질 것을 대비해서
폴더들을 분류 해놓았다. 지금 이 포스트는 study-github 분류이니 study/github 폴더에 저장했다.
즉, 이 저장소의 main/assets/img/study/github 폴더 안에 사진이 2개 들어간 것이다.

![이미지3](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-20_3.jpg?raw=true)

이렇게 깃허브 저장소 안에 있는 사진을 우클릭해서 링크주소복사를 눌러주면된다.
그리고, 이 과정에서 찍힌 사진 파일 또한 2022-01-20-3.jpg로 저장되어있는 상태이다.


---


![이미지4](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-20_4.jpg?raw=true)

중간점검과정! 이렇게 깃허브 저장소 안에 있는 md파일 상에서는 이미지가 잘 출력된다.


---

![이미지5](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-20_5.jpg?raw=true)

하지만.. 블로그 페이지로 와서 보니 이미지를 못찾은 것인지 깨져서 나온다. 무슨일인걸까??




---
## 나의 해결 방법


![이미지6](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-20_6.jpg?raw=true)

어..?
우클릭 해서 **이미지 주소 복사**를 해서 나온 링크를 다시 붙였더니 해결됐다..
무슨 차이인가 봤더니 링크의 맨뒤에 **?raw=true**가 추가되는 구조이다.

무슨 기능인지, 무슨 차이로 이렇게 된 것인지는 모르겠지만 뭔가 해결돼서 뿌듯하다..

이미지를 올릴 때 방법을 정리해야겠다.
포스트에 따른 이미지 이름 정하기 - 이미지 올릴 순서대로 번호를 정하기 - 이미지를 먼저 푸시하기 - 링크 가져와서 md파일에 배치하기

대략 이런 느낌으로..
뭔가 비효율적인 것 같지만 더 좋은 방법을 찾거나 연구해봐야겠다.

md파일 에디터도 있던데 그것도 공부 해봐야지.