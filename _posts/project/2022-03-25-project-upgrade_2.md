---
layout: post
title:  "[Github blog] 블로그 테마 업그레이드하기_2"
subtitle: 
date: 2022-03-25 09:01:13 +0900
categories: project
tags:
comments: true
related_posts:

---

# [Github blog] 블로그 테마 업그레이드하기_2

## 깃허브 블로그 사이드바 문제 해결을 계속해보자.

[new_ver 브랜치](https://github.com/WookeyKim95/WookeyKim95.github.io/tree/new_ver)

지난번의 사이드바의 배경이 바뀌지 않는 문제를 계속 해결해본다.<br/>

## 우선, Default 부분을 망가뜨려 보기로 했다.<br/>
<br/>

```
defaults:
  # You can use this to provide a default accent color and background for
  # all pages under a given path:
  - scope:
      path:            
    values:
      accent_color:    rgb(38,139,210)
      theme_color:     rgb(32,32,32)
      accent_image:    
        background:    main_left.JPG?raw=true
        overlay:       false
```
_config.yml파일에서 위의 path 부분을 삭제했다.<br/>

결과는 기존에 잘 적용되던 배경마저 검은색 화면으로 나온다.<br/>

```
defaults:
  # You can use this to provide a default accent color and background for
  # all pages under a given path:
  - scope:
      path:            https://github.com/WookeyKim95/WookeyKim95.github.io/tree/main/assets/img/
    values:
      accent_color:    rgb(38,139,210)
      theme_color:     rgb(32,32,32)
      accent_image:    
        background:    
        overlay:       false
```

복구를 한 뒤에, background 이미지를 없애보기로 했다.<br/>

어? 이 경우는 결과가 변함이 없다.<br/>

별도로 정해지지 않은 카테고리에서는 기본 화면이 나오고, 별도로 사진을 정해준 카테고리에서는 검은색 화면이 출력되는 현상이 나타난다.<br/>

```
accent_image:          https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/main_left.JPG?raw=true
accent_color:          rgb(79,177,186)

defaults:
  # You can use this to provide a default accent color and background for
  # all pages under a given path:
  - scope:
      path:            https://github.com/WookeyKim95/WookeyKim95.github.io/tree/main/assets/img/
    values:
      accent_color:    rgb(38,139,210)
      theme_color:     rgb(32,32,32)
      accent_image:    
        background:    main_left.JPG?raw=true
        overlay:       false
```
이 경우랑 결과가 똑같은 것이다.<br/>

그렇다면 상단에서 accent_image 부분을 없앤다면?<br/>

```
accent_image:          
accent_color:          rgb(79,177,186)
```

결과는 Hydejack의 기본 배경화면이 나타나게 된다.<br/>

즉, 배경이 변하는데에는 이 부분이 밀접하게 관련이 있다는 것이다.<br/>
후우우.. 골치 아프게 되었다..<br/>

Overlay 값을 true로 바꾸어도 먹히지 않는다..<br/>
<br/>

## _config.yml맨 위의 baseurl?<Br/>

```
# Config
# ========================================================================================

# Uncomment and set the URL of your site (with protocol, e.g. `https://`)
# NOTE: You don't need to provide this property when hosting on GitHub Pages or Netlify.
url:                   https://wookeykim95.github.io

# Uncomment and set the "base URL" of your site.
# When your site is in a subdirectory, set to `/<name of directory>`,
# with a leading `/` but no trailing `/`. Use the empty string '' otherwise.
# NOTE: You don't need to provide this property when hosting on GitHub Pages or Netlify.
baseurl:               /main
```

_config.yml 파일의 맨 위에 있는 코드이다.<br/>

github blog 런칭을 할 경우, 여기의 url과 baseurl을 알맞게 바꾸라고 되어있었다.<br/>

[출처](https://hydejack.com/docs/config/)

```
# Config
# ========================================================================================

# Uncomment and set the URL of your site (with protocol, e.g. `https://`)
# NOTE: You don't need to provide this property when hosting on GitHub Pages or Netlify.
url:                   https://wookeykim95.github.io

# Uncomment and set the "base URL" of your site.
# When your site is in a subdirectory, set to `/<name of directory>`,
# with a leading `/` but no trailing `/`. Use the empty string '' otherwise.
# NOTE: You don't need to provide this property when hosting on GitHub Pages or Netlify.
baseurl:               ''
```

위와 같이 바꿔보았다. 

```
ERROR `/assets/img/main_left.JPG' not found.
```
위와 같은 상황이 뜬다. 그래서 배경사진은 안나오고, 색깔만 배경으로 출력된다.<br/>

```
accent_image:          main_left.JPG?raw=true
accent_color:          rgb(79,177,186)
```

accent_image를 위와같이 바꿔도 마찬가지다.
그럼 만약 base url을 메인 브랜치로 바꾸면?<br/>

```
baseurl:               https://github.com/WookeyKim95/WookeyKim95.github.io/tree/main
```

```
 Server address: http://127.0.0.1:4000https://github.com/WookeyKim95/WookeyKim95.github.io/tree/main/
```
 아.. 로컬서버가 엉뚱하게 뜨는구나..

```
url:                   https://github.com/WookeyKim95/WookeyKim95.github.io/tree/main/assets/img
```
```
ERROR `/main_left.JPG' not found.
```
url을 적용하면 평소랑 달라지는 것이 없었다.<br/>

이것은 조금 더 연구가 필요할 것 같다.<br/>
