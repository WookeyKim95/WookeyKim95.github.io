---
layout: post
title:  "[C] 포인터 복습"
subtitle:
date: 2022-04-02 09:02:48 +0900
tags: C
categories: study
comments: true
related_posts:

---

# [C] 포인터 복습<Br/>

## pointer에 대해서 다시 공부해보기<br/>
<br/>

[이 포스트](https://wookeykim95.github.io/study/2022-03-16-data-pointer/)에서도 포인터에 대해서 간단하게 다루었지만, 배운 내용을 복습할 겸 다시 정리해보고자 한다.<br/>

```
#include <stdio.h>

int main() {

    int* p;
    int i = 3, j;

    p = &i;
    j = *p; // p에 저장된 값인 3을 저장함. 역참조라고도 부름.
    j++; // j에다가 1을 더함.

    printf("%d\n", *p); // 3
    printf("%d\n", p); // i의 주소
    printf("%d\n\n", j); // 4

    p = &j;

    printf("%d\n", *p); // i가 아니라 j를 가리키므로 4가 나온다.
    printf("%d\n\n", p); // j의 주소가 나온다.

    *p = *p + 3; // 주소 p에 저장된 값에다가 3을 더해서 다시 p에 넣어라.
    // 현재 상태에서는 j += 3 과 같은 효력을 가진다.

    printf("%d\n", i); // 3
    printf("%d\n", j); // 7
    printf("%d\n\n", *p); // 7

```

```
int* p
```
p에 주소를 저장하는 포인터 변수형식을 가지며,
```
int * p
int *p 
```
와 같이 선언하는 방법도 유효하다.

## 포인터의 크기<br/>

주소를 저장하므로 data 타입과 관계 없이 모두 4 바이트이다. (32비트 기준)<br/>
64비트 기준으로는 8 바이트이다.<br/>

포인터는 메모리 주소의 제일 상위 값을 가리키며,<br/>
그 주소부터 몇 바이트를 읽는 지는 포인터에 저장된 변수의 자료형을 보고 결정한다.

아래 그림을 통해서 설명을 해보겠다.<br/>

![그림](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/C/2022-04-02-pointer_1.jpg?raw=true)

포인터 변수가 메모리 4칸을 차지하며, 포인터가 변수가 저장된 위치를 가리키며, 변수가 차지하는 메모리 중 최상단의 위치를 가리키는 것이다.

그리고 C언어에서 자료형의 크기는

- 문자 : 1 바이트
- 정수 : 4 바이트
- 실수 : 8 바이트

이다.<br/>

위에 있는 값 만큼 최상단 메모리로부터 메모리를 차지하는 것이다.<br/>(정수면 4칸, 실수면 8칸, 문자면 1칸..)<br/>

