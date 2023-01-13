---
layout: post
title:  "[DirectX] IMGUI의 클래스 화 1"
subtitle:  
date: 2023-01-11 19:53:56 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] IMGUI의 클래스 화 1<br/>
<br/>

함수형으로 이루어진 IMGUI코드를 클래스로 관리하는 방식으로 바꿔보자.<br/>
<br/>

## ImGuiMgr 클래스의 구성<br/>
<Br/>

```
// 헤더 파일

#pragma once
#include <Engine\CSingleton.h>

#include "ImGui\imgui.h"
#include "ImGui\imgui_impl_dx11.h"
#include "ImGui\imgui_impl_win32.h"

class InspectorUI;

class ImGuiMgr :
    public CSingleton<ImGuiMgr>
{
    SINGLE(ImGuiMgr);
private:
    HWND            m_hMainHwnd;

    InspectorUI*    m_Inspector;



public:
    void init(HWND _hWnd);
    void progress();

private:
    void CreateUI();

    void begin();
    void tick();
    void finaltick();
    void render();
};
```

```
// cpp 파일

#include "pch.h"
#include "ImGuiMgr.h"

#include <Engine\CDevice.h>
#include <Engine\CLevelMgr.h>
#include <Engine\CKeyMgr.h>

#include <Engine\CGameObject.h>

#include "InspectorUI.h"


ImGuiMgr::ImGuiMgr()
    : m_hMainHwnd(nullptr)
    , m_Inspector(nullptr)
{

}

ImGuiMgr::~ImGuiMgr()
{
    // ImGui Release
    ImGui_ImplDX11_Shutdown();
    ImGui_ImplWin32_Shutdown();
    ImGui::DestroyContext();

    delete m_Inspector;
}


void ImGuiMgr::init(HWND _hWnd)
{
    m_hMainHwnd = _hWnd;

    // ImGui 초기화
    // Setup Dear ImGui context
    IMGUI_CHECKVERSION();
    ImGui::CreateContext();
    ImGuiIO& io = ImGui::GetIO(); (void)io;
    io.ConfigFlags |= ImGuiConfigFlags_NavEnableKeyboard;       // Enable Keyboard Controls
    //io.ConfigFlags |= ImGuiConfigFlags_NavEnableGamepad;      // Enable Gamepad Controls
    io.ConfigFlags |= ImGuiConfigFlags_DockingEnable;           // Enable Docking
    io.ConfigFlags |= ImGuiConfigFlags_ViewportsEnable;         // Enable Multi-Viewport / Platform Windows
    //io.ConfigViewportsNoAutoMerge = true;
    //io.ConfigViewportsNoTaskBarIcon = true;
    //io.ConfigViewportsNoDefaultParent = true;
    //io.ConfigDockingAlwaysTabBar = true;
    //io.ConfigDockingTransparentPayload = true;
    //io.ConfigFlags |= ImGuiConfigFlags_DpiEnableScaleFonts;     // FIXME-DPI: Experimental. THIS CURRENTLY DOESN'T WORK AS EXPECTED. DON'T USE IN USER APP!
    //io.ConfigFlags |= ImGuiConfigFlags_DpiEnableScaleViewports; // FIXME-DPI: Experimental.

    // Setup Dear ImGui style
    ImGui::StyleColorsDark();
    //ImGui::StyleColorsLight();

    // When viewports are enabled we tweak WindowRounding/WindowBg so platform windows can look identical to regular ones.
    ImGuiStyle& style = ImGui::GetStyle();
    if (io.ConfigFlags & ImGuiConfigFlags_ViewportsEnable)
    {
        style.WindowRounding = 0.0f;
        style.Colors[ImGuiCol_WindowBg].w = 1.0f;
    }

    // Setup Platform/Renderer backends
    ImGui_ImplWin32_Init(m_hMainHwnd);
    ImGui_ImplDX11_Init(DEVICE, CONTEXT);

    CreateUI();
}

void ImGuiMgr::progress()
{
    begin();

    tick();

    finaltick();

    render();
}


void ImGuiMgr::begin()
{
    ImGui_ImplDX11_NewFrame();
    ImGui_ImplWin32_NewFrame();
    ImGui::NewFrame();
}

void ImGuiMgr::tick()
{
    m_Inspector->tick();
}

void ImGuiMgr::finaltick()
{
    // Demo UI
    ImGui::ShowDemoWindow();

    // InspectorUI
    m_Inspector->finaltick();


    if (KEY_TAP(KEY::ENTER))
        ImGui::SetWindowFocus(nullptr);
}

void ImGuiMgr::render()
{
    // ImGui Rendering
    ImGui::Render();
    ImGui_ImplDX11_RenderDrawData(ImGui::GetDrawData());


    // Update and Render additional Platform Windows
    ImGuiIO& io = ImGui::GetIO(); (void)io;

    if (io.ConfigFlags & ImGuiConfigFlags_ViewportsEnable)
    {
        ImGui::UpdatePlatformWindows();
        ImGui::RenderPlatformWindowsDefault();
    }
}



void ImGuiMgr::CreateUI()
{
    m_Inspector = new InspectorUI;
   
}
```

<br/>

finaltick()에서 ImGUI가 명령어를 수행하도록 한다.<br/>

그리고 CreateUI에서 실제 사용되는 UI를 구성해준다.<br/>

이 때, UI들은 key값과 포인터를 이용한 map 자료구조로 관리한다.<br/>

<br/>

## UI의 클래스 구성<br/>
<Br/>

기존의 ImGUI는 UI하나 당 함수 하나의 형태로 주어져 있었지만, 이번에는 클래스 하나에 UI를 구성하여 UI가 객체가 되는 형태로 작성할 것이다.<br/>
<br/>

### 부모 UI 클래스 구성
<br/>

```
// 헤더파일

#pragma once


#include "ImGuiMgr.h"

class UI
{
private:
	const string	m_strName;		// 타이틀 이름, ID

	UI*				m_ParentUI;		// 부모 UI
	vector<UI*>		m_vecChildUI;	// 자식 UI 목록

public:
	virtual void tick() {}
	virtual void finaltick();
	virtual void render_update() = 0;


public:
	const string& GetName() { return m_strName; }
	void AddChildUI(UI* _UI)
	{
		_UI->m_ParentUI = this;
		m_vecChildUI.push_back(_UI);
	}


public:
	UI(const string& _Name);
	~UI();
};
```

```
// cpp 파일

#include "pch.h"
#include "UI.h"


UI::UI(const string& _Name)
	: m_strName(_Name)
	, m_ParentUI(nullptr)
{
	
}

UI::~UI()
{
	Safe_Del_Vec(m_vecChildUI);
}

void UI::finaltick()
{
	if (nullptr == m_ParentUI)
	{
		ImGui::Begin(m_strName.c_str());

		render_update();

		for (size_t i = 0; i < m_vecChildUI.size(); ++i)
		{
			m_vecChildUI[i]->finaltick();

			if(i != m_vecChildUI.size() - 1)
				ImGui::Separator();
		}

		ImGui::End();
	}
	else
	{
		ImGui::BeginChild(m_strName.c_str(), ImVec2(0.f, 200.f));

		render_update();

		for (size_t i = 0; i < m_vecChildUI.size(); ++i)
		{
			m_vecChildUI[i]->finaltick();

			if (i != m_vecChildUI.size() - 1)
				ImGui::Separator();
		}

		ImGui::EndChild();
	}
}
```

- m_vSize : UI의 크기 값을 담당한다.<br/>
초기값이 0이 들어가면 부모의 크기를 따라 쓰겠다는 표시이다.
- m_ParentUI : 부모 UI가 무엇인지를 저장한다.
- m_vecChildUI : 자식 UI 목록을 vector형태로 저장한다.

<br/>

## (예시) InspectorUI 클래스 구성<br/>
<br/>

```
// 헤더 파일

#pragma once
#include "UI.h"

class CGameObject;
class TransformUI;
class MeshRenderUI;

class InspectorUI :
    public UI
{
private:
    CGameObject*    m_pTarget;
    TransformUI*    m_TransformUI;
    MeshRenderUI*   m_MeshRenderUI;

    //ComponentUI*    m_arrComUI[(UINT)COMPONENT_TYPE::END];


public:
    virtual void tick() override;
    virtual void render_update() override;

public:
    void SetTargetObject(CGameObject* _Target) { m_pTarget = _Target; }




public:
    InspectorUI();
    ~InspectorUI();
};


```

```
// cpp 파일

#include "pch.h"
#include "InspectorUI.h"

#include <Engine\CGameObject.h>
#include <Engine\CTransform.h>
#include <Engine\CLevelMgr.h>
#include <Engine\CKeyMgr.h>

#include "TransformUI.h"
#include "MeshRenderUI.h"

InspectorUI::InspectorUI()
	: UI("Inspector")
	, m_pTarget(nullptr)
	, m_TransformUI(nullptr)
{
	m_TransformUI = new TransformUI;
	//m_arrComUI[(UINT)COMPONENT_TYPE::TRANSFORM] = new TransformUI;
	AddChildUI(m_TransformUI);

	m_MeshRenderUI = new MeshRenderUI;
	AddChildUI(m_MeshRenderUI);
}

InspectorUI::~InspectorUI()
{
	
}

void InspectorUI::tick()
{
	if (nullptr == m_pTarget)
	{
		m_pTarget = CLevelMgr::GetInst()->FindObjectByName(L"Player");

		m_TransformUI->SetTarget(m_pTarget);
		m_MeshRenderUI->SetTarget(m_pTarget);
	}
}

void InspectorUI::render_update()
{
	
}
```

Inspector UI는 고유한 이름 "Inspector"를 가진다.<br/>

그리고 ImGui::Text("Inspector UI")로 이름을 표시해준다.<br/>
(원하는 다른 이름이 있다면 다르게 표시해줄 수 있다.)

tick()함수에서 Inspector가 할 일을 구현해준다.<br/>

Inspector는 담당하는 오브젝트를 알아야한다.<br/>

그래서 LevelMgr에서 탐색하는 함수(FindObjectByName)를 만들어준다.<br/>

```
// CLevelMgr.cpp 중에서


CGameObject* CLevelMgr::FindObjectByName(const wstring& _strName)
{
	return m_pCurLevel->FindObjectByName(_strName);	
}

void CLevelMgr::FindObjectByName(const wstring& _strName, vector<CGameObject*>& _vec)
{
	m_pCurLevel->FindObjectByName(_strName, _vec);
}

```
<br/>

### Transform 정보를 출력하기<br/>
<br/>

```
	Vec3 vPos = m_Target->Transform()->GetRelativePos();
	Vec3 vScale = m_Target->Transform()->GetRelativeScale();
	Vec3 vRotation = m_Target->Transform()->GetRelativeRot();
	vRotation = (vRotation / XM_PI) * 180.f;

	ImGui::Text("Position");
	ImGui::SameLine();
	ImGui::InputFloat3("##Relative Position", vPos);

	ImGui::Text("Scale   ");
	ImGui::SameLine();
	ImGui::InputFloat3("##Relative Scale", vScale);

	ImGui::Text("Rotation");
	ImGui::SameLine();
	ImGui::InputFloat3("##Relative Rotation", vRotation);
}

```

InputFloat3 함수에서 텍스트와 3개의 float를 출력할 수 있다.<br/>

그리고 SameLine함수는 같은 줄에다가 출력될 수 있도록 만드는 함수이다.<br/>
<br/>

### Transform 정보를 입력하기<br/>
<br/>

반대로 좌표, 회전, 크기 정보를 입력해서 개체의 상태를 조절할 수 있도록 만들어보자.<br/>

```
	m_Target->Transform()->SetRelativePos(vPos);
	m_Target->Transform()->SetRelativeScale(vScale);

	vRotation = (vRotation / 180.f) * XM_PI;
	m_Target->Transform()->SetRelativeRot(vRotation);
```

UI창에서 수동으로 값을 넣고 엔터를 누르면 vPos, vScale, vRotation에 그 값이 들어가게 된다.<br/>

즉, 엔터를 눌렀다면 Transform()->SetRelative...함수를 통해서 새로운 값을 넣어주면 되는 것이다.<br/>
<br/>

그리고 추가 구성요소로 인해서 변동사항이 많다. 다음 포스트에 이어서 살펴보자.<br/>