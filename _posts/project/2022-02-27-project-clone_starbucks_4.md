---
layout: post
title:  "[클론프로젝트] 스타벅스코리아 4일차"
subtitle:
date: 2022-02-27 09:36:21 +0900
categories: project
tags:
comments: true
related_posts:

---

## 스타벅스 코리아 홈페이지 만들기 4일차

[내 프로젝트 깃허브 저장소 링크](https://github.com/WookeyKim95/clone_StarbucksKorea)
<br/>

API 끌어다 쓴 링크<br/>

[Gsap_scrollToPlugin](https://greensock.com/scrolltoplugin/)<br/>
<br/>

---

오늘은 Footer와 홈페이지의 맨 위로 이동하는 버튼을 추가하고 어제 배운 CSS의 Rotate를 활용하여 아주 소소한 나만의 기능을 만들어보았다.<br/>

### 겪어본 오류들<br/>

이번에는 크게 오류를 겪지는 않았던 것 같다.<br/>

아래와 같이 반복되는 실수들<br/>

- div 등 선택자와 클래스의 배치
- CSS에서 선택자 앞에는 . 붙이면 안됨.
```
ex)
.footer .btn {
    바르지 못한 예시
}

footer .btn {
    올바른 예시
}
```

충분히 반복적으로 하는 실수가 나왔었다.<br/>
물론 겪어보거나 배운 내용에서 나온 실수이기 때문에 빠르게 원인을 찾아서 해결할 수 있었다.<br/>
이것도 계속 코딩하다보면 고쳐질 오류라고 믿는다.<br/>


---

### 소소한 나만의 기능 만들어보기 (프로모션 토글 버튼).

이 프로젝트는 사실 남이 만든 코드를 그대로 따라 작성해보는 클론 프로젝트였다. <br/>

하지만 이대로 끝내면 섭하다. 응용을 한번 해봐야지<br/>

![이미지1](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/project/2022-02-27_starbucks_1.jpg?raw=true) <br/>

프로모션 토글이 열려있는 상태에서 커서를 올리지 않으면 위와 같은 상태이고,

![이미지2](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/project/2022-02-27_starbucks_2.jpg?raw=true) <br/>

프로모션 토글이 열려있는 상태에서 커서를 올리면 위와 같이 변한다.<br/>

![이미지3](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/project/2022-02-27_starbucks_3.jpg?raw=true) <br/>

반대로 프로모션 토글이 닫혀있는 상태에서 커서를 올리지 않으면 위와 같이 나타나고.<br/>

![이미지4](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/project/2022-02-27_starbucks_4.jpg?raw=true) <br/>

반대로 프로모션 토글이 닫혀있는 상태에서 커서를 올리면 위와 같이 변한다.<br/>


### 구현 코드

```
# HTML 코드
<section class="notice">

        <div class="notice-line">
            <div class="bg-left"></div>
            <div class="bg-right"></div>

            <div class="inner">
            <div class="inner__right">
                    <h2>스타벅스 프로모션</h2>
                    <div class="toggle-promotion" onclick="promotion_change()">
                        <div class="material-icons">download</div>
                    </div>

                </div>
            </div>
        </div>
</section>

# CSS 코드

.notice-line .inner__right .toggle-promotion {
    transform: rotateX(0deg);
}

.notice-line .inner__right .toggle-promotion:hover {
    transform: rotateX(180deg);
}

.notice-line .inner__right .toggle-promotion.hide {
    transform: rotateX(0deg);
}

.notice-line .inner__right .toggle-promotion.hide:hover {
    transform: rotateX(180deg);
}

# JS 코드

let is_promotion_hidden = false;

let promotion_change = function () {
    let toggle_button_up_down = document.querySelector('.toggle-promotion .material-icons') // 상태별 버튼의 방향을 바꾸기 위함.
    is_promotion_hidden = !is_promotion_hidden;
    // 값이 반대가 되게 해라, 즉 지금 상태에선 False에서 True로 바꾸어라.
    if (is_promotion_hidden) {
        // true : 숨김 처리
        promotion_toggle_button.classList.add('.hide');
        toggle_button_up_down.innerHTML='upload';
    } else {
        // false : 숨김 해제
        promotion_toggle_button.classList.remove('.hide');
        toggle_button_up_down.innerHTML='download';
    }
};

```

소스 코드는 위와 같다.<br/>

CSS코드가 없으면 딱딱하게 회전효과 없이 바뀌고, CSS를 통해서 부드럽게 회전하는 애니메이션효과를 넣어줄 수 있다. transform이다.<br/>

---

우선 페이지 자체를 만드는 작업은 모두 끝이 났다.<br/>

하지만 개발은 만든다고 끝나는 것이 아니라, 유지보수까지 해야한다. 만약에 변경하거나 유지보수 할 일이 생겼다면 일지에 계속 기록될 것이다.<br/>
