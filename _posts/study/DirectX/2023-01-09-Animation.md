---
layout: post
title:  "[DirectX] 2D 애니메이션"
subtitle:  
date: 2023-01-09 21:39:55 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] 2D 애니메이션<br/>
<br/>

컴포넌트를 상속받아서 2D 애니메이션 컴포넌트를 구성해보자.<br>
<br/>

## CAnimator2D 클래스의 구성<br/>
<Br/>

```
// 헤더 파일

#pragma once
#include "CComponent.h"

#include "ptr.h"
#include "CTexture.h"

class CAnim2D;

class CAnimator2D :
    public CComponent
{
private:
    map<wstring, CAnim2D*>  m_mapAnim;  // Animation 목록
    CAnim2D*                m_pCurAnim; // 현재 재생중인 Animation
    bool                    m_bRepeat;  // 반복


public:
    virtual void finaltick() override;


public:
    void CreateAnimation(const wstring& _strAnimName, Ptr<CTexture> _AtlasTex, Vec2 _vLeftTop, Vec2 _vSlice, int _FrameCount, int _FPS);

    CLONE(CAnimator2D);
public:
    CAnimator2D();
    ~CAnimator2D();
};

```

```
// cpp 파일

#include "pch.h"
#include "CAnimator2D.h"

#include "CAnim2D.h"

CAnimator2D::CAnimator2D()
	: CComponent(COMPONENT_TYPE::ANIMATOR2D)
{
}

CAnimator2D::~CAnimator2D()
{
}

void CAnimator2D::finaltick()
{
}

void CAnimator2D::CreateAnimation(const wstring& _strAnimName
	, Ptr<CTexture> _AtlasTex, Vec2 _vLeftTop, Vec2 _vSlice
	, int _FrameCount, int _FPS)
{
	CAnim2D* pAnim = new CAnim2D;
	pAnim->Create(_strAnimName, _AtlasTex, _vLeftTop, _vSlice, _FrameCount, _FPS);

	pAnim->m_pOwner = this;
	m_mapAnim.insert(make_pair(_strAnimName, pAnim));
}
```
<Br/>

그리고 CComponent와 CGameObject에도 Animator2D를 연동시켜준다.<br/>

```
// CComponent 헤더

public:
    GET_OTHER_COMPONENT(Animator2D);
```

```
// CGameObject 헤더

public:
    GET_COMPONENT(Animator2D, ANIMATOR2D);
```
<br/>

### CreateAnimation 함수<br/>
<br/>

```
void CAnimator2D::CreateAnimation(const wstring& _strAnimName
	, Ptr<CTexture> _AtlasTex, Vec2 _vLeftTop, Vec2 _vSlice
	, int _FrameCount, int _FPS)
{
	CAnim2D* pAnim = new CAnim2D;
	pAnim->Create(_strAnimName, _AtlasTex, _vLeftTop, _vSlice, _FrameCount, _FPS);

	pAnim->m_pOwner = this;
	m_mapAnim.insert(make_pair(_strAnimName, pAnim));
}

```

타일 맵에서 사용했던 기법처럼

- 잘라낼 부분의 좌상단과 이미지 슬라이스의 길이를 알경우
- 각 프레임 이미지가 일정한 간격으로 정렬되어 있을 경우

사용한다.<br/>
<br/>

### FindAnim 함수<br/>
<Br/>

```
CAnim2D* CAnimator2D::FindAnim(const wstring& _strName)
{
	map<wstring, CAnim2D*>::iterator iter = m_mapAnim.find(_strName);

	if (iter == m_mapAnim.end())
	{
		return nullptr;
	}

	return iter->second;
}
```

map으로 관리하고있는 자료구조에서 key값을 통해 저장된 애니메이션을 찾아내고 반환하는 구조이다.<br/>

이는 Play함수로 반환된다.<br/>

<br/>

### Play 함수<br/>
<Br/>

```
void CAnimator2D::Play(const wstring& _strName, bool _bRepeat)
{
	CAnim2D* pAnim = FindAnim(_strName);
	assert(pAnim);

	m_pCurAnim = pAnim;
	m_bRepeat = _bRepeat;
}
```
상기한 FindAnim 함수를 통해 찾은 애니메이션을 재생한다.<br/>


<Br/>


## CAnim2D 클래스의 구성<br/>
<br/>

그리고 애니메이션을 구성하는 클래스를 생성해준다.
Animator 클래스가 구성된 애니메이션을 작동시키기 위한 장치라면, 이 클래스는 애니메이션을 구성한다.<br/>

```
// 헤더파일

#pragma once
#include "CEntity.h"

#include "ptr.h"
#include "CTexture.h"

class CAnimator2D;

class CAnim2D :
    public CEntity
{
private:
    CAnimator2D*        m_pOwner;
    vector<tAnim2DFrm>  m_vecFrm;
    Ptr<CTexture>       m_AtlasTex;

    int                 m_iCurFrm;

public:
    void finaltick();
    void Create(const wstring& _strAnimName, Ptr<CTexture> _AtlasTex, Vec2 _vLeftTop, Vec2 _vSlice, int _FrameCount, int _FPS);


    CLONE(CAnim2D);
public:
    CAnim2D();
    ~CAnim2D();

    friend class CAnimator2D;
};
```

```
// cpp 파일

#include "pch.h"
#include "CAnim2D.h"

CAnim2D::CAnim2D()
	: m_pOwner(nullptr)
{
}

CAnim2D::~CAnim2D()
{
}

void CAnim2D::finaltick()
{

}

void CAnim2D::Create(const wstring& _strAnimName, Ptr<CTexture> _AtlasTex
	, Vec2 _vLeftTop, Vec2 _vSlice, int _FrameCount, int _FPS)
{
	assert(_AtlasTex.Get());

	SetName(_strAnimName);

	m_AtlasTex = _AtlasTex;

	for (size_t i = 0; i < _FrameCount; ++i)
	{
		tAnim2DFrm frm = {};

		frm.fDuration = 1.f / (float)_FPS;
		frm.LeftTopUV = Vec2(_vLeftTop.x + _vSlice.x * i, _vLeftTop.y);
		frm.SliceUV = _vSlice;

		m_vecFrm.push_back(frm);
	}
}
```
<br/>

### Create 함수<br/>
<br/>

애니메이터에는 CreateAnimation 함수가 있다면 Anim2D에는 Create 함수가 있다. 애니메이션을 만드는 기능을 한다.<br/>
<br/>

```
void CAnim2D::Create(const wstring& _strAnimName, Ptr<CTexture> _AtlasTex
	, Vec2 _vLeftTop, Vec2 _vSlice, int _FrameCount, int _FPS)
{
	assert(_AtlasTex.Get());

	SetName(_strAnimName);

	m_AtlasTex = _AtlasTex;

	for (size_t i = 0; i < _FrameCount; ++i)
	{
		tAnim2DFrm frm = {};

		frm.fDuration = 1.f / (float)_FPS;
		frm.LeftTopUV = Vec2(_vLeftTop.x + _vSlice.x * i, _vLeftTop.y);
		frm.SliceUV = _vSlice;

		m_vecFrm.push_back(frm);
	}
}
```

<br/>

## tAnim2DFrm<br/>
<br/>

1 프레임에 들어있는 정보를 담는 구조체를 구성한다.<br/>

```
struct tAnim2DFrm
{
	Vec2	LeftTopUV; // 좌상단 좌표
	Vec2	SliceUV; // 이미지를 자를 크기
	float	fDuration; // 1 프레임 당 이미지 지속 시간
};
```
<br/>

## 애니메이션 적용하기<br/>
<Br/>

CLevelMgr에서 객체를 만들며 애니메이션을 추가하고,<br/>

```
// CLevelMgr.cpp 중에서

	// 오브젝트 생성
	CGameObject* pParent = new CGameObject;
	pParent->AddComponent(new CAnimator2D);

	Ptr<CTexture> pAnimAtlas = CResMgr::GetInst()->FindRes<CTexture>(L"Link");
	pParent->Animator2D()->CreateAnimation(L"WalkDown", pAnimAtlas, Vec2(0.f, 520.f), Vec2(120.f, 130.f), Vec2(300.f, 300.f), 10, 16);
	pParent->Animator2D()->Play(L"WalkDown", true);
```

Play함수와 FindAnim함수에서 애니메이션을 찾으며,<br/>

finaltick함수와 누적시간을 통해서 프레임을 증가시켜야한다.<br/>

```
// CAnimator2D.cpp 중에서


void CAnimator2D::finaltick()
{
	if (nullptr != m_pCurAnim)
	{
		if (m_bRepeat && m_pCurAnim->IsFinish())
		{
			m_pCurAnim->Reset();
		}

		m_pCurAnim->finaltick();
	}	
}

```

```
// CAnim2D.cpp 중에서

void CAnim2D::finaltick()
{	
	if (m_bFinish)
		return;

	m_fTime += DT;

	if (m_vecFrm[m_iCurFrm].fDuration < m_fTime)
	{
		m_fTime = m_fTime - m_vecFrm[m_iCurFrm].fDuration;
		++m_iCurFrm;

		if (m_vecFrm.size() <= m_iCurFrm)
		{
			m_iCurFrm = m_vecFrm.size() - 1;
			m_bFinish = true;
		}
	}
}
```

Anim2D에서 시간값을 계속 누적하고, 만약 시간 값이 한 프레임 당 시간을 넘어버리면 다음 프레임 값으로 넘어가도록 해야한다.<br/>

그리고 최대 프레임을 넘어가지 않도록 안전장치도 마련해야할 것이다.<br/>

Vector의 끝에 도달하면 인덱스를 유지하고, finish 상태로 전환한다.<br/>

반복여부가 참일 경우 finish 상태이면 다시 처음으로 돌아갈 것이고,<br/>

거짓이라면 마지막 프레임을 계속 출력하도록 유지해야 한다.<br/>
<br/>

### 애니메이션 셰이더<br/>
<Br/>

그리고 애니메이션과 관련된 데이터로

- Animation Atlas Texture LeftTop
- Animation Atlas Texture Slice

이 데이터들이 쉐이더 안으로 들어와야 한다.<br/>

이 데이터를 사용할 물체인지 아닌지 여부를 판단하기 위해서 AnimUse 여부를 저장하는 데이터도 불러와야 한다.<br/>

```
// std2d.fx 파일 중에서

#define bAnimUse        g_int_0
#define LeftTop         g_vec2_0
#define Slice           g_vec2_1

```

그리고 렌더링은 결국 메쉬 렌더에서 진행한다.<br/>

메쉬 렌더 컴포넌트에서 애니메이션을 사용하는지 여부를 검사한다.<br/>

만약에 Animator2D 컴포넌트가 있다면, 사용하고 있다면

```
if (Animator2D())
{
	Animator2D()->UpdateData();
}
```
이를 통해서 데이터를 업데이트한다.<br/>

```
// CAnimator2D.cpp 중에서

void CAnimator2D::UpdataData()
{
	Ptr<CMaterial> pMtrl = MestRender()->GetMaterial();

	const tAnim2DFrm& frm = m_pCurAnim->GetCurFrame();

	int iAnimUse = 1;
	pMtrl->SetScalarParam(INT_0, &iAnimUse);
	pMtrl->SetScalarParam(VEC2_0, &frm.LeftTopUV);
	pMtrl->SetScalarParam(VEC2_1, &frm.SliceUV);
	pMtrl->SetTexParam(TEX_0, m_pCurAnim->GetAtlasTex());
}

```
위 함수를 통해서 셰이더로 데이터를 전송한다.<br/>

그리고 std2d.fx 셰이더에서 받아온 데이터로 출력할 범위를 계산한다.<br/>

```
// std2d.fx 파일 중에서

		if (bAnimUse)
        {
            float2 vUV = LeftTop + (BackSize * _in.vUV);       
            vUV -= ((BackSize - Slice) / 2.f);
            vUV -= Offset;
            
            if (LeftTop.x < vUV.x && vUV.x < LeftTop.x + Slice.x
                && LeftTop.y < vUV.y && vUV.y < LeftTop.y + Slice.y)
            {
                vOutColor = g_tex_0.Sample(g_sam_0, vUV);
            }
            else
            {
                vOutColor = float4(1.f, 1.f, 0.f, 1.f);
                //discard;
            }
        }
```

<br/>

## 애니메이션 적용하는 다른 방법<br/>
<br/>

하지만 아틀라스 이미지가 정렬되어 있지 않은 경우에는 어떻게 해야할까?<br/>

그리고 프레임 별로 크기가 다른 경우에는 어떻게 해야 할까?<br/>
<br/>

### 셰이더에서 원하는 부분만 샘플링 하기<br/>
<br/>

```
// CAnim2D.h 중에서

public:
    void finaltick();
    void Create(const wstring& _strAnimName, Ptr<CTexture> _AtlasTex, Vec2 _vLeftTop, Vec2 _vSlice, Vec2 _BackSize, int _FrameCount, int _FPS);

    const tAnim2DFrm& GetCurFrame() { return m_vecFrm[m_iCurFrm];}
    Vec2 GetBackSize() { return m_vBackSize; }
    Ptr<CTexture> GetAtlasTex() { return m_AtlasTex; }

    bool IsFinish() { return m_bFinish; }
    void Reset()
    {
        m_iCurFrm = 0;
        m_fTime = 0.f;
        m_bFinish = false;
    }

```

```
// CLevelMgr.cpp 중에서

	Ptr<CTexture> pAnimAtlas = CResMgr::GetInst()->FindRes<CTexture>(L"Link");
	pParent->Animator2D()->CreateAnimation(L"WalkDown", pAnimAtlas, Vec2(0.f, 520.f), Vec2(120.f, 130.f), Vec2(300.f, 300.f), 10, 16);
	pParent->Animator2D()->Play(L"WalkDown", true);

```

BackSize를 통해서 제일 큰 사이즈를 차지하는 프레임의 크기를 기준으로 삼는다.<br/>

그리고 Create함수는 해상도를 UV 좌표가 아니라 픽셀 단위로 받는다고 하면 아래와 같이 수정이 가능하다.<br/>

```
// CAnim2D.cpp 중에서 Create 함수

Vec2 vResolution = Vec2(m_AtlasTex->Width(), m_AtlasTex->Height());

	m_vBackSize = _vBackSize / vResolution;
```

텍스쳐의 해상도(Resolution)을 받아오면 된다.<br/>

그리고 셰이더에도 추가인자를 넣어준다.<br/>

```
// std2d.fx 파일 중에서

// ======================================
// Std2DLightShader
// RasterizerState      : None
// BlendState           : Mask
// DepthStencilState    : Less
//
// Parameter
#define bAnimUse        g_int_0
#define LeftTop         g_vec2_0
#define Slice           g_vec2_1
#define Offset          g_vec2_2
#define BackSize        g_vec2_3

// g_tex_0              : Output Texture
// g_tex_1              : Nomal Texture
// ======================================

```

제일 큰 사이즈를 차지하는 프레임의 크기를 기준으로 삼았다.<br/>

해당 크기만큼 텍스쳐를 가져오고,<br/>

원하는 영역만 샘플링을 하도록 조건문을 걸어줘야 한다.<br/>
<br/>

### Offset 가하기<br/>
<br/>

캐릭터가 서있는 상태에서 찌르는 모션으로 넘어가는 등<br/>

갑자기 흐트러지는 순간이 있다.<br/>

이 때 흐트러지지 않고 자연스러워 보일 수 있도록 Offset을 가해줘야 하는 경우가 생긴다.<br/>

Animation이 Create될 때 Offset을 주는 방법이 있다.<br/>

하드 코딩으로 하는 법은 아래와 같다.<br/>

```
// CAnim2D.cpp 중에서

void CAnim2D::Create(const wstring& _strAnimName, Ptr<CTexture> _AtlasTex
	, Vec2 _vLeftTop, Vec2 _vSlice, Vec2 _vBackSize, int _FrameCount, int _FPS)
{
	assert(_AtlasTex.Get());

	SetName(_strAnimName);

	m_AtlasTex = _AtlasTex;

	Vec2 vResolution = Vec2(m_AtlasTex->Width(), m_AtlasTex->Height());

	m_vBackSize = _vBackSize / vResolution;

	for (size_t i = 0; i < _FrameCount; ++i)
	{
		tAnim2DFrm frm = {};

		frm.fDuration = 1.f / (float)_FPS;
		frm.LeftTopUV = Vec2(_vLeftTop.x + _vSlice.x * i, _vLeftTop.y) / vResolution;
		frm.SliceUV = _vSlice / vResolution;
		
		if (i == 5)
			frm.Offset = Vec2(0.05f, 0.f);	

		m_vecFrm.push_back(frm);
	}
}

```

하지만 하드 코딩이 아니라 프레임 별로 오프셋을 가하는 방법은 다음에 에디터를 다룰 때 다루도록 하겠다.<br/>
