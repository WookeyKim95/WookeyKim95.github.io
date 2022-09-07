---
layout: post
title:  "[C++] 클래스의 가상함수와 추상화"
subtitle:  
date: 2022-09-07 10:12:25 +0900
categories: study
tags: C++
comments: true
related_posts:

---

# [C++] 클래스의 가상함수와 추상화<br/>
<br/>

추상 클래스라는 것이 있다. 추상 클래스는 가상함수를 사용하여 객체를 생성하지 못하는 클래스를 말한다.<br/>

그럼 가상함수라는 것은 무엇이고 이로 인해서 이루어지는 추상화란 무엇일까?<br/>
<br/>

## 가상함수란?<br/>
<br/>

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
	// 가상함수로 변경
	virtual void Test() 
	{
		std::cout << "퍄퍄 패런트 클래스" << std::endl;
	}

	virtual void Abstract() = 0; // 순수 가상함수
};


class CChild : public CParent
{
private:
	int m_3;
	int m_4;

public:
	// 오버라이딩
	virtual void Test() override
	{
		std::cout << "응애 차일드 클래스" << std::endl;
	}

	void otherTest()
	{
		std::cout << "응애응애 차일드 아더" << std::endl;
	}

	virtual void Abstract() // 가상함수 구현부
	{
		std::cout << "꺄륵 가상함수입니다" << std::endl;
	}

public:
	CChild():
		m_3(0),
		m_4(0)
	{
	}

	CChild(int a, int b, int c, int d) :
		CParent(a, b),
		m_3(c),
		m_4(d)
	{
	}


	CChild(const CChild& _other)
		: CParent(_other)
		, m_3(_other.m_3)
		, m_4(_other.m_4)
	{
	}

	~CChild()
	{
	}
};
```

위의 코드를 보면 눈에 띄는 키워드 **virtual**이 있다.
<br/>

```

// 부모 클래스 영역
	virtual void Test() 
	{
		std::cout << "퍄퍄 패런트 클래스" << std::endl;
	}

	virtual void Abstract() = 0; // 순수 가상함수

// 자식 클래스 영역

	// 오버라이딩
	virtual void Test() override
	{
		std::cout << "응애 차일드 클래스" << std::endl;
	}

	virtual void Abstract() // 가상함수 구현부
	{
		std::cout << "꺄륵 가상함수입니다" << std::endl;
	}
```

<br/>
필자가 공부하면서 느낀 가상함수를 그냥 요약하자면, 자식 클래스에서 구현할 함수와 기능들을 부모 클래스에서 미리 선언해 두는 모양새이다.<br/>
<br/>

헤더파일엔 함수를 선언하고 cpp파일에 구현을 해두는데, 그런 느낌이 든다.<br/>
<br/>
참고로, 부모 클래스에서 가상함수를 선언하면 그 부모 클래스는 더 이상 사용할 수 없게 된다. 이때 이런 상태의 부모 클래스, 즉 가상함수가 있는 클래스를 **추상 클래스**라고 한다.<br/>


```
int main()
{
	CChild C1;

	// 부모포인터로 자식클래스의 위치를 가리킴.
	CParent* pP = &C1;

	
	C1.Test(); // 자식의 Test

	pP->Test();
	pP->Abstract();

    return 0;
}
```

위에 코드를 선언해두고 밑의 main함수를 코딩한 뒤 구동하면 결과는 아래와 같이 출력된다.<br/>

<Br/>

```
응애 차일드 클래스
응애 차일드 클래스
꺄륵 가상함수입니다
```

여기서 이상한 점이 보인다!

pP->Test()는 부모 클래스 자료구조 형태의 포인터인데 왜 자식클래스에 있는 내용이 출력되는 것일까?<br/>

그리고 어떻게 pP->Abstract()가 정상적으로 작동할 수 있었을까?<Br/>
<br/>

## 부모와 자식클래스의 메모리 할당 구조<br/>
<br/>

그림으로 메모리 할당 구조를 살펴보자.<br/>
<br/>

![메모리할당1](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/C++/2022-09-07_1.jpg?raw=true)

<br/>

클래스를 생성하면 위 그림과 같이 부모와 자식 클래스가 메모리에 할당이 된다.<br/>

이 때, pP와 같이 부모클래스 포인터로 자식 클래스 지점을 가리키게 된다면 아래 그림과 같은 형태가 된다.<br/>

![메모리할당2](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/C++/2022-09-07_2.jpg?raw=true)

<br/>

포인터의 특징이 무엇인가? **주소 지점으로부터 자료형의 크기만큼 메모리를 읽어들이는 것이다.** <br/>

int 이면 4바이트만큼, double이면 8바이트만큼.. 그렇다면 부모클래스 포인터라면? 부모클래스의 크기만큼 자료를 읽어들인다.<br/>

근데 자식클래스가 있는 주소에 부모클래스 만큼 메모리를 읽어들인다면 자식클래스 주소를 읽었음 에도 불구하고 부모클래스의 자료를 읽어들인다. 그 이유가 바로 저 그림에 나와있는 것이다.<br/>

만약 반대로 자식클래스 포인터로 부모 클래스 주소를 읽어들인다면?<br/>

![메모리할당3](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/C++/2022-09-07_3.jpg?raw=true)

<br/>

위 그림과 같이 되는 것이므로 에러가 발생할 것이다.<br/>
다행히 Visual Studio는 이런 상황이 발생한다면 에러를 표시한다.

<br/>

## 부모클래스의 가상함수를 자식클래스에서 사용하기<br/>
<br/>

```

// 부모 클래스 영역
	virtual void Test() 
	{
		std::cout << "퍄퍄 패런트 클래스" << std::endl;
	}

	virtual void Abstract() = 0; // 순수 가상함수

// 자식 클래스 영역

	// 오버라이딩
	virtual void Test() override
	{
		std::cout << "응애 차일드 클래스" << std::endl;
	}

	virtual void Abstract() // 가상함수 구현부
	{
		std::cout << "꺄륵 가상함수입니다" << std::endl;
	}

int main()
{
	CChild C1;

	// 부모포인터로 자식클래스의 위치를 가리킴.
	CParent* pP = &C1;

	
	C1.Test(); // 자식의 Test

	pP->Test();
	pP->Abstract();

    return 0;
}

```

그래서 위의 코드를 실행하면 pP->Test()에서도 자식클래스의 내용이,<br/>
pP->Abstract()도 자식클래스의 내용이 실행된다.<br/>

만약에 부모클래스의 Test()가 가상함수가 아니었다면?<br/>

```
// 부모 클래스 영역
	void Test() 
	{
		std::cout << "퍄퍄 패런트 클래스" << std::endl;
	}

	virtual void Abstract() = 0; // 순수 가상함수

// 자식 클래스 영역

	void Test()
	{
		std::cout << "응애 차일드 클래스" << std::endl;
	}

	virtual void Abstract() // 가상함수 구현부
	{
		std::cout << "꺄륵 가상함수입니다" << std::endl;
	}
```

다시 '퍄퍄 패런트 클래스'가 출력된다.<br/>

```
// 출력 결과

응애 차일드 클래스
퍄퍄 패런트 클래스
꺄륵 가상함수입니다
```

<br/>

## 가상함수를 사용하는 이유?<br/>
<Br/>

필자가 느낀 결론부터 말하자면 상위 항목에서 기능을 설정하고 하위 항목에서 그 기능을 상속받아서 각기 구현을 쉽게 하고, 유지보수를 용이하게 하기 위해서 라고 생각한다.<br/>

또한, 최소한의 포인터로 많은 객체를 관리할 수 있게 되니까.(다형성)<br/>

가상함수, 추상화, 추상클래스의 특징은 객체를 생성할 수 없고 그 추상적인 개념을 표시한다는 점이다.<br/>

동물들을 다시 예시로 들어보자.<br/>

![동물 예시](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/C++/2022-09-07_4.jpg?raw=true)

<br/>

위의 그래프에서 실제로 객체상으로 존재하는 것은 무엇일까?<br/>

다시 말해, **현실 세계에 실존하는 것을 말하는 것**이다.<br/>

읽는 사람에 따라 기준이 다르겠지만, 개, 고양이가 실존한다고 할 수 있고, 아니면 고양이보다 하위 항목에 있는 러시안 블루, 코리안 숏헤어가 실존한다고도 할 수 있다.<br/>

하지만 읽는 사람이 누구던지, 조류, 파충류, 포유류 라는 객체 자체가 존재한다고는 하지 않을 것이다. <br/>

그저 각기 종류에 있는 동물들을 포함하는 개념상으로 일컫어 지는 것이다.<br/>
<br/>
<Br/>

게임에서도 마찬가지이다.<br/>

몬스터라는 상위 항목 아래에 드래곤, 오크, 고블린 이라는 하위 항목이 있을 것이고, 이들의 공통점은 몬스터라는 것이다.<br/>

몬스터들의 공통점은 움직이고, 공격을 한다는 점이다. 하지만 어떻게 움직이고 어떻게 공격을 하는지는 각기 다를 것이다.<br/>

그렇다면 클래스 상속구조를 이렇게 할 수 있지 않을까?

```
class 몬스터
{
    공격기능
    움직이기기능
}

class 고블린 : public 몬스터
{
    방망이로 공격
    빨리 움직임
}

class 오크 : public 몬스터
{
    도끼로 공격
    중간 속도로 움직임
}

class 오크 : public 몬스터
{
    불꽃 브레스로 공격
    움직이지 않음
}
```

이럴때 사용하는 것이 추상 클래스이다.<br/>

상위 항목의 기능을 설정하고, 실제로 기능을 사용하는 것은 하위 항목에서 사용한다.<br/>

이렇게 가상함수는 객체지향 프로그래밍에서 중요한 개념 중 추상화와 다형성을 담당한다.