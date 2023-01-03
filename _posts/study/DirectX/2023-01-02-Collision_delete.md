---
layout: post
title:  "[DirectX] 충돌 시 스크립트 조작 그리고 삭제"
subtitle:  
date: 2023-01-02 19:55:50 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] 충돌 시 스크립트 조작 그리고 삭제<br/>
<br/>

지난 시간에 어떻게 충돌처리 할지를 했었다면, 이번엔 충돌 시 어떤 행동을 할지 결정하는 과정을 작성할 것이다.<br/>
<br/>

## CScript 클래스에서 가상함수 만들기<br/>
<br/>

```
// 헤더파일 중에서

public:   
    virtual void finaltick() final {};

    virtual void BeginOverlap(CCollider2D* _Other) {}
    virtual void OnOverlap(CCollider2D* _Other) {}
    virtual void EndOverlap(CCollider2D* _Other) {}

```

플레이어 스크립트, 미사일 스크립트 등의 부모클래스인 CScript 클래스에 가상함수를 만들어준다.<br/>
<Br/>

### CPlayerScript 클래스<br/>
<br/>


```
// CMonsterScript.cpp 파일 중에서

void CMonsterScript::BeginOverlap(CCollider2D* _Other)
{
	if (L"Parent Object" == _Other->GetOwner()->GetName())
	{
		//Destroy();	

		SetLifeSpan(2.f);
	}
}

```

그리고 PlayerScript에서 (또는 MonsterScipt, MissileScipt 등이 될 수 있다!) BeginOverlap, OnOverlap, EndOverlap 함수에서 어떻게 행동할지를 작성해준다.<br/>
<br/>

## Script 작동시키기<br/>
<Br/>

### CGameObject 클래스의 변화<br/>
<Br/>

```
// 헤더파일 중에서

private:
    vector<CScript*>        m_vecScript;

public:
    template<typename T>
    T* GetScript();

    const vector<CScript*>& GetScripts() { return m_vecScript; }

template<typename T>
inline T* CGameObject::GetScript()
{
    for (size_t i = 0; i < m_vecScript.size(); ++i)
    {
        T* pScript = dynamic_cast<T*> (m_vecScript[i]);
        if (nullptr != pScript)
            return pScript;
    }

    return nullptr;
```

우선 CGameObject에서는 스크립트가 담긴 벡터를 반환시킨다.<br/>
<br/>

### CCollider2D 클래스의 변화<br/>
<br/>

```
// cpp파일 중에서

void CCollider2D::BeginOverlap(CCollider2D* _Other)
{
	m_iCollisionCount += 1;

	// Script 호출
	const vector<CScript*>& vecScript = GetOwner()->GetScripts();
	for (size_t i = 0; i < vecScript.size(); ++i)
	{
		vecScript[i]->BeginOverlap(_Other);
	}
}

void CCollider2D::OnOverlap(CCollider2D* _Other)
{
	// Script 호출
	const vector<CScript*>& vecScript = GetOwner()->GetScripts();
	for (size_t i = 0; i < vecScript.size(); ++i)
	{
		vecScript[i]->OnOverlap(_Other);
	}
}

void CCollider2D::EndOverlap(CCollider2D* _Other)
{
	m_iCollisionCount -= 1;

	// Script 호출
	const vector<CScript*>& vecScript = GetOwner()->GetScripts();
	for (size_t i = 0; i < vecScript.size(); ++i)
	{
		vecScript[i]->EndOverlap(_Other);
	}
}
```

그리고 for문을 이용해서 GameObject로부터 받아온 Script들을 호출해서 해당 Script 들을 작동시킨다.<br/>
<br/>

## 충돌 시 Object의 삭제 구현<br/>
<Br/>

```
// define.h 헤더

enum class EVENT_TYPE
{
    CREATE_OBJECT,
    DELETE_OBJECT,
    ...
}

```

이벤트 타입 중에서 DELETE_OBJECT 인자가 있으면 된다.<br/>

```
// func.h 헤더 중에서

void DestroyObject(CGameObject* _DeleteObejct)
```
를 추가하고

```
// func.cpp 파일 중에서

void DestroyObject(CGameObject* _DeleteObejct)
{
	tEvent evn = {};

	evn.Type = EVENT_TYPE::DELETE_OBJECT;
	evn.wParam = (DWORD_PTR)_DeletObject;
	
	CEventMgr::GetInst()->AddEvent(evn);
}
```

위의 코드를 준비해준다.<br/>

그리고 몬스터가 접촉을 시작할 경우(BeginOverlap) 몬스터를 삭제시켜보자.<br/>

```
// 몬스터 스크립트 cpp 파일 중에서

void CMonsterScript::BeginOverlap(CCollider2D* _Other)
{
	if (L"Parent Object" == _Other->GetOwner()->GetName())
	{
		Destroy();	
	}
}
```

Destroy함수를 호출하고, 그 안에 있는 DestroyObject 함수를 다시 호출해서 오브젝트를 삭제하는 형식이다.<br/>

DestroyObject는 EventMgr에서 오브젝트의 생성과 삭제를 관장하므로 EventMgr에게 삭제 이벤트 발생이 발생했다고 전달한다.<br/>
<br/>

### CGameObject의 변화<br/>
<br/>

```
// 헤더 파일 중에서
    bool                    m_bDead;
    float                   m_LifeTime;
    float                   m_CurLifeTime;
    bool                    m_bLifeSpan;
```

```
// cpp 파일 중에서
void CGameObject::finaltick()
{
	if (m_bLifeSpan)
	{
		m_CurLifeTime += DT;
		if (m_LifeTime < m_CurLifeTime)
		{
			DestroyObject(this);
		}
	}


	for (UINT i = 0; i < (UINT)COMPONENT_TYPE::SCRIPT; ++i)
	{
		if (nullptr != m_arrCom[i])
			m_arrCom[i]->finaltick();
	}

	for (size_t i = 0; i < m_vecChild.size(); ++i)
	{
		m_vecChild[i]->finaltick();
	}

	
	// 소속 레이어가 없는데 finaltick 이 호출되었다.
	assert(-1 != m_iLayerIdx); 

	// 레이어 등록
	CLayer* pCurLayer = CLevelMgr::GetInst()->GetCurLevel()->GetLayer(m_iLayerIdx);
	pCurLayer->RegisterObject(this);
}
```

이벤트 매니저에게 삭제 이벤트 발생을 전달하기 전에 CGameObject에서는 삭제 예정인가 여부를 판단하는 m_bDead 변수를 추가하고, EventMgr클래스를 친구 클래스로 설정해준다.<br/>
<br/>

### EventMgr 클래스의 변화
<br/>

```
// 헤더 파일

class CEventMgr :
    public CSingleton< CEventMgr>
{
    SINGLE(CEventMgr);
private:
    vector<tEvent>          m_vecEvent;    
    vector<CGameObject*>    m_vecGC;

public:
    void AddEvent(const tEvent& _evn) { m_vecEvent.push_back(_evn); }

private:
    void GC_Clear();

public:
    void tick();
};
```

```
// cpp 파일 중에서

	case EVENT_TYPE::DELETE_OBJECT:
	{
		CGameObject* DeleteObject = (CGameObject*)m_vecEvent[i].wParam;

		if (false == DeleteObject->m_bDead)
		{
			DeleteObject->m_bDead = true;
			m_vecGC.push_back(DeleteObject);
		}			
	}
		break;
```
```
// cpp 파일의 GC_Clear() 함수

void CEventMgr::GC_Clear()
{
	for (size_t i = 0; i < m_vecGC.size(); ++i)
	{
		if (nullptr != m_vecGC[i])
			delete m_vecGC[i];
	}
	m_vecGC.clear();
}

```

그리고 이벤트 매니저의 이벤트 큐에서 삭제 이벤트에 대한 처리 방법을 위의 코드와 같이 입력한다.<br/>

이렇게 하는 이유는 어떤 오브젝트(이하 A)에 대해 참조를 하고 있는 오브젝트가 있었는데, A가 삭제되어 A를 참조하고 있던 오브젝트는 쓰레기 값을 가리키게 되어 이 상태로 tick을 호출하게 된다면 오류가 발생하게 된다.<br/>

그래서 오브젝트의 참조를 정리하기 위해서 EventMgr에서 생성 이벤트, 삭제 이벤트를 처리하고 그 다음에 tick 함수를 호출하는 것이다.<br/>
<br/>

m_bDead가 true가 된다면 그 오브젝트를 가비지콜렉터(m_vecGC)에 담아둔다.<br/>

그리고 다음 프레임의 tick이 돌기 전에 GC_Clear()를 통해 가비지 콜렉터에 담긴 오브젝트들을 삭제하는 작업을 한다.<br/>

그런데 여기서 발생할 수 있는 문제는 아래와 같이 한 프레임에 Destroy 이벤트가 여러번 발생했을 경우이다.<br/>

```

if (L"Parent Object" == _Other->GetOwner()->GetName())
{
    Destroy();
    Destroy();
    Destroy();
    Destroy();
}
```
이 경우를 대비해서 if문을 써서 m_bDead가 false 상태일 때에만 가비지 콜렉터에 넣을 수 있도록 조치를 해야할 것이다.<br/>

그리고 또 발생할 수 있는 문제는 연속된 두 프레임에서 같은 오브젝트에 대해서 삭제 이벤트를 발생시키는 경우이다.<br/>

이미 삭제되었기 때문에 가비지 콜렉터에 담으려고 했더니 (혹은 삭제 예정인 오브젝트인데 또 가비지 콜렉터에 보내야 하는 경우) GameObject*가 nullptr이 될 수 있다.<br/>

이를 대비해서 m_bDead를 반환하는 IsDead()의 결과가 true인 경우 같은 물체에 대한 삭제 이벤트를 또 다시 발생시키지 않도록 조치를 취한다.<br/>

### CLayer 클래스에서<br/>
<br/>

```
// CLayer.cpp 파일 중에서

void CLayer::finaltick()
{
	vector<CGameObject*>::iterator iter = m_vecParentObj.begin();
	for (; iter != m_vecParentObj.end(); )
	{
		(*iter)->finaltick();

		if ((*iter)->IsDead())
		{
			iter = m_vecParentObj.erase(iter);
		}
		else
		{
			++iter;
		}
	}	
}

```

finaltick함수를 변경시켜보자. 왜냐하면 지금 엔진 구성이 finaltick()함수를 호출한 뒤에 충돌 검사를 진행하는 구조이다. <br/>

여기서 dead처리된 오브젝트는 EndOverlap함수를 발생시키고 나서 소멸해야 하는데 dead처리되어있을 경우 그렇게 할 수 없다.<br/>

그래서 finaltick함수를 돌릴 때 dead 상태인지를 확인하고, dead상태일 경우 벡터에서 제외시켜줘야 한다.<br/>

이렇게 finaltick 함수를 변경시켜준다.<br/>
<br/>

### CCollisionMgr 클래스에서<br/>
<br/>

```
// CCollisionMgr.cpp파일 중에서

void CCollisionMgr::CollisionBtwObject(CGameObject* _LeftObject, CGameObject* _RightObject)
{
	if (!(_LeftObject->Collider2D() && _RightObject->Collider2D()))
		return;

	// 충돌체 ID 생성
	CollisionID id = {};
	id.LeftID = _LeftObject->Collider2D()->GetID();
	id.RightID = _RightObject->Collider2D()->GetID();
	
	// ID 검색
	map<UINT_PTR, bool>::iterator iter = m_mapColID.find(id.id);
	if (iter == m_mapColID.end())
	{
		m_mapColID.insert(make_pair(id.id, false));
		iter = m_mapColID.find(id.id);
	}

	// 둘 중 하나라도 삭제 예정 상태라면(Dead 상태)
	bool bDead = false;
	if (_LeftObject->IsDead() || _RightObject->IsDead())
	{
		bDead = true;
	}
	
	// 두 충돌체가 지금 충돌 중인지 확인
	if (CollisionBtwCollider(_LeftObject->Collider2D(), _RightObject->Collider2D()))
	{
		// 이전에 충돌한 적이 있고, 둘중 하나 이상이 삭제 예정이라면
		if (bDead && iter->second)
		{
			_LeftObject->Collider2D()->EndOverlap(_RightObject->Collider2D());
			_RightObject->Collider2D()->EndOverlap(_LeftObject->Collider2D());
		}
		else if (iter->second)
		{
			// 계속 충돌 중
			_LeftObject->Collider2D()->OnOverlap(_RightObject->Collider2D());
			_RightObject->Collider2D()->OnOverlap(_LeftObject->Collider2D());
		}
		else
		{
			// 이번 프레임에 충돌
			if (!bDead) // 둘중 하나라도 Dead 상태면 충돌을 무시한다.
			{
				_LeftObject->Collider2D()->BeginOverlap(_RightObject->Collider2D());
				_RightObject->Collider2D()->BeginOverlap(_LeftObject->Collider2D());
				iter->second = true;
			}			
		}
	}

	else
	{
		// 충돌 해제
		if (iter->second)
		{
			_LeftObject->Collider2D()->EndOverlap(_RightObject->Collider2D());
			_RightObject->Collider2D()->EndOverlap(_LeftObject->Collider2D());
			iter->second = false;
		}
	}


}

```

상대방이 이번 프레임에서 삭제 예정이라면 EndOverlap함수를 호출하도록 코드를 수정한다.<br/>