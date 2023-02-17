---
layout: post
title:  "[DirectX] IMGUI - Content, Tree"
subtitle:  
date: 2023-02-16 20:15:16 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] IMGUI - Content, Tree<br/>
<br/>

Imgui를 이용해서 콘텐츠를 표시하는 UI를 설계해보자.<br/>
<Br/>

## ContentUI 클래스의 구성<br/>
<Br/>

우선 ContentUI의 클래스를 아래와 같이 구성해준다.<br/>

```
// ContentUI.h 파일

#pragma once
#include "UI.h"

class TreeUI;

class ContentUI :
    public UI
{
private:
    TreeUI*     m_Tree;

public:
    virtual int render_update() override;

public:
    void ResetContent();

public:
    ContentUI();
    ~ContentUI();
};


```

```
// ContentUI.cpp 파일

#include "pch.h"
#include "ContentUI.h"

#include <Engine\CResMgr.h>
#include "TreeUI.h"

ContentUI::ContentUI()
    : UI("##Content")
{
    SetName("ContentUI");

}

ContentUI::~ContentUI()
{

}


int ContentUI::render_update()
{
  
    return 0;
}

void ContentUI::ResetContent()
{
    // Tree Clear
    m_Tree->Clear();

    // 리소스 매니저에서 현재 모든 리소스 목록 받아옴

    // m_Tree 에 현재 리소스 목록을 AddItem

}


```


그리고 ContentUI.cpp에서 컨텐츠를 표시하기 위해서 Imgui의 트리구조(TreeNodeEX)를 활용할 것이다.<br/>

```
if (ImGui::TreeNodeEx("Parent1", flag))
{

    if (ImGui::TreeNodeEX("Child", flag))
    {
        // ...
        // 코드 입력
        // ...

        ImGui::TreePop();
    }

    // ...
    // 코드 입력
    // ...

    ImGui::TreePop();
}

if (ImGui::TreeNodeEx("Parent2", flag))
{

    // ...
    // 코드 입력
    // ...

    ImGui::TreePop();
}

```
트리노드 구조는 위의 구조를 통해서 생성을 할 수 있다.<br/>

하지만 트리의 개수는 계속해서 변할 수 있다.<br/>

따라서 Tree구조를 만들어주는 클래스를 새로 생성할 것이다.<br/>

이는 후술할 것이다.<br/>
<br/>

그리고 ContentUI의 활성화를 위해서 객체를 생성해준다.<br/>

```
// ImGuiMgr.cpp의 CreateUI 함수 중에서

    // ContentUI
    pUI = new ContentUI;
    pUI->SetActive(true);
    m_mapUI.insert(make_pair(pUI->GetID(), pUI));


```
<br/>


## TreeUI 클래스의 구성<br/>
<Br/>


```
// TreeUI.h

#pragma once
#include "UI.h"

class TreeUI :
    public UI
{
private:
    TreeNode*   m_RootNode; // 트리가 소유하고 있는 노드 중 루트 노드
    UINT        g_NextId;   // 생성되는 노드뒤에 붙여줄 고유 숫자

    bool        m_bShowRoot;

public:
    virtual int render_update() override;
public:
    void Clear();
    TreeNode* AddItem(const string& _strNodeName, DWORD_PTR _Data, TreeNode* _pParent = nullptr);
    void ShowRoot(bool _Show) { m_bShowRoot = _Show; }

public:
    TreeUI();
    ~TreeUI();
};


```

```
// TreeUI.cpp

#include "pch.h"
#include "TreeUI.h"

TreeUI::TreeUI()
    : UI("##Tree")
    , m_RootNode(nullptr)
    , g_NextId(0)
    , m_bShowRoot(true)
{   

}

TreeUI::~TreeUI()
{
    if (nullptr != m_RootNode)
        delete m_RootNode;
}

int TreeUI::render_update()
{
    if (nullptr != m_RootNode)
    {
        if (m_bShowRoot)
        {
            m_RootNode->render_update();
        }
        else
        {
            for (size_t i = 0; i < m_RootNode->m_vecChildNode.size(); ++i)
            {
                m_RootNode->m_vecChildNode[i]->render_update();
            }
        }
    }

    return 0;
}

void TreeUI::Clear()
{
    if (nullptr != m_RootNode)
    {
        delete m_RootNode;
        m_RootNode = nullptr;
    }     
}

TreeNode* TreeUI::AddItem(const string& _strNodeName, DWORD_PTR _Data, TreeNode* _pParent)
{    
    TreeNode* pNewNode = new TreeNode;

    pNewNode->m_Owner = this;
    pNewNode->m_strName = _strNodeName;
    pNewNode->m_Data = _Data;
    pNewNode->m_ID = g_NextId++;

    // 루트가 NULL 이다 ==> 트리에 들어온 최초의 데이터
    if (nullptr == m_RootNode)
    {
        // 최초 데이터 입력인데, 부모를 지정한 경우
        assert(!_pParent);

        m_RootNode = pNewNode;
    }

    // 트리에 들어온 데이터가 최초가 아니다.
    else
    {
        if (_pParent)
        {
            // 노드의 부모로 지정된 노드가 해당 트리 소속이 아니다.
            if (_pParent->m_Owner != this)
                assert(nullptr);

            // 지정된 부모의 자식으로 연결
            _pParent->m_vecChildNode.push_back(pNewNode);
            pNewNode->m_ParentNode = _pParent;
        }

        // 부모로 지정된 노드가 없는경우, 루트 밑으로 넣는다
        else
        {
            // 새로 생성한 노드를 루트노드의 자식으로 연결
            m_RootNode->m_vecChildNode.push_back(pNewNode);
            pNewNode->m_ParentNode = m_RootNode;            
        }
    }

    return pNewNode;
}
```
<br/>

AddItem을 할 때 트리노드의 고유한 ID값과 데이터의 주소값, 그리고 목적지에 해당하는 Parent Node의 주소(_pParent)를 같이 줄 것이다.<br/>

Parent Node는 기본적으로 nullptr값을 줄 것이다. 이는 Root노드의 역할을 하도록 한다.<br/>

이렇게 하는 이유는 같은 이름을 가진 오브젝트가 있을 수 있고, 또한 선택을 하면 어떤 오브젝트를 클릭했는지를 주소값을 통해서 알 수 있기 때문이다.<br/>

그리고 잘 만들어진 노드(TreeNode)에 대한 주소를 반환할 수 있도록 만든다.<br/>
<br/>

주의사항 (assert 지정)
- Tree가 여러개일 경우 원하는 트리가 아니라 다른 트리에 넣고 있지 않은지?
- Root가 지정이 되지 않은, 최초의 데이터 입력인데 부모를 지정한 경우인지?

<br/>

ShowRoot는 루트 노드를 보여줄지 말지를 결정하는 함수이다.<br/>

1. 아래와 같은 형식으로 보이고 싶을 수도 있고,

- 루트
    + 차일드1
    + 차일드2

2. 아래와 같이 보이고 싶을 때도 있을 것이다.
- 차일드1
- 차일드2

이때, ShowRoot를 켜면 1번처럼, 끄면 2번처럼 출력되게 만들 수 있다.<br/>

<br/>

## TreeNode 클래스<br/>
<Br/>

TreeUI는 friend class로 TreeNode 클래스를 가진다.<br/>

```
// 헤더


// =========
// TreeNode
// =========
class TreeUI;
class TreeNode
{
private:
    TreeUI*             m_Owner;        // 노드를 소유하고 있는 트리
    TreeNode*           m_ParentNode;   // 부모노드
    vector<TreeNode*>   m_vecChildNode; // 노드의 자식 노드
        
    string              m_strName;      // 노드의 출력 이름
    UINT                m_ID;           // 노드의 고유 ID

    DWORD_PTR           m_Data;         // 노드에 저장된 데이터


private:
    void render_update();

public:
    TreeNode();
    ~TreeNode();

    friend class TreeUI;
};


```

```
// cpp

// ========
// TreeNode
// ========
TreeNode::TreeNode()
    : m_Owner(nullptr)
    , m_ParentNode(nullptr)
{

}

TreeNode::~TreeNode()
{
    Safe_Del_Vec(m_vecChildNode);
}

void TreeNode::render_update()
{
    // FinalName 만들기
    string strFinalName = m_strName;
    strFinalName += "##";

    char szBuff[100] = {};
    itoa(m_ID, szBuff, 10);
    strFinalName += szBuff;

    ImGuiTreeNodeFlags_ flag = ImGuiTreeNodeFlags_::ImGuiTreeNodeFlags_None;

    if (ImGui::TreeNodeEx(strFinalName.c_str(), flag))
    {
        for (size_t i = 0; i < m_vecChildNode.size(); ++i)
        {
            m_vecChildNode[i]->render_update();
        }

        ImGui::TreePop();
    }

}

```


트리를 구성하는 노드 하나를 TreeNode라는 클래스로 지정하자.<br/>

그리고 트리가 알아야하는 정보인 RootNode를 변수로 지정해준다.<br/>

루트노드가 render update를 호출하면 자식의 render update를 호출할 수 있도록 설계를 해주는 것이다.<br/>
<br/>

```
void TreeNode::render_update()
{
    // FinalName 만들기
    string strFinalName = m_strName;
    strFinalName += "##";

    char szBuff[100] = {};
    itoa(m_ID, szBuff, 10);
    strFinalName += szBuff;

    ImGuiTreeNodeFlags_ flag = ImGuiTreeNodeFlags_::ImGuiTreeNodeFlags_None;

    if (ImGui::TreeNodeEx(strFinalName.c_str(), flag))
    {
        for (size_t i = 0; i < m_vecChildNode.size(); ++i)
        {
            m_vecChildNode[i]->render_update();
        }

        ImGui::TreePop();
    }

}
```

또한, 트리노드가 어떤 트리에 속해있는지를 알 수 있어야 하므로 변수를 설정해준다.<br/>
```
private:
    TreeUI*     m_Owner; // 노드를 소유하고 있는 트리
```

그리고 트리노드는 자식 트리노드를 가지고 있으므로 이를 저장하는 변수를 설정해준다.<br/>
```
private:
    vector<TreeNode*>   m_vecChildNode;
```
<br/>


## Tree 생성하기<br/>
<br/>
ID는 ##Tree로 넣되, Tree 별 Name은 사용을 해주는 Content에서 설정해주어야 한다.<br/>

```
// ContentUI.cpp 중에서 생성자 함수

TreeUI* pTreeUI = new TreeUI;
pTreeUI->SetName("ContentTree");
pTreeUI->SetActive(true);
AddChildUI(pTreeUI);

// 루트 생성
pTreeUI->AddItem("Root Node", 0);

// 루트 밑에 자식 2개 추가
TreeNode* pChild1Node = pTreeUI->AddItem("Child1", 0);
pTreeUI->AddItem("Child2", 0);

// Child1 밑에 자식 2개 추가
pTreeUI->AddItem("ChildChild1" , 0, pChild1Node);
pTreeUI->AddItem("ChildChild2" , 0, pChild1Node);
```

<br/>

## Content에 리소스 구성하기<br/>
<Br/>

define.h 에서 리소스 타입에 따라서 enum class 값을 지정하였다.<br/>

하지만 트리UI에서 사용하려면 이름을 문자열로 바꿔줘야할 것 이다.<br/>

define.h와 extern.cpp를 통해 작업을 해주자.<br/>

```
// define.h 중에서

enum class RES_TYPE
{
	MESHDATA,
	MATERIAL,
	PREFAB,

	MESH,			// 형태
	TEXTURE,		// 이미지
	SOUND,

	GRAPHICS_SHADER,
	COMPUTE_SHADER,

	END,
};

extern const char* RES_TYPE_STR[(UINT)RES_TYPE::END];
extern const wchar_t* RES_TYPE_WSTR[(UINT)RES_TYPE::END];

```
그리고 실제 문자열의 구현은 extern.cpp에서 해준다.<br/>

```
// extern.cpp 중에서

extern const char* RES_TYPE_STR[(UINT)RES_TYPE::END] =
{
	"MESHDATA",
	"MATERIAL",
	"PREFAB",
	"MESH",
	"TEXTURE",
	"SOUND",
	"GRAPHICS_SHADER",
	"COMPUTE_SHADER"
};

extern const wchar_t* RES_TYPE_WSTR[(UINT)RES_TYPE::END] =
{
	L"MESHDATA",
	L"MATERIAL",
	L"PREFAB",
	L"MESH",
	L"TEXTURE",
	L"SOUND",
	L"GRAPHICS_SHADER",
	L"COMPUTE_SHADER"
};

```
이렇게 하면 해당 enum 값에 해당하는 문자열을 매칭시킬 수 있다.<br/>

```
// func.h 중에서

const char* ToString(RES_TYPE);
const wchar_t* ToWString(RES_TYPE);

// func.cpp 중에서

const char* ToString(RES_TYPE type)
{
    return RES_TYPE_STR[type];
}
const wchar_t* ToWString(RES_TYPE type)
{
    return RES_TYPE_WSTR[type];
}
```

그리고 func 파일에 담아서 함수호출을 통해서 문자열로 변환할 수 있도록 만든다.<br/>


계속해서, ContentUI.cpp에서 리소스를 받아오는 작업을 해보자.<br/>
```
// ContentUI.cpp 중에서

void ContentUI::ResetContent()
{
    // Tree Clear
    m_Tree->Clear();
    m_Tree->AddItem("Root", 0);

    for (size_t i = 0; i < (UINT)RES_TYPE::END; ++i)
    {
        const map<wstring, Ptr<CRes>>& mapRes = CResMgr::GetInst()->GetResources((RES_TYPE)i);

        // m_Tree 에 현재 리소스 목록을 AddItem
        TreeNode* pCategory = m_Tree->AddItem(ToString((RES_TYPE)i), 0);

        for (const auto& pair : mapRes)
        {
            m_Tree->AddItem(string(pair.first.begin(), pair.first.end()), (DWORD_PTR)pair.second.Get(), pCategory);
        }
    }
}
```

<br/>

## 기타 꾸미기 기능<br/>
<Br/>

자신이 리프노드일경우 (자식이 없을 경우) 펼치기, 접기 기능을 하는 화살표가 표시되지 않도록 만들어보자.<br/>

```
// TreeUI.cpp 중에서

UINT flag = ImGuiTreeNodeFlags_::ImGuiTreeNodeFlags_None;

if (m_vecChildNode.empty())
{
    flag |= ImGuiTreeNodeFlags_Leaf;
}
```

ImGuiTreeNodeFlags_Leaf 를 flag에 달게 된다면 펼치기, 접기 표시를 하는 화살표가 활성화되지 않는다.<br/>

자식노드가 없다는 것은 m_vecChildNode의 길이가 0이거나 비어있다는 것이므로 해당 조건이 맞을 경우 옵션을 추가해준다.<br/>
<br>
<br/>

카테고리 노드에만 하이라이트를 넣어보자.<br/>

```
// TreeUI.h 중에서

bool                m_CategoryNode; // 항목 대표 노드
bool                m_Hilight;      // 노드 하이라이트 처리

public:
    void SetCategoryNode(bool _category)
    {
        m_CategoryNode = _category;
    }

```
자신이 카테고리노드인지 여부를 저장하는 멤버변수와 하이라이트를 쳐줘야 하는지 여부를 저장하는 멤버변수 그리고 자신이 카테고리노드인지 여부를 세팅 해주는 함수를 추가한다.<br/>

<br/>

```
// ContentUI.cpp 중에서

void ContentUI::ResetContent()
{
    // Tree Clear
    m_Tree->Clear();
    m_Tree->AddItem("Root", 0);

    for (size_t i = 0; i < (UINT)RES_TYPE::END; ++i)
    {
        const map<wstring, Ptr<CRes>>& mapRes = CResMgr::GetInst()->GetResources((RES_TYPE)i);

        // m_Tree 에 현재 리소스 목록을 AddItem
        TreeNode* pCategory = m_Tree->AddItem(ToString((RES_TYPE)i), 0);
        pCategory->SetCategoryNode(true);

        for (const auto& pair : mapRes)
        {
            m_Tree->AddItem(string(pair.first.begin(), pair.first.end()), (DWORD_PTR)pair.second.Get(), pCategory);
        }
    }
}
```

```
// TreeUI.cpp 중에서

if (m_Hilight || m_CategoryNode)
    flag |= ImGuiTreeNodeFlags_Selected;
```

여기서, m_Hilight는 자신이 선택되었는지 여부를 나타내는 bool 멤버변수이고,<br/>

m_CategoryNode는 자신이 카테고리 노드인지 여부를 나타내는 bool 멤버변수이다.<br/>