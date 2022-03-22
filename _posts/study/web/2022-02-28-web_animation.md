---
layout: post
title:  "[Web] 웹 애니메이션 1"
subtitle:  
date: 2022-02-28 09:01:17 +0900
categories: study
tags: web_&_frontend
comments: true
related_posts:

---

# [Web] 웹 애니메이션 1<br/>

## 웹 페이지에 애니메이션 부여하기 1<br/>

[클론 프로젝트 깃허브 링크](https://github.com/WookeyKim95/clone_StarbucksKorea)<br/>

[참고링크 : 박영웅님의 깃허브](https://github.com/Parkyoungwoong/starbucks-vanilla-app)

최근에 클론 프로젝트, 즉 홈페이지 따라 만들기로 스타벅스 코리아의 홈페이지를 만들었다.<br/>

여기서 재미있는 기능들, 특히 스크롤에 따른 애니메이션을 부여하는 기능이 재밌어서 여기다가 정리하려고 한다.<br/>
<br/>



### 투명도 변화 애니메이션
---

파워포인트에 있는 밝기 변화 나타내기와 같은 기능을 한다.<br/>


```
<section class="visual">

        <div class="inner">

            <div class="title fade-in">
                <img src="./images/visual_title.png" alt="타이틀">
                <a href="javasctipt:void(0)" class="btn btn--brown">자세히 보기</a>
            </div>
            <div class="fade-in">
                <img src="./images/visual_cup1.png" alt="cup1" class="cup1 image">
                <img src="./images/visual_cup1_text.png" alt="cup1_text" class="cup1 text">
            </div>
            <div class="fade-in">
                <img src="./images/visual_cup2.png" alt="cup2" class="cup2 image">
                <img src="./images/visual_cup2_text.png" alt="cup2_text" class="cup2 text">
            </div>
            <div class="fade-in">
                <img src="./images/visual_spoon.png" alt="spoon" class="spoon">
            </div>

        </div>
    </section>
```

지금 이 코드는 홈페이지에서 메인 배너의 자리를 차지하는 섹션이다.<br/>

여기서 fade-in 이라는 클래스를 부여하고

```
.visual .fade-in {
    opacity:0;
}

```

CSS에는 다음과 같은 클래스를 만들어준다.<br/>

그리고 자바스크립트로 이동해보면

```
const fade_elements = document.querySelectorAll('.visual .fade-in');
fade_elements.forEach(function (fade_element, index) {
    // gsap.to(요소, 지속시간(s 단위), 옵션)
    gsap.to(fade_element, .6, {
        delay: (index + 1) * .7, // 각 인덱스에 있는 요소가 시작 시간은 동시라서 순차적으로 나타내려면 index를 곱해야함!
                                // 0.7, 1.4, 2.1, 2.7 초 뒤에 각각!
        opacity: 1
    });
});
```

위와 같은 코드가 있다.<br/>
주로 gsap을 이용하며, delay와 opacity를 이용해서 만들 수 있다.<br/>

문서에서 visual 속의 fade-in이라는 클래스를 가진 객체를 선택하고
그 객체가 순차적으로 투명도가 변화하며 나타나도록 설정하는 것이다.<br/>

이때 각 객체들이 순차적으로 나타나게 하기 위해서

시간 간격 * 번째 수 를 delay에 부여함으로써 순서대로 나타나는 효과를 만들 수 있다.<br/>
<br/>

### 스크롤에 따른 애니메이션 1
---

스크롤 수치에 따라서도 애니메이션을 만들 수 있다.<br/>
우선, [Gsap 라이브러리](https://cdnjs.com/libraries/gsap)를 가져와서 스크립트로 활용해야한다는 것을 알아두자.

```
<div class="badges">
    <div class="badge">
        <img src="./images/badge1.jpg" alt="badge">
    </div>
    <div class="badge">
        <img src="./images/badge2.jpg" alt="badge">
    </div>
</div>
```

html에 다음과 같은 클래스가 있다. 화면 우측에 있는 뱃지 혹은 배너를 구성하는 요소이다.<br/>


```
// 스크롤에 따라 나타나고 사라질 요소 지정
const badge_element = document.querySelector('header .badges');

/* window : 브라우저가 가지는 명령*/
window.addEventListener('scroll', _.throttle( function () {
    /*throttle : 일정 시간마다 한번씩 실행시켜서 부하 방지하는 기능*/
    /*숫자는 ms 단위*/

    if (window.scrollY > 500) {
        // 배지를 숨기기
        // to top 버튼이 나타나는 스크립트
        // gsap.to(요소, 지속시간(s 단위), 옵션)
        gsap.to(badge_element, .6, {
            opacity: 0,
            display: 'none'
        })
    } else {
        // 배지 보이기
        // to top 버튼이 숨기는 스크립트
        gsap.to(badge_element, .6, {
            opacity: 1,
            display: 'block'
        });
    }
}, 300));
```


그리고 자바스크립트를 보면 위와 같이 코딩이 되어있는데, 찬찬히 살펴보자.

window.addEventListner('scroll') 을 통해서 스크롤을 감지할 수 있다.<br/> 이때, 스크롤을 할때 마다 매번 입력을 받으면 과부하가 걸릴 수 있으니 throttle을 통해 일정시간 딜레이를 넣어줌으로써 과부하를 방지한다.<br/>

여기서 조건문만 간단하게 살펴보자.

window.scrollY가 맨 위에서 얼마나 내려갔는지 나타내는데, 만약 저 수치가 500px이상이면 배지가 사라지고, 아니라면 다시 나타나는 구조이다.

즉, gsap, window.scrollY를 활용해서 스크롤에 따른 애니메이션을 부여할 수 있는 것이다.<br/>


이외에도, 자바스크립트로 애니메이션을 부여할 수 있는 방법이 있다. 그 방법은 다음에 포스팅하도록 하겠다.