---
layout: post
title:  "[DirectX] IMGUI - Outliner"
subtitle:  
date: 2023-02-18 21:08:14 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] IMGUI - Outliner<br/>
<br/>

Imgui를 이용해서 Inspector에 선택사항을 표시하는 UI를 설계해보자.<br/>
<Br/>

## OutlinerUI 클래스의 구성<br/>
<Br/>

우선 OutlinerUI의 클래스를 아래와 같이 구성해준다.<br/>

```
// OutlinerUI.h

#pragma once
#include "UI.h"

class TreeUI;

class OutlinerUI :
    public UI
{
private:
    TreeUI* m_Tree;

public:
    virtual void tick() override;
    void ResetOutliner();


public:
    virtual int render_update() override;

public:
    OutlinerUI();
    ~OutlinerUI();
};


```

```
// OutlinerUI.cpp

#include "pch.h"
#include "OutlinerUI.h"

#include <Engine\CEventMgr.h>
#include <Engine\CLevelMgr.h>
#include <Engine\CLevel.h>
#include <Engine\CLayer.h>
#include <Engine\CGameObject.h>

#include "TreeUI.h"

OutlinerUI::OutlinerUI()
    : UI("##Outliner")
	, m_Tree(nullptr)
{
    SetName("Outliner");

	// OutlinerUI 안에 자식으로 Tree 를 추가한다.
	m_Tree = new TreeUI;
	m_Tree->SetName("OutlinerTree");
	m_Tree->SetActive(true);
	m_Tree->ShowRoot(false);
	AddChildUI(m_Tree);
}

OutlinerUI::~OutlinerUI()
{
}

int OutlinerUI::render_update()
{
    return 0;
}

void OutlinerUI::tick()
{
	if (CEventMgr::GetInst()->IsLevelChanged())
	{
		ResetOutliner();
	}
}

void OutlinerUI::ResetOutliner()
{
	// Tree Clear
	m_Tree->Clear();
	m_Tree->AddItem("Root", 0);

	// 리소스 매니저에서 현재 모든 리소스 목록 받아옴
	CLevel* pCurLevel = CLevelMgr::GetInst()->GetCurLevel();

	for (UINT i = 0; i < (UINT)MAX_LAYER; ++i)
	{
		CLayer* pLayer = pCurLevel->GetLayer(i);

		const vector<CGameObject*>& vecParentObj = pLayer->GetParentObject();

		for (size_t i = 0; i < vecParentObj.size(); ++i)
		{
			m_Tree->AddItem(   string(vecParentObj[i]->GetName().begin()
									, vecParentObj[i]->GetName().end())
				            ,  (DWORD_PTR)vecParentObj[i]);
		}
	}
}

```

그리고 생성은 아래와 같이 해준다.<br/>

```
// ImGuiMgr.cpp 중에서

    // Outliner
    pUI = new OutlinerUI;
    pUI->SetActive(true);
    m_mapUI.insert(make_pair(pUI->GetID(), pUI));
```


<Br/>

## 레벨의 물체 정보 받아오기<br/>
<Br/>

Outliner는 레벨에 있는 물체의 주변에 아웃라인을 형성시키는 함수이므로 레벨매니저로부터 레벨에 있는 물체에 대한 정보를 받아와야한다.<br/>

```
// Outliner.cpp의 ResetOutliner 함수 중에서

void OutlinerUI::ResetOutliner()
{
	// Tree Clear
	m_Tree->Clear();
	m_Tree->AddItem("Root", 0);

	// 리소스 매니저에서 현재 모든 리소스 목록 받아옴
	CLevel* pCurLevel = CLevelMgr::GetInst()->GetCurLevel();

	for (UINT i = 0; i < (UINT)MAX_LAYER; ++i)
	{
		CLayer* pLayer = pCurLevel->GetLayer(i);

		const vector<CGameObject*>& vecParentObj = pLayer->GetParentObject();

		for (size_t i = 0; i < vecParentObj.size(); ++i)
		{
			m_Tree->AddItem(   string(vecParentObj[i]->GetName().begin()
									, vecParentObj[i]->GetName().end())
				            ,  (DWORD_PTR)vecParentObj[i]);
		}
	}
}
```

<br/>

그리고, EventMgr로부터 Level에서 변경점이 있었는지 여부도 받아와야하며, EventMgr에서도 이를 지정하는 변수와 함수가 있어야한다.<br/>

```
// CEventMgr.h 중에서

private:
    bool                    m_LevelChanged;

public:
    bool IsLevelChanged() { return m_LevelChanged; }
```

```
// CEventMgr.cpp 중에서

void CEventMgr::tick()
{
	m_LevelChanged = false;

	GC_Clear();

	for (size_t i = 0; i < m_vecEvent.size(); ++i)
	{
		switch (m_vecEvent[i].Type)
		{
		// wParam : GameObject, lParam : Layer Index
		case EVENT_TYPE::CREATE_OBJECT:
		{
			CGameObject* NewObject = (CGameObject*)m_vecEvent[i].wParam;
			int iLayerIdx = (int)m_vecEvent[i].lParam;
			CLevelMgr::GetInst()->GetCurLevel()->AddGameObject(NewObject, iLayerIdx, false);

			m_LevelChanged = true;
		}
			break;
		case EVENT_TYPE::DELETE_OBJECT:
		{
			CGameObject* DeleteObject = (CGameObject*)m_vecEvent[i].wParam;

			if (false == DeleteObject->m_bDead)
			{
				DeleteObject->m_bDead = true;
				m_vecGC.push_back(DeleteObject);
			}

			m_LevelChanged = true;
		}
			break;
		case EVENT_TYPE::ADD_CHILD:

			m_LevelChanged = true;
			break;
		case EVENT_TYPE::DELETE_RESOURCE:
		}
	}

	m_vecEvent.clear();
}

```
위의 EventMgr의 tick함수에서<br/>

Level에서 변경점이 생길때마다 m_LevelChanged 변수를 true로 지정해준다.<br/>
<br/>

## 선택된 객체를 Inspector에 표시하는 기능 만들기<br/>
<br/>

Delegate를 활용한다.<br/>

```
// OutlinerUI.h

#pragma once
#include "UI.h"

class TreeUI;

class OutlinerUI :
    public UI
{
private:
    TreeUI* m_Tree;

public:
    virtual void tick() override;
    virtual int render_update() override;

public:
    void ResetOutliner();
    void SetTargetToInspector(DWORD_PTR _SelectedNode);

public:
    OutlinerUI();
    ~OutlinerUI();
};

```
위의 코드 중에서

```
void SetTargetToInspector(DWORD_PTR _SelectedNode);
```

인스펙터에게 알려줄 함수를 구현할 것이다.<br/>

```
// OutlinerUI.cpp 중에서

void OutlinerUI::SetTargetToInspector(DWORD_PTR _SelectedNode)
{

}
```

그리고 Delegate를 등록해놓기 위해서 생성자 함수에 다음 코드를 추가한다.<br/>

```
m_Tree->AddDynamic_Select(this, &OutlinerUI::SetTargetToInspector);
```
<br/>
<br/>

한편, TreeUI에서는 멤버변수로
```
UI_DELEGATE_1   m_SelectFunc;
```
을 추가해준다.<br/>

그리고 멤버함수로

```
public:
    void AddDynamic_Select(UI* _UI, UI_DELEGATE_1 _MemFunc)
    {
        m_SelectInst = _UI;
        m_SelectFunc = _MemFunc;
    }
```
을 추가해준다.<br/>

그리고 선택된 노드를 표시해주기 위해서<br/>

```
void TreeUI::SetSelectedNode(TreeNode* _Node)
{
    if (m_SelectedNode)
        m_SelectedNode->m_Hilight = false;
    
    m_SelectedNode = _Node;

    if (m_SelectedNode)
    {
        m__SelectedNode->m_Hilight = true;
        
        if (m_SelectInst && m_SelectFunc)
        {
            (m_SelectInst->*m_SelectFunc)((DWORD_PTR)m_SelectedNode);
        }
    }
}

```
위의 함수를 추가한다.<br/>

Delegate가 세팅되어있을 경우, 노드에 있는 Delegate함수를 호출하는 것이다.<br/>

그리고 Inspector에게 데이터를 전달하기 위해서 OutlinerUI.cpp에 아래 함수를 구현한다.<br/>

```
// OutlinerUI.cpp 중에서

void OutlinerUI::SetTargetToInspector(DWORD_PTR _SelectedNode)
{
    // 무엇이 선택됐는지 값을 불러오자.
    TreeNode* pSelectedNode = (TreeNode*)_SelectedNode;
    CGameObject* pSelectObject = (CGameObject*)pSelectedNode->GetData();

    // Inspector에 선택된 GameObject를 알려준다.
    ImGuiMgr::GetInst()->FindUI("Inspector");
    InspectorUI* pInspector = (InspectorUI*)ImGuiMgr::GetInst()->FindUI("##Inspector");
    pInspector->SetTargetObject(pSelectObject);
}

```

새로운 타겟을 설정해주기 위해서 InspectorUI.cpp에서 아래 함수를 구현해준다.<br/>

```
//InspectorUI.cpp 중에서 SetTargetObject함수

void InspectorUI::ClearTargetObject()
{
	// 타겟오브젝트 정보 노출
	m_pTargetObj = nullptr;

	for (UINT i = 0; i < (UINT)COMPONENT_TYPE::END; ++i)
	{
		if (nullptr == m_arrComUI[i])
			continue;

		m_arrComUI[i]->SetTarget(nullptr);
	}
}
```

그리고 ComponentUI.cpp 에서도 SetTarget함수를 수정해준다.
```
// ComponentUI.cpp 중에서 SetTarget함수

void ComponentUI::SetTarget(CGameObject* _Target)
{
	m_Target = _Target;

	if (nullptr == m_Target)
	{
		SetActive(false);
		return;
	}

	if (nullptr == m_Target->GetComponent(m_Type))
	{
		SetActive(false);
	}
	else
	{
		SetActive(true);
	}
}

```
<br/>
