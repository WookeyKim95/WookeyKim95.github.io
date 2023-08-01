---
layout: post
title:  "[JavaScript] JavaScript의 기초 문법"
subtitle:   
date: 2023-08-02 06:10:35 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [JavaScript] JavaScript의 기초 문법<br/>
<br/>

슬금 슬금 기초 문법에 대해 알아보자.<br/>
<br/>

## prompt로 입력받기<br/>
<br/>

```
var txt = prompt("입력하세요")ㅣ
console.log(txt);
```

이렇게 하면 입력하세요 라는 경고창이 뜨고 텍스트를 입력받게 된다.<br/>

그리고 입력한 값이 그대로 출력된다.<br/>
<br/>

## type 변환<br/>
<br/>

```
let a = 10;
let b = Number(a);
let c = String(a);
```

위와 같이 Number, String을 이용해서 형변환을 시킬 수 있다.<br/>

마치 파이썬의 int, str에 대응된다.<br/>
<br/>

## 문자열 이어 붙이기<br/>
<br/>

```
let a = "ABC" + "BBQ";
let b = "BBQ" + 10;

console.log(a);
console.log(b);

// 결과
// ABCBBQ
// BBQ10
```

```
let a = "KFC" + 119 + "BBQ";
console.log(a.length);

// 결과 : 9
```

파이썬처럼 +을 이용해서 문자열을 자유롭게 합칠 수 있다.<br/>
<br/>

## 비교 연산자<br/>
<br/>

- \> 와 \>= 종류는 C언어와 동일하다.

다만 같다, 다르다를 나타내는 부분에서는 주의를 해야한다.<br/>

- ==와 !=는 type가 달라도 값만 같으면 true가 반환된다.
- ===와 !==는 값과 type 둘다 같아야 true가 반환된다.

주로 !==와 ===을 사용하는 것이 좋을 것 같다.<br/>
<br/>

## 반복문 (for, while)<br/>
<br/>

C언어 모양과 동일하다.<br/>

```
for (let i = 10; i >= 1; --i)
{
    console.log(i);
}

let a = 1;
while (a <= 10)
{
    console.log(a);
    a++;
}
```