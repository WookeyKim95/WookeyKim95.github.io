---
layout: post
title:  "[DirectX] Render Manager"
subtitle:  
date: 2022-12-22 09:31:52 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] Render Manager<br/>
<br/>

렌더링의 기준을 카메라로 설정하며 더이상 레벨 매니저에서는 렌더링이 진행되지 않는다.<br/>

레벨 매니저에서는 tick() 함수만 돌아가고 render를 전담하는 매니저 객체를 만들 것이다.<br/>

카메라를 렌더매니저에 등록해서 사용하는 방식으로 사용할 것이다.<br/>
<br/>

## CRenderMgr 클래스<br/>
<br/>

```
// 헤더파일

#pragma once
#include "CSingleton.h"

class CCamera;

class CRenderMgr :
    public CSingleton<CRenderMgr>
{
    SINGLE(CRenderMgr);
private:
    vector<CCamera*>    m_vecCam;
    

public:
    void init();
    void render();



public:
    int RegisterCamera(CCamera* _Cam, int _idx);

    CCamera* GetMainCam() 
    { 
        if (m_vecCam.empty())
            return nullptr;

        return m_vecCam[0];
    }
};

```

```
// cpp파일

#include "pch.h"
#include "CRenderMgr.h"

#include "CCamera.h"

CRenderMgr::CRenderMgr()
{

}

CRenderMgr::~CRenderMgr()
{

}


void CRenderMgr::init()
{
}

void CRenderMgr::render()
{
    for (size_t i = 0; i < m_vecCam.size(); ++i)
    {
        if (nullptr == m_vecCam[i])
            continue;

        m_vecCam[i]->SortObject();
        m_vecCam[i]->render();
    }    
}

int CRenderMgr::RegisterCamera(CCamera* _Cam, int _idx)
{
    if (m_vecCam.size() <= _idx)
    {
        m_vecCam.resize(_idx + 1);
    }

    m_vecCam[_idx] = _Cam;    
    return _idx;
}
```

RegisterCamera 함수를 통해서 카메라를 렌더 매니저에 등록한다.<br/>

그리고 render함수에서 카메라가 여러 대일 수 있으니 반복문을 돌면서 카메라 별로 렌더링을 진행시킨다.<br/>

이때, 오브젝트의 사용 재질에 따라 도메인에 맞게 분류를 진행할 것이다.<br/>

그리고 분류가 완료된 뒤에 카메라 별 렌더링을 진행한다.<br/>

이때 물체를 분류하는 역할을 하는 함수가 카메라 클래스의 SortObject()이다.<br/>
<br/>

## CCamera 클래스<br/>
<br/>

```
// 헤더파일
private:
    int           m_iCamIdx;

private:
    void CalcViewMat(); // 뷰행렬 계산
    void CalcProjMat(); // 투영행렬 계산

    vector<CGameObject*>    m_vecOpaque;
    vector<CGameObject*>    m_vecMask;
    vector<CGameObject*>    m_vecTransparent;
    vector<CGameObject*>    m_vecPost;
    vector<CGameObject*>    m_vecUI;
```

```
// cpp파일 코드 추가

void CCamera::SetLayerMask(int _iLayer, bool _Visible)
{
	if (_Visible)
	{
		m_iLayerMask |= 1 << _iLayer;
	}
	else
	{
		m_iLayerMask &= ~(1 << _iLayer);
	}
}

void CCamera::SetLayerMaskAll(bool _Visible)
{
	if (_Visible)
		m_iLayerMask = 0xffffffff;
	else
		m_iLayerMask = 0;
}

void CCamera::SetCameraIndex(int _idx)
{
	m_iCamIdx = _idx;
	CRenderMgr::GetInst()->RegisterCamera(this, m_iCamIdx);
}

void CCamera::SortObject()
{
	// 이전 프레임 분류정보 제거
	clear();

	// 현재 레벨 가져와서 분류
	CLevel* pCurLevel = CLevelMgr::GetInst()->GetCurLevel();

	for (UINT i = 0; i < MAX_LAYER; ++i)
	{
		// 레이어 마스크 확인
		if (m_iLayerMask & (1 << i))
		{
			CLayer* pLayer = pCurLevel->GetLayer(i);
			const vector<CGameObject*>& vecObject = pLayer->GetObjects();

			for (size_t j = 0; j < vecObject.size(); ++j)
			{
				CRenderComponent* pRenderCom = vecObject[j]->GetRenderComponent();

				// 렌더링 기능이 없는 오브젝트는 제외
				if (   nullptr == pRenderCom 
					|| nullptr == pRenderCom->GetMaterial()
					|| nullptr == pRenderCom->GetMaterial()->GetShader())
					continue;

				// 쉐이더 도메인에 따른 분류
				SHADER_DOMAIN eDomain = pRenderCom->GetMaterial()->GetShader()->GetDomain();
				switch (eDomain)
				{
				case SHADER_DOMAIN::DOMAIN_OPAQUE:
					m_vecOpaque.push_back(vecObject[j]);
					break;
				case SHADER_DOMAIN::DOMAIN_MASK:
					m_vecMask.push_back(vecObject[j]);
					break;
				case SHADER_DOMAIN::DOMAIN_TRANSPARENT:
					m_vecTransparent.push_back(vecObject[j]);
					break;
				case SHADER_DOMAIN::DOMAIN_POSTPROCESS:
					m_vecPost.push_back(vecObject[j]);
					break;
				case SHADER_DOMAIN::DOMAIN_UI:
					m_vecUI.push_back(vecObject[j]);
					break;				
				}
			}
		}
	}
}

void CCamera::render()
{
	// 행렬 업데이트
	g_transform.matView = m_matView;
	g_transform.matProj = m_matProj;

	// 쉐이더 도메인에 따라서 순차적으로 그리기
	render_opaque();
	render_mask();
	render_transparent();
	render_postprocess();
	render_ui();
}



void CCamera::clear()
{
	m_vecOpaque.clear();
	m_vecMask.clear();
	m_vecTransparent.clear();
	m_vecPost.clear();
	m_vecUI.clear();
}

void CCamera::render_opaque()
{
	for (size_t i = 0; i < m_vecOpaque.size(); ++i)
	{
		m_vecOpaque[i]->render();
	}
}

void CCamera::render_mask()
{
	for (size_t i = 0; i < m_vecMask.size(); ++i)
	{
		m_vecMask[i]->render();
	}
}

void CCamera::render_transparent()
{
	for (size_t i = 0; i < m_vecTransparent.size(); ++i)
	{
		m_vecTransparent[i]->render();
	}
}

void CCamera::render_postprocess()
{
	for (size_t i = 0; i < m_vecPost.size(); ++i)
	{
		m_vecPost[i]->render();
	}
}

void CCamera::render_ui()
{
	for (size_t i = 0; i < m_vecUI.size(); ++i)
	{
		m_vecUI[i]->render();
	}
}
```

카메라 우선순위, 몇번째 카메라인지를 나타내는 변수를 추가해준다.<br/>

그리고 뷰행렬과 투영행렬을 계산하는 코드를 다른 함수로 분리시켜준다.<br/>

또한, RenderMgr도 참조시킨다.<br/>

SetCameraIdx()함수로 카메라를 몇번 카메라로 설정해준다.<br/>

도메인 별로 렌더 함수를 다르게 설정해주고, render함수 내에서 순차별로 렌더링을 진행한다.<br/>

그리고 렌더링 하기 전에 행렬 업데이트를 실시하도록 변경한다.<br/>

그리고 각 도메인 별 render함수에서 반복문을 돌면서 렌더링을 진행하도록 코드를 작성한다.<br/>
<br/>

### SortObject 함수<br/>
<br/>

```

void CCamera::SortObject()
{
	// 이전 프레임 분류정보 제거
	clear();

	// 현재 레벨 가져와서 분류
	CLevel* pCurLevel = CLevelMgr::GetInst()->GetCurLevel();

	for (UINT i = 0; i < MAX_LAYER; ++i)
	{
		// 레이어 마스크 확인
		if (m_iLayerMask & (1 << i))
		{
			CLayer* pLayer = pCurLevel->GetLayer(i);
			const vector<CGameObject*>& vecObject = pLayer->GetObjects();

			for (size_t j = 0; j < vecObject.size(); ++j)
			{
				CRenderComponent* pRenderCom = vecObject[j]->GetRenderComponent();

				// 렌더링 기능이 없는 오브젝트는 제외
				if (   nullptr == pRenderCom 
					|| nullptr == pRenderCom->GetMaterial()
					|| nullptr == pRenderCom->GetMaterial()->GetShader())
					continue;

				// 쉐이더 도메인에 따른 분류
				SHADER_DOMAIN eDomain = pRenderCom->GetMaterial()->GetShader()->GetDomain();
				switch (eDomain)
				{
				case SHADER_DOMAIN::DOMAIN_OPAQUE:
					m_vecOpaque.push_back(vecObject[j]);
					break;
				case SHADER_DOMAIN::DOMAIN_MASK:
					m_vecMask.push_back(vecObject[j]);
					break;
				case SHADER_DOMAIN::DOMAIN_TRANSPARENT:
					m_vecTransparent.push_back(vecObject[j]);
					break;
				case SHADER_DOMAIN::DOMAIN_POSTPROCESS:
					m_vecPost.push_back(vecObject[j]);
					break;
				case SHADER_DOMAIN::DOMAIN_UI:
					m_vecUI.push_back(vecObject[j]);
					break;				
				}
			}
		}
	}
}

```

레벨매니저, 레벨클래스, 레이어, 게임오브젝트, 재질, 그래픽스 쉐이더 등을 참조하여 분류 목록을 받아온다.<br/>

렌더링 순서를 위해서 도메인별 분류 작업을 진행한다.<br/>

그리고 렌더에서 제외할 것을 분류하는 작업도 필요하다.<br/>

그래서 카메라에서 찍고 싶은 레이어만 찍을 수 있도록 후술할 함수를 통해 레이어 마스킹을 진행할 것이다.<br/>

그리고 반복문 속에 마스킹이 된 레이어만 찍을 수 있도록 조건문을 달아놓는다.<br/> 

실제로 찍는 레이어라면 레벨에서 해당 레이어를 얻어온뒤, 레이어에 있는 오브젝트를 가져온다.<br/>

이때 반환된 것은 오브젝트의 포인터들이 담긴 벡터이므로 얻어온 벡터를 순회한다.<br/>

오브젝트들의 렌더컴포넌트들을 확인하면서 재질, 쉐이더를 확인하며 렌더링을 진행한다.<br/>

렌더컴포넌트의 참조하는 쉐이더를 가져와서 도메인을 확인해보고, swtich문에서 해당하는 도메인에 따라서 해당 타입에 맞는 오브젝트를 분류한다.<br/>
 
<br/>

### SetLayerMask 함수<br/>
<br/>

```
void CCamera::SetLayerMask(int _iLayer, bool _Visible)
{
	if (_Visible)
	{
		m_iLayerMask |= 1 << _iLayer;
	}
	else
	{
		m_iLayerMask &= ~(1 << _iLayer);
	}
}

void CCamera::SetLayerMaskAll(bool _Visible)
{
	if (_Visible)
		m_iLayerMask = 0xffffffff;
	else
		m_iLayerMask = 0;
}
```

비트 연산을 통해서 Visible로 설정된 레이어에 비트를 1로 넣어주고, 그렇지 않으면 빼는 함수이다.

만약 모든 레이어를 찍거나 그렇지 않는 카메라라면 SetLayerMaskAll 함수가 작동되어 모든 비트에다가 1을 넣어주는 과정을 진행한다.<br/>


<br/>

## CLevelMgr 클래스<br/>

```
// cpp파일

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

	m_pCurLevel->AddGameObject(pMainCam, 0);



	// 오브젝트 생성
	CGameObject* pObj = new CGameObject;
	pObj->SetName(L"Player");
	pObj->AddComponent(new CTransform);
	pObj->AddComponent(new CMeshRender);
	pObj->AddComponent(new CPlayerScript);

	Ptr<CMesh> pMesh = CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh");
	Ptr<CMaterial> Std2DMtrl = CResMgr::GetInst()->FindRes<CMaterial>(L"Std2DMtrl");

	Ptr<CTexture> PlayerTex = CResMgr::GetInst()->FindRes<CTexture>(L"CharacterTex");
	Std2DMtrl->SetTexParam(TEX_0, PlayerTex);

	pObj->Transform()->SetRelativePos(Vec3(0.f, 0.f, 500.f));
	pObj->Transform()->SetRelativeScale(Vec3(200.f, 200.f, 1.f));

	pObj->MeshRender()->SetMesh(pMesh);
	pObj->MeshRender()->SetMaterial(Std2DMtrl);

	m_pCurLevel->AddGameObject(pObj, 1);

	// Test Object
	pObj = new CGameObject;
	pObj->SetName(L"Test Object");
	pObj->AddComponent(new CTransform);
	pObj->AddComponent(new CMeshRender);	

	pObj->Transform()->SetRelativePos(Vec3(200.f, 0.f, 200.f));
	pObj->Transform()->SetRelativeScale(Vec3(200.f, 200.f, 1.f));

	pObj->MeshRender()->SetMesh(CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh"));
	pObj->MeshRender()->SetMaterial(CResMgr::GetInst()->FindRes<CMaterial>(L"TestMtrl"));

	m_pCurLevel->AddGameObject(pObj, 0);
}


```

init()함수에서 카메라를 등록하는 과정을 진행한다.<br/>
<br/>

## CEngine 클래스<br/>

```
// cpp 파일

int CEngine::init(HWND _hWnd, UINT _iWidth, UINT _iHeight)
{
	// 메인 윈도우 핸들
	m_hWnd = _hWnd;
	m_vResolution = Vec2((float)_iWidth, (float)_iHeight);

	// 해상도에 맞는 작업영역 크기 조정
	RECT rt = { 0, 0, (int)_iWidth, (int)_iHeight};
	AdjustWindowRect(&rt, WS_OVERLAPPEDWINDOW, false);
	SetWindowPos(m_hWnd, nullptr, 10, 10, rt.right - rt.left, rt.bottom - rt.top, 0);
	ShowWindow(m_hWnd, true);


	// Device 초기화
	if (FAILED(CDevice::GetInst()->init(m_hWnd, _iWidth, _iHeight)))
	{
		MessageBox(nullptr, L"Device 초기화 실패", L"에러", MB_OK);
		return E_FAIL;
	}


	// Manager 초기화
	CPathMgr::GetInst()->init();
	CKeyMgr::GetInst()->init();
	CTimeMgr::GetInst()->init();
	CResMgr::GetInst()->init();
	CRenderMgr::GetInst()->init();
	CLevelMgr::GetInst()->init();		

	return S_OK;
}

void CEngine::progress()
{
	tick();

	render();
}

void CEngine::tick()
{
	// Manager Tick
	CTimeMgr::GetInst()->tick();
	CKeyMgr::GetInst()->tick();	

	CLevelMgr::GetInst()->tick();
}

void CEngine::render()
{
	CTimeMgr::GetInst()->render();

	// 렌더링 시작
	float arrColor[4] = { 0.4f, 0.4f, 0.4f, 1.f };
	CDevice::GetInst()->ClearTarget(arrColor);


	CRenderMgr::GetInst()->render();


	// 렌더 종료
	CDevice::GetInst()->Present();
}

```

엔진에서 렌더매니저를 init하며, 렌더는 이제 렌더매니저에서 진행하도록 수정한다.<br/>
<br/>

## CRenderComponent 클래스<br/>
<Br/>

```
// 헤더파일

#pragma once
#include "CComponent.h"


#include "CMesh.h"
#include "CMaterial.h"
#include "ptr.h"

class CRenderComponent :
    public CComponent
{
private:
    Ptr<CMesh>              m_pMesh;
    Ptr<CMaterial>          m_pMtrl;

public:
    virtual void render() = 0;


public:
    void SetMesh(Ptr<CMesh> _Mesh) { m_pMesh = _Mesh; }
    void SetMaterial(Ptr<CMaterial> _Mtrl) { m_pMtrl = _Mtrl; }

    Ptr<CMesh> GetMesh() { return m_pMesh; }
    Ptr<CMaterial> GetMaterial() { return m_pMtrl; }


public:
    CRenderComponent(COMPONENT_TYPE _type);
    ~CRenderComponent();
};


```

```
// cpp파일

#include "pch.h"
#include "CRenderComponent.h"

CRenderComponent::CRenderComponent(COMPONENT_TYPE _type)
	: CComponent(_type)
{
}

CRenderComponent::~CRenderComponent()
{
}

```

컴포넌트를 상속받은 렌더컴포넌트 클래스를 설정해주고, 다시 메쉬렌더, 파티클시스템, 타일 맵 등이 렌더컴포넌트를 상속받는다.<br/>
<br/>

### CMeshRender 클래스<br/>
<Br/>

```
// 헤더파일

#pragma once
#include "CRenderComponent.h"


class CMeshRender :
    public CRenderComponent
{
public:
    virtual void finaltick() override;
    virtual void render() override;

    CLONE(CMeshRender);
public:
    CMeshRender();
    ~CMeshRender();
};


```

```
// cpp파일

#include "pch.h"
#include "CMeshRender.h"

#include "CTransform.h"

CMeshRender::CMeshRender()
	: CRenderComponent(COMPONENT_TYPE::MESHRENDER)		
{
}

CMeshRender::~CMeshRender()
{
}

void CMeshRender::finaltick()
{
}

void CMeshRender::render()
{	
	if (nullptr == GetMesh() || nullptr == GetMaterial())
		return;

	// Transform 에 UpdateData 요청
	Transform()->UpdateData();

	// 재질 업데이트
	GetMaterial()->UpdateData();

	// 렌더
	GetMesh()->render();
}
```
<br/>

### CGameObject 클래스<br/>
<br/>

```
// 헤더파일

#pragma once
#include "CEntity.h"


class CComponent;
class CTransform;
class CMeshRender;
class CCamera;
class CRenderComponent;

class CGameObject :
    public CEntity
{
private:
    CComponent*         m_arrCom[(UINT)COMPONENT_TYPE::END];
    CRenderComponent*   m_RenderCom;


public:
    void tick();
    void finaltick();


    void render();


public:
    void AddComponent(CComponent* _Component);

    CTransform* Transform() { return (CTransform*)m_arrCom[(UINT)COMPONENT_TYPE::TRANSFORM]; }
    CMeshRender* MeshRender() { return (CMeshRender*)m_arrCom[(UINT)COMPONENT_TYPE::MESHRENDER]; }
    CCamera* Camera() { return (CCamera*)m_arrCom[(UINT)COMPONENT_TYPE::CAMERA]; }

    CRenderComponent* GetRenderComponent() {  return m_RenderCom; }


    CLONE(CGameObject)    
public:
    CGameObject();
    ~CGameObject();

};



```

```
// cpp파일

#include "pch.h"
#include "CGameObject.h"

#include "CComponent.h"
#include "CMeshRender.h"


CGameObject::CGameObject()
	: m_arrCom{}
	, m_RenderCom(nullptr)
{
}

CGameObject::~CGameObject()
{
	/*for (UINT i = 0; i < (UINT)COMPONENT_TYPE::END; ++i)
	{
		if (nullptr != m_arrCom[i])
			delete m_arrCom[i];
	}*/

	Safe_Del_Array(m_arrCom);
}

void CGameObject::tick()
{
	for (UINT i = 0; i < (UINT)COMPONENT_TYPE::END; ++i)
	{
		if (nullptr != m_arrCom[i])
			m_arrCom[i]->tick();
	}
}

void CGameObject::finaltick()
{
	for (UINT i = 0; i < (UINT)COMPONENT_TYPE::SCRIPT; ++i)
	{
		if (nullptr != m_arrCom[i])
			m_arrCom[i]->finaltick();
	}
}

void CGameObject::render()
{
	if (nullptr != m_RenderCom)
		m_RenderCom->render();
}

void CGameObject::AddComponent(CComponent* _Component)
{
	// 이미 보유하고 있는 컴포넌트인 경우
	assert(!m_arrCom[(UINT)_Component->GetType()]);

	_Component->m_pOwner = this;
	m_arrCom[(UINT)_Component->GetType()] = _Component;

	// RenderComponent 확인
	if (COMPONENT_TYPE::MESHRENDER <= _Component->GetType() 
		&& _Component->GetType() <= COMPONENT_TYPE::DECAL)
	{
		assert(!m_RenderCom);
		m_RenderCom = (CRenderComponent*)_Component;
	}
}

```

오브젝트 당 렌더 컴포넌트 포인터는 1개만 등록할 수 있도록 조치한다.<br/>
<br/>

## 카메라 확대 및 축소<br/>
<br/>

### CCamera클래스<br/>
<Br/>

```
// 헤더파일
private:
    float       m_fScale;

public:
    float GetScale() { return m_fScale; }
    void SetScale(float _fScale) { m_fScale = _fScale; }
```

```
// cpp파일

void CCamera::CalcProjMat()
{
	// =============
	// 투영 행렬 계산
	// =============
	m_matProj = XMMatrixIdentity();
	
	if (PROJ_TYPE::ORTHOGRAPHIC == m_ProjType)
	{
		// 직교 투영
		Vec2 vResolution = CDevice::GetInst()->GetRenderResolution();
		m_matProj =  XMMatrixOrthographicLH(vResolution.x * (1.f / m_fScale), vResolution.y * (1.f / m_fScale), 1.f, 10000.f);
	}
	else
	{	
		// 원근 투영
		m_matProj = XMMatrixPerspectiveFovLH(XM_PI / 2.f, m_fAspectRatio, 1.f, 10000.f);
	}

}

```
카메라 클래스에 m_fScale변수와 SetScale, GetScale 함수를 추가한다.<br/>

그리고 직교투영 함수에서 해당 변수를 위 코드와 같이 사용한다.<br/>
<br/>

### CCameraMoveScript 클래스<br/>
<br/>

```
void CCameraMoveScript::Camera2DMove()
{
	// 키 입력에 따른 카메라 이동
	Vec3 vPos = Transform()->GetRelativePos();

	if (KEY_PRESSED(KEY::W))
	{
		vPos.y += DT * m_fCamSpeed;
	}

	if (KEY_PRESSED(KEY::S))
	{
		vPos.y -= DT * m_fCamSpeed;
	}

	if (KEY_PRESSED(KEY::A))
	{
		vPos.x -= DT * m_fCamSpeed;
	}

	if (KEY_PRESSED(KEY::D))
	{
		vPos.x += DT * m_fCamSpeed;
	}


	if (KEY_PRESSED(KEY::_1))
	{
		float fScale = Camera()->GetScale();
		fScale += DT * 1.f;
		Camera()->SetScale(fScale);
	}

	if (KEY_PRESSED(KEY::_2))
	{
		float fScale = Camera()->GetScale();
		fScale -= DT * 1.f;
		Camera()->SetScale(fScale);
	}

	Transform()->SetRelativePos(vPos);
}
```

키를 누름에 따라서 카메라를 확대, 축소할 수 있도록 위의 코드를 설정한다.<br/>