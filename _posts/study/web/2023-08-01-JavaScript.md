---
layout: post
title:  "[JavaScript] JavaScript의 기초"
subtitle:   
date: 2023-08-01 06:58:32 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [JavaScript] JavaScript의 기초<br/>
<br/>

자바스크립트를 통해서 외부 API를 호출하고<br/>

HTML문서를 제어할 수 있다.<br/>

자바스크립트가 인기를 얻게 된 요소가 있는데 간단하게 Ajax랑 jQuery에 대해 알아보자<br/>
<br/>


## Ajax란?<br/>
<br/>

자바스크립트와 XML을 이용하여 비동기 통신을 구현하는 방법이다.<br/>

구글이 주로 사용하면서 세상에 알려졌으며, 현재는 xml대신에 json을 사용한다.<br/>

비동기 통신은 서버에 있는 새로운 정보를 받아오기 위해서<br/>

웹 페이지 전체를 서버에 전달하지 않고, 일부만 서버에게 요청받아서 변경가능한 방식이다.<br/>

즉, 클릭하더라도 새로고침이 발생하지 않고 특정 영역만 바뀌게 할 수 있다.<br/>

예를 들어, 네이버의 지도나, 이전에 있었던 실시간 검색어를 들 수 있다.<br/>
<br/>

## jQuery란?<br/>
<br/>

아주 쉽게, 자주쓰는 자바스크립트 소스코드를 모아둔 라이브러리이다.<br/>

이를 이용한 플러그인으로 애니메이션 등의 멋진 효과를 줄 수 있다.<br/>
<br/>

## Hello, World 출력하기<br>
<br/>

```
console.log("Hello, World")

alert("Hello, World")
```
console.log는 콘솔창에 출력이 되고, <br/>

alert는 경고창을 통해서 출력이 된다!<br/>
<br/>

## 변수 선언하기<br/>
<br/>

```
var a = 10;
let b = 20;
const c = 30;
```
변수 선언의 종류는 3가지가 있다.

- var
- let
- const

자료형과 관계 없이 위의 형태로 변수선언을 진행한다.<br/>
<br/>

### var<br/>

재할당이 가능하며, 함수레벨 스코프의 변수이다.

```
var a = 10;
var a = 20;
```

함수레벨 스코프란, 함수 내에서 선언된 변수는 함수 내에서만 유효하다는 의미이다.<br/>

```
function hello(){
    var a = 5;
}
hello();

console.log(a);

// 결과 : 에러

if (true)
{
    // if, for 등에서 사용하는 블록 내부
    var c = 5;
}
console.log(c);

// 결과 : 5
```

var 보다는 let, const을 사용하는 것이 권장되고 있다.<br/>
<br/>

### let<br/>

재선언이 가능하며, 블록레벨 스코프의 변수이다.<br/>
블록레벨 스코프란, 블록 내에서 선언된 변수는 블록 내에서만 유효하다는 의미이다.<br/>

```
let a = 10;
// 이 상태에서 다시 let a = 20;은 불가능.
a = 20;

{
    let b = 5;
}
console.log(b);
// 결과 : 에러
```
<br/>

### const<br/>

재선언, 재할당도 안되는 블록레벨 스코프의 변수이다.<br/>

즉, 상수를 지정하고 싶을 때 const를 쓰고, 나머지는 let을 쓰면 된다고 생각하자.<br/>

<br/>

## 자바스크립트의 자료형<br/>

크게 원시형 타입, 참조형 타입으로 나뉜다.<br/>

- 원시형 타입
    + number (int, float 구분 없이 모두)
    + string
    + boolean
    + null
    + undefined

- 참조형 객체 타입
    + Array
    + Object
    + Function


typeof()을 통해서 자료형을 확인할 수 있다.<br/>

```
const stringB = "123";
console.log(typeof(stringB));

// 결과 : string
```

### null과 undefined의 차이점<br/>
<br/>

- null : 값이 비어있다고 명시된 상태
- undefined : 값을 할당받지 않은 상태

쉽게 말해 개발자의 의도가 담겨있는가 여부를 보면된다.<br/>
<br/>

### 함수표현식<br/>

자바스크립트에서는 함수를 변수에 저장하는 것도 가능하다<br/>

```
let go = function() {
    console.log("##");
};

go();

// 결과 : ##

function ABC() {
    console.log("chicken");
};

bbc = ABC;
bbc();

// 결과 : chicken
```
<br/>

### 호이스팅<br/>

호이스팅이란 런타임 이전의 변수의 선언문들만 먼저 맨 위로 끌어올리는 과정이다.<br/>

일단 이 코드를 보자.
```
var a;
console.log(a);

a = 100;
// 결과 : undefined
```

보통 같으면 에러가 나야하는데, 이 코드는 에러가 나지 않는다.<br/>

코드를 작동시키기에 앞서 변수를 최상단으로 끌어올리는 과정인 호이스팅이 일어나기 때문이다.<br/>

let과 const는 호이스팅이 일어나지 않는다.<br/>

따라서 var보다는 let을 쓰는 것이 더 권장되는 이유가 된다.<br/>

함수도 호이스팅이 일어난다.<br/>

```
hello();

function hello (){
    console.log("Hello World");
}

// 결과 : Hello World
```

그런데 잘못 쓰면 골때리는 사례가 나타난다.<br/>

```
var hello;

hello();

function hello (){
    console.log("Hello World");
}

// 결과 : not a function
```

왜냐하면 변수 호이스팅이 일어나서 hello는 undefined인데

undefined 상태인 것을 작동시키려고 하니까 not a function이 일어난다.<br/>

이 쯤 되면 짜증나니 var는 쓰지말자..<br/>

<br/>

### 객체 자료형의 메모리 할당 과정, 그리고 값의 변경<br/>

```
const obj = {
    a : 5;
}

obj.a = 100;

```

놀랍게도 이 코드는 정상작동한다.<br/>

왜냐하면

- obj 자체는 콜스택 메모리에 저장된다.
- obj 내부의 값은 힙 메모리에 저장이 된다.
- const는 콜스택 메모리에 대한 값 수정을 막는다.

즉, obj.a에 대해서 힙 메모리로 직접 접근해버리면 값을 변경할 수 있는 것이다.<br/>

이쯤 되면 골때린다.<br/>

그럼 값을 못바꾸게 할 수 있을까?

lodash, object.freeze를 사용하면 가능하다.<br/>
