---
layout: post
title:  "[DirectX] 새로운 Shader Code 형성, Mask Blend"
subtitle:  
date: 2022-12-21 09:42:43 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] 새로운 Shader Code 형성, Mask Blend<br/>
<br/>

## 새로운 쉐이더 만들기<br/>
<br/>

헤더파일로 만들면서 .fx파일 형식으로 생성한다.<br/>

그리고

```
#ifndef _STD2D
#define _STD2D

셰이더 코드

#endif
```

형식으로 작성하며, 속성에서 아래를 설정해준다.<br/>

- 셰이더 형식 : 효과(/fx)
- 셰이더 모델 : Shader Model 5.0

<br/>

## std2d.fx 코드<br/>
<br/>

```
#ifndef _STD2D
#define _STD2D

#include "value.fx"

struct VS_IN
{
    float3 vLocalPos : POSITION;
    float2 vUV : TEXCOORD;
};

struct VS_OUT
{
    float4 vPosition : SV_Position;
    float2 vUV : TEXCOORD;
};

// ============================
// Std2DShader
// RasterizerState      : None
// BlendState           : Mask
// DepthStencilState    : Less
//
// Parameter
// g_tex_0              : Output Texture
// ============================
VS_OUT VS_Std2D(VS_IN _in)
{
    VS_OUT output = (VS_OUT) 0.f;
    
    output.vPosition = mul(float4(_in.vLocalPos, 1.f), g_matWVP);
    output.vUV = _in.vUV;
        
    return output;
}


float4 PS_Std2D(VS_OUT _in) : SV_Target
{
    float4 vOutColor = (float4) 0.f;
        
    if (g_btex_0)
    {
        vOutColor = g_tex_0.Sample(g_sam_0, _in.vUV);
    }
    else
    {
        vOutColor = float4(1.f, 0.f, 1.f, 1.f);
    }    
    
    if (0.f == vOutColor.a)
        discard;
    
    return vOutColor;
}

#endif
```

앞으로 재질에서 전달받은 텍스쳐에서 색깔을 추출하는 역할을 여기서 진행한다.<br/>

PS_Std2D 함수에서 텍스쳐가 바인딩이 안되어있을 경우 마젠타(R:255, G:0, B:255)로 출력될 수 있도록 역할을 하고 있다.<br/>

이를 진행하는데는 후술할 value.fx 셰이더 코드와 연계가 된다.<br/>
<br/>

### struct.h 파일<br/>
<br/>

그리고 struct.h에서 tTransform 구조체의 멤버를 추가해준다.<br/>

```

struct tTransform
{
	Matrix matWorld;
	Matrix matView;
	Matrix matProj;

    Matrix matWV;
    Matrix matWVP;
};

```
<br/>

### CTransform.cpp 파일<br/>
<br/>

그리고 UpdataData 함수도 다음과 같이 수정해준다.<br/>

```
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
<br/>

### CResMgr.cpp 파일<br/>
<br/>

CreateDefaultGraphicsShader() 함수에 Std2DShader를 추가하는 코드를 작성해준다.<br/>
```
	// ============================
	// Std2DShader
	// RasterizerState      : None
	// BlendState           : Mask
	// DepthStencilState    : Less
	//
	// Parameter
	// g_tex_0              : Output Texture
	// ============================
	pShader = new CGraphicsShader;
	pShader->SetKey(L"Std2DShader");
	pShader->CreateVertexShader(L"shader\\std2d.fx", "VS_Std2D");
	pShader->CreatePixelShader(L"shader\\std2d.fx", "PS_Std2D");

	pShader->SetRSType(RS_TYPE::CULL_NONE);
	pShader->SetDSType(DS_TYPE::LESS);
	pShader->SetBSType(BS_TYPE::MASK);

	pShader->SetDomain(SHADER_DOMAIN::DOMAIN_MASK);

	AddRes(pShader->GetKey(), pShader);
```
<br/>

## value.fx<br/>
<Br/>

재질에 관련된 내용을 관장하는 셰이더 코드<br/>
재질 상수가 담겨져있다.<br/>

```
#ifndef _VALUE
#define _VALUE

cbuffer TRANSFORM : register(b0)
{
    row_major matrix g_matWorld;
    row_major matrix g_matView;
    row_major matrix g_matProj;
    
    row_major matrix g_matWV;
    row_major matrix g_matWVP;
};

cbuffer MATERIAL : register(b1)
{
    int g_int_0;
    int g_int_1;
    int g_int_2;
    int g_int_3;
    
    float g_float_0;
    float g_float_1;
    float g_float_2;
    float g_float_3;
    
    float2 g_vec2_0;
    float2 g_vec2_1;
    float2 g_vec2_2;
    float2 g_vec2_3;

    float4 g_vec4_0;
    float4 g_vec4_1;
    float4 g_vec4_2;
    float4 g_vec4_3;
    
    matrix g_mat_0;
    matrix g_mat_1;
    matrix g_mat_2;
    matrix g_mat_3;
    
    int g_btex_0;
    int g_btex_1;
    int g_btex_2;
    int g_btex_3;
    int g_btex_4;
    int g_btex_5;
    int g_btex_6;
    int g_btex_7;   
};


Texture2D g_tex_0 : register(t0);
Texture2D g_tex_1 : register(t1);
Texture2D g_tex_2 : register(t2);
Texture2D g_tex_3 : register(t3);
Texture2D g_tex_4 : register(t4);
Texture2D g_tex_5 : register(t5);
Texture2D g_tex_6 : register(t6);
Texture2D g_tex_7 : register(t7);


SamplerState g_sam_0 : register(s0);
SamplerState g_sam_1 : register(s1);


#endif
```
<br/>

### struct.h 파일<br/>
<br/>

그리고 struct.h에서 tMtrlConst 구조체의 멤버를 추가해준다.<br/>

```
struct tMtrlConst
{
	int arrInt[4];
	float arrFloat[4];
	Vec2 arrV2[4];
	Vec4 arrV4[4];
	Matrix arrMat[4];

	int arrTex[(UINT)TEX_PARAM::TEX_END];
};

```
<br/>

### CMaterial.cpp 파일<br/>
<br/>

```
#include "pch.h"
#include "CMaterial.h"

#include "CDevice.h"
#include "CConstBuffer.h"

CMaterial::CMaterial()
	: CRes(RES_TYPE::MATERIAL)
	, m_Const{}
	, m_arrTex{}
{	
}

CMaterial::~CMaterial()
{
}


void CMaterial::UpdateData()
{
	if (nullptr == m_pShader)
		return;

	m_pShader->UpdateData();


	// Texture Update
	for (UINT i = 0; i < TEX_END; ++i)
	{
		if (nullptr == m_arrTex[i])
		{
			m_Const.arrTex[i] = 0;
			continue;
		}

		else
		{
			m_Const.arrTex[i] = 1;
			m_arrTex[i]->UpdateData(i, PIPELINE_STAGE::PS_PIXEL);
		}
	}

	// Constant Update
	CConstBuffer* pMtrlBuffer = CDevice::GetInst()->GetConstBuffer(CB_TYPE::MATERIAL);
	pMtrlBuffer->SetData(&m_Const);
	pMtrlBuffer->UpdateData();
}


void CMaterial::SetScalarParam(SCALAR_PARAM _Param, void* _Src)
{
	switch (_Param)
	{
	case INT_0:
	case INT_1:
	case INT_2:
	case INT_3:		
		m_Const.arrInt[_Param] = *((int*)_Src);
		break;
	case FLOAT_0:
	case FLOAT_1:
	case FLOAT_2:
	case FLOAT_3:
		m_Const.arrFloat[_Param - FLOAT_0] = *((float*)_Src);
		break;

	case VEC2_0:
	case VEC2_1:
	case VEC2_2:
	case VEC2_3:
		m_Const.arrV2[_Param - VEC2_0] = *((Vec2*)_Src);
		break;

	case VEC4_0:
	case VEC4_1:
	case VEC4_2:
	case VEC4_3:
		m_Const.arrV4[_Param - VEC4_0] = *((Vec4*)_Src);
		break;

	case MAT_0:
	case MAT_1:
	case MAT_2:
	case MAT_3:
		m_Const.arrMat[_Param - MAT_0] = *((Matrix*)_Src);
		break;	
	}
}

void CMaterial::SetTexParam(TEX_PARAM _Param, const Ptr<CTexture>& _Tex)
{
	m_arrTex[_Param] = _Tex;
}
```

<br/>

### CResMgr.cpp 파일<br/>
<br/>

CreateDefaultMaterial() 함수에 Std2DShader를 사용하도록 코드를 작성해준다.<br/>
```
void CResMgr::CreateDefaultMaterial()
{
	Ptr<CMaterial> pMtrl = nullptr;

	// Test Material
	pMtrl = new CMaterial;
	pMtrl->SetShader(FindRes<CGraphicsShader>(L"TestShader"));
	AddRes(L"TestMtrl", pMtrl);


	// Std2D Material
	pMtrl = new CMaterial;
	pMtrl->SetShader(FindRes<CGraphicsShader>(L"Std2DShader"));
	AddRes(L"Std2DMtrl", pMtrl);
}

```

<br/>

### CLevelMgr.cpp 파일<br/>
<br/>

init() 함수에서도 수정을 해준다.<br/>

init 함수에서 오브젝트를 생성하며 어떤 쉐이더를 사용할지를 결정해준다.<br/>

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

	m_pCurLevel->AddGameObject(pMainCam, 0);



	// 오브젝트 생성
	CGameObject* pObj = new CGameObject;
	pObj->SetName(L"Player");
	pObj->AddComponent(new CTransform);
	pObj->AddComponent(new CMeshRender);
	pObj->AddComponent(new CPlayerScript);

	Ptr<CMesh> pMesh = CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh");
	Ptr<CMaterial> Std2DMtrl = CResMgr::GetInst()->FindRes<CMaterial>(L"Std2DMtrl");

	Ptr<CTexture> PlayerTex = CResMgr::GetInst()->FindRes<CTexture>(L"SmokeTex");
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

	pObj->Transform()->SetRelativePos(Vec3(400.f, 0.f, 200.f));
	pObj->Transform()->SetRelativeScale(Vec3(200.f, 200.f, 1.f));

	pObj->MeshRender()->SetMesh(CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh"));
	pObj->MeshRender()->SetMaterial(CResMgr::GetInst()->FindRes<CMaterial>(L"TestMtrl"));

	m_pCurLevel->AddGameObject(pObj, 0);
}
```
<br/>

## Mask Blend 옵션<br/>
<br/>

### CDevice.cpp 코드<br/>
<Br/>

Mask 옵션을 설정하는 코드는 아래와 같다.<br/>

Default 옵션과 차이점은 AlphaToCoverageEnable 옵션이 true로 설정되어있다는 것이다.<br/>

```
// CreateBlendState()함수 중에서

// Mask
    D3D11_BLEND_DESC Desc = {};
    Desc.AlphaToCoverageEnable = true;
    Desc.IndependentBlendEnable = false;   

    Desc.RenderTarget[0].BlendEnable = true;
    Desc.RenderTarget[0].BlendOp = D3D11_BLEND_OP_ADD;
    Desc.RenderTarget[0].SrcBlend = D3D11_BLEND_ONE;
    Desc.RenderTarget[0].DestBlend = D3D11_BLEND_ZERO;

    Desc.RenderTarget[0].BlendOpAlpha = D3D11_BLEND_OP_ADD;
    Desc.RenderTarget[0].SrcBlendAlpha = D3D11_BLEND_ONE;
    Desc.RenderTarget[0].DestBlendAlpha = D3D11_BLEND_ZERO;

	Desc.RenderTarget[0].RenderTargetWriteMask = D3D11_COLOR_WRITE_ENABLE_ALL;
    DEVICE->CreateBlendState(&Desc, m_BSState[(UINT)BS_TYPE::MASK].GetAddressOf());

```
<br/>

### 알파블렌드 사용의 문제점?<br/>
<br/>

알파 블렌드를 사용함으로 인해서 반투명, 투명 상태를 사용하게 된다면 두 가지 문제가 발생한다.

- 렌더링 순서가 상관이 있어진다. 즉 렌더링 정렬이 필요하다.<br/>
Default, Mask 옵션을 사용하는 객체들을 먼저 그리고 그다음에 알파블렌드를 사용하는 객체를 그려야한다.

- 깊이 문제가 발생한다.<br/>
투명한 색깔이지만 깊이 값은 남아있는 상태가 발생한다.<br/>
이렇게 된다면 투명한 색깔임에도 불구하고 깊이 값의 영향을 받아서 깊이 값이 더 큰 물체가 깊이 값이 더 작은 물체의 투명 영역에 가려져버리는 현상이 발생한다.<br/>
반투명, 투명한 물체는 결국 깊이를 남기면 안된다.<br/>
이를 해결하기 위해서 알파블렌드를 사용하는 물체는 DSType를 NO_WRITE로 설정한다.<br/>

```
// CResMgr.cpp 파일 중에서.

void CResMgr::CreateDefaultGraphicsShader()
{
	Ptr<CGraphicsShader> pShader = nullptr;

	// ===========
	// Test Shader
	// ===========
	pShader = new CGraphicsShader;
	pShader->SetKey(L"TestShader");
	pShader->CreateVertexShader(L"shader\\test.fx", "VS_Test");
	pShader->CreatePixelShader(L"shader\\test.fx", "PS_Test");

	pShader->SetRSType(RS_TYPE::CULL_NONE);
	pShader->SetDSType(DS_TYPE::NO_WRITE); // 깊이 테스트 진행, 깊이 기록 X
	pShader->SetBSType(BS_TYPE::ALPHA_BLEND);

	pShader->SetDomain(SHADER_DOMAIN::DOMAIN_TRANSPARENT);
	
	AddRes(L"TestShader", pShader);


	// ============================
	// Std2DShader
	// RasterizerState      : None
	// BlendState           : Mask
	// DepthStencilState    : Less
	//
	// Parameter
	// g_tex_0              : Output Texture
	// ============================
	pShader = new CGraphicsShader;
	pShader->SetKey(L"Std2DShader");
	pShader->CreateVertexShader(L"shader\\std2d.fx", "VS_Std2D");
	pShader->CreatePixelShader(L"shader\\std2d.fx", "PS_Std2D");

	pShader->SetRSType(RS_TYPE::CULL_NONE);
	pShader->SetDSType(DS_TYPE::LESS);
	pShader->SetBSType(BS_TYPE::MASK);

	pShader->SetDomain(SHADER_DOMAIN::DOMAIN_MASK);

	AddRes(pShader->GetKey(), pShader);
}
```
<br/>

### AlphaToCoverage 옵션<br/>
<br/>

알파 값까지 감안해서 깊이 판정을 하는 옵션이다.<br/>
단, 완전히 투명하거나 완전히 불투명한 개체에 한정해서 사용할 수 있다.<br/>
<br/>

### 불투명과 반투명의 분리<br/>
<br/>

- 불투명(Opaque) : AlphaToCoverage를 true 상태로 설정한다.
- 반투명(Transparent) : DepthStencilType를 No_write로 설정한다.

<br/>

### Shader Domain의 설정<br/>
<br/>

어떤 BSType, DSType를 설정할지 알맞게 분류 조건을 설정해서 렌더링 순서를 잡아주는 옵션이다.<br/>

```
// define.h의 코드 추가

enum class SHADER_DOMAIN
{
    DOMAIN_OPAQUE, // 불투명 오브젝트
    DOMAIN_MASK, // 불투명, 투명
    DOMAIN_TRANSPARENT, // 반투명
	DOMAIN_POSTPROCESS, // 후 처리

	DOMAIN_UI, // UI용
    DOMAIN_UNDEFINED, // 미정
}
```