---
layout: post
title:  "[C++] 클래스의 static 멤버 번수"
subtitle:  
date: 2022-08-01 19:12:40 +0900
categories: study
tags: C++
comments: true
related_posts:

---

# [C++] 클래스의 static 멤버 번수<br/>
<br/>

## Static 멤버 변수<br/>
<br/>

클래스에서 사용할 수 있는 일반적인 멤버 변수와 달리,<br/>

static 멤버 변수는 모든 객체 내에 있는 같은 변수의 값을 공유시킬 수 있다.<br/>

같은 객체를 아무리 생성하더라도 모든 객체가 공유하는 메모리 딱 1개만 만들어지게 된다.<br/>

이 멤버변수는 반드시 클래스 외부에 따로 선언해주어야 한다.<br/>

클래스 밑에 선언 방식을 적어두었다.<Br/>

이 static 멤버변수는 프로그램을 종료할 때 까지 메모리를 차지한다.<br/>

```
class CStatic
{
public:
	CStatic()
	{

	}

	~CStatic()
	{

	}

public:
	int	m_A;
	static int	m_B;

public:
	void Output()
	{
		std::cout << "A : " << m_A << std::endl;
		std::cout << "B : " << m_B << std::endl;
	}

	static void OutputStatic()
	{
		// 이 함수 내부에선 this 포인터 사용이 불가능하다.
		// 그래서 static 멤버변수만 사용이 가능하다.
		// std::cout << "A : " << m_A << std::endl; // 사용불가
		std::cout << "B : " << m_B << std::endl; // 사용가능
	}
};

// static 멤버변수는 선언부분이 별도로 클래스의 외부에 필요하다.
int	CStatic::m_B = 0;
```

예시 클래스를 하나 들어서 위와 같이 int CStatic::m_B에 있는 것처럼 사용이 가능하다.<br/>

이 코드에서는 m_A는 일반 멤버변수, m_B는 Static 멤버 변수이다.<br/>

```
int main()
{
	CStatic st1, st2, st3;

	st1.m_A = 100;
	st2.m_A = 200;
	st3.m_A = 300;

	st1.m_B = 100;
	st2.m_B = 200;
	st3.m_B = 300;

	std::cout << "st1 A 결과 : " << st1.m_A << std::endl; // 100
	std::cout << "st2 A 결과 : " << st2.m_A << std::endl; // 200
	std::cout << "st3 A 결과 : " << st3.m_A << std::endl; // 300

	std::cout << "st1 B 결과 : " << st1.m_B << std::endl; // 300
	std::cout << "st2 B 결과 : " << st2.m_B << std::endl; // 300
	std::cout << "st3 B 결과 : " << st3.m_B << std::endl; // 300


	CStatic::m_B = 400;
	CStatic::OutputStatic(); // 400


	return 0;
}
```

main 함수를 이렇게 만들어주고 출력을 진행한다면
출력 결과는 아래와 같이 나타난다.<br/>

```
st1 A 결과 : 100
st2 A 결과 : 200
st3 A 결과 : 300
st1 B 결과 : 300
st2 B 결과 : 300
st3 B 결과 : 300
B : 400
```
A 변수는 각자 객체가 서로 다른 값을 가지지만,<br/>

B 변수는 같은 종류의 객체끼리는 같은 메모리를 공유하고 있기 때문에 한 객체에서 B값을 바꾼다면 나머지 모든 객체에 있는 B의 값도 바뀌게 된다.<br/>

따라서 B는 100, 200은 출력되지 않고 마지막에 입력된 300만이 출력된 것이다.<br/>
<br/>

## Static 멤버 함수<br/>
<br/>

```
	static void OutputStatic()
	{
		// 이 함수 내부에선 this 포인터 사용이 불가능하다.
		// 그래서 static 멤버변수만 사용이 가능하다.
		// std::cout << "A : " << m_A << std::endl; // 사용불가
		std::cout << "B : " << m_B << std::endl; // 사용가능
	}
```

이런 함수가 있는 것도 보았다. 이런 종류의 함수를 보고 static 멤버 함수라고 한다.<br/>
```
CStatic::OutputStatic();
```
위와 같은 형식으로 사용할 수 있다.<br/>

마치 출력할 때 std::cout 을 이용하는 것 처럼.<br/>

Static 멤버 함수의 특징은 함수 내에 사용하는 변수는 일반 멤버 변수는 사용할 수 없고, 오직 Staitc 멤버 변수만을 사용할 수 있다.<br/>

따라서 위의 코드에 있는 m_A변수는 사용이 불가능하고,<br/>
Static 변수인 m_B함수만이 사용이 가능하다.<br/>

