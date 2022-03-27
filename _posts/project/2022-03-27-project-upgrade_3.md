---
layout: post
title:  "[Github blog] 블로그 테마 업그레이드하기_3"
subtitle: 
date: 2022-03-27 09:01:13 +0900
categories: project
tags:
comments: true
related_posts:

---

# [Github blog] 블로그 테마 업그레이드하기_3

[new_ver 브랜치](https://github.com/WookeyKim95/WookeyKim95.github.io/tree/new_ver)

결론부터 말하면 테마마다 배경화면 바꾸기에 성공했다.<br/>

지금부터 그 방법을 기록하겠다.<br/>

## 깃허브 블로그 사이드바 배경화면바꾸기

[Hydejack 홈페이지](https://hydejack.com/docs/config/#changing-accent-colors-and-sidebar-images)

```
accent_image: /assets/img/sidebar-bg.jpg
accent_color: rgb(79,177,186)
```

_config.yml 파일 안에 있는 내용이다. 이 부분에서 사이드바의 배경화면과 카테고리 테마컬러를 설정해 줄 수 있다.<br/>

핵심은 **변경하고자 하는 카테고리에서 이 레이아웃을 그대로 따라주어야 한다는 것이었다.**<br/>

```
layout: list
title: Hobby
slug: hobby
menu: true
submenu: true
order: 4
description: >
  취미생활 기록일지
accent_color: '#6666cc'
accent_image:
  background: 
  overlay:    false  
```

블로그 리소스 폴더의 _featured_categories에 있는 파일 중 하나의 내용이다.<br/>

여기서
```
accent_color: '#6666cc'
accent_image:
  background: 
  overlay:    false  
```
이 부분을 아래와 같이 수정하였다.

```
accent_image: '웹 이미지의 주소'
accent_color: '색깔코드'
```

```
accent_image: 'https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/og_picture.jpg?raw=true'
accent_color: '#6666cc'
```
필자의 경우는 위와 같았다.<br/>

이 코드를 적용하고 bundle exec jekyll serve 코드를 실행하였다. 그랬더니..<br/>

![기본테마](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-26_upgrade_1.jpg?raw=true)

기본테마<br/>

![블로그](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-26_upgrade_2.jpg?raw=true)

블로그 카테고리로 이동 후 <br/>

사이드바 배경화면 전환에 성공했다..<br/>

후.. 이제 여기다가 새로 만든 나만의 배경을 넣어주면 된다.<br/>

해냈따 해냈어...<br/>

다만, 터미널에서
```
[2022-03-26 10:20:53] ERROR `/{"background"=>nil, "overlay"=>false}' not found.
```
이런 에러 코드가 화면 전환시에 발생한다. 근데 어쩌겠나.. overlay 코드 형식을 따랐더니 구동이 안되는걸..<br/>
<br/>

이제 로고문제를 해결하면 될 것 같다.<br/>
<br/>

**오늘의 교훈<br/>**

[설명서](https://hydejack.com/docs/config/#changing-accent-colors-and-sidebar-images)를 잘 읽자.