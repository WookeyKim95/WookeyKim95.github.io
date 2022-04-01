---
layout: post
title:  "[Github blog] 블로그 테마 업그레이드하기_7"
subtitle: 
date: 2022-04-01 09:00:05 +0900
categories: project
tags:
comments: true
related_posts:

---
# [Github blog] 블로그 테마 업그레이드하기_7<br/>

## 이전 글, 다음 글 버튼 모양 문제의 해결<br/>
<br/>

### 문제의 발생<br/>
<Br/>

![에러사진](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-04-01-project_1.jpg?raw=true)

위 사진은 버전을 업그레이드 한 뒤의 포스트에 있는 사진이며, 이전글, 다음글 버튼에 기능만 적용되었고, CSS는 적용되지 않아서 문제가 발생하였다.<br/>

필자는 CSS를 적용시키기 위해서 SCSS 파일에 아래와 같은 코드를 붙여넣기를 시도해보았다.

```
.page-control {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.page-control>div {
  max-width: 50%;
}

/* width > 500px일 때 */
@media (min-width:501px) {
  .button {
    display: inline-block;
    font-family: Arial;
    font-size: 25px;
    font-weight: bold;
    padding: 6px 100px;
    text-decoration: none;
    text-shadow: 0px 1px 0px #ffffff;
  }
}

/* width <= 500px일 때 */
@media (max-width:500px) {
  .button {
    display: inline-block;
    font-family: Arial;
    font-size: 16px;
    font-weight: bold;
    padding: 6px 20px;
    text-decoration: none;
    text-shadow: 0px 1px 0px #ffffff;
  }
}

.button.url_false {
  cursor: default;
  color: #8f8f8f;
}

.button.url_true {
  cursor: pointer;
  color: #277ef0;
}

.button.url_true:hover {
  color: #164786;
  text-shadow: 0px 2px 0px #ffffff;
}

.button.url_true:active {
  position: relative;
  top: 1px;
}
```

그러나 로컬서버에서는 잘 작동했지만, 웹 상에서는 적용되지 않는 문제점이 계속해서 발생하였다.<br/>

이 때, 한 가지 생각이 들었다.

**CSS 스타일을 page-button.html에 직접 적용하면?**<br/>

이전글, 다음글의 링크를 감지하고 작동하는 기능은 살아있는 것으로 보아 page-button.html은 잘 작동하는 것으로 보인다.<br/>

그리고 html의 div, a 등의 속성에 스타일을 직접 적용할 수 있다. 그러나 이렇게하면 html파일은 지저분해지겠지만 시도를 해보기로 했다.<br/>

```
<div class="page-control" style="  height: 100%;
display: flex;
align-items: center;
justify-content: space-between;">
    <div style="max-width: 50%;">
        {\% if page.previous.url \%}
        <a id="prev" class="button url_true" style="  cursor: pointer;
        color: #277ef0;" href="{{ page.previous.url }}">&laquo;
            이전 글</a>
        {\% else \%}
        <a id="prev" class="button url_false" sytle="  cursor: default;
        color: #8f8f8f;">&laquo;이전 글</a>
        {\% endif \%}
    </div>
    <div style="max-width: 50%;">
        {\% if page.next.url \%}
        <a id="next" class="button url_true" style="  cursor: pointer;
        color: #277ef0;" href="{{ page.next.url }}">
            다음 글 &raquo;</a>
        {\% else \%}
        <a id="next" class="button url_false" sytle="  cursor: default;
        color: #8f8f8f;"> 다음 글 &raquo;</a>
        {\% endif \%}
    </div>
</div>
```

그래서 작성된 html파일은 위와 같다.<br/>

우선 로컬서버에는 잘 적용되었다.<br/>

그리고 조금 더 기다린 뒤, 웹상에서 구동한 결과는..<br/>

![결과1](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-04-01-project_2.jpg?raw=true)

오케이! display:flex가 잘 적용되었다. 그러나 글자의 폰트가 바뀌지 않았는데 필자가 폰트를 가져오지 않은 것 같다.<br/>

```
<div class="page-control"
style="height: 100%;
display: flex;
align-items: center;
justify-content: space-between;">
    <div style="max-width: 50%;">
        <div class="button"
        style="/* width > 500px일 때 */
        @media (min-width:501px) {
          .button {
            display: inline-block;
            font-family: Arial;
            font-size: 25px;
            font-weight: bold;
            padding: 6px 100px;
            text-decoration: none;
            text-shadow: 0px 1px 0px #ffffff;
          }
        }
        
        /* width <= 500px일 때 */
        @media (max-width:500px) {
          .button {
            display: inline-block;
            font-family: Arial;
            font-size: 16px;
            font-weight: bold;
            padding: 6px 20px;
            text-decoration: none;
            text-shadow: 0px 1px 0px #ffffff;
          }
        }">
        {\% if page.previous.url \%}
        <a id="prev" class="button url_true" style="  cursor: pointer;
        color: #277ef0;" href="{{ page.previous.url }}">&laquo;
            이전 글</a>
        {\% else \%}
        <a id="prev" class="button url_false" sytle="  cursor: default;
        color: #8f8f8f;">&laquo;이전 글</a>
        {\% endif \%}
        </div>
    </div>
    <div style="max-width: 50%;">
        <div class="button"
        style="/* width > 500px일 때 */
        @media (min-width:501px) {
          .button {
            display: inline-block;
            font-family: Arial;
            font-size: 25px;
            font-weight: bold;
            padding: 6px 100px;
            text-decoration: none;
            text-shadow: 0px 1px 0px #ffffff;
          }
        }
        
        /* width <= 500px일 때 */
        @media (max-width:500px) {
          .button {
            display: inline-block;
            font-family: Arial;
            font-size: 16px;
            font-weight: bold;
            padding: 6px 20px;
            text-decoration: none;
            text-shadow: 0px 1px 0px #ffffff;
          }
        }">
        {\% if page.next.url \%}
        <a id="next" class="button url_true" style="  cursor: pointer;
        color: #277ef0;" href="{{ page.next.url }}">
            다음 글 &raquo;</a>
        {\% else \%}
        <a id="next" class="button url_false" sytle="  cursor: default;
        color: #8f8f8f;"> 다음 글 &raquo;</a>
        {\% endif \%}
        </div>
    </div>
</div>
```

코드를 다음과 같이 수정해보았다.<br/>

너무 지저분해지고있다..<br/>

![결과2](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-04-01-project_3.jpg?raw=true)

아이고.. 폰트는 의도한 대로 됐지만 배열이 세로로 되어버렸다.. 어디가 문제인걸까..?<br/>

### div를 a로!<br/>

생각해보니 링크 연결 담당은 div지만, 이전 글, 다음 글 폰트를 나타내는 것은 a의 담당이었다. 따라서 해당 되는 부분 즉, div를 a로 수정하였다.

```
<div class="page-control"
style="height: 100%;
display: flex;
align-items: center;
justify-content: space-between;">
    <div style="max-width: 50%;">
        <a class="button"
        style="
        /* width > 500px일 때 */
        @media (min-width:501px) {
        
            display: inline-block;
            font-family: Arial;
            font-size: 25px;
            font-weight: bold;
            padding: 6px 100px;
            text-decoration: none;
            text-shadow: 0px 1px 0px #ffffff;
          
        }
        
        /* width <= 500px일 때 */
        @media (max-width:500px) {
          
            display: inline-block;
            font-family: Arial;
            font-size: 16px;
            font-weight: bold;
            padding: 6px 20px;
            text-decoration: none;
            text-shadow: 0px 1px 0px #ffffff;
          
        }">
        {\% if page.previous.url \%}
        <a id="prev" class="button url_true" style="  cursor: pointer;
        color: #277ef0;" href="{{ page.previous.url }}">&laquo;
            이전 글</a>
        {\% else \%}
        <a id="prev" class="button url_false" sytle="  cursor: default;
        color: #8f8f8f;">&laquo;이전 글</a>
        {\% endif \%}
        </a>
    </div>
    <div style="max-width: 50%;">
        <a class="button"
        style="/* width > 500px일 때 */
        @media (min-width:501px) {
          .button {
            display: inline-block;
            font-family: Arial;
            font-size: 25px;
            font-weight: bold;
            padding: 6px 100px;
            text-decoration: none;
            text-shadow: 0px 1px 0px #ffffff;
          }
        }
        
        /* width <= 500px일 때 */
        @media (max-width:500px) {
          .button {
            display: inline-block;
            font-family: Arial;
            font-size: 16px;
            font-weight: bold;
            padding: 6px 20px;
            text-decoration: none;
            text-shadow: 0px 1px 0px #ffffff;
          }
        }">
        {\% if page.next.url \%}
        <a id="next" class="button url_true" style="  cursor: pointer;
        color: #277ef0;" href="{{ page.next.url }}">
            다음 글 &raquo;</a>
        {\% else \%}
        <a id="next" class="button url_false" sytle="  cursor: default;
        color: #8f8f8f;"> 다음 글 &raquo;</a>
        {\% endif \%}
        </a>
    </div>
</div>
```

위와 같이 수정한 결과,

![결과3](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-04-01-project_4.jpg?raw=true)

오케이! 정렬되었다! 다만 위에 있는 선이 거슬린다.<br/>
하지만 이부분은 간단했다.

```
<a class="button"
```

이 부분을

```
<a
```

이렇게 클래스를 지워주니 해결되었다.<br/>

자 이제 푸시해서 웹 상에서 구동시켜 볼 차례다.<br/>

![실패](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-04-01-project_5.jpg?raw=true)

아아악.. 왜이럴까..<br/>

로컬이랑 웹이랑 뭐가 다른걸까..?<br/>

![개발자 도구](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-04-01-project_6.jpg?raw=true)

개발자 도구를 켜서 보니 스타일의 위치가 잘못 적용된 것인가 싶었다.<br/>
<br/>

### html에 style란 따로 만들기.<br/>
<br/>
근데 생각해보니까..

```
<style>
CSS
</style>

<html>
HTML
</html>
```

이 형식으로 할 수 있었잖아...<br/>

```
<style>
.page-control {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.page-control>div {
  max-width: 50%;
}

/* width > 500px일 때 */
@media (min-width:501px) {
  .button {
    display: inline-block;
    font-family: Arial;
    font-size: 25px;
    font-weight: bold;
    padding: 6px 100px;
    text-decoration: none;
    text-shadow: 0px 1px 0px #ffffff;
  }
}

/* width <= 500px일 때 */
@media (max-width:500px) {
  .button {
    display: inline-block;
    font-family: Arial;
    font-size: 16px;
    font-weight: bold;
    padding: 6px 20px;
    text-decoration: none;
    text-shadow: 0px 1px 0px #ffffff;
  }
}

.button.url_false {
  cursor: default;
  color: #8f8f8f;
}

.button.url_true {
  cursor: pointer;
  color: #277ef0;
}

.button.url_true:hover {
  color: #164786;
  text-shadow: 0px 2px 0px #ffffff;
}

.button.url_true:active {
  position: relative;
  top: 1px;
}
</style>
```

page-button.html 제일 위에 저 코드를 넣어보았다.<br/>

![성공.](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-04-01-project_7.jpg?raw=true)

바로 되네..?<br/>

내가 이 생각을 왜 못했을까??<br/>

늘 html파일과 css 파일을 따로 만들다 보니까 그랬던 것 같다.<br/>

뭔가 1~2시간동안 삽질을 한 것 같아 자괴감이 든다.<br/>

그래도 해결한게 어디야.<br/>


