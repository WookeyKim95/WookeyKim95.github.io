---
layout: post
title:  "[Web] Google new tab 만들기"
subtitle:   
date: 2023-08-04 02:35:04 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [Web] Google new tab 만들기<br/>
<br/>

지금까지 배운 HTML/CSS/JS를 이용하여 구글 새 탭 페이지를 만들어보았다.<br/>
<br/>

## HTML<br/>
<br/>

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Google New Tab</title>

    <link rel="stylesheet" href="./styles.css">

</head>
<body>
    <header>
        <ul class="header-nav-list-wrapper">
            <li>Gmail</li>
            <li>이미지</li>

            <li>
                <button class="toggle-button">다크 모드</button>
            </li>
        </ul>
    </header>

    <main>
        <!-- 로고 파트 -->
        <h1>
            <img src="./image/logo.png" class="image-logo">
        </h1>

        <!-- 검색창 파트 -->
        <div class="search-container">
            <input id="search-input" placeholder="Google 검색 또는 URL 입력">
            <img class="search-icon" src="./image/search.png">
        </div>

        <!-- 북마크 세션 -->
        <section class="bookmark-section">
            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

            <a href="#none" target="_blank" class="bookmark-item">
                <div class="img-icon-wrapper">
                    <img src="./image/ssafy.png">
                </div>
                <div class="bookmark-text">ssafy</div>
            </a>

        </section>

    </main>


    <script src="./index.js"></script>
    
</body>
</html>
```

<br/>

## CSS<br/>
<br/>

```
*{
    /* border가 width에 포함 */
    box-sizing: border-box;
}

body{
    background-color: white;
}

/*헤더 파트*/

.header-nav-list-wrapper{
    /*ul 기본으로 보이는 점 모양 없애기*/
    list-style: none;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    /*
    flex-start : 젤 시작점에 붙인다.
    flex-end : 젤 끝점에 붙인다.
    */
}

.header-nav-list-wrapper > li{
    margin-left: 10px;
}

.toggle-button{
    background-color: black;
    color: white;
    /* 위 오른쪽 아래 왼쪽 */
    /*5px 8px 5px 8px 와 같은 효과*/
    padding: 5px 8px;

    border: 1px solid #eeeeee;
    border-radius: 5px;
    cursor: pointer;
}

/*메인 파트*/

main{
    position: absolute;
    left: 50%;
    top: 50%;
    width: 600px;
    height: 400px;
    transform: translate(-50%, -50%);
    text-align: center;
}

.image-logo{
    width: 272px;
}

.search-container{
    position: relative;
    margin-top: 50px;
}

#search-input{
    width: 100%;
    height: 44px;
    border-radius: 50px;
    border: 1px solid #ddd;
    padding-left: 40px;
}

.search-icon{
    width: 17px;
    position: absolute;
    top: 13px;
    left: 13px;
}

.bookmark-section{
    margin-top: 40px;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    
}

.bookmark-item{
    margin-top: 20px;
    width: 112px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    /* a tag 기본 속성 없애기 */
    text-decoration: none;
}

.bookmark-item :hover{
    cursor: pointer;
    /* 투명도 (0~1) 0으로 갈수록 투명 */
    opacity: 0.5;
}

.img-icon-wrapper{
    width: 60px;
    height: 60px;
    background-color: #f2f3f5;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
}

.img-icon-wrapper>img{
    width: 60%;
    height: 60%;
}

.bookmark-text{
    margin-top: 5px;
    color:black;
}


/* 다크모드용 CSS */

.toggle-button-darkmode{
    background-color: #fff;
    color: #000;
}

.toggle-background-darkmode{
    background-color: #000;
}

.img-icon-wrapper-darkmode {
    background-color: #fff;
}

.text-darkmode {
    color: #fff;
}

```

<br/>

## JavaScript 코드<br/>
<Br/>

```
const toggleButton = document.querySelector('.toggle-button');
const body = document.querySelector('body');
const header = document.querySelector('.header-nav-list-wrapper');
const bookmarkTexts = document.querySelectorAll(".bookmark-text");
const searchInput = document.querySelector("#search-input");

toggleButton.addEventListener('click', function(){
    toggleButton.classList.toggle("toggle-button-darkmode");
    body.classList.toggle("toggle-background-darkmode");
    header.classList.toggle("text-darkmode");

    if (toggleButton.classList.contains("toggle-button-darkmode")){
        toggleButton.textContent = "일반 모드";
    }
    else {
        toggleButton.textContent = "다크 모드";
    }

    for (let i = 0; i < bookmarkTexts.length; ++i) {
        bookmarkTexts[i].classList.toggle("text-darkmode");
    }
});

searchInput.addEventListener("keyup", function(e){
    // Enter을 입력한 경우에만 검색창으로 넘어가게 만들기.
    // Enter는 e.code에 담긴다.
    if (e.code === "Enter") {
        // 값이 비어있으면 검색 x
        if (e.target.value === "") {
            alert("검색어를 입력하지 않으셨습니다.");
            return;
        }
        else {
            // 검색어가 들어가면 넘어갈 것.
            // https://www.google.com/search?q=검색어 형태를 활용할 것.

            // 자바스크립트 이동 2가지
            // 1. location.href 바로 이동
            // 2. window.open 새창을 열고 이동
            
            const targetAd = "https://www.google.com/search?q=";
            // location.href = (targetAd + e.target.value);
            window.open(targetAd + e.target.value, "_blank");
        }
    }
})
```

## 주요사항<br/>
<br/>

### 다크모드의 구현<br/>

```
toggleButton.addEventListener('click', function(){
    toggleButton.classList.toggle("toggle-button-darkmode");
    body.classList.toggle("toggle-background-darkmode");
    header.classList.toggle("text-darkmode");

    if (toggleButton.classList.contains("toggle-button-darkmode")){
        toggleButton.textContent = "일반 모드";
    }
    else {
        toggleButton.textContent = "다크 모드";
    }

    for (let i = 0; i < bookmarkTexts.length; ++i) {
        bookmarkTexts[i].classList.toggle("text-darkmode");
    }
});
```

다크모드 버튼을 클릭할 때마다 toggle을 이용해서 다크모드 관련 css가 클래스 목록에 추가되도록 구현하였다.<br/>

이렇게하면 배경은 검은색과 흰색을 왔다갔다 하고, 텍스트는 반대로 흰색과 검은색을 왔다갔다 하게 만들 수 있다.<br/>

그리고 버튼의 텍스트도 일반모드, 다크모드를 왔다갔다 하게 만들 수 있다.<br/>
<br/>

### 검색기능의 구현<br/>

```
searchInput.addEventListener("keyup", function(e){
    // Enter을 입력한 경우에만 검색창으로 넘어가게 만들기.
    // Enter는 e.code에 담긴다.
    if (e.code === "Enter") {
        // 값이 비어있으면 검색 x
        if (e.target.value === "") {
            alert("검색어를 입력하지 않으셨습니다.");
            return;
        }
        else {
            // 검색어가 들어가면 넘어갈 것.
            // https://www.google.com/search?q=검색어 형태를 활용할 것.

            // 자바스크립트 이동 2가지
            // 1. location.href 바로 이동
            // 2. window.open 새창을 열고 이동
            
            const targetAd = "https://www.google.com/search?q=";
            // location.href = (targetAd + e.target.value);
            window.open(targetAd + e.target.value, "_blank");
        }
    }
})
```

e.code와 e.target.value를 통해서 어떤키가 입력됐는지, 무슨 내용이 입력됐는지 검색할 수 있었다.<br/>

만약에 입력이 아무것도 안됐으면 경고창이 출력되게, 입력됐으면 해당 키워드를 검색한 구글 페이지로 넘어가도록 하였다.

1. 구글 검색 주소는 항상 ("https://www.google.com/search?q=")로 시작함을 이용할 수 있다.<br/>

2. location.href는 그 페이지를 전환시킨다.
3. window.open()은 새 탭을 열어서 페이지를 연다.

