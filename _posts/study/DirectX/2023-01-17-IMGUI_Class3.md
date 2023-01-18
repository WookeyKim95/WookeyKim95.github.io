---
layout: post
title:  "[DirectX] IMGUI의 클래스 화 3"
subtitle:  
date: 2023-01-17 15:31:54 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] IMGUI의 클래스 화 3<br/>
<br/>

지난 포스트에 이어서 UI를 구성해보자.<br/>
리스트에서 어떤 재질이나 메쉬를 선택하면 해당 재질이나 메쉬로 바뀌도록 넣어보자.<br/>
<br/>

## ListUI의 재구성<br/>
<Br/>

```
// ListUI.cpp 중에서

void ListUI::Reset(const string& _strName, ImVec2 _vSize)
{
    Clear();

    SetName(_strName);

    SetSize(_vSize.x, _vSize.y);

    SetActive(true);

    // 위치 조정
    HWND hWnd = ImGuiMgr::GetInst()->GetMainHwnd();

    RECT rt = {};
    //GetClientRect(hWnd, &rt);
    GetWindowRect(hWnd, &rt);

    float CenterX = (float)((rt.left + rt.right) / 2);
    float CenterY = (float)((rt.bottom + rt.top) / 2);

    SetPopupPos(ImVec2(CenterX - _vSize.x / 2.f, CenterY - _vSize.y / 2.f));
}
```

중복되는 함수가 많으니 ListUI에서 Reset함수를 다시 구성 해주자.<br/>

Reset함수는 리스트를 초기화 하는 기능도 있지만 리스트가 메인 윈도우 기준 가운데에서 출현하도록 만드는 기능도 있다.<br/>

GetWindowRect를 통해 창의 크기를 반환하고 좌우와 상하의 중앙 값을 각각 x, y 좌표로 잡아서 중간지점을 구한다.<br/>

<br/>

### GetClientRect와 GetWindowRect<br/>
<br/>

- GetClientRect는 순수 메인 작업 창의 크기를 반환한다.<br/>
- GetWindowRect는 상하좌우 패딩까지 모두 더한 크기를 반환한다.<br/>
<br/>

### 기능 추가 : 특정 키가 눌리면 종료<br/>
<br/>

```
// UI.h 파일 중에서

private:
	ImVec2			m_vPopupPos;	// UI 위치

public:
	ImVec2 GetPopupPos() { return m_vPopupPos; }
	void SetPopupPos(ImVec2 _vPos) { m_vPopupPos = _vPos; }
```

헤더파일에 m_vPopupPos 변수를 추가해주고,

```
// UI.cpp 파일 중에서

// 모달
else
{
    ImGui::SetNextWindowPos(m_vPopupPos);
    ImGui::SetNextWindowSize(m_vSize);

    ImGui::OpenPopup(strFullName.c_str());
    if (ImGui::BeginPopupModal(strFullName.c_str(), &m_Active))
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
```

이제는 윈도우 중간에서 뜨도록 PopupPos를 설정해준다.<br/>
<br/>

그리고 특정 키가 눌리면 꺼지도록 설정을 해줄 것이다.<br/>

```
// ListUI.cpp 중에서 finaltick 함수

void ListUI::finaltick()
{
    UI::finaltick();

    // Esc 눌리면 비활성화
    if (KEY_TAP(KEY::ESC))
    {
        SetActive(false);

        // 모든 UI 포커스 해제
        ImGui::SetWindowFocus(nullptr); 
    }
}
```
또한, 활성화 된 UI만 Finaltick을 실행하도록 ImGuiMgr에서 조치를 취해야한다.<br/>

```
// ImGuiMgr.cpp의 finaltick함수 중에서

void ImGuiMgr::finaltick()
{
    // InspectorUI
    for (const auto& pair : m_mapUI)
    {
        if (pair.second->IsActive())
        {
            pair.second->finaltick();
        }        
    }
}
```
<br/>

### ImGuiMgr의 Delegate 설정<br/>
<br/>

```
// 헤더파일 중에서

typedef void (UI::* UI_DELEGATE)(void);
```

기능 별로 함수를 호출하기 위해서 DeleGate를 설정해준다.<br/>

반환 타입은 void인 UI의 멤버 함수이고, 받는 인자는 없다.<br/>

```
typedef void (UI::* UI_DELEGATE_1)(DWORD_PTR);
typedef void (UI::* UI_DELEGATE_2)(DWORD_PTR, DWORD_PTR);
```
이런 식으로 여러종류를 지원하도록 한다.
<Br/>

그리고 ListUI의 헤더파일에서 멤버함수의 실 소유객체를 가리키는 변수를 만들어주고,<br/>
선택된 아이템 문자열을 주는 Delegate함수의 변수를 설정한다.

```
    UI*             m_SelectInst;
    UI_DELEGATE_1   m_SelectDelegate;
```

그리고 등록을 해주는 함수, Set하는 함수도 설정해준다.<br/>
```
public:
    void AddDynamic_Select(UI* _Inst, UI_DELEGATE_1 _Func)
    {
        m_SelectInst = _Inst;
        m_SelectDelegate = _Func;
    }
```
<Br/>

자 이제 매칭시켜보자.

MeshRenderUI에서<br/>

```
// 헤더파일

public:
    void SelectMesh(DWORD_PTR _Key);
    void SelectMaterial(DWORD_PTR _Key);
```

```
// cpp파일 

void MeshRenderUI::SelectMesh(DWORD_PTR _Key)
{
	string strKey = (char*)_Key;
	Ptr<CMesh> pMesh = CResMgr::GetInst()->FindRes<CMesh>(wstring(strKey.begin(), strKey.end()));
	GetTarget()->MeshRender()->SetMesh(pMesh);
}

void MeshRenderUI::SelectMaterial(DWORD_PTR _Key)
{
	string strKey = (char*)_Key;
	Ptr<CMaterial> pMtrl = CResMgr::GetInst()->FindRes<CMaterial>(wstring(strKey.begin(), strKey.end()));
	GetTarget()->MeshRender()->SetMaterial(pMtrl);
}
```
MeshRenderUI는 UI를 상속받은 관계이므로 설정이 가능하다.<br/>

다만, AddDynamic_Select가 받는 함수가 UI의 멤버함수이다 보니 등록하는 과정에서 UI포인터로 강제캐스팅을 해주어야한다.<br/>
<br/>

```
// ListUI.cpp 중에서

// Select Delegate 가 등록되어있다면 호출해 준다.
if (m_SelectInst && m_SelectDelegate)
{
    (m_SelectInst->*m_SelectDelegate)((DWORD_PTR)m_vecStrData[i].c_str());
}
```

그리고 Select Delegate가 등록되어있을 경우에만 호출하도록 조치한다.<br/>

<br/>

```
// MestRenderUI.cpp 중에서 SelectMesh 함수

void MeshRenderUI::SelectMesh(DWORD_PTR _Key)
{
	string strKey = (char*)_Key;
	Ptr<CMesh> pMesh = CResMgr::GetInst()->FindRes<CMesh>(wstring(strKey.begin(), strKey.end()));
	GetTarget()->MeshRender()->SetMesh(pMesh);
}
```

이제 위의 함수를 넣어줘서 메쉬를 바꾸어준다.<br/>
<br/>

```
// MestRenderUI.cpp 중에서 SelectMaterial 함수

void MeshRenderUI::SelectMaterial(DWORD_PTR _Key)
{
	string strKey = (char*)_Key;
	Ptr<CMaterial> pMtrl = CResMgr::GetInst()->FindRes<CMaterial>(wstring(strKey.begin(), strKey.end()));
	GetTarget()->MeshRender()->SetMaterial(pMtrl);
}
```

마찬가지로, 재료도 바꿔줄 수 있다.<br/>
<br/>

## Draw Call<br/>
<br/>

한 프레임에 들어가는 렌더링 횟수이다.<br/>
이를 줄일 필요가 있다.<br/>

사례를 보자. 스타크래프트에서 저글링이 40마리가 있다고 할 때,<br/>
각기 다른 저글링을 렌더링 하기 위해서 Draw Call이 40회 발생한다.<br/>

그런데 만일 재질을 공유하는 방식으로 설계했다면?<br/>
40마리의 저글링이 모두 같은 재질을 참조하도록 했다면?<br/>
재질을 공유하며, 같은 메쉬를 사용한다면 UpdateData를 한번만 하고 GPU에 전달하면 된다.<br/>

또한, CPU와 GPU 간의 데이터 전송 등의 상호작용을 가능한 줄여야 한다.<br/>
이를 위해 GPU의 Instancing 기능을 활용할 수 있다.<br/>
많은 양의 데이터 전송을 하고 일괄적으로 GPU에서 처리를 하고 다음 데이터를 받는 것이다.<br/>

이를 위한 조건이 재질과 텍스쳐가 같아야 한다는 것인데 조건이 맞다.<br/>
우리는 재질이 같으면 최대한 공유하도록 조치를 해주면 된다.<br/>

그리고 한 객체만 다른 효과를 일시적으로 받고 싶을 때 일시적으로 다른 재질을 가지도록 조치하면 된다.<br/>

```
// CRenderComponent.h 중에서

private:
    Ptr<CMaterial>  m_pSharedMtrl; // 원본 재질
    Ptr<CMaterial>  m_pDynamicMtrl;// 원본 재질 복사본
    Ptr<CMaterial>  m_pCurrentMtrl; // 현재 사용중인 재질

public:
    Ptr<CMaterial> GetSharedMaterial() { return m_pSharedMtrl; }
    Ptr<CMaterial> GetDynamicMaterial(); // cpp에 따로 구현
    Ptr<CMaterial> GetMaterial() { return m_pCurrentMtrl; } 
```
그래서 이제는 재질을 3가지로 분류해서 관리할 것이다.<br/>

```
// CRenderComponent.cpp

Ptr<CMaterial> CRenderComponent::GetDynamicMaterial()
{
	// 원본 재질이 없다 -> Nullptr 반환
	if (nullptr == m_pSharedMtrl)
	{
		m_pCurrentMtrl = nullptr;
		return m_pCurrentMtrl;
	}

	// 동적 재질 최초 요청시 제작 해서 준다.
	if (nullptr == m_pDynamicMtrl)
	{		
		m_pDynamicMtrl = m_pSharedMtrl->Clone();		
	}

	// 동적 재질을 현재 사용재질로 지정한다.
	m_pCurrentMtrl = m_pDynamicMtrl;


	return m_pCurrentMtrl;
}

```

SetMaterial들은 따라서 위와 같이 사용할 수 있다.<br/>
<br/>

## CRes의 복사생성자 구현<br/>
<br/>

안정적인 동적재질(DynamicMaterial)을 사용하기 위해서 Res클래스의 복사생성자를 구현할 필요가 있다.

```
// CRes.h 파일 중에서

public:
    CRes(RES_TYPE _type);
    CRes(const CRes& _Other);
    virtual ~CRes();
```

```
// CRes.cpp 파일 중에서

CRes::CRes(const CRes& _Other)
	: CEntity(_Other)
	, m_Type(_Other.m_Type)
	, m_iRefCount(0)
	, m_strKey(_Other.m_strKey)
	, m_strRelativePath(_Other.m_strRelativePath)
{
}
```

복사 생성자를 통해 레퍼런스 카운트를 0으로 초기화 해줘야한다.<br/>
