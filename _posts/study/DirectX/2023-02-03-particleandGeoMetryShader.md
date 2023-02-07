---
layout: post
title:  "[DirectX] Particle and Geometry Shader"
subtitle:  
date: 2023-02-03 21:08:33 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] Particle and Geometry Shader<br/>
<br/>

원하는 입자만 렌더링 하고 싶으면 어떻게 해야할까?<br/>
<Br/>

## 활성화 및 비활성화의 구분<br/>
<br/>

인스턴싱을 한 뒤에는 원하지 않는 파티클까지 렌더링이 진행되는 문제가 발생한다.<br/>

인스턴싱을 이용하면 100개의 스레드가 한 프레임에 동시에 렌더링을 하게 되면 어쩔 수 없이 원하는 파티클 뿐만 아니라 다른 파티클까지 한꺼번에 렌더링이 진행된다.<br/>

따라서 파티클이 활성화되어있는 것만 렌더링, 업데이트를 하도록 코드를 개선할 것이다.<br/>
<br/>

### particle_render.fx의 개선?<br/>
<br/>

픽셀 쉐이더에서 discard나 clip(-1)을 이용한다.<br/>

다만, 이 방법은 픽셀쉐이더가 멈추는거지 파이프라인을 처음 부터 시작하지 않는 것은 아니다.<br/>

즉, 정점쉐이더, 레스터라이저가 다 진행되고나서 픽셀쉐이더가 진행되는 것인데, 이렇게 하면 불필요한 연산을 하게 되는 것이다.<br/>

버퍼 안에서 필요한 것만 파이프라인이 진행되도록 만들 순 없을까?<br/>
<br/>

### 파이프라인의 순서<br/>

- 정점 쉐이더
- 테셀레이션
- 지오메트리 쉐이더
- 레스터라이저
- 픽셀 쉐이더
- 깊이 판정
- 블렌드 판정

우리는 이제 지오메트리 쉐이더를 활용할 것이다.<br/>

가장 빠르게 멈출 수 있는 시점이 지오메트리 쉐이더이다.<br/>

지오메트리 쉐이더를이용하면 레스터라이저 이후로는 호출할 필요가 없어진다.<br/>
<br/>

### 지오메트리 쉐이더의 역할<br/>
<br/>

- 중간에 정점을 생성시킨다. 없던 정점을 만들어낼 수 있다.
- 정점을 생성하지 않으면 해당 정점의 파이프라인을 멈춘다.

지오메트리 쉐이더가 없을 때에는 정점 쉐이더에서 만든 것을 바로 레스터라이저에게 넘길 수 있었지만 이제는 정점 쉐이더에서 설정한, 받은 위치에서 정점을 지오메트리 쉐이더에서 정점을 생성해서 레스터라이저에게 넘겨줘야 한다.<br/>

지오메트리 쉐이더에서 정점을 생성하지 않으면 정점을 생성하지 않는 것으로 간주하여 다음 파이프라인 단계가 진행되지 않는다.<br/>

이를 이용하여 불필요한 정점은 더 이상 렌더링 되지 않도록 조절할 것이다.<br/>
<br/>

### 지오메트리 쉐이더의 사용 목적<br/>
<br/>

1. 파이프라인의 제어 : 상술한 것과 같은 역할을 하기 위해서 사용한다.
2. 빌보드 처리 : 카메라가 쳐다보는 방향이 바뀌어도 카메라와 마주 볼 수 있게 조정한다.

<br/>

뷰 스페이스의 특징

- 카메라의 위치가 원점
- 카메라가 쳐다보는 방향이 z축

위 특징이 있다. 그러면 카메라가 바라보고 있는 방향이 설정된 월드 좌표계에서 일일이 파티클의 위치를 계산하기 보다는, <br/>

뷰 스페이스에서 정점을 확장시킨 다음에 투영 행렬을 곱해서 월드 좌표계에 배치하는 방법을 사용하면 될 것이다.<br/>

이제는 정점 쉐이더에서 기존에 정점을 4개씩 입력했던 것과는 달리 정점을 1개 입력받을 것이다.<br/>

그리고 정점을 1개 전달받은 지오메트리 쉐이더에서 정점을 4개 생성하는 방법을 사용할 것이다.<br/>

만일 활성화 할 파티클이면 지오메트리 쉐이더를 진행시키면 되고, 아니라면 진행을 멈추면 되는 것이다.<br/>
<br/>

## 코드의 변경<br/>
<br/>

### CGraphicsShader 클래스<br/>
<Br/>

```
// 헤더파일

#pragma once
#include "CShader.h"

class CGraphicsShader :
    public CShader
{
private:
    ComPtr<ID3DBlob>                m_VSBlob;
    ComPtr<ID3DBlob>                m_HSBlob;
    ComPtr<ID3DBlob>                m_DSBlob;
    ComPtr<ID3DBlob>                m_GSBlob;
    ComPtr<ID3DBlob>                m_PSBlob;    

    ComPtr<ID3D11VertexShader>      m_VS;
    ComPtr<ID3D11HullShader>        m_HS;
    ComPtr<ID3D11DomainShader>      m_DS;
    ComPtr<ID3D11GeometryShader>    m_GS;
    ComPtr<ID3D11PixelShader>       m_PS;

    ComPtr<ID3D11InputLayout>	    m_Layout;
    D3D11_PRIMITIVE_TOPOLOGY        m_eTopology;

    RS_TYPE                         m_RSType;
    DS_TYPE                         m_DSType;
    BS_TYPE                         m_BSType;

    SHADER_DOMAIN                   m_Domain;


public:
    void CreateVertexShader(const wstring& _strFileName, const string& _strFuncName);
    void CreateGeometryShader(const wstring& _strFileName, const string& _strFuncName);
    void CreatePixelShader(const wstring& _strFileName, const string& _strFuncName);
    void SetTopology(D3D11_PRIMITIVE_TOPOLOGY _Topology) { m_eTopology = _Topology; }
    void SetRSType(RS_TYPE _Type) { m_RSType = _Type; }
    void SetDSType(DS_TYPE _Type) { m_DSType = _Type; }
    void SetBSType(BS_TYPE _Type) { m_BSType = _Type; }
    void SetDomain(SHADER_DOMAIN _domain) { m_Domain = _domain; }

    SHADER_DOMAIN GetDomain() { return m_Domain; }
    virtual void UpdateData() override;


public:
    CGraphicsShader();
    ~CGraphicsShader();
};


```


```
// cpp 파일

#include "pch.h"
#include "CGraphicsShader.h"

#include "CPathMgr.h"
#include "CDevice.h"

CGraphicsShader::CGraphicsShader()
	: CShader(RES_TYPE::GRAPHICS_SHADER)
	, m_eTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST)
	, m_RSType(RS_TYPE::CULL_BACK)
	, m_DSType(DS_TYPE::LESS)
	, m_BSType(BS_TYPE::DEFAULT)
	, m_Domain(SHADER_DOMAIN::DOMAIN_UNDEFINED)
{
}

CGraphicsShader::~CGraphicsShader()
{
}

void CGraphicsShader::CreateVertexShader(const wstring& _strFileName, const string& _strFuncName)
{
	// Shader 파일 경로
	wstring strShaderFile = CPathMgr::GetInst()->GetContentPath();
	strShaderFile += _strFileName;

	// VertexShader Compile
	if (FAILED(D3DCompileFromFile(strShaderFile.c_str(), nullptr, D3D_COMPILE_STANDARD_FILE_INCLUDE
		, _strFuncName.c_str(), "vs_5_0", 0, 0, m_VSBlob.GetAddressOf(), m_ErrBlob.GetAddressOf())))
	{
		MessageBoxA(nullptr, (const char*)m_ErrBlob->GetBufferPointer()
			, "Vertex Shader Compile Failed!!", MB_OK);
	}

	// 컴파일된 객체로 VertexShader, PixelShader 를 만든다.
	DEVICE->CreateVertexShader(m_VSBlob->GetBufferPointer(), m_VSBlob->GetBufferSize()
		, nullptr, m_VS.GetAddressOf());



	// InputLayout 생성
	D3D11_INPUT_ELEMENT_DESC LayoutDesc[3] = {};

	LayoutDesc[0].SemanticName = "POSITION";
	LayoutDesc[0].SemanticIndex = 0;
	LayoutDesc[0].AlignedByteOffset = 0;
	LayoutDesc[0].Format = DXGI_FORMAT_R32G32B32_FLOAT;
	LayoutDesc[0].InputSlot = 0;
	LayoutDesc[0].InputSlotClass = D3D11_INPUT_PER_VERTEX_DATA;
	LayoutDesc[0].InstanceDataStepRate = 0;

	LayoutDesc[1].SemanticName = "COLOR";
	LayoutDesc[1].SemanticIndex = 0;
	LayoutDesc[1].AlignedByteOffset = 12;
	LayoutDesc[1].Format = DXGI_FORMAT_R32G32B32A32_FLOAT;
	LayoutDesc[1].InputSlot = 0;
	LayoutDesc[1].InputSlotClass = D3D11_INPUT_PER_VERTEX_DATA;
	LayoutDesc[1].InstanceDataStepRate = 0;

	LayoutDesc[2].SemanticName = "TEXCOORD";
	LayoutDesc[2].SemanticIndex = 0;
	LayoutDesc[2].AlignedByteOffset = 28;
	LayoutDesc[2].Format = DXGI_FORMAT_R32G32_FLOAT;
	LayoutDesc[2].InputSlot = 0;
	LayoutDesc[2].InputSlotClass = D3D11_INPUT_PER_VERTEX_DATA;
	LayoutDesc[2].InstanceDataStepRate = 0;


	if (FAILED(DEVICE->CreateInputLayout(LayoutDesc, 3
		, m_VSBlob->GetBufferPointer(), m_VSBlob->GetBufferSize()
		, m_Layout.GetAddressOf())))
	{
		assert(nullptr);
	}
}

void CGraphicsShader::CreateGeometryShader(const wstring& _strFileName, const string& _strFuncName)
{
	// Shader 파일 경로
	wstring strShaderFile = CPathMgr::GetInst()->GetContentPath();
	strShaderFile += _strFileName;

	// Shader Compile	
	if (FAILED(D3DCompileFromFile(strShaderFile.c_str(), nullptr, D3D_COMPILE_STANDARD_FILE_INCLUDE
		, _strFuncName.c_str(), "gs_5_0", 0, 0, m_GSBlob.GetAddressOf(), m_ErrBlob.GetAddressOf())))
	{
		MessageBoxA(nullptr, (const char*)m_ErrBlob->GetBufferPointer()
			, "Shader Compile Failed!!", MB_OK);
	}

	// 컴파일된 객체로 Shader 를 만든다.
	DEVICE->CreateGeometryShader(m_GSBlob->GetBufferPointer(), m_GSBlob->GetBufferSize()
		, nullptr, m_GS.GetAddressOf());
}

void CGraphicsShader::CreatePixelShader(const wstring& _strFileName, const string& _strFuncName)
{
	// Shader 파일 경로
	wstring strShaderFile = CPathMgr::GetInst()->GetContentPath();
	strShaderFile += _strFileName;


	// PixelShader Compile	
	if (FAILED(D3DCompileFromFile(strShaderFile.c_str(), nullptr, D3D_COMPILE_STANDARD_FILE_INCLUDE
		, _strFuncName.c_str(), "ps_5_0", 0, 0, m_PSBlob.GetAddressOf(), m_ErrBlob.GetAddressOf())))
	{
		MessageBoxA(nullptr, (const char*)m_ErrBlob->GetBufferPointer()
			, "Pixel Shader Compile Failed!!", MB_OK);
	}

	// 컴파일된 객체로 PixelShader 를 만든다.
	DEVICE->CreatePixelShader(m_PSBlob->GetBufferPointer(), m_PSBlob->GetBufferSize()
		, nullptr, m_PS.GetAddressOf());
}

void CGraphicsShader::UpdateData()
{
	CONTEXT->IASetInputLayout(m_Layout.Get());
	CONTEXT->IASetPrimitiveTopology(m_eTopology);
		
	CONTEXT->VSSetShader(m_VS.Get(), nullptr, 0);
	CONTEXT->HSSetShader(m_HS.Get(), nullptr, 0);
	CONTEXT->DSSetShader(m_DS.Get(), nullptr, 0);
	CONTEXT->GSSetShader(m_GS.Get(), nullptr, 0);
	CONTEXT->PSSetShader(m_PS.Get(), nullptr, 0);

	CONTEXT->RSSetState(CDevice::GetInst()->GetRSState(m_RSType).Get());
	CONTEXT->OMSetDepthStencilState(CDevice::GetInst()->GetDSState(m_DSType).Get(), 0);
	CONTEXT->OMSetBlendState(CDevice::GetInst()->GetBSState(m_BSType).Get(), Vec4(0.f, 0.f, 0.f, 0.f), 0xffffffff);
}

```

지오메트리 쉐이더를 만들고, 지오메트리 쉐이더까지 업데이트 하도록 코드를 조정한다.<br/>

<br/>

### CResMgr.cpp<br/>
<Br/>

이제는 RectMesh가 아니라 점 하나 짜리 메쉬를 사용할 것이다.<br/>
점이 하나인 포인트 메쉬를 생성한다.<Br/>

```
// PointMesh의 생성
v.vPos = Vec3(0.f, 0.f, 0.f);
v.vUV = Vec2(0.f, 0.f);
v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);

pMesh = new CMesh;
UINT idx = 0;
pMesh->Create(&v, 1, &idx, 1);
AddRes(L"PointMesh", pMesh);
```

그리고 점이 하나인 쉐이더는 토폴로지를 직접 세팅한다.<br/>

```
	// ============================
	// ParticleRender
	// 
	// RS_TYPE : CULL_NONE
	// DS_TYPE : NO_WRITE
	// BS_TYPE : ALPHA_BLEND

	// Parameter
	// g_int_0 : Particle Index
	// 
	// Domain : TRANSPARENT
	// ============================
	pShader = new CGraphicsShader;
	pShader->SetKey(L"ParticleRenderShader");
	pShader->CreateVertexShader(L"shader\\particle_render.fx", "VS_ParticleRender");
	pShader->CreatePixelShader(L"shader\\particle_render.fx", "PS_ParticleRender");

	pShader->SetRSType(RS_TYPE::CULL_NONE);
	pShader->SetDSType(DS_TYPE::NO_WRITE);
	pShader->SetBSType(BS_TYPE::ALPHA_BLEND);
	pShader->SetTopology(D3D11_PRIMITIVE_TOPOLOGY::D3D11_PRIMITIVE_TOPOLOGY_POINTLIST);

	pShader->SetDomain(SHADER_DOMAIN::DOMAIN_TRANSPARENT);

	AddRes(pShader->GetKey(), pShader);

```
<br/>

### particle_render.fx<br/>
<br/>

정점 쉐이더에서 지오메트리 쉐이더로, 지오메트리 쉐이더에서 레스터라이저로 넘겨줄 수 있도록 코드를 작성할 것이다.<br/>

```
#ifndef _PARTICLE_RENDER
#define _PARTICLE_RENDER

#include "value.fx"
#include "struct.fx"


// ========================
// Particle Render Shader
// mesh : RectMesh

// Parameter
// g_int_0 : Particle Index
// =========================

StructuredBuffer<tParticle> ParticleBuffer : register(t20);


struct VS_IN
{
    float3 vPos : POSITION;    
    uint iInstID : SV_InstanceID;
};

struct VS_OUT
{
    float3 vPos : POSITION;
    uint iInstID : SV_InstanceID;
};

VS_OUT VS_ParticleRender(VS_IN _in)
{
    VS_OUT output = (VS_OUT) 0.f;      
     
    output.vPos = _in.vPos;
    output.iInstID = _in.iInstID;
    
    return output;
}

// GeometryShader 사용
// 1. 파이프라인 제어
// 2. 빌보드 처리 (카메라를 바라보는..)
struct GS_OUT
{
    float4 vPosition : SV_Position;
    float2 vUV : TEXCOORD;
};

[maxvertexcount(6)]
void GS_ParticleRender (point VS_OUT _in[1], inout TriangleStream<GS_OUT> _outstream)
{    
    uint id = _in[0].iInstID;
    
    if (ParticleBuffer[id].Age < 0.f)
        return;

    float3 vParticleViewPos = mul(float4(ParticleBuffer[id].vWorldPos.xyz, 1.f), g_matView).xyz;
    float2 vParticleScale = ParticleBuffer[id].vWorldScale.xy;
    
    // 0 -- 1
    // |    |
    // 3 -- 2
    float3 NewPos[4] =
    {
        float3(vParticleViewPos.x - vParticleScale.x / 2.f, vParticleViewPos.y + vParticleScale.y / 2.f, vParticleViewPos.z),
        float3(vParticleViewPos.x + vParticleScale.x / 2.f, vParticleViewPos.y + vParticleScale.y / 2.f, vParticleViewPos.z),
        float3(vParticleViewPos.x + vParticleScale.x / 2.f, vParticleViewPos.y - vParticleScale.y / 2.f, vParticleViewPos.z),
        float3(vParticleViewPos.x - vParticleScale.x / 2.f, vParticleViewPos.y - vParticleScale.y / 2.f, vParticleViewPos.z)
    };
    
    GS_OUT output[4] = { (GS_OUT) 0.f, (GS_OUT) 0.f, (GS_OUT) 0.f, (GS_OUT)0.f };
    
    output[0].vPosition = mul(float4(NewPos[0], 1.f), g_matProj);
    output[0].vUV = float2(0.f, 0.f);
    
    output[1].vPosition = mul(float4(NewPos[1], 1.f), g_matProj);
    output[1].vUV = float2(1.f, 0.f);
    
    output[2].vPosition = mul(float4(NewPos[2], 1.f), g_matProj);
    output[2].vUV = float2(1.f, 1.f);
    
    output[3].vPosition = mul(float4(NewPos[3], 1.f), g_matProj);
    output[3].vUV = float2(0.f, 1.f);
    
    
    // 정점 생성
    _outstream.Append(output[0]);
    _outstream.Append(output[1]);
    _outstream.Append(output[2]);
    _outstream.RestartStrip();
    
    _outstream.Append(output[0]);
    _outstream.Append(output[2]);
    _outstream.Append(output[3]);
    _outstream.RestartStrip();
}


float4 PS_ParticleRender(GS_OUT _in) : SV_Target
{    
    return float4(1.f, 0.f, 0.f, 1.f);
}


#endif


```

maxvertexcount(6)은 지오메트리 쉐이더의 함수로, 최대 만들 정점의 수를 지정하는 것이다.<br/>

```
point VS_OUT _in[1], inout TriangleStream<GS_OUT> _outstream
```

위 코드는 정점 1개를 input해서 삼각형 형태로 output을 생성 시키는 것이다.<br/>

해당 정점의 Age값이 음수라면 (비활성화) 바로 return 하여 파이프라인 과정을 중단 시킨다.<br/>

그렇지 않다면 정점의 좌표를 중심으로 입자의 scale 값에 따라서 좌상단, 우상단, 우하단, 좌하단 좌표를 지정하여 정점 4개를 생성하게 한다.<br/>

이때 생성되는 자료는 vPosition(위치 좌표), vUV(UV 좌표)이다.<br/>

output 인자마다 투영행렬을 곱하고, _outstream.Append로 인덱스 버퍼에 인덱스를 넣듯이 면을 형성시킨다.<br/>

<br/>