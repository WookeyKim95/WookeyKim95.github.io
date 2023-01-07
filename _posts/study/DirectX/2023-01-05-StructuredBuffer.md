---
layout: post
title:  "[DirectX] 구조화 버퍼"
subtitle:  
date: 2023-01-05 21:40:21 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] 구조화 버퍼<br/>
<br/>

## 광원이 늘어나거나 줄어들 경우?<Br/>
<Br/>

광원이 늘어나거나 줄어들 경우에는 어떻게 해야할까?<br/>

상수레지스터는 크기 제한이 있으나 텍스쳐는 무엇이 들어올지 알 수 없기 때문에 포인터로 가리키는 개념이다.<br/>

그래서 레지스터에서 참조 형태로 접근이 가능하다.<br/>

이게 된다면 어떻게 되느냐, LightInfo의 크기 별로 버퍼를 생성할 수 있다.<br/>

상수버퍼처럼 크기를 고정시키는게 아니다. 그래서 가변적으로 바인딩을 할 수 있다.<br/>

```
cbuffer LIGHT : register(b2)
{
    tLightInfo  arrInfo[10];
    int         iLightCount;
    int3        iLightPadding;
}

Texture2D g_tex_0 : register(t0);
...

StructuredBuffer<tLightInfo> g_buffer_0 : register(t8);

SamplerState g_sam_0 : register(s0);
SamplerState g_sam_1 : register(s1);

```

그래서 구조화 버퍼에다가 값을 넣어서 텍스쳐 레지스터에다가 바인딩을 거는 형식으로 광원을 생성해볼 수 있다.<br/>
<br/>

## 구조화 버퍼의 클래스 구성<br/>
<br/>

```
// 헤더파일

#pragma once
#include "CEntity.h"

class CStructuredBuffer :
    public CEntity
{
private:
    ComPtr<ID3D11Buffer>                m_SB;
    ComPtr<ID3D11ShaderResourceView>    m_SRV;
    D3D11_BUFFER_DESC                   m_tDesc;

    UINT                                m_iElementSize;
    UINT                                m_iElementCount;

public:
    void Create(UINT _iElementSize, UINT _iElementCount);
    void SetData(void* _pSrc, UINT _iSize = 0);

    // PIPELINE_STAGE
    void UpdateData(UINT _iRegisterNum, UINT _iPipeLineStage);

    UINT GetElementSize() { return m_iElementSize; }
    UINT GetElementCount() { return m_iElementCount; }



    CLONE_DISABLE(CStructuredBuffer);
public:
    CStructuredBuffer();
    ~CStructuredBuffer();
};
```

```
// cpp 파일

#include "pch.h"
#include "CStructuredBuffer.h"

#include "CDevice.h"

CStructuredBuffer::CStructuredBuffer()
	: m_iElementSize(0)
	, m_iElementCount(0)
{
}

CStructuredBuffer::~CStructuredBuffer()
{
}

void CStructuredBuffer::Create(UINT _iElementSize, UINT _iElementCount)
{
	m_SB = nullptr;
	m_SRV = nullptr;


	m_iElementSize = _iElementSize;
	m_iElementCount = _iElementCount;

	UINT iBufferSize = m_iElementSize * _iElementCount;

	// 16바이트 단위 메모리 정렬
	assert(!(iBufferSize % 16));

	// 상수버퍼 생성
	m_tDesc.ByteWidth = iBufferSize;				// 버퍼 크기
	m_tDesc.StructureByteStride = m_iElementSize;	// 데이터 간격

	m_tDesc.BindFlags = D3D11_BIND_SHADER_RESOURCE;	// Texture 레지스터에 바이딩하기 위한 플래그
	m_tDesc.MiscFlags = D3D11_RESOURCE_MISC_BUFFER_STRUCTURED;	// 구조화 버퍼 체크

	m_tDesc.Usage = D3D11_USAGE_DYNAMIC;
	m_tDesc.CPUAccessFlags = D3D11_CPU_ACCESS_WRITE;
	
	if (FAILED(DEVICE->CreateBuffer(&m_tDesc, nullptr, m_SB.GetAddressOf())))
	{
		assert(nullptr);
	}


	// ShaderResourceView 생성
	D3D11_SHADER_RESOURCE_VIEW_DESC	m_SRVDesc = {};

	m_SRVDesc.ViewDimension = D3D_SRV_DIMENSION_BUFFEREX;
	m_SRVDesc.BufferEx.NumElements = m_iElementCount;

	if (FAILED(DEVICE->CreateShaderResourceView(m_SB.Get(), &m_SRVDesc, m_SRV.GetAddressOf())))
	{
		assert(nullptr);
	}
}

void CStructuredBuffer::SetData(void* _pSrc, UINT _iSize)
{
	D3D11_MAPPED_SUBRESOURCE tSub = {};
	CONTEXT->Map(m_SB.Get(), 0, D3D11_MAP::D3D11_MAP_WRITE_DISCARD, 0, &tSub);

	memcpy(tSub.pData, _pSrc, _iSize);	

	CONTEXT->Unmap(m_SB.Get(), 0);
}

void CStructuredBuffer::UpdateData(UINT _iRegisterNum, UINT _iPipeLineStage)
{
	if (PIPELINE_STAGE::PS_VERTEX & _iPipeLineStage)
	{
		CONTEXT->VSSetShaderResources(_iRegisterNum, 1, m_SRV.GetAddressOf());
	}

	if (PIPELINE_STAGE::PS_HULL & _iPipeLineStage)
	{
		CONTEXT->HSSetShaderResources(_iRegisterNum, 1, m_SRV.GetAddressOf());
	}

	if (PIPELINE_STAGE::PS_DOMAIN & _iPipeLineStage)
	{
		CONTEXT->DSSetShaderResources(_iRegisterNum, 1, m_SRV.GetAddressOf());
	}

	if (PIPELINE_STAGE::PS_GEOMETRY & _iPipeLineStage)
	{
		CONTEXT->GSSetShaderResources(_iRegisterNum, 1, m_SRV.GetAddressOf());
	}

	if (PIPELINE_STAGE::PS_PIXEL & _iPipeLineStage)
	{
		CONTEXT->PSSetShaderResources(_iRegisterNum, 1, m_SRV.GetAddressOf());
	}
}
```

구조화 버퍼를 생성할 때

- 텍스쳐가 바인드로 쉐이더 리소스를 쓰므로 Bing Flag로 D3D11_BIND_SHADER_RESOURCE로 잡아준다.<br/>
- 텍스쳐인지 구조화 버퍼인지를 구분해주기 위해서 MiscFlag를 설정해준다. (D3D11_RESOURCE_MISC_BUFFER_STRUCTURED)
- 쉐이더 리소스뷰를 반드시 활용한다.<br/>


나머지는 상수 버퍼를 생성할 때와 설정이 같다.<br/>
<br/>

## 구조화 버퍼 적용하기<br/>
<Br/>

### CStructuredBuffer.cpp 파일 중에서<br/>
<Br/>

렌더 매니저의 init함수에서 구조화 버퍼를 생성해서 적용할 수 있다.<br/>

그리고 이제는 상수버퍼로 데이터를 보내는 것이 아니라 LightBuffer에다가 데이터를 보내도록 조치할 것이다.<br/>

```
// CStructuredBuffer.cpp 에서
void CStructuredBuffer::SetData(void* _pSrc, UINT _iSize)
{
	D3D11_MAPPED_SUBRESOURCE tSub = {};
	CONTEXT->Map(m_SB.Get(), 0, D3D11_MAP::D3D11_MAP_WRITE_DISCARD, 0, &tSub);

	memcpy(tSub.pData, _pSrc, _iSize);	

	CONTEXT->Unmap(m_SB.Get(), 0);
}

```

SetData함수에서 구조화 버퍼를 Map, UnMap을 사용하면서 동적으로 사용할 수 있도록 한다.<br/>

<br/>

## 새로운 상수버퍼 적용하기<br/>
<br/>

그리고 구조화 버퍼안에 광원의 개수는 몇개인지 정보를 따로 보내줘야 한다.<br/>

목적은 효율적인 메모리 관리이다.<br/>

이는 새로운 상수버퍼에서 실행할 것이다.<br/>
<br/>

### value.fx 파일 중에서<br/>

```

cbuffer GLOBAL : register(b2)
{
    float2 g_Resolution; // 해상도
    float  g_DT; // Delta Time
    float  g_AccTime;
    uint   g_Light2DCount; // 2차원 광원의 개수
    uint   g_Light3DCount; // 3차원 광원의 개수
    int2   g_globalpadding; // 16바이트 단위로 채우기 위한 패딩
}

```

이제 b2는 전역 상수버퍼로서 역할을 수행할 것이다.<br/>
<br/>

### define.h 파일 중에서<br/>
<br/>

그리고 이를 맞추기 위해서 define.h파일에서도 수정을 한다.

```
enum class CB_TYPE
{
	TRANSFORM,	// b0
	MATERIAL,	// b1
	GLOBAL,		// b2
	END,
};
```
<br/>

### struct.h 파일 중에서<br/>
<br/>

```
struct tGlobal
{
	Vec2  Resolution;
	float tDT;
	float tAccTime;
	UINT  Light2DCount;
	UINT  Light3DCount;
	int	  globalpadding[2];
};

extern tGlobal GlobalData;
```

글로벌 헤더에 입력된 값을 구조체로 맞춰준다.<br/>
<br/>

### CDevice.cpp 파일 중에서<br/>
<br/>

그리고 글로벌 버퍼를 CDevice.cpp에서 생성해준다.<br/>

```
void CDevice::CreateConstBuffer()
{
    m_arrConstBuffer[(UINT)CB_TYPE::TRANSFORM] = new CConstBuffer((UINT)CB_TYPE::TRANSFORM);
    m_arrConstBuffer[(UINT)CB_TYPE::TRANSFORM]->Create(sizeof(tTransform), 1);

    m_arrConstBuffer[(UINT)CB_TYPE::MATERIAL] = new CConstBuffer((UINT)CB_TYPE::MATERIAL);
    m_arrConstBuffer[(UINT)CB_TYPE::MATERIAL]->Create(sizeof(tMtrlConst), 1);

    m_arrConstBuffer[(UINT)CB_TYPE::GLOBAL] = new CConstBuffer((UINT)CB_TYPE::GLOBAL);
    m_arrConstBuffer[(UINT)CB_TYPE::GLOBAL]->Create(sizeof(tGlobal), 1);
}
```
<br/>

### CRenderMgr.cpp 파일 중에서<br/>
<br/>

GlobalData버퍼의 Light2DCount에다가 광원의 개수를 채워 넣는다.<br/>

그리고 디바이스로부터 전역 상수버퍼를 가져와서 데이터를 업데이트시킨다.<br/>

```
void CRenderMgr::UpdateData()
{
    // GlobalData 에 광원 개수정보 세팅
    GlobalData.Light2DCount = m_vecLight2D.size();

    // 구조화버퍼의 크기가 모자라면 더 크게 새로 만든다.
    if (m_Light2DBuffer->GetElementCount() < m_vecLight2D.size())
    {
        m_Light2DBuffer->Create(sizeof(tLightInfo), m_vecLight2D.size());
    }

    // 구조화버퍼로 광원 데이터를 옮긴다.
    m_Light2DBuffer->SetData(m_vecLight2D.data(), sizeof(tLightInfo) * m_vecLight2D.size());
    m_Light2DBuffer->UpdateData(8, PIPELINE_STAGE::PS_PIXEL);


    // 전역 상수 데이터 바인딩
    CConstBuffer* pGlobalBuffer = CDevice::GetInst()->GetConstBuffer(CB_TYPE::GLOBAL);
    pGlobalBuffer->SetData(&GlobalData, sizeof(tGlobal));
    pGlobalBuffer->UpdateData();
}
```