---
layout: post
title:  "[Web] 웹 애니메이션 2"
subtitle:  
date: 2022-03-02 11:54:39 +0900
categories: study
tags: web_&_frontend
comments: true
related_posts:

---

# [Web] 웹 애니메이션 2<br/>

## 웹 페이지에 애니메이션 부여하기 2<br/>

[클론 프로젝트 깃허브 링크](https://github.com/WookeyKim95/clone_StarbucksKorea)<br/>

[참고링크 : 박영웅님의 깃허브](https://github.com/Parkyoungwoong/starbucks-vanilla-app)

<br/>
지난 포스트에 이어서 웹 페이지에 애니메이션을 부여하는 방법을 포스팅해보고자 한다. 이번에 나오는 애니메이션은 아래와 같다.<br/> 
<br/>

- 버튼을 클릭하면 변화를 주는 애니메이션
- hover시 객체가 뒤집히는 애니메이션

<br/>


### 버튼을 클릭하면 변화를 주는 애니메이션
---

클론프로젝트에서 스타벅스 프로모션 옆에 있는 버튼을 누르면 슬라이드가 없어졌다가 나타났다가 한다. <br/>

아래 링크로에 있는 Swiper를 이용할 수 있다.<br/>
[Swiper](https://swiperjs.com/)<br/>

```
<div class="inner__right">
    <h2>스타벅스 프로모션</h2>
    <div class="toggle-promotion" onclick="promotion_change()">
        <div class="material-icons">download</div>
    </div>

</div>

<div class="promotion">
    <div class="swiper-container">
        <div class="swiper-wrapper">
            <div class="swiper-slide">
                <img src="./images/promotion_slide1.jpg" alt="뉴이어 스타벅스">
                <a href="javascript:void(0)" class="btn">자세히 보기</a>
            </div>
            <div class="swiper-slide">
                <img src="./images/promotion_slide2.jpg" alt="뉴이어 스타벅스">
                <a href="javascript:void(0)" class="btn">자세히 보기</a>
            </div>
            <div class="swiper-slide">
                <img src="./images/promotion_slide3.jpg" alt="뉴이어 스타벅스">
                <a href="javascript:void(0)" class="btn">자세히 보기</a>
            </div>
            <div class="swiper-slide">
                <img src="./images/promotion_slide4.jpg" alt="뉴이어 스타벅스">
                <a href="javascript:void(0)" class="btn">자세히 보기</a>
            </div>
            <div class="swiper-slide">
                <img src="./images/promotion_slide5.jpg" alt="뉴이어 스타벅스">
                <a href="javascript:void(0)" class="btn">자세히 보기</a>
            </div>
        </div>
    </div>
    <div class="swiper-pagination"></div>
    <div class="swiper-prev">
        <div class="material-icons">arrow_back</div>
    </div>
    <div class="swiper-next">
        <div class="material-icons">arrow_forward</div>
    </div>
</div>

```

지금 이 코드는 홈페이지에서 프로모션  배너의 자리를 차지하는 클래스이다.<br/>

사진으로 나타내면 바로 여기이다.

![프로모션 배너](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/web_and_frontend/2022-03-02_1.jpg?raw=true)

<br/>

여기에다가 CSS 에서는<br/>

```
.notice .promotion {
    height: 693px;
    background-color: #f6f5ef;
    position: relative;
    transition: height .4s;
    overflow: hidden;
}

.notice .promotion.hide { /*띄어쓰기 하지 말것!*/
    height: 0;
}

.notice .promotion .swiper-container {
    width: calc(819px*3 + 20px);
    height: 553px;
    position:absolute;
    top: 40px;
    left: 50%;
    margin-left: calc((819px*3 + 20px) / -2);
}

.notice .promotion .swiper-slide {
    opacity: .5;
    transition: opacity 1s;
}

.notice .promotion .swiper-slide-active {
    opacity: 1;
}

.notice .promotion .swiper-slide .btn {
    position: relative;
    margin: auto;
    left: 0;
    right: 0;
    bottom: 40px;
}

.notice .promotion .swiper-pagination {
    bottom: 40px;
    left: 0px;
    right: 0px;
}

.notice .promotion .swiper-pagination .swiper-pagination-bullet {
    background-color: transparent;
    background-image: url(../images/promotion_slide_pager.png);
    width:12px;
    height:12px;
    margin-right: 6px;
    outline: none;
}

.notice .promotion .swiper-pagination .swiper-pagination-bullet:last-child {
    margin-right: 0;
}

.notice .promotion .swiper-pagination .swiper-pagination-bullet-active {
    background-image: url(../images/promotion_slide_pager_on.png);
}

.notice .promotion .swiper-prev, /*선택자를 여러개 하고 싶을 때!*/
.notice .promotion .swiper-next {
    width: 42px;
    height: 42px;
    border: 2px solid #333;
    border-radius: 50%;
    position: absolute;
    top: 300px;
    z-index: 1;
    cursor: pointer;
    outline: none;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: .4s;
}

.notice .promotion .swiper-prev {
    left: 50%;
    margin-left: -480px;
}

.notice .promotion .swiper-next {
    right: 50%;
    margin-right: -480px;
}

.notice .promotion .swiper-prev:hover, 
.notice .promotion .swiper-next:hover {
    background-color: #333;
    color: #fff
}

```

위와같이 추가를 할 수 있다.<br/>

그리고 자바스크립트로 이동해보면

```

new Swiper('.promotion .swiper-container', {
    direction: 'horizontal', // 재생 방향
    slidesPerView: 3, // 한번에 보일 슬라이드 수
    spaceBetween: 10, // 슬라이드 사이 여백 (픽셀)
    centeredSlides: true,
    loop: true, // 반복재생
    autoplay: { // 자동재생
        delay:5000, // (단위 : ms)
    },
    pagination: {
        el:'.promotion .swiper-pagination', // 페이지 번호 요소 선택자
        clickable: true,  // 클릭이 가능한지?
    },
    navigation: {
        prevEl: '.promotion .swiper-prev', // 버튼 누르면 이전 슬라이드로
        nextEl: '.promotion .swiper-next', // 버튼 누르면 다음 슬라이드로
    }
});

const promotion_element = document.querySelector('.promotion');
const promotion_toggle_button = document.querySelector('.toggle-promotion');

let is_promotion_hidden = false;

let promotion_change = function () {
    let toggle_button_up_down = document.querySelector('.toggle-promotion .material-icons') // 상태별 버튼의 방향을 바꾸기 위함.
    is_promotion_hidden = !is_promotion_hidden;
    // 값이 반대가 되게 해라, 즉 지금 상태에선 False에서 True로 바꾸어라.
    if (is_promotion_hidden) {
        // true : 숨김 처리
        promotion_element.classList.add('hide');
        promotion_toggle_button.classList.add('.hide');
        toggle_button_up_down.innerHTML='upload';
    } else {
        // false : 숨김 해제
        promotion_element.classList.remove('hide');
        promotion_toggle_button.classList.remove('.hide');
        toggle_button_up_down.innerHTML='download';
    }
};

```

위와 같은 코드가 있다.<br/>
Swiper를 이용한 애니메이션이며, 이 코드를 통해서 스타벅스 프로모션 오른쪽에 있는 버튼을 누르면 컨테이너가 사라지며 다시 클릭하면 다시 나타나는 구조가 된다.<br/>

즉, 위에서 HTML에 대한 CSS와 JS의 역할은

- HTML : swiper를 받아들일 블록을 준비한다.<br/>
이때, container - wrapper - (slide1, slide2, slide3, ...) 구조를 따를 것.
- CSS : 부드럽게 사라졌다가 나타나도록 하는 기능을 부여한다.
- JS : 사진이 자동으롯 슬라이드 되도록 한다.<br/>
그리고 버튼을 클릭하면 사라졌다가 나타나도록 하는 기능을 부여한다.
<br/>
<br/>

### 커서를 올리면 나타나는 애니메이션
---

어떤 배너에 커서를 올리면 애니메이션이 나타나도록 할 수 있다.<br/>


```
<!--Reserve Coffee-->

    <section class="reserve-coffee scroll-spy">
        <div class="inner">
            <img src="./images/reserve_logo.png" alt="" class="reserve-logo back-to-position to-right delay-0">

            <div class="text-group">
                <img src="./images/reserve_text.png" alt="" class="description back-to-position to-right delay-1">
                <div class="more back-to-position to-right delay-2">
                    <a href="javascript:void(0)" class="btn btn--gold">자세히 보기</a>
                </div>
            </div>

            <img src="./images/reserve_image.png" alt="" class="product back-to-position to-left delay-3">
        </div>
```

html에 다음과 같은 클래스가 있다. 사진으로는 아래와 같다.<br/>

![리저브 배너 앞](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/web_and_frontend/2022-03-02_2.jpg?raw=true)
<br/>
커서를 올리지 않았을 때
<br/>
<br/>

![리저브 배너 뒤](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/web_and_frontend/2022-03-02_3.jpg?raw=true)
<br/>
커서를 올렸을 때
<br/>
위와 같이 커서가 올라가면 뒷면이 보이도록, 그렇지 않으면 앞면이 보이도록 할 수 있다.<br/>
<br/>

```
.reserve-store {
    background-image: url(../images/reserve_store_bg.jpg);
    background-repeat: no-repeat;
    background-position: center;
    background-attachment: fixed; /*배경이 같이 스크롤 되지 않음.*/
    background-size: cover;
}

.reserve-store .inner {
    height: 600px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.reserve-store .inner .container {
    width: 334px;
    height: 334px;
    perspective: 600px; /*원근법 효과를 부여함.*/
}

.reserve-store .inner .container .item {
    width: 334px;
    height: 334px;
    backface-visibility: hidden;
    transition: 1s;
}

.reserve-store .inner .container .item.front {
    position:absolute;
    transform: rotateY(0deg);
}

.reserve-store .inner .container:hover .item.front {
    transform: rotateY(180deg);
}

.reserve-store .inner .container .item.back {
    transform: rotateY(-180deg);
}

.reserve-store .inner .container .item.back .btn {
    position: absolute;
    top:240px;
    left:0;
    right:0;
    margin: auto;
}

.reserve-store .inner .container:hover .item.back {
    transform: rotateY(0deg);
}

```

<br/>

이번에는 자바스크립트를 이용하지 않고 CSS만으로 구현할 수 있다.<br/>
transform: rotateY(#deg); 기능을 통해서.<br/>
rotateY를 통해서 hover 여부에 따라서 y축방향을 축으로 해서 회전시킬 수 있고, 반대로 rotateX 기능을 통해서 x축방향을 축으로 하여 회전이 가능하다.<br/>

hover여부에 따른 클래스를 HTML에서 분류하고,<br/>
CSS에서 각 상태에 따른 클래스를 만들어주면 된다.<br/>

<br/>
<br/>

### 객체가 둥둥 떠다니는 애니메이션
---

또한 JavaScript를 통해서 마치 떠다니는 듯한 애니메이션을 만들 수 있다.<br/>

![떠다니는 애니메이션](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/web_and_frontend/2022-03-02_4.jpg?raw=true)

```
// 범위 랜덤 함수(소수점 2자리까지)
function random(min, max) {
    // `.toFixed()`를 통해 반환된 문자 데이터를,
    // `parseFloat()`을 통해 소수점을 가지는 숫자 데이터로 변환
    return parseFloat((Math.random() * (max - min) + min).toFixed(2))
  }
function floating_object(selector, delay, size) {
    //gsap.to(요소, 시간, 옵션)
    gsap.to(selector, random(1.5, 2.5), { // random에서 실행한 함수로 반환 된 값으로 실행.
        // 옵션
        y: 20,
        repeat: -1, // -1 : 무한반복
        yoyo: true, // 내려갔다가 올라갔다가 하는 옵션
        ease: Power1.easeInOut, // 부드럽게 시작 부드럽게 종료를 해주는 옵션. https://greensock.com/docs/v2/Easing
        delay: random(0, 1.5), // 애니메이션 동작 시간
    })
}

floating_object('.floating1', 1, 15);
floating_object('.floating2', .5, 15);
floating_object('.floating3', 1.5, 20);
```

위와 같은 코드를 이용할 수 있다.<br/>
parseFloat를 통해서 물체가 이동하는 효과를 부여할 수 있다.<br/>

그리고, 물체가 부드럽게 이동하도록 하는 효과로 주석에 달린것 처럼 아래의 링크에서 코드를 따올 수 있다.<br/>



[Greensock_Easing](https://greensock.com/docs/v2/Easing)
<br/>
그리고 여기서 random이 들어간 이유는 항상 일정한 간격이 아니라, 한번 왕복할 때 마다 떠다니는 간격을 다르게 하기 위함이다.<br/>

```
<section class="youtube">
        <div class="youtube__area">
            <div id="player"></div>
        </div>
        <div class="youtube__cover"></div>
        <div class="inner">
            <img src="./images/floating1.png" alt="Icon" class="floating floating1">
            <img src="./images/floating2.png" alt="Icon" class="floating floating2">

        </div>
    </section>

    <!--Season Product-->
    <section class="season-product scroll-spy">
        <div class="inner">
            <img src="./images/floating3.png" alt="Icon" class="floating floating3">
            <img src="./images/season_product_image.png" alt="" class="product back-to-position to-right delay-0">


            <div class="text-group">
                <img src="./images/season_product_text1.png" alt="" class="title back-to-position to-left delay-1">
                <img src="./images/season_product_text2.png" alt=""
                    class="description back-to-position to-left delay-2">
                <div class="more back-to-position to-left delay-3">
                    <a href="javascript:void(0)" class="btn">자세히 보기</a>
                </div>
            </div>
        </div>

    </section>
```

HTML 코드는 위와 같다. 여기서 section이 2개인 이유는 3번째 플로팅 객체 즉 floating3가 두 섹션에 걸쳐서 떠다니기 때문이다.<br/>

그래서 첫번째와 두번째 객체는 youtube section안의 div 블록에, 세번째 floating 객체는 그 다음 섹션의 inner에다가 배치된 형태를 띄고 있다.<br/>
