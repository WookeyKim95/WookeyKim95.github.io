---
layout: post
title:  "[Github blog] 블로그 테마 업그레이드하기_4"
subtitle: 
date: 2022-03-28 09:02:24 +0900
categories: project
tags:
comments: true
related_posts:

---

# [Github blog] 블로그 테마 업그레이드하기_4

[new_ver 브랜치](https://github.com/WookeyKim95/WookeyKim95.github.io/tree/new_ver)

## 사이드바에 로고를 넣자!<br/>
<br/>

```
logo:                  /assets/img/LeftMe.png
```

_config.yml파일에 있는 코드이다. 보통 여기에다가 로고를 사용할 사진을 입력하면<br/>

좌측 사이드바에 로고가 뜨게 된다.<br/>

하지만 이전 포스트에 있는 것처럼 보았듯이, 사이드바에는 로고가 뜨지 않았다.<br/>

_config.yml파일을 아무리 뒤져보아도 문제가 해결되지 않았는데,<br/>

**그러면 sidebar.html파일을 직접 고치면 되는 것 아닌가?** 생각이 들었다.<br/>
<br/>

### sidebar.html 고치기<br/>
<br/>

```
<div class="sidebar-about">

        <h2 class="h1"><a href="{{ '/' | relative_url }}">{{ site.title }}</a></h2>
        {% assign text = site.tagline | default:site.description %}
        {% if text %}
          <p class="{% if text.size > 100 %}fine{% endif %}">
            {{ text | markdownify | replace:'<p>','' | replace:'</p>','' }}
          </p>
        {% endif %}
  </div>
```

sidebar.html의 이 부분이 로고와 블로그 타이틀을 관장하는 파트이다.<br/>

보통 _config.yml의 내용을 따라서 출력이 진행되지만,<br/>
왜인지 타이틀과 tag를 제외하곤 출력이 안되는 문제가 있는 것 같다.<br/>

여기서 필자는 

```
<a>
  <img src="/assets/img/LeftMe.png">
</a>
```
h2 파트 위에 이 코드를 넣어주기로 했다.<br/>

```
<div class="sidebar-about">

        <a>
          <img src="/assets/img/LeftMe.png">
        </a>

        <h2 class="h1"><a href="{{ '/' | relative_url }}">{{ site.title }}</a></h2>
        {% assign text = site.tagline | default:site.description %}
        {% if text %}
          <p class="{% if text.size > 100 %}fine{% endif %}">
            {{ text | markdownify | replace:'<p>','' | replace:'</p>','' }}
          </p>
        {% endif %}
  </div>
```

따라서 현재 이 상태인 것이다.<br/>

이렇게 하고 로컬서버를 돌려본 결과.<br/>

![에러](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-28-project_1.jpg?raw=true)

오.. 이런..<br/>

크기가 매우 큰 사진에 비해서 스타일 지정을 안해서 생긴 문제라는 생각이 들었다.<br/>

그렇다면 스타일 지정은 어떻게 해줘야할까??<br/>
<br/>

### Hydejack 홈페이지 방문.<br/>

[Hydejack 홈페이지](https://hydejack.com/docs/config/#changing-accent-colors-and-sidebar-images)

여기는 사이드바에 로고가 잘 들어가져있다.<br/>

여기서 F12를 누르면 개발자를 위한 창이 나오는데, 여기서 코드를 열람할 수 있다.<br/>

![사진](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-28-project_2.jpg?raw=true)

그렇다. 저 사진에 나와있는 부분 중에서 네모 부분을 그대로 따라서 치면 되지 않을까?

```
<div class="sidebar-about">

        <a class="no-hover" href="/" tabindex="-1">
          <img src="/assets/img/LeftMe.png" class="avatar" alt="logo" width="120" hieght="120" loading="lazy">
        </a>

        <h2 class="h1"><a href="{{ '/' | relative_url }}">{{ site.title }}</a></h2>
        {% assign text = site.tagline | default:site.description %}
        {% if text %}
          <p class="{% if text.size > 100 %}fine{% endif %}">
            {{ text | markdownify | replace:'<p>','' | replace:'</p>','' }}
          </p>
        {% endif %}
  </div>
```
그래서 위와 같이 코드를 수정하고 로컬 서버를 구동하였다.<br/>

그리고 그 결과는 아래와 같았다.<br/>

![사진2](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-28-project_3.jpg?raw=true)
<br/>
<br/>

### 배열 고치기<br/>
<br/>

음! 우선 로고는 잘 출력된다! 진전이 있다.<br/>

다만 제목 옆에 출력이 되는 것이 아니라<br/>

제목<br/>

로고<br/>

태그라인<br/>

메뉴<br/>

순서대로 출력이 되어야 하는데..<br/>

이 부분은 순서를 다시 정해서 코딩을하고 br을 잘 사용하면 되지 않을까? 생각했다.<br/>

```
<div class="sidebar-about">

        <h2 class="h1"><a href="{{ '/' | relative_url }}">{{ site.title }}</a></h2>
        {% assign text = site.tagline | default:site.description %}
        {% if text %}
          <p class="{% if text.size > 100 %}fine{% endif %}">
            {{ text | markdownify | replace:'<p>','' | replace:'</p>','' }}
          </p>
        {% endif %}
          
        <br/>

        <a class="no-hover" href="/" tabindex="-1">
          <img src="/assets/img/LeftMe.png" class="avatar" alt="logo" width="120" hieght="120" loading="lazy">
        </a>

      </div>
```

위와 같이 코드를 수정하였다.<br/>

![사진3](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-28-project_4.jpg?raw=true)

떼어내는데에는 성공했다!<br/>

그런데 로고가 우측으로 정렬이된다.<br/>

이런 경우에는 내 생각이 맞다면 a부분의 style을 지정해주면 될 것이라 생각했다.<br/>

[CSS 가운데 정렬하기](https://deeplify.dev/front-end/markup/align-div-center)

실마리는 이 블로그에서 찾을 수 있었다.<br/>

div 안에 a를 정의해준 다음에 div의 스타일은 display:flex를,<br/>
안에 있는 a의 스타일은 margin:0 auto를 해주면 된다.<br/>

```
<div class="sidebar-about">

        <h2 class="h1"><a href="{{ '/' | relative_url }}">{{ site.title }}</a></h2>
        {% assign text = site.tagline | default:site.description %}
        {% if text %}
          <p class="{% if text.size > 100 %}fine{% endif %}">
            {{ text | markdownify | replace:'<p>','' | replace:'</p>','' }}
          </p>
        {% endif %}
      <div style="display:flex">
        <a class="no-hover" href="/" tabindex="-1" style="margin:0 auto;">
          <img src="/assets/img/LeftMe.png" class="avatar" alt="logo" width="120" hieght="120" loading="lazy">
        </a>
      </div>
    </div>
```
그래서 코드를 다음과 같이 수정하였다.<br/>

그리고 그 결과는?<br/>

![성공!](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/2022-03-28-project_5.jpg?raw=true)

성공하였다!<br/>
<Br/>

## 후기<br/>
<br/>

굵직하게 해결하고 싶은 문제는 모두 해결한 것 같다.<br/>

이제 분리해놨던 branch를 잘 merge 시키면 된다.<br/>

merge하기 전에, 각 카테고리 별로 적용할 테마 배경화면을 그려야겠다.<br/>

**행복해.**