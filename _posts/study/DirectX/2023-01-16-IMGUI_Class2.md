---
layout: post
title:  "[DirectX] IMGUI의 클래스 화 2"
subtitle:  
date: 2023-01-16 21:03:54 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] IMGUI의 클래스 화 2<br/>
<br/>

지난 포스트에 이어서 Inspector UI를 구성해보자.<br/>
<br/>

## InspectorUI 구성하기<br/>
<br/>

### ComponentUI 클래스의 구성<br/>
<br/>

TransformUI와 MeshRenderUI는 공통된 부분이 있으므로 상속을 이용할 것이다.<br/>
이를 위해서 이제는 ComponentUI에 구성을 한 뒤 상속시키는 방법을 이용하겠다.<br/>

```
// 헤더 파일

#pragma once
#include "UI.h"

#include <Engine\ptr.h>
#include <Engine\CRes.h>

class CGameObject;

class ComponentUI :
    public UI
{
private:
    CGameObject*            m_Target;
    const COMPONENT_TYPE    m_Type;

public:
    void SetTarget(CGameObject* _Target);
    
    CGameObject* GetTarget() { return m_Target; }

    COMPONENT_TYPE GetComponentType() { return m_Type; }

    void GetResKey(Ptr<CRes> _Res, char* _Buff, size_t _BufferSize);

public:
    virtual int render_update() override;


public:
    ComponentUI(const string& _Name, COMPONENT_TYPE _Type);
    ~ComponentUI();
};



```

```
// cpp 파일

#include "pch.h"
#include "ComponentUI.h"

#include <Engine\CGameObject.h>

ComponentUI::ComponentUI(const string& _Name, COMPONENT_TYPE _Type)
	: UI(_Name)
	, m_Type(_Type)
{
}

ComponentUI::~ComponentUI()
{
	
}

int ComponentUI::render_update()
{
	if (nullptr == m_Target || nullptr == m_Target->GetComponent(m_Type))
		return FALSE;

	ImGui::PushID(0);
	ImGui::PushStyleColor(ImGuiCol_Button, (ImVec4)ImColor::HSV(0.f / 7.0f, 0.6f, 0.6f));
	ImGui::PushStyleColor(ImGuiCol_ButtonHovered, (ImVec4)ImColor::HSV(0.f / 7.0f, 0.6f, 0.6f));
	ImGui::PushStyleColor(ImGuiCol_ButtonActive, (ImVec4)ImColor::HSV(0.f / 7.0f, 0.6f, 0.6f));
	ImGui::Button(GetName().c_str());
	ImGui::PopStyleColor(3);
	ImGui::PopID();	

	return TRUE;
}


void ComponentUI::SetTarget(CGameObject* _Target)
{
	m_Target = _Target;

	if (nullptr == m_Target->GetComponent(m_Type))
	{
		SetActive(false);
	}
	else
	{
		SetActive(true);
	}
}

void ComponentUI::GetResKey(Ptr<CRes> _Res, char* _Buff, size_t _BufferSize)
{
	memset(_Buff, 0, sizeof(char) * _BufferSize);

	if (nullptr == _Res)			
		return;	

	wstring wstrKey = _Res->GetKey();
	string	strKey = string(wstrKey.begin(), wstrKey.end());
	memcpy(_Buff, strKey.data(), sizeof(char) * strKey.length());
}
```

컴포넌트 타입의 m_Type 변수는 컴포넌트 UI가 어떤 컴포넌트를 담당하는지를 기록하는 역할을 한다.<br/>

그리고 자식 클래스에 어떤 컴포넌트인지 전달을 하기 위해서 자식 생성자의 인자에 COMPONENT_TYPE가 들어간다.<br/>

그리고, 리소스의 키 값을 받아올 수 있도록 GetResKey함수를 만든다.<br/>

```
InspectorUI.cpp 중에서 (생성자)

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
```


### TransformUI 클래스의 구성<br/>
<br/>

```
// 헤더파일

#pragma once
#include "UI.h"

class CGameObject;

class TransformUI :
    public UI
{
private:
    CGameObject*    m_Target;

public:
    virtual void render_update() override;

public:
    void SetTarget(CGameObject* _Target) { m_Target = _Target; }

public:
    TransformUI();
    ~TransformUI();
};


```

```
// cpp 파일

#include "pch.h"
#include "TransformUI.h"

#include <Engine\CGameObject.h>
#include <Engine\CTransform.h>

TransformUI::TransformUI()
	: UI("Transform")
	, m_Target(nullptr)
{
}

TransformUI::~TransformUI()
{
}

void TransformUI::render_update()
{
	if (nullptr == m_Target)
		return;

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

	m_Target->Transform()->SetRelativePos(vPos);
	m_Target->Transform()->SetRelativeScale(vScale);

	vRotation = (vRotation / 180.f) * XM_PI;
	m_Target->Transform()->SetRelativeRot(vRotation);
}

```
<br/>

추가로, 텍스트만 제목으로 해놓으면 밋밋하니 버튼을 이용해서 제목을 붙여보자.<br/>

```
// TransformUI.cpp 파일 중에서


	if (FALSE == ComponentUI::render_update())
		return FALSE;

	Vec3 vPos = GetTarget()->Transform()->GetRelativePos();
	Vec3 vScale = GetTarget()->Transform()->GetRelativeScale();
	Vec3 vRotation = GetTarget()->Transform()->GetRelativeRot();
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
```

아래 사항을 이용한다.

- 버튼의 기능이 없다.
- 버튼이 hover되어있건, 평소 상태이건 색깔을 같게 만든다.


<Br/>

### MeshRenderUI 클래스의 구성<br/>
<Br/>

오브젝트가 보유하고 있는 렌더 정보를 출력하도록 한다.<br/>

```
// 헤더파일

#pragma once
#include "ComponentUI.h"

class MeshRenderUI :
    public ComponentUI
{

public:
    virtual int render_update() override;

public:
    MeshRenderUI();
    ~MeshRenderUI();
};
```

```
// cpp 파일

#include "pch.h"
#include "MeshRenderUI.h"

#include <Engine\CMeshRender.h>

MeshRenderUI::MeshRenderUI()
	: ComponentUI("MeshRender", COMPONENT_TYPE::MESHRENDER)	
{
}

MeshRenderUI::~MeshRenderUI()
{
}


int MeshRenderUI::render_update()
{
	if (FALSE == ComponentUI::render_update())
		return FALSE;

	char szBuff[50] = {};

	Ptr<CMesh> pMesh = GetTarget()->MeshRender()->GetMesh();
	Ptr<CMaterial> pMtrl = GetTarget()->MeshRender()->GetMaterial();
		
	ImGui::Text("Mesh    ");
	ImGui::SameLine();	
	GetResKey(pMesh.Get(), szBuff, 50);
	ImGui::InputText("##MeshName", szBuff, 50, ImGuiInputTextFlags_ReadOnly);
	ImGui::SameLine();
	ImGui::Button("##MeshSelectBtn", ImVec2(18, 18));
	
	ImGui::Text("Material");
	ImGui::SameLine();
	GetResKey(pMtrl.Get(), szBuff, 50);
	ImGui::InputText("##MtrlName", szBuff, 50, ImGuiInputTextFlags_ReadOnly);
	ImGui::SameLine();
	ImGui::Button("##MtrlSelectBtn", ImVec2(18, 18));

	return TRUE;
}
```
<br/>

ImGuiInputTextFlags_ReadOnly로 설정함으로써 메쉬의 이름을 바꾸지 못하도록 지정할 수 있다.<br/>

그리고 담당 컴포넌트에게 메쉬와 재질에 대한 정보를 받아오도록 조치한다.<br/>

그리고 메쉬, 재질 정보를 담은 목록을 보여주는 UI를 보여줄 수 있도록 버튼도 만들어 줄 수 있다.<br/>
<br/>

### ListUI<br/>
<Br/>

한편, 리스트를 담은 UI는 InspectorUI가 아닌 완전 별도의 UI이므로 새로운 클래스를 생성해주어야 한다.<br/>

```
// 헤더파일

#pragma once
#include "UI.h"

class ListUI :
    public UI
{
private:
    vector<string>          m_vecStrData; 
    int                     m_iSelectedIdx;

public:
    virtual int render_update() override;

public:
    void AddItem(const string& _strItem) {  m_vecStrData.push_back(_strItem); }
    void Clear() { m_vecStrData.clear(); m_iSelectedIdx = -1; }

public:
    ListUI();
    ~ListUI();
};
```

```
// cpp 파일

#include "pch.h"
#include "ListUI.h"

ListUI::ListUI()
    : UI("##List")
    , m_iSelectedIdx(0)
{
}

ListUI::~ListUI()
{
}

int ListUI::render_update()
{    
    // 최근 UI 의 작업영역 사이즈를 알아낸다.
    ImVec2 ListUIContentSize = ImGui::GetContentRegionAvail();
        
    if (ImGui::BeginListBox("##list", ListUIContentSize))
    {
        for (int i = 0; i < m_vecStrData.size(); i++)
        {
            const bool is_selected = (m_iSelectedIdx == i);

            // 리스트 아이템 출력, 반환값은 클릭 True/ False
            if (ImGui::Selectable(m_vecStrData[i].c_str(), is_selected))
            {
                m_iSelectedIdx = i;
            }             

            // Set the initial focus when opening the combo (scrolling + keyboard navigation focus)
            if (is_selected)
                ImGui::SetItemDefaultFocus();
        }
        ImGui::EndListBox();
    }
       

    return TRUE;
}

```

메쉬에 있는 버튼을 누르면 메쉬 리스트를, 재질에 있는 버튼을 누르면 재질 리스트를 보여줄 수 있도록 한다.<br/>

List를 출력할 땐 BeginListbox를 이용해볼 수 있다.<br/>

```
// imgui_demo.cpp 파일 중에서

const char* items[] = { "AAAA", "BBBB", "CCCC", "DDDD", "EEEE", "FFFF", "GGGG", "HHHH", "IIII", "JJJJ", "KKKK", "LLLLLLL", "MMMM", "OOOOOOO" };

static int item_current_idx = 0; // Here we store our selection data as an index.
if (ImGui::BeginListBox("listbox 1"))
{
	for (int n = 0; n < IM_ARRAYSIZE(items); n++)
	{
		const bool is_selected = (item_current_idx == n);
		if (ImGui::Selectable(items[n], is_selected))
			item_current_idx = n;

		// Set the initial focus when opening the combo (scrolling + keyboard navigation focus)
		if (is_selected)
			ImGui::SetItemDefaultFocus();
	}
	ImGui::EndListBox();
}

```
<br/>

위의 포맷을 갖다 쓸 것이다.<br/>

```
// ListUI.cpp 중에서 render_update 함수

int ListUI::render_update()
{    
    // 최근 UI 의 작업영역 사이즈를 알아낸다.
    ImVec2 ListUIContentSize = ImGui::GetContentRegionAvail();
        
    if (ImGui::BeginListBox("##list", ListUIContentSize))
    {
        for (int i = 0; i < m_vecStrData.size(); i++)
        {
            const bool is_selected = (m_iSelectedIdx == i);

            // 리스트 아이템 출력, 반환값은 클릭 True/ False
            if (ImGui::Selectable(m_vecStrData[i].c_str(), is_selected))
            {
                m_iSelectedIdx = i;
            }             

            // Set the initial focus when opening the combo (scrolling + keyboard navigation focus)
            if (is_selected)
                ImGui::SetItemDefaultFocus();
        }
        ImGui::EndListBox();
    }
       

    return TRUE;
}
```

- GetWindowSize()함수를 통해 ListUISize에 값을 저장해서
- GetContentRegionAvail()함수를 통해 ListUIContentSize에 값을 저장해서

List의 크기가 부모 윈도우 UI의 크기와 일치하도록 조정할 수 있다.<br/>

차이점은 리스트와 윈도우 사이에 패딩이 있는가 없는가 여부이다.<br/>

그리고 리스트에 값을 저장하는 데는 vector 자료구조를 사용할 것이다.<br/>

```
// ListUI.h 헤더파일 중에서

private:
	vector<string>		m_vecStrData;
```
위와 같이 변수를 생성해준다.<br/>


그리고 문자열 데이터를 추가하는 과정을 아래와 같이 생성한다.<br/>

```
// ListUI.h 중에서

public:
    void AddItem(const string& _strItem) {  m_vecStrData.push_back(_strItem); }

```
<br/>

### 메쉬 목록을 List로 가져오기<br/>
<Br/>

우선, 리소스 매니저로부터 메쉬 목록을 가져오기 위해서 함수를 아래와 같이 생성한다.<br/>

이때, map에서 키값만을 가져올 것이므로 데이터 보존을 위해서 const reference 방식을 사용한다.<br/>

```
// CResMgr.h 중에서

public:
    const map<wstring, Ptr<CRes>>& GetResources(RES_TYPE _Type) { return m_arrRes[(UINT)_Type]; }
```

그리고 ImGuiMgr.cpp 중에서 UI를 반환하는 함수(FindUI)를 아래와 같이 추가한다.<br/>
```
// ImGuiMgr.cpp 중에서

UI* ImGuiMgr::FindUI(const string& _UIName)
{
    map<string, UI*>::iterator iter = m_mapUI.find(_UIName);

    if(iter == m_mapUI.end())
        return nullptr;

    return iter->second;
}
```

그리고 아래 코드를 통해서 MeshRenderUI에서 리스트를 가져온다.<br/>

```
// MeshRenderUI.cpp 중에서

int MeshRenderUI::render_update()
{
	if (FALSE == ComponentUI::render_update())
		return FALSE;

	char szBuff[50] = {};

	Ptr<CMesh> pMesh = GetTarget()->MeshRender()->GetMesh();
	Ptr<CMaterial> pMtrl = GetTarget()->MeshRender()->GetMaterial();
		
	ImGui::Text("Mesh    ");
	ImGui::SameLine();	
	GetResKey(pMesh.Get(), szBuff, 50);
	ImGui::InputText("##MeshName", szBuff, 50, ImGuiInputTextFlags_ReadOnly);
	ImGui::SameLine();
	if (ImGui::Button("##MeshSelectBtn", ImVec2(18, 18)))
	{
		const map<wstring, Ptr<CRes>>& mapMesh = CResMgr::GetInst()->GetResources(RES_TYPE::MESH);

		ListUI* pListUI = (ListUI*)ImGuiMgr::GetInst()->FindUI("##List");

		pListUI->SetName("Mesh List");
		pListUI->Clear();
		for (const auto& pair : mapMesh)
		{			
			pListUI->AddItem(string(pair.first.begin(), pair.first.end()));
		}
		pListUI->SetActive(true);
	}
	
	ImGui::Text("Material");
	ImGui::SameLine();
	GetResKey(pMtrl.Get(), szBuff, 50);
	ImGui::InputText("##MtrlName", szBuff, 50, ImGuiInputTextFlags_ReadOnly);
	ImGui::SameLine();
	if (ImGui::Button("##MtrlSelectBtn", ImVec2(18, 18)))
	{
		const map<wstring, Ptr<CRes>>& mapMtrl = CResMgr::GetInst()->GetResources(RES_TYPE::MATERIAL);

		ListUI* pListUI = (ListUI*)ImGuiMgr::GetInst()->FindUI("##List");

		pListUI->SetName("Material List");
		pListUI->Clear();
		for (const auto& pair : mapMtrl)
		{
			pListUI->AddItem(string(pair.first.begin(), pair.first.end()));
		}
		pListUI->SetActive(true);
	}

	return TRUE;
}
```



<Br/>

### Modal방식 및 BeginPopupModal<br>
<br/>

모달방식은 어떤 UI가 켜져있을 때 다른 UI나 다른 부분이 클릭이 되지 않도록 만드는 기능이다.<br/>

IMGUI는 이 기능을 지원하는데, 특정한 UI를 모달방식을 사용하도록 하고 싶으면 BeginPopupModal을 사용하면 된다.<br/>

UI 클래스 에서 모달 방식여부에 따라서 분기를 설정해줄 필요가 있다.<br/>

```
// 헤더파일

bool	m_Modal;
```

```
// UI.cpp의 finaltick함수 중에서

void UI::finaltick()
{
	if (!m_Active)
		return;

	// 부모 UI
	if (nullptr == m_ParentUI)
	{
		// 모달리스
		if (!m_Modal)
		{
			ImGui::Begin(m_strName.c_str(), &m_Active);

			render_update();

			for (size_t i = 0; i < m_vecChildUI.size(); ++i)
			{
				// 자식UI 가 비활성화 상태면 건너뛴다.
				if (!m_vecChildUI[i]->IsActive())
					continue;

				m_vecChildUI[i]->finaltick();

				// 자식 UI 간의 구분선
				if (i != m_vecChildUI.size() - 1)
					ImGui::Separator();
			}

			ImGui::End();
		}

		// 모달
		else
		{
			ImGui::OpenPopup(m_strName.c_str());
			if (ImGui::BeginPopupModal(m_strName.c_str(), &m_Active))
			{
				render_update();

				for (size_t i = 0; i < m_vecChildUI.size(); ++i)
				{
					// 자식UI 가 비활성화 상태면 건너뛴다.
					if (!m_vecChildUI[i]->IsActive())
						continue;

					m_vecChildUI[i]->finaltick();

					// 자식 UI 간의 구분선
					if (i != m_vecChildUI.size() - 1)
						ImGui::Separator();
				}

				ImGui::EndPopup();
			}
		}
	}

	// 자식 UI
	else
	{
		ImGui::BeginChild(m_strName.c_str(), m_vSize);

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
<br/>

다음 내용은 다음 포스트에 이어서 포스팅 하곘다.<br/>