---
layout: post
title:  "[Github Blog] 이전, 다음 글 이동 버튼 만들기"
subtitle:
date: 2022-02-06 11:13:28 +0900
tags: github
categories: study
comments: true
related_posts:

---

# [Github Blog] 이전, 다음 글 이동 버튼 만들기<br/>

## 깃허브 블로그 포스트에 이전 글, 다음 글로 이동하는 버튼을 만들어보자.

포스트 내에서 버튼을 만드는 과정은 크게 아래와 같다.<br/>

1. 블로그 소스 폴더 내 _includes 폴더에 page-button.html 파일 구성하기.
2. 블로그 소스 폴더 내 _layouts 폴더에 있는 post.html 파일에 page-button.html 파일 연동시키기
3. 버튼의 CSS 구성하기
<br/>
<br/>

### 1. 블로그 소스 폴더 내 _includes 폴더에 page-button.html 파일 구성하기.<br/>

참조 링크 : [따라쟁이님의 블로그, 깃허브 블로그 버튼 만들기](https://khw11044.github.io/blog/githubpages/2020-12-26-making-blog-11/)<br/>

우선, 페이지 화면 내에 들어갈 버튼의 html을 구성해보자.<br/>

_includes 폴더에다가 page-button.html파일을 새로 생성해주자.<br/>
그리고 코드를 다음 링크에 있는 코드와 같이 넣어주자.<br/>

[필자의 파일 링크](https://github.com/wookikim95/wookikim95.github.io/blob/main/_includes/page-button_1.html)<br/>

`<div class="page-control">`를 통해서 버튼들이 배치될 블록을 만들 수 있고, 그 안에다가 이전 글로 이동할 버튼과 다음 글로 이동할 버튼을 넣는 것이다.<br/>

이전 글이나 다음 글이 있을 때에만 버튼이 나타나도록 if문이 역할을 해주고 있고, 그렇지 않을 경우에는 버튼이 나타나지 않는다.<br/>

그리고 버튼에 들어갈 내용에 따라서 버튼 안에 어떤 내용이 들어가는 지 결정된다.<br/>


```
# << 이전 글
&laquo;이전 글

# 다음 글 >>
다음 글 &raquo;
```

필자는 위와 같은 형태로 나타나도록 작성하였다.<br/>
<br/>

### 2. 블로그 소스 폴더 내 _layouts 폴더에 post.html 파일 구성하기.<br/>

_layouts 폴더에 post.html파일에 들어가보자.<br/>

[필자의 파일 링크](https://github.com/wookikim95/wookikim95.github.io/blob/main/_layouts/post.html)<br/>

만약에 아직 버튼이 없는 경우라면 중간의 `include page-button.html`코드가 없을 것이다.<br/>

이 코드가 어디에 있냐에 따라서 이전 글, 다음 글 버튼의 위치가 달라지게 된다. 필자는 포스트 바로 밑에 오도록 배치해두었다.<br/>
<br/>

### 3. 버튼의 CSS 구성하기.<br/>

필자가 사용하는 Hydejack 기준으로 블로그 소스 폴더의 assets - css - hydejack.css 파일에 들어가서 코드를 추가해주면 된다. 다른 테마의 경우에도 assets - css에 css 구성파일이 있을 것이라고 추측된다.<br/>

css 파일 맨 밑에다가 아래의 코드들을 추가해주자.

```
.page-control {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.page-control > div {
  max-width: 50%;
}

.button {
	display:inline-block;
	font-family:Arial;
	font-size:1em;
	font-weight:bold;
	padding: 6px 100px;
	text-decoration:none;
	text-shadow:0px 1px 0px #ffffff;
}

.button:hover {
  color:#164786;
	text-shadow:0px 2px 0px #ffffff;
}

.button:active {
  position:relative;
	top:1px;
  }
```

위의 상황을 설명하면 필자는 버튼 모양을 아래와 같이 설정하고 싶었다.<br/>
- 가운데정렬로 약간 떨어져 있는 형태로
- 테두리가 없이 파란색 글자만 나타나게 하고 싶음.
- 마우스 커서를 올리면 색깔이 변하게 하고 싶음.
- 클릭하면 버튼이 아래로 살짝 움직이게 하고 싶음.

순서대로 `.page-control & .page-control > div / .button / .button:hover / .button:acitve` 클래스들이 기능을 하며, 현재 그렇게 설정되어 있는 상태이다.<br/>

버튼의 모양을 다르게 설정하고 싶다면 background, font속성이나 border 속성 등을 활용해서 버튼의 모양을 조정할 수 있다.<br/>
<br/>

### 4. 이전 글이나 다음 글이 없을 경우 버튼 다르게 하기.<br/>

위의 과정대로 하면 이전 글이나 다음 글이 없을 경우 버튼이 나타나지 않고 빈 자리만이 남는다. 아래의 사진처럼.<br/>

![노링크시](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-02-06_1.jpg?raw=true)

만약에 글이 없는 경우에 버튼 색깔을 흐리게 한다던가, 클릭이 안되게 한다던가 하고 싶다면

- 글의 링크가 있을 경우
- 글의 링크가 없을 경우

로 나누어서 다른 css를 지정해서 나타내면 된다.<br/>

_includes 폴더에다가 page-button.html파일의 코드를 아래 링크의 내용과 같이 설정해주자<br/>

[필자의 파일 링크](https://github.com/wookikim95/wookikim95.github.io/blob/main/_includes/page-button.html)<br/>

url이 있을 경우와 없을 경우로 나누었다.<br/>

그리고 css도 다르게 설정해주어야한다.<br/>
```
.button {
	display:inline-block;
	font-family:Arial;
	font-size:1em;
	font-weight:bold;
	padding: 6px 100px;
	text-decoration:none;
	text-shadow:0px 1px 0px #ffffff;
}

.button.url_false {
	cursor:default;
	color:#8f8f8f;
}

.button.url_true {
  cursor:pointer;
  color:#277ef0;
}
.button.url_true:hover {
  color:#164786;
	text-shadow:0px 2px 0px #ffffff;
}

.button.url_true:active {
  position:relative;
	top:1px;
  }
```
색깔만 바꿀 것이기 때문에 기본적인 button 클래스를 설정해 준 다음, 링크가 있을 때에만 클릭이 되고 색깔이 변하도록 구성하였다.<br/>
링크가 없을 때에는 회색으로 나타나고 클릭이 되지 않는다.<br/>

![노링크시2](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-02-06_2.jpg?raw=true)

구성을 완료하였다.<br/>
<br/>

### 5. 모바일 세로보기에서 깨지는 현상.<br/>

<img src='https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-02-06_3.jpg?raw=true' alt='모바일 이미지 깨짐' style="width:40%; height:40%"><br/>

모바일로 블로그를 둘러보는데 이전글, 다음글 버튼이 깨지는 현상이 발생한다.<br/>
필자는 이 글 작성일 기준으로 갤럭시z폴드2를 사용하고 있었다.<br/>

세로보기 할 때에는 깨지지만, PC나 가로보기 할 때에는 깨지지 않는다.<br/>
원인을 분석해보니 세로보기 할때 화면 width값이 작아지는데 좌우 padding 값이 그에 비해서 커서 깨지는 것이었다.<br/>

그러면 가로보기, PC화면에서는 정상적으로 보이게 하되, 세로보기 할 때에만 폰트크기를 줄이고 padding 값을 줄이면 될 것이라 생각하였다.<br/>

참조 링크 : [모바일의 가로세로에 따라 달라지는 CSS 만들기](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=qor3326&logNo=221172351222)

위 링크를 참조하여 hydejack.css에 있는 버튼 코드를 아래와 같이 수정하였다.<br/>

```
/* Landscape mode */
 @media only screen and (orientation: landscape) {
  .button {
    display:inline-block;
    font-family:Arial;
    font-size:25px;
    font-weight:bold;
    padding: 6px 100px;
    text-decoration:none;
    text-shadow:0px 1px 0px #ffffff;
  }
  .portrait_only   { display: none; }
}

/* Portrait mode */
@media only screen and (orientation: portrait) {
  .button {
    display:inline-block;
    font-family:Arial;
    font-size:16px;
    font-weight:bold;
    padding: 6px 20px;
    text-decoration:none;
    text-shadow:0px 1px 0px #ffffff;
  }
  .landscape_only  { display: none; }
}
```
가로모드나 PC버전 일때에는 좌우 padding 값이 크고 글자 크기가 커지도록, 세로모드일 때에는 좌우 padding 값이 작아지고 글자 크기가 작아지도록 만든다.<br/>

![모바일시험](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-02-06_4.jpg?raw=true)

VS Code 터미널에서 bundle exec jekyll serve를 실행하고,
크롬이나 MS Edge에서 F12를 눌러 표시한 버튼을 누르면 모바일 환경에서의 UI를 시뮬레이션 할 수 있었다.<br/>

![모바일시험2](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-02-06_5.jpg?raw=true)

왼쪽이 세로모드, 오른쪽이 가로모드이다.<br/>
오케이. MS Edge 개발자 도구에서 시험해보니 원하는 대로 출력된다.<br/>
<br/>

### 6. 혹은 width 크기에 따라서?<br/>

아니면.. Width크기에 따라서 조절을 해볼 수 도 있다.<br/>
개인적인 추측으로 가로모드, 세로모드가 결정되는 기준이 width랑 height 중에서 어느 쪽이 더 큰지 비교한 결과인 것 같다.<br/>

![모바일시험3](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-02-06_8.jpg?raw=true)

핸드폰 같은 부분에서 세로모드로 보면 지금과 같은 폰트크기나 padding이 알맞지만, 태블릿 pc 같이 화면이 큰 상태에서 세로모드로 보면 오히려 폰트크기가 작으면 보기가 불편했다..<br/>

버튼이 깨지는 원인은 좌우 padding이 화면의 width보다 커서 발생한 것이니까, width값이 작을 때에만 padding을 조절하면 될까나??<br/>

```
/* width > 500px일 때 */
 @media (min-width:501px) {
  .button {
    display:inline-block;
    font-family:Arial;
    font-size:25px;
    font-weight:bold;
    padding: 6px 100px;
    text-decoration:none;
    text-shadow:0px 1px 0px #ffffff;
  }
}

/* width <= 500px일 때 */
@media (max-width:500px) {
  .button {
    display:inline-block;
    font-family:Arial;
    font-size:16px;
    font-weight:bold;
    padding: 6px 20px;
    text-decoration:none;
    text-shadow:0px 1px 0px #ffffff;
  }
}
```

가로모드, 세로모드에 따라 분류했던 부분을 수정했다. 버튼 클래스 안의 요소는 그대로 두고, 조건을 각기 다르게 부여했다.<br/>
필자는 width의 크기 500px을 경계값으로 설정했다.

![모바일시험4](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-02-06_9.jpg?raw=true)

css 코드를 바꾼 뒤, MS Edge에서 시뮬레이션 해보았다.<br/>
450x824 그리고 1024x1500으로 같은 세로모드지만 버튼 속성이 다르게 적용되었다.<br/>

그리고 다시 갤럭시z폴드2에서 봤다. 역시 적용이 잘 되었다.<br/>
<br/>

### 해결해야 할 것<br/>

하지만 카테고리 별이 아닌 전체 포스트를 기준으로 이전 글과 다음 글의 링크가 연결된다.

CSS와 웹을 더 공부하고 나서, 카테고리 별로 보고 싶으면 카테고리 별로 볼 수 있도록 하는 방법도 짜야겠다.<br/>