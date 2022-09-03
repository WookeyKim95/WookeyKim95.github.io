---
layout: post
title:  "[C++] 클래스의 상속"
subtitle:  
date: 2022-09-03 13:55:23 +0900
categories: study
tags: C++
comments: true
related_posts:

---

# [C++] 클래스의 상속<br/>
<br/>

클래스의 하이라이트는 상속을 할 수 있다는 점이다.<br/>

## 클래스의 상속이란?<br/>
<br/>

쉽게 요약하면 공통적인 특징을 가진 객체들이 같은 코드를 여러번 재사용 할 수 있도록 기존에 있던 코드를 새로운 클래스가 사용할 수 있도록 하는 것이다.<br/>

동물로 예시를 한번 들어보자.<br/>

동물에는 여러가지 종류가 있는데, 필자는 대표적으로 조류, 포유류, 파충류를 들어보겠다.<br/>

이들의 공통점은 동물이다. 동물의 주요 특징으로 아래와 같은 특징을 들 수 있다.<br/>

- 살아있다.
- 눈, 코, 입이있다.

이런 특징은 조류, 포유류, 파충류 모두 가지고 있고, 동물이라는 특징을 가진 객체들은 모두 공유한다.<br/>

따라서 조류, 포유류, 파충류는 모두 위의 특징들을 상속받는다.<br/>
<br/>

그리고 조류의 특징은 날개가 있다는 점이고,<br/>
포유류의 특징은 젖을 새끼에게 먹인다는 점이며,<br/>
파충류의 특징은 가죽이 질기다는 점이다.<br/>

그리고 조류에는 닭, 오리, 참새 등이 있고, <br/>
포유류는 개, 고양이, 사람 등이 있으며<br/>
파충류는 악어, 이구아나 등이 있다.<br/>

그림으로 클래스 상속구조를 다음과 같이 그릴 수 있다.<br/>
<Br/>

![동물의 상속](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/C++/2022-09-03_inheritance.jpg?raw=true)

## 클래스의 상속을 코드로 구현하기.<br/>
<br/>

간단하게 구현을 하자면 아래와 같이 구현할 수 있다.

```
class CParent
{
protected:
	int m_1;
	int m_2;
public:
	CParent():
		m_1(0),
		m_2(0)
	{
	}

	CParent(int a, int b) :
		m_1(a),
		m_2(b)
	{
	}


	~CParent()
	{
	}

public:
	void Test() 
	{
		std::cout << "퍄퍄 패런트 클래스" << std::endl;
	}

};


class CChild : public CParent
{
private:
	int m_3;
	int m_4;

public:
	void Test()
	{
		std::cout << "응애 차일드 클래스" << std::endl;
	}

	void otherTest()
	{
		std::cout << "응애응애 차일드 아더" << std::endl;
	}

public:
	CChild():
		m_3(0),
		m_4(0)
	{
	}

	~CChild()
	{
	}
};
```

여기서 특이한 특징이 있다.<br/>
<br/>

### Protected<br/>
<br/>

기능부터 설명하자면 변수의 조작을 자신과 자식클래스의 함수 내에서 조작할 수 있도록 제한하는 기능이다.<br/>

위의 코드에서 CChild 내에서 m_3, m_4 변수는 오직 CChild 객체 내에 있는 함수에서만 조작할 수 있다면<br/>

CParent 내에 있는 m_1, m_2 변수는 CParent내의 함수, 그리고 CChild 내에 있는 함수에서 조작할 수 있다.<br/>

만약에 m_1, m_2 변수도 private로 설정되어 있다면 자식클래스인 CChild 에서도 조작을 할 수 없게 된다.<br/>

private을 활용한 은닉성을 강화하는 특징을 살리면서 상속의 기능을 잘 사용하기 위해서 protected가 존재하는 것으로 보인다.<br/>
<br/>

### Override<br/>
<br/>

잘 보면, Parent 클래스와 Child 클래스에 Test()라는 같은 함수가 존재하는 것을 볼 수 있다.<br/>

결론부터 말하면 Child 클래스로 생성한 객체가 있다면 그 객체가 Test()함수를 호출할 때에 Child 클래스에 있는 Test()가 호출된다.<br/>

그래서 "응애 차일드 클래스" 가 출력된다.<br/>

```
CChild C1;
CParent P1;

P1.Test();
C1.Test();

/*
출력 결과

퍄퍄 패런트 클래스
응애 차일드 클래스

*/
```

같은 이름의 함수를 사용하면서 부모의 기능을 자식이 덮어써서 사용하는 것을 오버라이드(Override)라고 한다.<br/>

대부분의 부모의 기능을 물려 받으면서, 자식클래스에서 부모의 기능을 다르게 사용하고 싶을 때 오버라이드를 사용한다.<br/>

