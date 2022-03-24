---
layout: post
title:  "[Github blog] 블로그 테마 업그레이드하기_1"
subtitle: 
date: 2022-03-24 09:07:23 +0900
categories: project
tags:
comments: true
related_posts:

---

## 깃허브 블로그 업그레이드하기.

[new_ver 브랜치](https://github.com/WookeyKim95/WookeyKim95.github.io/tree/new_ver)

2022년 3월 23일 기준 Hydejack 6.4버전을 사용하고 있었다.<br/>

그리고 조만간 9.x대 버전으로 업그레이드 하려고 한다.<br/>

사실, Start Kit을 내려받아서 복사 붙여넣기만 하면 될 줄 알았는데, 그게 아니었다.<br/>

왼쪽의 Sidebar 구성도 그렇고, 포스트의 레이아웃도 바뀐 것인지 포스트의 구성을 새로 바꿔줘야 했다.<br/>

### 1. 포스트의 구성<br/>

![포스트구성1](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-24_upgrade_1.jpg?raw=true)
<br/>

![포스트구성2](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-24_upgrade_2.jpg?raw=true)
<br/>

title이 아니라, 글의 맨 첫번째 줄을 제목으로 사용하게 된다.<br/>
이거는 그냥 포스트 안에서 첫째줄에 제목을 넣어주었다.<br/>
<br/>

### 2. 좌측 Sidebar의 구성<br/>

카테고리별로 테마 컬러와 사이드바의 배경을 바꿀 수 있게 설계된 것 같다.<br/>
이건 정말로 좋다.<br/>

근데 왜 sidebar_bg.jpg파일이 아니라 엉뚱한 사진이 배경으로 나오는지 모르겠다..<br/>

그리고 지정한 사진으로 바뀌도록 하면 검은색 화면만 나온다.<br/>

Ruby 언어의 문제인지, 레이아웃의 문제인지 구분부터 해야할 것 같다.<br/>
<br/>

사이드바에서 나오는 문제, 해결해야할 점을 다시 정리하면

- 로고가 나오지 않는다.
- Description이 나오지 않는다.
- 엉뚱한 배경이 나온다.

이 정도이다.<br/>

[Hydejack 홈페이지](https://hydejack.com/docs/config/)

사실 여기에 힌트가 있긴하다.

_config.yml 파일의 구성을 바꾸라고 한다.<br/>

하지만

```
# Sidebar image and theme color of the site.
accent_image:          /assets/img/sidebar-bg.jpg
accent_color:          rgb(79,177,186)

defaults:
  # You can use this to provide a default accent color and background for
  # all pages under a given path:
  - scope:
      path:            assets/img/
    values:
      accent_color:    rgb(38,139,210)
      theme_color:     rgb(32,32,32)
      accent_image:    
        background:    sidebar-bg.jpg
        overlay:       false
```

위와 같이 바꿨는데도 달라지는 것은 없었다. 흠.. 뭐가 문제일까<br/>

생각해보면 기본 배경으로 나오는 물결 사진은 내 깃허브 블로그 소스 폴더에는 없다.<br/>

그렇다면 웹을 통해서 불러왔다는 것인데 그 방법을 생각하면..?<br/>

```
accent_image:          https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/main_left.JPG?raw=true
accent_color:          rgb(79,177,186)

defaults:
  # You can use this to provide a default accent color and background for
  # all pages under a given path:
  - scope:
      path:            https://github.com/WookeyKim95/WookeyKim95.github.io/tree/main/assets/img/
    values:
      accent_color:    rgb(38,139,210)
      theme_color:     rgb(32,32,32)
      accent_image:    
        background:    main_left.JPG?raw=true
        overlay:       false
```

현재 필자가 사용하고 있는 배경의 웹 경로를 붙여넣었다.<br/>

그리고 그 결과..<br/>

![성공!](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-24_upgrade_3.jpg?raw=true)

아싸!!!! 해결!!<br/>

짜릿하다. 늘 새롭다.<br/>

그렇다면 카테고리 목록에 있는<br/>

```
accent_color: '#268bd2'
accent_image:
  background: owner_char.png?raw=true
  overlay:    false
```

이 부분도 잘 적용 될 것 같다. 웹 경로를 넣던지, 이미지를 넣어서 실험해봐야겠다.<br/>

일단, 상대 경로를 넣었을 땐 검은화면이 나왔다.<br/>

```
accent_color: '#268bd2'
accent_image:
  background: https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/owner_char.png?raw=true
  overlay:    false
```
그리고 웹 경로를 넣어도 검은화면만 출력되었다.<br/>

음.. 추측되는 원인이 있다. jpg 파일을 사용해야하는데 png파일을 사용해서 그런 것 같다는 생각이 들었다.<br/>

아무 jpg파일로 한번 바꿔보았다.<br/>

결과는.. **실패..**<br/>
<br/>

하지만 탐나는 기능이라 포기할 수 없다..<br/>

그래도 오늘은 진전이 있었고 힌트도 얻었으니 뭐가 문제인지 원리가 무엇인지 꼭 찾을테다.<br/>

