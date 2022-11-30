---
layout: post
title:  "[WinAPI] Singleton 패턴"
subtitle:  
date: 2022-09-14 12:49:18 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [WinAPI] Singleton 패턴<br/>
<br/>

싱글톤 패턴에 대해서 살펴보자.<br/>

## 싱글톤 패턴과 그의 코드<br/>
<br/>

객체의 생성과 관련된 패턴으로, 특정 클래스의 객체가 오직 하나만 존재하도록 보장하는 패턴이다.<br/>

```
// 싱글톤 패턴
class CEngine
{
private:	
	static CEngine* m_pInst;

public:

	static CEngine* GetInst()
	{		
		// 정적 맴버변수는 Data 영역에 하나만 존재하기 때문에 this 를 알 필요가 없다.
		// 정적 맴버 함수에서도 접근이 가능하다.
		if (nullptr == m_pInst)
		{
			m_pInst = new CEngine;
		}

		return m_pInst;
	}

	static void Delete()
	{
		if (nullptr != m_pInst)
		{
			delete m_pInst;
			m_pInst = nullptr;
		}
	}	

private:
	CEngine();
	~CEngine();
};

```

위의 코드를 보자. 싱글톤 패턴의 대표적인 예시로 게임에서 엔진 내 객체가 구동하기 시작할 때, 그리고 삭제될 때를<br/>
나타낸 표현이다.<br/>

GetInst()함수에서 CEngine 포인터가 가리키는 m_pInst가 nullptr이 아닐 때에만 new CEngine이 구동되므로 클래스는 오직 하나만 생성될 수 밖에 없다.<br/>

그리고 Delete()함수에서도 마찬가지로 삭제를 해준 뒤에 m_pInst를 nullptr로 되돌려서 CEngine을 사용한 객체가 나중에 다시 하나만 생성될 수 있도록 만들어주었다.<br/>

실무에서 많이 쓰이는 패턴이라고 한다. <br/>
<br/>

## 또 다른 코드 작성 기법<br/>
<Br/>

하지만 위의 코드는 힙영역, 동적할당을 이용해서 메모리를 덜 잡아먹는다는 장점이 있으나, 삭제까지 신경써줘야하므로 불편하다는 점이 있다.<br/>

데이터영역 메모리를 사용하고, 위의 코드보다 더 짧고 간단한 코드가 있다.<br/>

```
class CEngine
{
public:
	// 정적 맴버함수
	static CEngine* GetInst()
	{		
		static CEngine engine;
		return &engine;
	}

private:
	CEngine();
	~CEngine();
```

정적변수는 오직 하나만 생성할 수 있다는 점을 이용해서 GetInst()함수 내에서 CEngine 객체를 생성한다.<br/>

프로그램이 종료되면 데이터 영역에 생성된 정적변수는 자동으로 해제되므로 위의 힙영역을 이용한 코드와는 달리 종료 함수를 따로 작성할 필요가 없다.<br/>

실무에서 많이 쓰이며, 이후에는 이를 이용해서 윈도우 프로그램이 동작하는 원리를 알아가볼 것이다.<br/>
<br/>

## 싱글톤의 상속<br/>
<br/>

```
typedef void (*EXIT)(void);


template<typename T>
class CSingleton
{
private:
	static T* m_Inst;

public:
	static T* GetInst();
	static void Destroy();

public:
CSingleton() {}
virtual ~CSingleton() {}

};

template<typename T>
T* CSingleton<T>::m_Inst = nullptr;

template<typename T>
inline T* CSingleton<T>::GetInst()
{
	if (m_Inst == nullptr)
	{
		m_Inst = new T;
		atexit( (EXIT) &CSingleton<T>::Destroy); // main 함수 종료 직전에 호출되는 함수
	}

	return m_Inst;
}

template<typename T>
inline void CSingleton<T>::Destroy()
{
	if (m_Inst != nullptr)
	{
		delete m_Inst;
		m_Inst = nullptr;
	}
}



// 엔진 등 다른 클래스에서 상속 받을 때
class CEngine
	: public CSingleton<CEngine>
{
	...

public:
	void init(HWND _hWnd, UINT _iWidth, UINT _iHeight);
	
	...
}
```