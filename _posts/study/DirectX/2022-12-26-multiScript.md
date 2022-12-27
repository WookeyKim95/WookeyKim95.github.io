---
layout: post
title:  "[DirectX] 여러 개의 Script"
subtitle:  
date: 2022-12-26 19:54:19 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] 여러 개의 Script<br/>
<br/>

오브젝트가 여러개의 Script를 가질 수 있도록 구성해보자.<br/>
<br/>

## GameObject<br/>
<Br/>

기존의 게임오브젝트는 컴포넌트를 배열을 잡았다.<br/>

그리고 스크립트 또한 컴포넌트의 구성 중 하나였었다.<br/>

그러다보니 게임오브젝트 하나당 스크립트 하나만 가질 수 있었다.<br/>

스크립트도 컴포넌트의 구성원 중 하나로 남기면서 여러개를 가질 수 있도록<br/>

스크립트를 담을 벡터를 새로 구성할 것이다.<br/>

```
// CGameObject.h 중에서

vector<Script*> m_vecScript;
```
<br/>

### CGameObject 클래스의 변화<br/>
<br/>

```
// 헤더 파일

#pragma once
#include "CEntity.h"


class CComponent;
class CTransform;
class CMeshRender;
class CCamera;
class CRenderComponent;
class CScript;

class CGameObject :
    public CEntity
{
private:
    CComponent*             m_arrCom[(UINT)COMPONENT_TYPE::END];
    CRenderComponent*       m_RenderCom;
    vector<CScript*>        m_vecScript;

    CGameObject*            m_Parent;
    vector<CGameObject*>    m_vecChild;

    int                     m_iLayerIdx;


public:
    void begin();       // 레벨이 시작될 때 호출 or 시작 된 레벨에 합류할 때
    void tick();        
    void finaltick();
    void render();


public:
    void AddComponent(CComponent* _Component);
    void AddChild(CGameObject* _Object);

    CGameObject* GetParent() const { return m_Parent; }

    CTransform* Transform() const { return (CTransform*)m_arrCom[(UINT)COMPONENT_TYPE::TRANSFORM]; }
    CMeshRender* MeshRender() const { return (CMeshRender*)m_arrCom[(UINT)COMPONENT_TYPE::MESHRENDER]; }
    CCamera* Camera() const { return (CCamera*)m_arrCom[(UINT)COMPONENT_TYPE::CAMERA]; }
    CRenderComponent* GetRenderComponent() const {  return m_RenderCom; }


    CLONE(CGameObject)    
public:
    CGameObject();
    ~CGameObject();

    friend class CLayer;
};
```

```
// cpp 파일

void CGameObject::begin()
{
	for (UINT i = 0; i < (UINT)COMPONENT_TYPE::END; ++i)
	{
		if (nullptr != m_arrCom[i])
			m_arrCom[i]->begin();
	}

	for (size_t i = 0; i < m_vecScript.size(); ++i)
	{
		m_vecScript[i]->begin();
	}

	for (size_t i = 0; i < m_vecChild.size(); ++i)
	{
		m_vecChild[i]->begin();
	}
}

void CGameObject::AddComponent(CComponent* _Component)
{
	_Component->m_pOwner = this;

	// 컴포넌트가 스크립트인 경우
	if (COMPONENT_TYPE::SCRIPT == _Component->GetType())
	{
		m_vecScript.push_back((CScript*)_Component);
	}

	// 스크립트를 제외한 일반 컴포넌트인 경우
	else
	{		
		// 이미 보유하고 있는 컴포넌트인 경우
		assert(!m_arrCom[(UINT)_Component->GetType()]);

		m_arrCom[(UINT)_Component->GetType()] = _Component;

		// RenderComponent 확인
		if (COMPONENT_TYPE::MESHRENDER <= _Component->GetType()
			&& _Component->GetType() <= COMPONENT_TYPE::DECAL)
		{
			assert(!m_RenderCom);
			m_RenderCom = (CRenderComponent*)_Component;
		}
	}
}
```

AddComponent 함수에서 스크립트 컴포넌트의 경우에는 별도의 벡터에 넣어줄 수 있도록 코드를 재구성한다.<br/>

컴포넌트가 스크립트라면 m_vecScript 벡터에 push_back을 해준다.<Br/>

단, Component를 상속받은 Script 클래스 포인터로 다운캐스팅을 해준다.<br/>

그리고 tick()함수에서 스크립트의 tick함수를 실행할 수 있도록 for문을 추가해준다.<br/>

또한 레벨이 시작되는 순간을 위한 begin함수를 추가해준다.<br/>
<br/>

그리고 CLevel, CLayer, CComponent에도 begin과 관련된 코드를 추가해준다.<br/>
<br/>

### CLevel<Br/>
<Br/>

```
// cpp 파일 중에서
void CLevel::begin()
{
	for (UINT i = 0; i < MAX_LAYER; ++i)
	{
		m_arrLayer[i]->begin();
	}
}
```
<br/>

### CLayer<br/>
<Br/>

```
// cpp 파일 중에서
void CLayer::begin()
{
	for (size_t i = 0; i < m_vecParentObj.size(); ++i)
	{
		m_vecParentObj[i]->begin();
	}
}
```

### CComponent<br/>
<br/>

```
// 헤더파일 중에서
public:
    virtual void begin() {}
```
<br/>

### begin함수의 용도<br/>
<br/>

레벨이 시작되는 순간이나 레벨에 합류할 때 호출되는 함수이다.<br/>

나중에 툴까지 연동되어있는 상태라면 툴을 실행할 때에는 레벨이 멈춰있을 것이다.<br/>

레벨이 시작될 때 실행할 초기작업을 여기서 하는 것이다.<br/>

그리고 **한정된 개수만큼 미리 생성해두었다가** 현재 진행 중인 레벨에 합류시킬 때에도 begin함수가 사용된다.<br/>

생성자랑 헷갈리지 말자.<br/>

이를 사용하면 new와 delete의 비용을 아끼고, 이 과정에서 발생하는 지연을 막을 수 있다.<br/>
