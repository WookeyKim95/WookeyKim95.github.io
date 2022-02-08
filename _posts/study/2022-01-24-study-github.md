---
layout: post
title:  "[Github Blog] 깃허브 블로그에 수학 방정식 넣기."
subtitle:   "지킬 깃허브에 수식을 넣으려면?"
date: 2022-01-24 14:26:23 +0900
tags: github
categories: study
comments: true
related_posts:

---

   
## 어라? 수식이 반영이 안됐나?<br/>
---
<br/>

![이미지1](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-24_2.jpg?raw=true)
<br/>

오늘 아침에 버블정렬에 대해서 공부한 내용을 포스팅했는데, 블로그에서 보니까 에러가 나있었다.<br/>

![이미지2](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-24_1.jpg?raw=true)
<br/>

우선 VSCode에서 마크다운 미리보기로 보았을 땐 수식의 모양이 잘 나타났다.<br/>
 그런데 블로그에서 이러는 것을 보니, 블로그에서 수식을 구동하기 위해선 '뭔가 추가조치가 필요하구나.' 라는 생각이 들었다.<br/>



 ## 깃허브 블로그에 수식을 넣어주기 위한 조치.<br/>
 ---
 역시나. 기본적으로 jekyll 깃허브 블로그는 지원이 안된다고 한다.<br/>
 VSCode 마크다운 미리보기에서 나온 것처럼 만들기 위해서는 변경해야할 내용이 있다고 한다.<br/>

 참고 링크 : [Eun0님의 네이버 블로그](https://blog.naver.com/PostView.nhn?blogId=prt1004dms&logNo=221525385428&parentCategoryNo=&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView)<br/>
<br/>
### 먼저, _config.yml 파일에 아래 내용을 추가하자.<br/>


```
# Math Equation
markdown: kramdown
highlighter: rouge
lsi: false
excerpt_separator: "\n\n"
incremental: false
```
<br/>

![이미지3](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-24_3.jpg?raw=true)<br/>
_config.yml 파일에 이런 코드가 있던데, 이 부분과 연계 하는 것 같다.<br/>

<br/>

### 그 다음으로, _includes 폴더에 mathjax_support.html 파일을 만든다.<br/>
<br/>

아래 내용을 넣어주어 html파일을 생성한다.
```
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
    TeX: {
        equationNumbers: {
            autoNumber: "AMS"
        }
    },
    tex2jax: {
        inlineMath: [['$', '$']],
        displayMath: [['$$', '$$']],
        processEscapes: true,
    }
});
MathJax.Hub.Register.MessageHook("Math Processing Error", function (message) {
    alert("Math Processing Error: "+message[1]);
    });
MathJax.Hub.Register.MessageHook("TeX Jax - parse error",function (message) {
    alert("Math Processing Error: "+message[1]);
});
</script>
<script type="text/javascript" async
    src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
```
<br/>

### _includes 폴더 안의 head.html 수정하기<br/>
<br/>
head.html에 아래 코드를 추가해주자.<br/>

```
{% if page.use_math %}
    {% include mathjax_support.html %}
{% endif %}
```

<br/>
음! 포스트에 mathjax 수학공식을 사용한다면 아까 작성한 html파일을 활용하라는 내용인 것 같다!<br/>
<br/>

### 포스트 작성할 때 추가할 내용<br/>
<br/>
위에서 head.html에 추가한 코드가 작동하기 위해서는 이 포스트가 use_math했습니다! 라는 것을 알려줘야 할 것이고, 
그 기능을 하도록 하는 조치인 것 같다.<br/>

```
---
layout: post
title:  "[알고리즘] 버블정렬."
subtitle:   
date: 2022-01-24 09:14:35 +0900
categories: study
tags: algorithm
comments: true
related_posts:

---
```
예전에 [알고리즘] 버블정렬. 포스트의 레이아웃은 위와 같았다.<br/>
하지만 이제 여기다가 `use_math: true`를 추가해주어<br/>

```
---
layout: post
title:  "[알고리즘] 버블정렬."
subtitle:   
date: 2022-01-24 09:14:35 +0900
categories: study
tags: algorithm
use_math: true
comments: true
related_posts:

---
```
이렇게 바꿔보았다.

### 이제 git에다가 push해보면?<br/>
<br/>

![이미지4](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-24_6.jpg?raw=true)<br/>
<br/>
와우! VSCode에서 bundle exec jekyll serve로 시뮬레이션해보니 잘 작동한다!<br/>
그러나 대부분 여기서 곧 바로 문제가 해결되지만 나한텐 또 다른 문제가 발생했다.<br/>
<br/>

## 내게 발생한 또 다른 문제<br/>
---
![이미지5](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-24_5.jpg?raw=true)<br/>
<br/>
다른 페이지에 갔다가 다시 돌아오면 또 저렇게 나타난다.<br/>

![이미지6](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-24_6.jpg?raw=true)<br/>
<br/>
또한, 같은 페이지에서 새로고침을 해야 올바르게 반영이 되는 모습이 보인다. 흠..<br/>
<br/>
지금 문제 상황이 이렇다.<br/>
필자가 사용하는 지킬테마는 **Hydejack**이다.<br/>
지금 문제에서 핵심이 첫 페이지를 열었을 땐 수학 방정식 문법이 반영이 안되는 것이고,<br/>
같은 페이지에서 새로고침을 해야 반영이 되는 것이다.<br/>
그러면.. 해결방안은?<br/>
<br/>

### 개인적인 생각<br/>

맨 처음에 페이지를 로딩할 때 mathjax_support.html이 반영될 수 있도록 만드는 것이다.<br/>

![이미지6](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-24_7.jpg?raw=true)<br/>
<br/>
Hydejack기준으로 필자는 _layouts 디렉토리의 post.html에도<br/>

```
{% if page.use_math %}
    {% include mathjax_support.html %}
{% endif %}
```

을 추가해보았다.<br/>
<br/>

![이미지6](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/github/2022-01-24_8.jpg?raw=true)<br/>
<br/>
그랬더니 블로그를 첫 로드하고나서 게시물에 들어가니 mathjax_support.html이 반영이 된 것 같다.<br/>
공식이 잘 나온다.<br/>
<br/>

**하지만 아까와 마찬가지로 새로고침을 하지 않은 채로 다른 게시물에 갔다가 돌아오니 다시 깨져있다.**<br/>
(씁쓸)<br/>

**게다가, home.html에 추가해두었던 코드를 삭제하고 post.html에만 남겨두어도 같은 현상이 발생했다.**<br/>

어떻게 하면 다른 게시물에 갔다가 다시 돌아와도 수학 방정식이 나타나게 할 수 있을까??<br/>
점점 더 머리가 아파온다. 지금은 좀 접어두었다가 새로운 해결방안이 보이면 다시 일지를 써야겠다.<br/>



