---
layout: post
title:  "[클론프로젝트] 스타벅스코리아 1일차"
subtitle:
date: 2022-02-24 18:45:51 +0900
categories: project
tags:
comments: true
related_posts:

---

## 스타벅스 코리아 홈페이지 만들기

웹 프론트엔드 개발자를 지망하는 만큼 홈페이지를 잘 만들줄 알아야한다.<br/>
하지만 개발 공부를 시작한지 3개월차가 되어가는 내가 할 수 있는 것은 인터넷 강의를 따라서 홈페이지를 따라 만들어 보는 것이다.<br/>
<br/>
그래서 홈페이지를 따라 만들기로 해보았다.<br/>
수강한 강의는 <br/>

**패스트캠퍼스 - 프론트엔드 초격차 패키지**이다.<br/>

[내 프로젝트 깃허브 저장소 링크](https://github.com/WookeyKim95/clone_StarbucksKorea)

README에는 간단하게 업데이트 히스토리를, 이곳에는 업데이트 내용과 오류 내역을 기록해보려 한다.<br/>


### 1일차
---

상단의 로고를 배치하고 헤더와 태그 메뉴를 만들었다.<br/>

링크는 아직 달지 않은 상태이다.

### 겪어본 오류

![오류1](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_24_1.jpg?raw=true)

헤더 메뉴에서 메뉴 목록이 출력되지 않는 오류를 겪었다.<br/>

해결과정은 아래와 같다.<br/>

<br/>

---

아래는 오류 발생 원인 지점이다.<br/>
<br/>
![오류원인](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_24_1_cause.jpg?raw=true)

여기서 ul이아니라 div 클래스를 사용해야했다. 그리고 ul 를 class를 inner로 사용해야한다.<br/>

(각각의 하위 메뉴 목록을 가운데 정렬하기 위해서 contents__menu는 div 클래스, display는 block을 상속받은 상태이며 ul는 display가 flex인 형태이다.)

![오류수정](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_24_1_fix.jpg?raw=true)

그래서 위와같이 코드를 수정하였다.<br/>

---

![오류수정](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_24_1_fix_result.jpg?raw=true)

의도한대로 깔끔하게 출력되었다.<br/>




