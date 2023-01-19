---
layout: post
title:  "[DirectX] IMGUI - CameraUI"
subtitle:  
date: 2023-01-18 19:40:51 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] IMGUI - CameraUI<br/>
<br/>

지난 포스트에 이어서 UI를 구성해보자.<br/>
<br/>

## CameraUI 클래스의 구성<br/>
<br/>

```
// 헤더파일

#pragma once
#include "ComponentUI.h"
class CameraUI :
    public ComponentUI
{

public:
    virtual int render_update() override;

public:
    CameraUI();
    ~CameraUI();
};


```

```
// cpp 파일

#include "pch.h"
#include "CameraUI.h"


CameraUI::CameraUI()
    : ComponentUI("##Camera", COMPONENT_TYPE::CAMERA)
{
    SetName("Camera");
}

CameraUI::~CameraUI()
{
}

int CameraUI::render_update()
{
    if (FALSE == ComponentUI::render_update())
        return FALSE;

    return TRUE;
}

```
<br/>

### Editor Camera의 구현<br/>
<Br/>

```
// CRenderMgr.h 중에서

void RegisterEditorCamera(CCamera* _Cam) { m_pEditorCam = _Cam; }
```
렌더 매니저에서 에디터 카메라에 대한 변수를 추가해준다.<br/>


```
// CEditorObjMgr.h 중에서

#pragma once

class CGameObjectEx;

class CEditorObjMgr
	: public CSingleton<CEditorObjMgr>
{
	SINGLE(CEditorObjMgr);
private:
	vector<CGameObjectEx*>	m_vecEditorObj;

	CGameObjectEx*			m_DebugShape[(UINT)SHAPE_TYPE::END];
	vector<tDebugShapeInfo> m_DebugShapeInfo;

public:
	void init();
	void progress();

private:
	void tick();
	void render();
};


```

```
// CEditorObjMgr.cpp 중에서

	// EditorObject 생성
	CGameObjectEx* pEditorCamObj = new CGameObjectEx;
	pEditorCamObj->AddComponent(new CTransform);
	pEditorCamObj->AddComponent(new CCamera);
	pEditorCamObj->AddComponent(new CCameraMoveScript);

	pEditorCamObj->Camera()->SetLayerMaskAll(true);

	m_vecEditorObj.push_back(pEditorCamObj);
	CRenderMgr::GetInst()->RegisterEditorCamera(pEditorCamObj->Camera());
```

```
// CRenderMgr.cpp 중에서 두 함수

void CRenderMgr::render_play()
{
    // 카메라 기준 렌더링
    for (size_t i = 0; i < m_vecCam.size(); ++i)
    {
        if (nullptr == m_vecCam[i])
            continue;

        m_vecCam[i]->SortObject();
        m_vecCam[i]->render();
    }
}

void CRenderMgr::render_editor()
{
    m_pEditorCam->SortObject();
    m_pEditorCam->render();    
}
```
렌더 매니저에서 렌더링 상황 두 종류를 만들어준다(Play, Editor)<br/>
이때 render_play는 메인 카메라 기준으로 렌더링하는 것이고,<br/>
render_editor는 에디터 카메라를 통해서 렌더링이 되는 것이다.<br/>

어떤 카메라로 렌더링을 할 지는 레벨의 상태에 따라서 결정이 되는 것이다.<br/>

```
// CRenderMgr.cpp 중에서 render 함수

void CRenderMgr::render()
{
    // 렌더링 시작
    float arrColor[4] = { 0.f, 0.f, 0.f, 1.f };
    CDevice::GetInst()->ClearTarget(arrColor);

    // 출력 타겟 지정    
    CDevice::GetInst()->OMSet();

    // 광원 및 전역 데이터 업데이트 및 바인딩
    UpdateData();

    // 렌더 함수 호출
    (this->*RENDER_FUNC)();
    
    // 광원 해제
    Clear();
}
```

상태 변수는 define.h에다가 선언해준다.<br/>

```
// define.h 중에서

enum class LEVEL_STATE
{
    PLAY,
    PAUSE,
    STOP
}
```
<br/>

### CLevel Class의 재구성<br/>
<br/>

그리고 레벨의 상태를 변경할 수 있도록한다.<br/>

생성자를 통해 기본 상태는 STOP으로 결정해주고, ChangeState함수를 통해서 레벨을 바꿀 수 있도록 조작한다.<br/>

```
// CLevel.cpp 중에서

void CLevel::ChangeState(LEVEL_STATE _State)
{	
	m_State = _State;

	if (LEVEL_STATE::PLAY == m_State)
	{
		CRenderMgr::GetInst()->SetRenderFunc(true);
	}
	else
	{
		CRenderMgr::GetInst()->SetRenderFunc(false);
	}
}
```

그리고 멤버함수 포인터를 조작하는 함수를 CRenderMgr에다가 만들어준다.

```
// CRenderMgr.cpp 중에서 SetRenderFunc 함수

void CRenderMgr::SetRenderFunc(bool _IsPlay)
{
    if(_IsPlay)
        RENDER_FUNC = &CRenderMgr::render_play;
    else
        RENDER_FUNC = &CRenderMgr::render_editor;
}
```

<br/>

### Stop 상태에서 레벨을 멈추기<br/>
<br/>

정지 상태라면 레벨이 멈춰야할 것이다.<br/>

CLevelMgr에서 조건을 달아줘서 이를 조절할 수 있다.<br/>

레벨의 상태가 stop이면 tick이 돌지 않도록 만드는 것이다.<br/>

```
// CLevelMgr.cpp 중에서 tick함수의 수정

void CLevelMgr::tick()
{
	m_pCurLevel->clear();

	if (LEVEL_STATE::PLAY == m_pCurLevel->GetState())
	{
		m_pCurLevel->tick();		
	}	

	m_pCurLevel->finaltick();
}

```

사실 지금 상태는 Stop상태보단 Pause 상태를 구현한 것에 더 가깝다.<br/>

그리고 Pause 상태면 이어서 동작할 수 있도록 구현해야하지만, Stop이면 Play하기 이전의 원 상태로 복귀시켜야한다.<br/>

이를 어떻게 구현할 수 있을까?<br/>

**초기 레벨을 복사해놓으면 된다.**<br/>

이를 위해선 레벨을 저장하고 로드하는 기능을 구현해야한다.<br/>

그 과정은 나중에 진행하기로 한다.<br/>
<br/>