---
layout: post
title:  "[공통프로젝트] 플랫폼 제작 일지 2"
subtitle: 
date: 2024-01-20 06:17:34 +0900
categories: project
tags:
comments: true
related_posts:

---
# [공통프로젝트] 플랫폼 제작 일지 2<br/>

총 개발기간 : 01월 08일 ~ 02월 16일<br/>
<Br/>

요약하면 백엔드 측은 ERD, API, 기능 명세서 작성에 들어갔고<br/>

프론트엔드 측은 그동안 React 공부 및 기본 템플릿 코드를 작성을 하기로 했다.<br/>

즉, 2주차 스프린트는 아래와 같다.<br/>

- React, GSAP 공부
- API 명세서 작성 (백엔드와 같이 진행)
- UI 배치를 위한 기본 템플릿 코드를 작성

<br/>

## React, GSAP 공부<br/>
<br/>

본격적으로 React를 공부하는데 나는 아래 강의를 참고하였다.<br/>

- 패스트캠퍼스 프론트엔드 개발 초격차 패키지
- [코딩애플 리액트 강의](https://www.youtube.com/watch?v=00yJy7W0DQE&list=PLfLgtT94nNq0qTRunX9OEmUzQv4lI4pnP)

<br/>

### UI 배치를 위한 기본 템플릿 코드를 작성하는 과정에서 실수<br/>

React 프로젝트를 구성하고, 우선 패스트캠퍼스 강의를 따라치면서 폴더를 구성해보았다.<br/>

그런데 올바르게 쳤다고 생각했는데 홈페이지가 제대로 작동이 되지 않았다.<br/>

알고보니 store 문제였다.<br/>

프로젝트를 구성하는 과정에서 src 폴더 내에는 components, pages, store이 있는데<br/>

아무래도 쇼핑몰을 구성하는 프로젝트였다보니 강사님께서 임의로 store라고 지으셨다고 생각했다.<br/>

그래서 난 store를 다른 이름으로 지었었는데 그게 실수였다.<br/>

store.js가 있다는 것도 이제 알았고, store가 고유명사라는 것을 알아버렸다.<br/>

그래서 store로 다시 고쳐주니까 올바르게 작동되었다.<br/>

<br/>

### React의 SCSS 적용<br/>

놀랬던게 scss를 적용하기 위해서는 css로 컴파일을 해야한다고 생각했다.<br/>

그런데 sass가 잘 설치 됐다면 그럴 필요 없이

```
import "./App.scss"
```

바로 scss파일을 import 시키면 된다. 이렇게 하면 자동으로 scss를 적용시켜준다.<br/>
<br/>

## ERD 작성 및 검사<br/>
<br/>

백엔드 측에서 ERD를 작성했고, 컨설턴트님께 검사를 받았다.<br/>

검사를 받은 뒤에는 굉장히 고려할 것이 많다는 것을 깨달았다.<br/>

- 포인트 충전 및 소비 로그를 세세하게 기록을 해줘야 한다는 점
+ 포인트 충전 일시, 소비 일시
+ 포인트 충전 수단
+ 포인트 소비 유저

- 경매 진행 로그
+ 입찰 유찰 여부
+ 예정 시간
+ 실제로 시작된시간
+ 끝난 시간
+ 누가 얼마에 샀는지?

- **수수료 테이블**

<br/>

## 상품 목록 및 상세 페이지 구현<br/>
<br/>

패스트캠퍼스 강의를 참고하면서 구현을 실시하였다.<br/>

![화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/General_Project2_1.png?raw=true)

우선은 상품이 올바르게 나타나는지 확인하기 위해서 fakestoreapi에서 상품을 따왔다.<br/>

그렇기 때문에 카테고리 명도 우선은 클론을 실시했지만 나중에는 General, Auction 등으로<br/>

바꿔주어야한다.<br/>

이제 할 일은 백엔드 서버가 구축이 되면 상품의 사진을 불러오는 등 연결을 실시하는 것이다.<br/>

상품 목록 상세 페이지도 아래와 같이 구현하였다.<br/>

![Alt text](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/General_Project2_2.png?raw=true)