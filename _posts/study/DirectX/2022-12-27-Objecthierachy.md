---
layout: post
title:  "[DirectX] 오브젝트 계층구조"
subtitle:  
date: 2022-12-27 08:24:12 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] 오브젝트 계층구조<br/>
<br/>

객체 간에 형성된 부모와 자식관계를 가리킨다.<br/>
<br/>

## m_vRelativePos<br/>
<Br/>

```
Vec3    m_vRelativePos;
```

객체가 가지고 있는 좌표는 부모에 대해서 상대적이라는 것이다.<br/>

만약 부모가 없는 객체가 있다면 그의 좌표는 World 좌표 기준으로, 즉 절대 좌표계 기준으로 형성이 된다.<br/>

반면에 부모가 있는 객체는 부모를 따라서 좌표를 이동하게 된다.<br/>
<br/>

## m_vRelativeScale<br/>
<br/>

```
Vec3    m_vRelativeScale;
```

자식의 상대좌표가 부모의 배율에도 영향을 받는다.<br/>

예를 들어, 캐릭터가 부모이고, 이펙트가 자식일 때 캐릭터가 커지게 된다면 자식도 커지면서 캐릭터 - 이펙트간의 거리도 멀어져야 한다.<br/>

그렇게 해야 일정한 비율로 크기가 커진 것 처럼 보일 수 있다.<br/>

만약에 거리가 멀어지지 않으면 이펙트가 캐릭터의 몸속으로 파고 들어가는 것처럼 보일 것이다.<br/>
<Br/>


## CGameObject의 변화<br/>
<Br/>

```
// 헤더파일

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

#include "pch.h"
#include "CGameObject.h"

#include "CComponent.h"
#include "CMeshRender.h"

#include "CScript.h"

#include "CLevelMgr.h"
#include "CLevel.h"
#include "CLayer.h"


CGameObject::CGameObject()
	: m_arrCom{}
	, m_RenderCom(nullptr)
	, m_Parent(nullptr)
	, m_iLayerIdx(-1)
{
}

CGameObject::~CGameObject()
{
	Safe_Del_Array(m_arrCom);
	Safe_Del_Vec(m_vecScript);
	Safe_Del_Vec(m_vecChild);
}

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

void CGameObject::tick()
{
	for (UINT i = 0; i < (UINT)COMPONENT_TYPE::END; ++i)
	{
		if (nullptr != m_arrCom[i])
			m_arrCom[i]->tick();
	}

	for (size_t i = 0; i < m_vecScript.size(); ++i)
	{
		m_vecScript[i]->tick();
	}

	for (size_t i = 0; i < m_vecChild.size(); ++i)
	{
		m_vecChild[i]->tick();
	}
}

void CGameObject::finaltick()
{
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

void CGameObject::render()
{
	if (nullptr != m_RenderCom)
		m_RenderCom->render();
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

void CGameObject::AddChild(CGameObject* _Object)
{
	_Object->m_Parent = this;
	m_vecChild.push_back(_Object);
}

```

하나의 게임 오브젝트가 자식 게임오브젝트를 가질 수 있도록 코드를 구성한다.<br/>

m_Parent를 포인터를 통해서 가리키게 되고, 자식이 어떤 것이 있는지를 저장하는 벡터를 형성한다.<br/>

또한, AddChild를 통해 객체를 자식으로 추가할 수 있도록 할 것이다.<br/>

그리고 부모를 반환하는 함수도 추가한다.<br/>
<br/>

## tick함수와 render함수 호출 구성<br/>
<Br/>

자, 그럼 발생하는 문제점이 있다. 지금은 레이어 안에 오브젝트가 소속되는 개념이 있다.<br/>

그런데 레이어에서 tick을 진행할 때 부모->자식 순서대로 tick을 진행시키고 싶지만 이 코드 구성으로는 그럴 수 있다는 보장이 없다.<br/>

좌표를 계산해야 render와 tick을 진행할 수 있는데, 자식의 상대좌표는 부모를 기준으로 계산되기 때문에 부모->자식 순으로 순차적으로 tick이 진행되어야 한다.<br/>

이를 어떻게 조치할 수 있을까?<br/>

가장 상위인 부모 오브젝트만 Layer가 가지고 있으면 된다.<br/>

그리고 자식 오브젝트의 tick을 자식 오브젝트의 부모 오브젝트가 실행시키는 식으로 관리를 하면 된다.<br/>
<br/>

### CLayer의 변화<br/>
<Br/>

```
// 헤더 파일

vector<CGameObject*>    m_vecParentObj;
```

그래서 Layer 클래스에서 변수명을 m_vecObject에서 m_vecParentObj로 수정을 한다.<br/>
<br/>

### CGameObject의 변화<br/>
<br/>

자식 오브젝트의 begin함수, tick함수, finaltick함수를 부모 오브젝트가 실행시키는 식으로 구성한다.<br/>

마지막 자식일 떄 까지 이를 반복한다.<br/>

다만, render함수는 이를 따를 필요가 없다.<br/>

부모의 자식 관계는 형성이 되나 서로 다른 레이어에 소속되어 있는 경우가 있을 수 있다.<br/>

예를 들어, 캐릭터가 반투명한 방패를 들고 있을 경우. (반투명 물체는 렌더링 순서를 뒤로 미루기 때문에 다른 레이어에서 관리한다.)<br/>

그래서 렌더링은 계층구조에 따르기 보단 레이어 단위로 진행이 되어야 한다.<br/>

그리고 finaltick함수의 마지막에 자신이 어느 레이어 소속인지를 등록하는 과정을 거칠 것이다.<br/>
<br/>

### CLayer의 변화<br/>
<Br/>

그래서 레이어는 오브젝트를 두 종류로 관리하고 있어야 한다.<br/>

그리고 AddGameObject 함수에서 자식 오브젝트가 있는지, 소속 레이어가 있는지를 조사하는 과정을 거칠 것이다.<br/>


```
// 헤더 파일

vector<CGameObject*>    m_vecParentObj; // 부모 오브젝트
vector<CGameObject*>    m_vecObject; // 이 레이어에 속한 모든 오브젝트 
```

```
// cpp 파일

#include "pch.h"
#include "CLayer.h"

#include "CGameObject.h"

CLayer::CLayer()
{
}

CLayer::~CLayer()
{
	Safe_Del_Vec(m_vecParentObj);
}

void CLayer::begin()
{
	for (size_t i = 0; i < m_vecParentObj.size(); ++i)
	{
		m_vecParentObj[i]->begin();
	}
}

void CLayer::tick()
{
	for (size_t i = 0; i < m_vecParentObj.size(); ++i)
	{
		m_vecParentObj[i]->tick();
	}
}

void CLayer::finaltick()
{
	for (size_t i = 0; i < m_vecParentObj.size(); ++i)
	{
		m_vecParentObj[i]->finaltick();
	}
}

void CLayer::AddGameObject(CGameObject* _Object, bool _bMove)
{
	m_vecParentObj.push_back(_Object);
	
	// 소유하고 있는 모든 자식오브젝트가 있는지 검사
	static list<CGameObject*> queue;
	queue.clear();

	queue.push_back(_Object);

	while (!queue.empty())
	{
		CGameObject* pObject = queue.front();
		queue.pop_front();

		for (size_t i = 0; i < pObject->m_vecChild.size(); ++i)
		{
			queue.push_back(pObject->m_vecChild[i]);			
		}

		// 부모타입 or 소속 레이어가 없는경우 or 부모와 같이 이동하는 경우
		if(nullptr == pObject->m_Parent || -1 == pObject->m_iLayerIdx || _bMove)
			pObject->m_iLayerIdx = m_iLayerIdx;
	}	
}

```

이때, 계층 구조 순회를 하기위해서 BFS를 활용한다.<br/>

리스트를 통해서 큐를 만들고, 부모 오브젝트를 넣은 뒤 while문을 활용하는 방식으로 BFS 코드를 형성한다.<br/>

```
// 트리 구조를 순회하는 BFS 코드

void CLayer::AddGameObject(CGameObject* _Object, bool _bMove)
{
	m_vecParentObj.push_back(_Object);
	
	// 소유하고 있는 모든 자식오브젝트가 있는지 검사
	static list<CGameObject*> queue;
	queue.clear();

	queue.push_back(_Object);

	while (!queue.empty())
	{
		CGameObject* pObject = queue.front();
		queue.pop_front();

		for (size_t i = 0; i < pObject->m_vecChild.size(); ++i)
		{
			queue.push_back(pObject->m_vecChild[i]);			
		}

		// 부모타입 or 소속 레이어가 없는경우 or 부모와 같이 이동하는 경우
		if(nullptr == pObject->m_Parent || -1 == pObject->m_iLayerIdx || _bMove)
			pObject->m_iLayerIdx = m_iLayerIdx;
	}	
}

```

그리고 if문에서는 부모오브젝트일경우, 소속된 레이어가 없을 경우, 레이어를 옮겨야하는 경우에 걸리면 레이어 소속을 옮기도록 조정한다.<br/>
<br/>

## CTransform의 변화 (연산방식의 수정)<br/>
<br/>

연산 방식도 바꿔줘야 한다.<br/>

객체가 부모가 있는지를 검사하고, 부모의 좌표를 알아내며, 상대적인 좌표를 계산할 수 있도록 조치해야한다.<br/>

최종 m_matWorld를 구하는 방식을 바꾸어야 한다.<br/>

기존에는 크기, 회전, 이동 행렬 순으로 곱해서 곧 바로 World 행렬을 구했다.<br/>

최상위 부모라면 이것이 곧 최종 World 행렬이 되겠지만 자식은 부모의 좌표를 기준으로 구해야한다.<br/>

그 방법은 자신의 World행렬 뒤에 부모의 World행렬을 곱하는 것이다.<br/>

즉, 자식으로 갈 때마다 World 행렬을 누적시키면 된다.<br/>

```
// 헤더 파일

public:
    void SetRelativePos(Vec3 _vPos) { m_vRelativePos = _vPos; }
    void SetRelativeScale(Vec3 _vScale) { m_vRelativeScale = _vScale; }
    void SetRelativeRot(Vec3 _vRot) { m_vRelativeRot = _vRot; }

    void SetRelativePos(float _x, float _y, float _z) { m_vRelativePos = Vec3(_x, _y, _z); }
    void SetRelativeScale(float _x, float _y, float _z) { m_vRelativeScale = Vec3(_x, _y, _z); }
    void SetRelativeRot(float _x, float _y, float _z) { m_vRelativeRot = Vec3(_x, _y, _z);  }

    Vec3 GetRelativePos() const { return m_vRelativePos; }
    Vec3 GetRelativeScale() const { return m_vRelativeScale; }
    Vec3 GetRelativeRot() const { return m_vRelativeRot; }

    Vec3 GetRelativeDir(DIR_TYPE _type) const { return m_vRelativeDir[(UINT)_type]; }

    const Matrix& GetWorldMat() const { return m_matWorld; }

```

World 행렬을 반환하는 함수를 추가해주고

```
// cpp 파일
// 부모의 World 행렬 곱하기

void CTransform::finaltick()
{
	Matrix matScale = XMMatrixIdentity();	
	matScale = XMMatrixScaling(m_vRelativeScale.x, m_vRelativeScale.y, m_vRelativeScale.z);
	
	Matrix matRot = XMMatrixIdentity();
	matRot = XMMatrixRotationX(m_vRelativeRot.x);
	matRot *= XMMatrixRotationY(m_vRelativeRot.y);
	matRot *= XMMatrixRotationZ(m_vRelativeRot.z);

	Matrix matTranslation = XMMatrixTranslation(m_vRelativePos.x, m_vRelativePos.y, m_vRelativePos.z);

	
	m_matWorld = matScale * matRot * matTranslation;

	Vec3 vDefaultDir[3] = {
		  Vec3(1.f, 0.f, 0.f)
		, Vec3(0.f, 1.f, 0.f)
		, Vec3(0.f, 0.f, 1.f)
	};

	for (int i = 0; i < 3; ++i)
	{
		m_vRelativeDir[i] = XMVector3TransformNormal(vDefaultDir[i], matRot);
	}

	// 부모 오브젝트 확인
	CGameObject* pParent = GetOwner()->GetParent();
	if (pParent)
	{
		m_matWorld *= pParent->Transform()->m_matWorld;
	}
}
```

<br/>

### CLevelMgr의 변화<br/>
<br/>

한편, 부모와 자식 클래스를 레벨에 추가하는 CLevelMgr의 init함수에서는 자식이 좌표가 더 이상 절대적인 좌표가 아니라 부모에 대한 상대적인 비율로 따라가므로 코드를 다음과 같이 수정해준다.<br/>

```

void CLevelMgr::init()
{
	m_pCurLevel = new CLevel;

	// Main Camera Object 생성
	CGameObject* pMainCam = new CGameObject;
	pMainCam->SetName(L"MainCamera");

	pMainCam->AddComponent(new CTransform);
	pMainCam->AddComponent(new CCamera);
	pMainCam->AddComponent(new CCameraMoveScript);
	
	pMainCam->Camera()->SetProjType(PROJ_TYPE::ORTHOGRAPHIC);
	pMainCam->Camera()->SetCameraIndex(0);		// MainCamera 로 설정
	pMainCam->Camera()->SetLayerMaskAll(true);	// 모든 레이어 체크

	m_pCurLevel->AddGameObject(pMainCam, 0, false);


	// 오브젝트 생성
	CGameObject* pParent = new CGameObject;
	pParent->SetName(L"Parent Object");
	pParent->AddComponent(new CTransform);
	pParent->AddComponent(new CMeshRender);
	pParent->AddComponent(new CPlayerScript);
	//pParent->AddComponent(new CPlayerScript);

	Ptr<CMesh> pMesh = CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh");
	Ptr<CMaterial> Std2DMtrl = CResMgr::GetInst()->FindRes<CMaterial>(L"Std2DMtrl");

	Ptr<CTexture> PlayerTex = CResMgr::GetInst()->FindRes<CTexture>(L"CharacterTex");
	Std2DMtrl->SetTexParam(TEX_0, PlayerTex);

	pParent->Transform()->SetRelativePos(Vec3(0.f, 0.f, 500.f));
	pParent->Transform()->SetRelativeScale(Vec3(200.f, 200.f, 1.f));

	pParent->MeshRender()->SetMesh(pMesh);
	pParent->MeshRender()->SetMaterial(Std2DMtrl);

	// Child Object
	CGameObject* pChild = new CGameObject;
	pChild->SetName(L"Child Object");
	pChild->AddComponent(new CTransform);
	pChild->AddComponent(new CMeshRender);

	pChild->Transform()->SetRelativePos(Vec3(2.f, 0.f, 0.f));
	pChild->Transform()->SetRelativeScale(Vec3(1.f, 1.f, 1.f));

	pChild->MeshRender()->SetMesh(CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh"));
	pChild->MeshRender()->SetMaterial(CResMgr::GetInst()->FindRes<CMaterial>(L"TestMtrl"));


	pParent->AddChild(pChild);
	m_pCurLevel->AddGameObject(pParent, 0, false);
}
```

그리고 중복 등록으로 인한 프레임 드랍을 방지하기 위해서 tick 함수에서 오브젝트들의 tick, finaltick함수를 돌리기에 앞서 clear를 진행한다.<br/>

```
void CLevelMgr::tick()
{
	m_pCurLevel->clear();

	m_pCurLevel->tick();
	m_pCurLevel->finaltick();
}
```

## Absolute 옵션<br/>
<br/>

부모 오브젝트가 회전을 하면 자식 오브젝트도 회전을 하는데 자식 오브젝트는 형태는 회전해도, 좌표계는 회전을 하지 않고 있어야 한다.<br/>

### CTransform 클래스<br/>
<Br/>

이를 위해 CTransform 클래스를 아래와 같이 다시 구성해준다.<br/>

```
// 헤더파일

#pragma once
#include "CComponent.h"

class CTransform :
    public CComponent
{
private:
    Vec3    m_vRelativePos;
    Vec3    m_vRelativeScale;
    Vec3    m_vRelativeRot;

    bool    m_bAbsolute;    // 상대 이동, 크기를 절대값으로 지정    

    Vec3    m_vRelativeDir[3];
    Vec3    m_vWorldDir[3];

    Matrix  m_matWorldScale;    // 월드 크기 행렬
    Matrix  m_matWorld; // 크기, 회전, 이동 정보를 합쳐놓음


public:
    void SetRelativePos(Vec3 _vPos) { m_vRelativePos = _vPos; }
    void SetRelativeScale(Vec3 _vScale) { m_vRelativeScale = _vScale; }
    void SetRelativeRot(Vec3 _vRot) { m_vRelativeRot = _vRot; }

    void SetRelativePos(float _x, float _y, float _z) { m_vRelativePos = Vec3(_x, _y, _z); }
    void SetRelativeScale(float _x, float _y, float _z) { m_vRelativeScale = Vec3(_x, _y, _z); }
    void SetRelativeRot(float _x, float _y, float _z) { m_vRelativeRot = Vec3(_x, _y, _z);  }

    // 상대 이동, 크기를 절대값으로 지정  
    void SetAbsolute(bool _Set) { m_bAbsolute = _Set; }    

    Vec3 GetRelativePos() const { return m_vRelativePos; }
    Vec3 GetRelativeScale() const { return m_vRelativeScale; }
    Vec3 GetRelativeRot() const { return m_vRelativeRot; }

    Vec3 GetRelativeDir(DIR_TYPE _type) const { return m_vRelativeDir[(UINT)_type]; }
    Vec3 GetWorldDir(DIR_TYPE _type) const { { return m_vWorldDir[(UINT)_type]; } }
    Vec3 GetWorldPos() { return m_matWorld.Translation(); }

    const Matrix& GetWorldScaleMat() { return m_matWorldScale; }
    const Matrix& GetWorldMat() const { return m_matWorld; }

public:
    virtual void finaltick() override;    
    void UpdateData();

    CLONE(CTransform);
public:
    CTransform();
    ~CTransform();
};
```

```
// cpp 파일

#include "pch.h"
#include "CTransform.h"

#include "CDevice.h"
#include "CConstBuffer.h"

CTransform::CTransform()
	: CComponent(COMPONENT_TYPE::TRANSFORM)
	, m_vRelativeScale(Vec3(1.f, 1.f, 1.f))
	, m_bAbsolute(false)	
	, m_vRelativeDir{
		  Vec3(1.f, 0.f, 0.f)
		, Vec3(0.f, 1.f, 0.f)
		, Vec3(0.f, 0.f, 1.f)}	
{
}

CTransform::~CTransform()
{
}

void CTransform::finaltick()
{
	m_matWorldScale = XMMatrixIdentity();
	m_matWorldScale = XMMatrixScaling(m_vRelativeScale.x, m_vRelativeScale.y, m_vRelativeScale.z);
	
	Matrix matRot = XMMatrixIdentity();
	matRot = XMMatrixRotationX(m_vRelativeRot.x);
	matRot *= XMMatrixRotationY(m_vRelativeRot.y);
	matRot *= XMMatrixRotationZ(m_vRelativeRot.z);

	Matrix matTranslation = XMMatrixTranslation(m_vRelativePos.x, m_vRelativePos.y, m_vRelativePos.z);

	
	m_matWorld = m_matWorldScale * matRot * matTranslation;

	Vec3 vDefaultDir[3] = {
		  Vec3(1.f, 0.f, 0.f)
		, Vec3(0.f, 1.f, 0.f)
		, Vec3(0.f, 0.f, 1.f)
	};

	for (int i = 0; i < 3; ++i)
	{
		m_vWorldDir[i] = m_vRelativeDir[i] = XMVector3TransformNormal(vDefaultDir[i], matRot);
	}

	// 부모 오브젝트 확인
	CGameObject* pParent = GetOwner()->GetParent();
	if (pParent)
	{
		if (m_bAbsolute)
		{
			Matrix matParentWorld = pParent->Transform()->m_matWorld;
			Matrix matParentScale = pParent->Transform()->m_matWorldScale;
			Matrix matParentScaleInv = XMMatrixInverse(nullptr, matParentScale);

			// 월드 = 로컬월드 * 부모크기 역 * 부모 월드(크기/회전/이동)
			m_matWorld = m_matWorld * matParentScaleInv * matParentWorld;
		}
		else
		{
			m_matWorldScale = pParent->Transform()->m_matWorldScale;
			m_matWorld *= pParent->Transform()->m_matWorld;
		}
		

		for (int i = 0; i < 3; ++i)
		{
			m_vWorldDir[i] = XMVector3TransformNormal(vDefaultDir[i], m_matWorld);
			m_vWorldDir[i].Normalize();
		}
	}
}

void CTransform::UpdateData()
{
	// 위치값을 상수버퍼에 전달 및 바인딩		
	CConstBuffer* pTransformBuffer = CDevice::GetInst()->GetConstBuffer(CB_TYPE::TRANSFORM);

	g_transform.matWorld = m_matWorld;
	g_transform.matWV = g_transform.matWorld * g_transform.matView;
	g_transform.matWVP = g_transform.matWV * g_transform.matProj;


	pTransformBuffer->SetData(&g_transform);
	pTransformBuffer->UpdateData();
}


```


부모 오브젝트가 있을 경우 world방향만 다시 계산해서 진짜 방향을 알아낸 다음에 정규화(Normalize)를 해준다.<br/>

그리고 카메라는 View 행렬을 계산할 때 WorldDirection을 가져올 수 있어야 한다.<br/>

한편, m_bAbsolute 옵션의 역할은 부모의 크기에 따른 상대 좌표값을 적용할 것인지, 아니면 그냥 절대 좌표값을 적용할 것인지를 결정하는 역할이다.<br/>

m_bAbsolute 옵션이 켜져있다면 부모 행렬을 갖고 오며, 부모 파트의 크기 행렬의 역행렬을 끼워넣는다.<br/>

이렇게 해서 크기 행렬과 크기 행렬의 역행렬을 곱해서 단위 행렬을 만들어낸다.<br/>

단, 현재 자식 오브젝트의 부모가 최상위 부모의 몇 번째 자식인지 알지를 못한다. <br/>

그래서 월드 크기 행렬만을 따로 누적해놓은 변수를 새로 추가해준다.<br/>

m_matWorldScale 변수가 그 기능을 수행한다.<br/>

그리고 역행렬은 XMMatrixInverse함수를 통해서 구해줄 수 있다.<br/>

이 함수로 부모의 크기 행렬의 역행렬을 구한다.<br/>
<br/>

### CLevelMgr 클래스<br/>
<Br/>

그리고 Absolute 옵션을 켜준 상태를 LevelMgr에서 표현할 때 init함수는 아래와 같은 코드로 표현해준다.<br/>

```
// cpp파일 init함수 중에서

void CLevelMgr::init()
{
	m_pCurLevel = new CLevel;

	// Layer 이름설정
	m_pCurLevel->GetLayer(0)->SetName(L"Default");
	m_pCurLevel->GetLayer(1)->SetName(L"Player");
	m_pCurLevel->GetLayer(2)->SetName(L"Monster");
	m_pCurLevel->GetLayer(3)->SetName(L"PlayerProjectile");
	m_pCurLevel->GetLayer(4)->SetName(L"MonsterProjectile");


	// Main Camera Object 생성
	CGameObject* pMainCam = new CGameObject;
	pMainCam->SetName(L"MainCamera");

	pMainCam->AddComponent(new CTransform);
	pMainCam->AddComponent(new CCamera);
	pMainCam->AddComponent(new CCameraMoveScript);
	
	pMainCam->Camera()->SetProjType(PROJ_TYPE::ORTHOGRAPHIC);
	pMainCam->Camera()->SetCameraIndex(0);		// MainCamera 로 설정
	pMainCam->Camera()->SetLayerMaskAll(true);	// 모든 레이어 체크

	m_pCurLevel->AddGameObject(pMainCam, 0, false);


	// 오브젝트 생성
	CGameObject* pParent = new CGameObject;
	pParent->SetName(L"Parent Object");
	pParent->AddComponent(new CTransform);
	pParent->AddComponent(new CMeshRender);
	pParent->AddComponent(new CPlayerScript);
	//pParent->AddComponent(new CPlayerScript);

	Ptr<CMesh> pMesh = CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh");
	Ptr<CMaterial> Std2DMtrl = CResMgr::GetInst()->FindRes<CMaterial>(L"Std2DMtrl");	
	Ptr<CTexture> PlayerTex = CResMgr::GetInst()->FindRes<CTexture>(L"CharacterTex");
	Std2DMtrl->SetTexParam(TEX_0, PlayerTex);

	pParent->Transform()->SetRelativePos(Vec3(0.f, 0.f, 500.f));
	pParent->Transform()->SetRelativeScale(Vec3(200.f, 200.f, 1.f));

	pParent->MeshRender()->SetMesh(pMesh);
	pParent->MeshRender()->SetMaterial(Std2DMtrl);

	// Child Object
	CGameObject* pChild = new CGameObject;
	pChild->SetName(L"Child Object");
	pChild->AddComponent(new CTransform);
	pChild->AddComponent(new CMeshRender);

	pChild->Transform()->SetAbsolute(true);
	pChild->Transform()->SetRelativePos(Vec3(400.f, 0.f, 0.f));
	pChild->Transform()->SetRelativeScale(Vec3(200.f, 200.f, 1.f));

	pChild->MeshRender()->SetMesh(CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh"));
	pChild->MeshRender()->SetMaterial(CResMgr::GetInst()->FindRes<CMaterial>(L"TestMtrl"));

	pParent->AddChild(pChild);
	m_pCurLevel->AddGameObject(pParent, 0, false);
}
```