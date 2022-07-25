---
layout: post
title:  "[C++] 꼬리 재귀와 함수 포인터"
subtitle:  
date: 2022-07-25 18:50:20 +0900
categories: study
tags: C++
comments: true
related_posts:

---

# [C++] 꼬리 재귀와 함수 포인터<br/>
<br/>

재귀함수란, 함수 내에서 자기 자신을 다시 호출하는 함수를 말했다.<br/>

예시로, 팩토리얼을 재귀함수를 이용해서 나타낼 수 있었다.

```
int Factorial(int Number)
{
	if (Number == 1)
		return 1;
	
	return Number * Factorial(Number - 1);
```

그런데 재귀함수의 단점은 여러번 호출을 할 경우 시간이 오래 걸린다는 점이다.<br/>

이를 보완하기 위한 방법으로 꼬리 재귀가 있다.<br/>
<br/>

## 꼬리 재귀란?<br/>
<br/>

일종의 최적화 기법으로, 기존 재귀함수가 가지는 단점을 극복하기 위한 방법으로 쓰인다.<br/>

팩토리얼을 예시로 들면 아래와 같다.<br/>

```
int Factorial_tail(int Number, int Result)
{
	if (Number == 1)
		return Result;

	return Factorial_tail(Number - 1, Result * Number);
}

int Factorial_tail(int Number)
{
	return Factorial_tail(Number, 1);
}

```
아참, 여기서 또 알아두어야 할 내용이 C++에서는 투입되는 인자의 수가 다를경우 다른 함수로 인식하게 된다.<br/>

여기서 아래쪽에 있는 인자가 1개인 Factorial_tail이 먼저 실행되고 그 후에 인자가 2개인 위쪽에 있는 Factorial_tail을 실행시킨다.<br/>

이때 컴파일러는 이 두 구성을 반복문으로 인식하여 더 빨리 연산을 할 수 있고,<br/> 꼬리재귀는 추가저인 연산이 일어나지 않는다.<br/>

<Br/>

## 함수 포인터<br/>
<br/>

함수 또한 포인터 특성을 이용할 수 있다. 결론부터 적어놓자면 아래의 속성이 같은 함수 끼리 배열로 묶을 수 있다는 소리이다.<br/>

- 투입되는 인자의 개수
- 투입되는 인자의 자료형
- 반환되는 인자의 자료형

```
int Sum(int Num1, int Num2)
{
	return Num1 + Num2;
}

int Minus(int Num1, int Num2)
{
	return Num1 - Num2;
}
```

Sum 함수와 Minus함수는 int형을 반환하며, int형 2개를 투입하는 특징이 같다.<br/>

따라서 아래의 코드와 같이 같은 속성을 가진 함수끼리 있는 배열로 묶을 수 있다.<br/>

```
int(*FuncArray[2])(int, int);

FuncArray[0] = Sum;
FuncArray[1] = Minus;

std::cout << FuncArray[0](30, 30) << std::endl; // 결과 : 60
std::cout << FuncArray[1](30, 30) << std::endl; // 결과 : 0
```

C++의 배열의 특징은 연속된 메모리를 사용한다는 점과, 배열의 이름이 곧 맨 앞자리를 가리키는 포인터라는 점을 이용해서 위와 같은 문법을 사용할 수 있다.<br/>

