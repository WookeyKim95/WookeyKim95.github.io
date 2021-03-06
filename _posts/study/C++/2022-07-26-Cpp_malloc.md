---
layout: post
title:  "[C++] 동적 할당"
subtitle:  
date: 2022-07-26 16:40:29 +0900
categories: study
tags: C++
comments: true
related_posts:

---

# [C++] 동적 할당<br/>
<br/>

## 동적 할당이란?<br/>
<Br/>

프로그램이 구동되는 동안 무조건 할당되는 지역변수, 전역변수와는 달리,<br/>

메모리를 프로그램이 구동하는 동안 원하는 시점에 할당하고 해제 할 수 있게 해주는 기능이다.<br/>

필요할 때에만 쓰기 때문에 메모리 관리를 더 효율적으로 할 수 있다는 장점이 있다.<br/>

지역변수, 전역변수 가리지 않고 동적 할당된 메모리는 힙 영역에 공간이 할당된다.<br/>

C++에서는 new로 동적할당을 하고 delete로 동적할당된 메모리를 제거할 수 있다.<br/>

C에선 malloc으로 동적할당을 하고 free로 동적할당된 메모리를 제거한다.<br/>
<Br/>

여기서는 C++이니까 new와 delete를 이용할 것이다.<Br/>
<br/>

```
int* p = new int;

*p = 3020;

delete p;
```
<Br/>

new를 이용해서 동적할당된 메모리는 반드시 delete를 이용해서 제거해주어야 한다.<br/>
그렇지 않으면 할당된 메모리는 계속 할당된 상태로 있게 된다.<br/>
이때 프로그램을 종료하면 메모리 릭이 남게 된다. (메모리 누수)<br/>

그래서 동적할당을 하면 delete가 가장 중요하다.<br/>

~~현업에서 이거 메모리누수 발생하는 순간 시말서 사유다.~~ <br/>

배열에서의 동적할당은 아래와 같이 할 수 있다.<br/>

```
// 배열에서의 쓰임

int* pArray = new int[10];

pArray[2] = 300;
	
delete[]	pArray;
```
<Br/>

TIP<br/>

참고로, VS 2022는 릴리즈모드, 디버거 모드가있는데 디버그 모드에서는 릭이 발생할 우려가 적지만
릴리즈 모드에서는 100% 발생하니 주의하자.<br/>
<br/>

## 함수 내에서의 동적 할당 시 특히 주의하자.<br/>
<br/>

```
void Test()
{
int Number1 = 200;

// 특히 여기서 메모리 할당 해제를 해주지 않으면 테스트 함수 호출할 때 마다 메모리 누수가 발생한다. 꼭 주의하자.
int* p = new int;
	
delete p;
}
```

특히, 함수 내에서는 메모리 해제를 하지 않으면 **함수를 호출할 때마다 메모리 할당이 계속 발생해서** 메모리 누수량이 기하급수적으로 증가한다.<br/>