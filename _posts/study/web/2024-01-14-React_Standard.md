---
layout: post
title:  "[React] 리액트 기본 구조"
subtitle:   
date: 2024-01-14 07:08:03 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [React] 리액트 기본 구조<br/>
<br/>

## create-react-app<br/>
<Br/>

create-react-app은 리액트 프로젝트를 생성할 때 필요한 웹팩, 바벨의 설치 및 설정 과정을 생략하고 바로 간편하게 프로젝트 작업 환경을 구축해 주는 도구이다.<br/>

Node.js를 설치하고 터미널에 아래 명령어를 입력해서 바로 리액트 구조를 세팅할 수 있다.<br/>

```
$ npm init react-app hello-react
$ cd hello-react
$ npm start
```

## 리액트 디렉토리 폴더 구조<br/>
<br/>

리액트를 세팅할 시 아래와 같은 구조가 설정된다.<br/>

```
my-app/
    README.md
    node_modules/
    package.json
    public/
        index.html
        pavicon.ico
    src/
        App.css
        App.js
        App.test.js
        index.css
        index.js
        logo.svg
```

각각 폴더에 대한 설명은 아래와 같다.<br/>

- node_modules : npm install을 하여 설치된 모듈들이 위치하는 폴더
- public : static 자원 폴더, 정적 파일들을 위한 폴더
- src : 리액트를 작업할 폴더

## public 폴더<br/>
<br/>

### favicon.ico<br/>

웹 사이트 상단에 위치하는 파비콘이다.<br/>
<br/>

### index.html<br/>
<br/>

가상 DOM을 위한 html 파일이다. (빈 껍데기 파일)<br/>

메인 프로그램인 index.js에 대응되는 것으로, HTML 템플릿 파일이다.<Br/>

이 파일이 직접 표시되는 것은 아니고, index.js에 의해 일어 와서 렌더링된 경과가 표시된다.<br/>
<br/>

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"/>
  
    <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
  
    <title>React App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>
```

## src 폴더<br/>
<br/>

### index.js<br/>
<br/>

index.html과 비슷하게 메인 파일로, 여기서 HTML 템플릿 밀 JavaScript의 컴포넌트를 조합하여 렌더링하고 실제 표시한다.<br/>

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);

reportWebVitals();
```

ReactDom.render( ) 안에 보여주고 싶은 component를 넣는다.<br/>
<Br/>

### App.js<br/>
<Br/>

컴포넌트를 정의하는 작업 파일로, 실제로 화면에 표시되는 내용 등은 여기에서 정의된다.<br/>

```
import logo from './logo.svg';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
```

<br/>

### App.css<br/>
<Br/>

App.js에 대한 css 파일이다.<br/>
