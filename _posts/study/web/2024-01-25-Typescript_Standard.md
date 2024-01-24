---
layout: post
title:  "[TypeScript] 타입스크립트 기본"
subtitle:   
date: 2024-01-25 06:57:08 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [TypeScript] 타입스크립트 기본<br/>
<Br/>

JavaScript는 원래 클라이언트 측 언어로 도입됐다.<Br/>

그런데 Node.js의 개발로 인해 JavaScript를 클라이언트 측 뿐만이 아닌 서버 측 기술로도 활용되게 되었다.<br/>

그러나 JavaScripy 코드가 커질 수록 소스 코드가 더 복잡해져서 코드를 유지 관리하고 재사용하기가 어려워졌다.<br/>

더욱이, Type 검사 및 컴파일 시 오류 검사의 기능을 수용하지 못하기 때문에<br/>

JavaScript가 본격적인 서버 측 기술로 엔터프라이즈 수준에서 성공하지 못한다.<br/>

이 간극을 해소하고자 TypeScript가 나오게 되었다.<br/>

## TypeScript란?<br/>
<br/>

타입스크립트는 자바스크립트에 타입을 부여한 언어이다.<br/>

타입스크립트는 자바스크립트와 달리 브라우저에서 실행하려면 파일을 한번 컴파일 해주어야 한다.<br/>

## TypeScript를 사용하는 이유?<br/>
<Br/>

- 타입스크립트는 자바스크립트를 단순화하여 더 쉽게 읽고 디버그 할 수 있도록 도와준다.
- 타입스크립트는 오픈소스이다.
- 타입스크립트를 사용하면 코드를 더 쉽게 읽고 이해할 수 있다.
- 타입스크립트는 ES6의 모든 이점과 더 많은 생산성을 제공한다.
- 타입스크립트는 코드 유형 검사를 통해 자바스크립트를 작성할 때 개발자가 일반적으로 겪는 버그를 피하는데 도움이 된다.

<br/>

## TypeScript의 제공 타입<br/>
<Br/>

### 기본 타입<br/>

기본 타입은 아래와 같다.<br/>

- number : 숫자 값
- boolean : true와 false
- string : 문자열
- symbol : 고유한 상수 값
- undefined : 초기화 되지 않은 기본 값
- null : 아무것도 없는 경우
<br/>

그리고 선언 방식은 아래와 같다.

```
// number, string, etc..
let num: number = 3;
let name: string = "John";

// function
const getNumber: (i: number): void => {
  console.log(i)
}

// array
const arr: string[] = ['a', 'b', 'c']

// class
let music: Music = new Music()

// object
let point: { x:number; y: number} = {x:20, y:10}
```

### Any<br/>
<Br/>

앱을 만들 때, 잘 알지 못하는 타입을 표현해야 할 때 사용한다.<br/>

이 값들은 사용자로부터 받은 데이터나 서드 파티 라이브러리 같은 동적인 컨텐츠에서 올 수도 있다.<br/>

```
let something: any = "Hello World!";
something = 23;
something = true;

let arr: any[] = ["John", 212, true];
arr.push("Smith");
console.log(arr); [ 'John', 212, true, 'Smith' ]
```

<br/>

### Union<Br/>
<Br/>

타입스크립트를 사용하면 변수 또는 합수 매개변수에 대해 둘 이상의 데이터 유형을 사용할 수 있다.<br/>

이 때 유니온 타입을 사용한다.<br/>

```
let code: (string | number);
code = 123;
code = "ABC";
code = false; // error
```

### Enum<Br/>
<br/>

Enum은 값들의 집합을 명명하고 이를 사용하도록 만든다.<br/>

여기서는 PrintMedia라 불리는 집합을 기억하기 어려운 숫자 대신 친숙한 이름으로 사용하기 위해 enum을 활용할 수 있다.<br/>

```
enum PrintMedia {
  Newspaper, //0
  Newsletter,//1
  Magazine,  //2
  Book       //3
}
```

```
let mediaType: number = PrintMedia.Book
```

#### Object와의 차이점<br/>

Object는 이후 객체에 자료 값을 추가할 수 있지만, enum은 그렇지 않다.<br/>

<br/>

### Void<br/>
<Br/>

Java와 같은 언어와 유사하게 데이터가 없는 경우 사용된다.<br/>

예를 들어 함수의 값을 반환하지 않으면 void를 사용하면 된다.<br/>

```
function sayHi(): void {
  console.log('Hi!');
}

let Speech: void = sayHi();
console.log(Speech); //Output : undefined
```

<br/>

### Never<br/>
<br/>

절대 발생하지 않은 값을 나타내는 Never이다.<br/>

즉, 어떤 일이 절대 일어나지 않을 것이라고 확신할 때 사용한다.<br/>

일반적으로 함수의 리턴 타입으로 사용된다.<br/>

```
function throwError(errorMsg: string): never {
  throw new Error(errorMsg);
}
```
