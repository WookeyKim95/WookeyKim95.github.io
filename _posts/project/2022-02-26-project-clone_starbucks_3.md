---
layout: post
title:  "[클론프로젝트] 스타벅스코리아 3일차"
subtitle:
date: 2022-02-26 17:53:26 +0900
categories: project
tags:
comments: true
related_posts:

---

## 스타벅스 코리아 홈페이지 만들기 3일차

[내 프로젝트 깃허브 저장소 링크](https://github.com/WookeyKim95/clone_StarbucksKorea)
<br/>

API 끌어다 쓴 링크<br/>

[Swiper](https://swiperjs.com/)<br/>
[Gsap_scrollToPlugin](https://greensock.com/scrolltoplugin/)<br/>
[ScrollMagic](https://cdnjs.com/libraries/ScrollMagic)<br/>
<br/>

---

오늘은 섹션을 여러가지 추가해보았다.<br/>

사용해본 API, CDN은

- 스와이퍼
- 스크롤매직
- Gsap의 ScrollToplugin


이 세개를 통해서 슬라이드를 하면서 순차적으로 애니메이션을 부여하는 방법을 배웠다.<br/>
신기하기도 했지만 자세한 것을 다룰려면 아직 연습이 더 필요할 것 같다.<br/>

뭔가 PPT에 애니메이션을 불어넣어서 만들어보는 느낌이었다. PPT보다 훨씬 어렵지만..<br/>
<br/>

## 겪어본 오류들<br/>


### 첫 번째 오류
---

![오류1](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_1.jpg?raw=true)

지금 이 오류 현상은 무엇이냐, 유튜브 동영상이 나타나지 않는다.<br/>

![오류1 원인](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_1_cause_1.jpg?raw=true)

알고보니까 id="player"에 큰 따옴표 ("")를 감싸지 않아서 발생하는 것 같았다.<br/>

![오류1 원인2](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_1_cause_2.jpg?raw=true)

그리고 CSS에서도 클래스 안에서

```
display: "block";
```
으로 큰 따옴표로 감싸면 안되는 부분에 큰 따옴표를 감쌌다.<br/>
<br/>

![오류1 원인](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_1_fix_1.jpg?raw=true)

HTML의 id 이름에는 큰 따옴표를 감싸주었고,<br/>

![오류1 원인2](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_1_fix_2.jpg?raw=true)

그리고 CSS에서도 클래스 안에서

```
display: block;
```
으로 수정하였다. 위의 사진과 같이.<br/>
<br/>
<br/>

![오류1 해결](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_1_result.jpg?raw=true)<br/>

오케이. 해결되었다. 영상이 잘 나온다.<br/>
<br/>

### 두 번째 오류
---

![오류1](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_2.jpg?raw=true)

![오류2 원인](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_2_cause_1.jpg?raw=true)

위 두 사진을 같이 보면 내가 하려고 했던 것은 저 배너들이 한 번에 5개씩 나타도록 만들려고 했지만 그보다 많은 7개가 나타난다.<br/>

분명 강사님의 코드엔 문제가 없었다.<br/>

**하지만 이번에는 바로 해결할 수 있었다.<br/>**

![오류2 수정](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_2_fix.jpg?raw=true)

```
overflow: hidden;
```

어제 겪었던 오류랑 똑같은 오류였다. 그래서 그런지 1시간을 해맸던 어제와는 달리 이번엔 바로 해결할 수 있었다.<br/>

이게 바로 오답노트의 힘인가..<br/>

![오류2 해결](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_26_2_result.jpg?raw=true)

오케이! 이제 의도한 대로 5개만 나타난다.<br/>

---

내 첫 클론 프로젝트이다. 이 프로젝트도 어느새 막바지에 접어들고있다. <br/>
다음 단계로 푸터와 페이지의 맨 위로 향하는 버튼을 만들면 이 프로젝트는 끝이난다. <br/>

근데 프로젝트 만들기는 끝이지만 코드 자체는 여러번 볼 것 같다.<br/>
나중에 내 홈페이지를 만들 때 참고해야하니까.<br/>

나중에 시간이 날 때 블로그에 각 API 사용법을 정리해야겠다. <br/>
너무 유용하네.<br/>
