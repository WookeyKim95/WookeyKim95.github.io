---
layout: post
title:  "[Scss] Scss 사용하기"
subtitle:   
date: 2024-01-16 06:06:39 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [Scss] Scss 사용하기<br/>
<br/>

## Scss(Sass)란?<br/>

Scss(Sass)는 CSS로 컴파일 되는 스타일 시트 확장 언어이며 CSS 전처리기의 하나이다.<br/>

기술 방식에는 Sass 와 Scss 가 있는데 Sass 는 우리가 일반적으로 쓰는 css 문법과 다르기 때문에<br/>

동일하게 중괄호 문법을 사용하는 scss 를 쓴다!<br/>
<br/>

## 설치 과정<br/>

1. node.js를 설치한다.
2. node-sass 를 설치한다. 터미널을 열고 아래 명령어를 입력하면 된다.
```
npm install -g node-sass
```

이렇게 하면 sass가 설치가 완료된다.

## 사용 과정<br/>

1. VSCODE에서 프로젝트를 생성해주고, 해당 프로젝트 안에 'index.html'파일을 만들어 준다.
2. 같은 디렉토리에 css폴더를 만들어준다.
3. CSS폴더 안에 'styles.scss'와 'styles.css' 파일을 생성해준다.
4. HTML파일에서 styles.css를 불러온다. link="./css/styles.css"로 해준다.
5. 터미널을 열어 아래 명령어를 입력한다.

```
npx sass --watch css/style.scss css/style.css
```

여기서, 명령어의 뜻은 아래와 같다.

- npx : Node Package eXecute의 약자. 즉, 노드 패키지를 실행하라는 뜻.
- --watch : 소스에 변형이 일어날 때마다 자동으로 컴파일을 시켜주는 명령어.
- css/style.scss css/- style.css : 'css폴더 안의 style.scss가 css폴더 안의 style.css파일로'라는 뜻.

이 명령어를 실행하면 scss 파일이 바뀌는 것을 자동으로 감지해서 css 파일로 자동으로 컴파일이 된다.<br/>

6. styles.css.map 파일이 생성되면 성공.

<br/>