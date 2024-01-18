---
layout: post
title:  "[React] JSX와 리액트의 렌더링 원리"
subtitle:   
date: 2024-01-19 07:01:41 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [React] JSX와 리액트의 렌더링 원리<br/>
<br/>

리액트는 HTML대신에 JSX라는 파일을 통해서 UI를 구성한다.<br/>

그렇다면 이 JSX는 무엇일까?<br/>

## JSX란?<br/>

JSX는 JavaScript Extension으로, 자바스크립트의 확장 문법이다.<br/>

자바스크립트와 HTML이 함께 쓰인다. 그래서 MarkUP구조를 같이 사용하므로

UI의 데이터가 변하는 것이나 이벤트들이 처리되는 부분을 쉽게 구현할 수 있다.<br/>

## 리액트에서 원래 화면을 렌더링 하는 방식<br/>

React.createElement API를 이용해서 엘리먼트를 생성한 후 이 엘리먼트를 in-Memory에 저장한다. 그리고 ReactDOM.render 함수를 사용해서 실제 웹 브라우저에 렌더링을 진행한다.<br/>

또한, 모든 UI를 만들때 마다 createElement를 사용해서 컴포넌트를 만들 수는 없다.<br/>

그렇기에 JSX를 사용한 후 그걸 바벨에 다시 createElement로 바꿔서 사용한다.<br/>

즉, JSX는 createElement를 쉽게 사용하기 위해 사용하는 것이다.<br/>

```
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

reportWebVitals();
```

```
function App() {
  return {
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>

        <a
          calssName="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"  
        >

        Learn React
        </a>
      </header>
    </div>
  };
}
```
<Br/>

## JSX를 사용하며 주의할 문법 규칙<br/>

JSX는 컴포넌트에 여러 엘리먼트 요소가 있따면 반드시 부모 요소 하나로 감싸줘야 한다는 것을 잊지 말자.<br/>


### 잘못된 코드<br/>

```
function hello() {
  return {
    <div>Hello World!</div>
    <div>what are you doing?</div>
  }
}
```

### 올바른 코드<br/>

```
function hello() {
  return <div>
    <div>Hello World!</div>
    <div>what are you doing?</div>
  }
</div>
```


