---
layout: post
title:  "[DirectX] ComputeShader"
subtitle:  
date: 2023-01-31 19:53:23 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] ComputeShader<br/>
<br/>

## UAV(Unordered Access View)<br/>
<br/>

읽기와 쓰기가 동시에 되는 레지스터가 UAV이다.<br/>

<br/>

### SetColor.fx (test 쉐이더 코드)<br/>
<Br/>

```
#ifndef _SETCOLOR
#define _SETCOLOR

#include "value.fx"

RWTexture2D<float4> g_Output : register(u0);

[numthreads(1, 1, 1)] // 스레드 그룹 개수 (1 * 1 * 1)
// 만약에 2, 2, 2가 되었다면 스레드 그룹 개수는 8 (2 * 2 * 2)개.
void CS_SetColor(int3 _ID : SV_DispatchThreadID)
{
    g_Output[0] = float4(1.f, 0.f, 0.f, 1.f);
}

#endif

```

u0, 즉 u로 시작하는 레지스터 번호가 읽기와 쓰기가 동시에 되는 레지스터 번호이다.<br/>
그리고 선언할 때에도 기존 레지스터의 선언방식과는 달리, RW를 앞에 붙이고, 뒤에는 픽셀 포맷을 적어줘야한다.<br/>

```
RWTexture2D<float4> g_Output : register(u0);
```

스레드의 개수는 위에 나온 코드와 같이 numthreads(a, b, c)에서 a, b, c의 값을 조정하여 조절할 수 있으며, a * b * c의 값이 호출되는 스레드 그룹의 개수이다.<br/>

HLSL 5.0기준, 최대 호출 가능 개수는 1024개이다.<br/>
<br/>

## ID값 활용하기<br/>
<Br/>

쓰레드마다 자신이 받아오는 _ID 값이 다르며, 이를 활용해서 각 쓰레드가 다른 픽셀을 칠하도록 만들어야 할 것이다.<br/>

<br/>


### ComputeShader 입력 Sematic<br/>
<br/>

- SV_GroupThreadID : 그룹 내에서의 인덱스 좌표
- SV_GroupID : 스레드가 몇번째 그룹에 속해있는가?
- SV_GroupIndex : 모든 스레드 그룹을 나열했을 때 자신의 위치
- SV_DispatchThreadID : 그룹 내에서 (0,0,0) 으로부터 몇 번째 스레드인가?

<br/>

### 예시 Dispatch : (5, 3, 2), ThreadGroup : (10, 8, 3)<br/>
<Br/>

- SV_GroupThreadID : (7, 5, 0)
- SV_GroupID : (2, 1, 0)
- SV_GroupIndex : ((2,1,0) * (10, 8, 3)) + (7, 5, 0) = (27, 13, 0)
- SV_DispatchThreadID : (0 * 10 * 8 + 5 * 10 + 7) = 57 



```
CONTEXT->Dispatch(x, y, z);
```
위의 코드에서 x, y, z의 값을 지정해서 스레드 그룹의 개수를 지정할 수 있다.<br/>

```
#ifndef _SETCOLOR
#define _SETCOLOR

#include "value.fx"

RWTexture2D<float4> g_Output : register(u0);

[numthreads(32, 32, 1)]
void CS_SetColor(int3 _ID : SV_DispatchThreadID)
{
    g_Output[_ID.xy] = float4(1.f, 0.f, 0.f, 1.f);
}

#endif

```

스레드 그룹의 개수는 1024(32 * 32 * 1 구성)개, 색칠해야하는 픽셀의 개수는 1280 * 768개 일때?<br/>

거로로 40개, 세로로 24개가 필요하므로

```
CONTEXT->Dispatch(40, 24, 1);
```
을 지정하면 된다.<br/>

그리고 자신의 담당픽셀을 칠하기 위해서
```
g_Output[_ID.xy]
```
fx파일에서 이렇게 작성해서 매칭시킨다.<br/>
<br/>

## ComputeShader 클래스의 구성<br/>
<br/>

```
// 헤더파일

#pragma once
#include "CShader.h"

class CComputeShader :
    public CShader
{
private:
    ComPtr<ID3D11ComputeShader>     m_CS;
    ComPtr<ID3DBlob>                m_CSBlob;

public:
    void CreateComputeShader(const wstring& _strFileName, const string& _strFuncName);

    void Dispatch(UINT _X, UINT _Y, UINT _Z);
    virtual void UpdateData();
    virtual void Clear();


    CLONE_DISABLE(CComputeShader);
public:
    CComputeShader();
    ~CComputeShader();
};
```

```
// cpp 파일

#include "pch.h"
#include "CComputeShader.h"

#include "CPathMgr.h"
#include "CDevice.h"


CComputeShader::CComputeShader()
	: CShader(RES_TYPE::COMPUTE_SHADER)
{
}

CComputeShader::~CComputeShader()
{
}

void CComputeShader::CreateComputeShader(const wstring& _strFileName, const string& _strFuncName)
{
	// Shader 파일 경로
	wstring strShaderFile = CPathMgr::GetInst()->GetContentPath();
	strShaderFile += _strFileName;

	// Shader Compile	
	if (FAILED(D3DCompileFromFile(strShaderFile.c_str(), nullptr, D3D_COMPILE_STANDARD_FILE_INCLUDE
		, _strFuncName.c_str(), "cs_5_0", 0, 0, m_CSBlob.GetAddressOf(), m_ErrBlob.GetAddressOf())))
	{
		MessageBoxA(nullptr, (const char*)m_ErrBlob->GetBufferPointer()
			, "Compute Shader Compile Failed!!", MB_OK);
	}

	// 컴파일된 객체로 Shader 를 만든다.
	DEVICE->CreateComputeShader(m_CSBlob->GetBufferPointer(), m_CSBlob->GetBufferSize()
		, nullptr, m_CS.GetAddressOf());
}

void CComputeShader::Dispatch(UINT _X, UINT _Y, UINT _Z)
{
	CONTEXT->CSSetShader(m_CS.Get(), nullptr, 0);
	CONTEXT->Dispatch(_X, _Y, _Z);
}

void CComputeShader::UpdateData()
{	
	
}

void CComputeShader::Clear()
{
}
```
<br/>

## ResMgr에서 컴퓨트 쉐이더 추가하기<br/>

```
// CResMgr.cpp 중에서 CreateDefaultComputeShader함수

void CResMgr::CreateDefaultComputeShader()
{
	Ptr<CComputeShader> pCS = nullptr;

	// Texture 색상 변경 쉐이더
	pCS = new CComputeShader;
	pCS->SetKey(L"SetColorCS");
	pCS->CreateComputeShader(L"shader\\setcolor.fx", "CS_SetColor");
	AddRes(pCS->GetKey(), pCS);
}

```

## UpdateData_CS함수<br/>
<Br/>

파이프라인 스테이지로 보내는 것이 아니라 UAV를 사용하도록 새로운 함수를 생성해준다.<br/>

```
// CTexture.cpp 파일 중에서

void CTexture::UpdateData_CS(int _iRegisterNum)
{
	m_iRecentCSNum = _iRegisterNum;

	UINT i = -1;
	CONTEXT->CSSetUnorderedAccessViews(_iRegisterNum, 1, m_UAV.GetAddressOf(), &i);
}
```
<br/>

### 적용해보기<br/>

```
// CLevelMgr.cpp 중에서 init 함수

    // 텍스쳐 색칠하기
	// 텍스쳐 생성(UnorderedAccess)
	Ptr<CTexture> pCreateTex = CResMgr::GetInst()->CreateTexture(
								L"SampleTexture"
								, 1280, 768
								, DXGI_FORMAT::DXGI_FORMAT_R8G8B8A8_UNORM
								, D3D11_BIND_SHADER_RESOURCE | D3D11_BIND_UNORDERED_ACCESS
								, D3D11_USAGE_DEFAULT);		 
	
	// U0 에 바인딩
	pCreateTex->UpdateData_CS(0);

	// ComputeShader 로 텍스쳐 색 변경하기
	Ptr<CComputeShader> pCS = CResMgr::GetInst()->FindRes<CComputeShader>(L"SetColorCS");
	pCS->Dispatch(pCreateTex->Width() / 32, pCreateTex->Height() / 32, 1);

	// U0 에 바인딩 된 텍스쳐 해제
	pCreateTex->Clear();

```

그리고 바인딩 해제를 위한 Clear 함수 구현

```
// CTexture.cpp 파일 중에서

void CTexture::Clear()
{
	ID3D11UnorderedAccessView* pUAV = nullptr;
	UINT i = -1;
	CONTEXT->CSSetUnorderedAccessViews(m_iRecentCSNum, 1, &pUAV, &i);
}

```
<br/>

## C++ 클래스와 연동<br/>
<Br/>

일일이 셰이더 코드를 호출할 때 마다 스레드 개수를 일일이 계산해줄 필요가 없도록 하고 싶다.<br/>

이를 위해서 setcolor 셰이더코드를 C++ 클래스화 시킨다면?<br/>
<br/>

### CComputeShader 클래스의 재구성<br/>
<br/>

```
// 헤더파일

#pragma once
#include "CShader.h"

class CComputeShader :
    public CShader
{
private:
    ComPtr<ID3D11ComputeShader>     m_CS;
    ComPtr<ID3DBlob>                m_CSBlob;    

protected:
    // 쉐이더에 전달할 상수 데이터
    tMtrlConst                      m_Const;

    // 그룹 개수
    UINT                            m_iGroupX; 
    UINT                            m_iGroupY;
    UINT                            m_iGroupZ;

    // 그룹 1개당 스레드 개수
    UINT                            m_iGroupPerThreadX;
    UINT                            m_iGroupPerThreadY;
    UINT                            m_iGroupPerThreadZ;

public:
    void CreateComputeShader(const wstring& _strFileName, const string& _strFuncName);
    void Execute();

private:
    virtual void UpdateData() = 0;
    virtual void Clear() = 0;


    CLONE_DISABLE(CComputeShader);
public:
    CComputeShader();
    ~CComputeShader();
};



```

```
// cpp 파일

#include "pch.h"
#include "CComputeShader.h"

#include "CPathMgr.h"
#include "CDevice.h"
#include "CConstBuffer.h"


CComputeShader::CComputeShader()
	: CShader(RES_TYPE::COMPUTE_SHADER)
	, m_iGroupX(1)
	, m_iGroupY(1)	
	, m_iGroupZ(1)
{
}

CComputeShader::~CComputeShader()
{
}

void CComputeShader::CreateComputeShader(const wstring& _strFileName, const string& _strFuncName)
{
	// Shader 파일 경로
	wstring strShaderFile = CPathMgr::GetInst()->GetContentPath();
	strShaderFile += _strFileName;

	// Shader Compile	
	if (FAILED(D3DCompileFromFile(strShaderFile.c_str(), nullptr, D3D_COMPILE_STANDARD_FILE_INCLUDE
		, _strFuncName.c_str(), "cs_5_0", 0, 0, m_CSBlob.GetAddressOf(), m_ErrBlob.GetAddressOf())))
	{
		MessageBoxA(nullptr, (const char*)m_ErrBlob->GetBufferPointer()
			, "Compute Shader Compile Failed!!", MB_OK);
	}

	// 컴파일된 객체로 Shader 를 만든다.
	DEVICE->CreateComputeShader(m_CSBlob->GetBufferPointer(), m_CSBlob->GetBufferSize()
		, nullptr, m_CS.GetAddressOf());
}

void CComputeShader::Execute()
{
	UpdateData();

	static CConstBuffer* pCB = CDevice::GetInst()->GetConstBuffer(CB_TYPE::MATERIAL);
	pCB->SetData(&m_Const);
	pCB->UpdateData_CS();

	CONTEXT->CSSetShader(m_CS.Get(), nullptr, 0);
	CONTEXT->Dispatch(m_iGroupX, m_iGroupY, m_iGroupZ);

	Clear();
}


```

UpdataData와 Clear함수를 가상함수화 시켜서<br/>

자식클래스가 사용할 수 있는 추상클래스로 만들어준다.<br/>
<br/>

### CSetColorShader 클래스의 구성<br/>
<br/>

```
// 헤더 파일

#pragma once
#include "CComputeShader.h"

#include "ptr.h"
#include "CTexture.h"

class CSetColorShader :
    public CComputeShader
{
private:
    Ptr<CTexture>   m_OutTex;    

public:
    void SetTargetTexture(Ptr<CTexture> _Tex) {m_OutTex = _Tex; }
    void SetColor(Vec3 _RGB) { m_Const.arrV4[0] = _RGB; }

public:
    virtual void UpdateData() override;
    virtual void Clear() override;

public:
    CSetColorShader(UINT _iGroupPerThreadX, UINT _iGroupPerThreadY, UINT _iGroupPerThreadZ);
    ~CSetColorShader();
};


```

```
// cpp 파일

#include "pch.h"
#include "CSetColorShader.h"

#include "CTexture.h"

CSetColorShader::CSetColorShader(UINT _iGroupPerThreadX, UINT _iGroupPerThreadY, UINT _iGroupPerThreadZ)
{
	m_iGroupPerThreadX = _iGroupPerThreadX;
	m_iGroupPerThreadY = _iGroupPerThreadY;
	m_iGroupPerThreadZ = _iGroupPerThreadZ;
}

CSetColorShader::~CSetColorShader()
{
}

void CSetColorShader::UpdateData()
{
	m_OutTex->UpdateData_CS(0);

	// 그룹 개수 계산
	m_iGroupX = m_OutTex->Width() / m_iGroupPerThreadX;
	m_iGroupY = m_OutTex->Height() / m_iGroupPerThreadY;
	m_iGroupZ = 1;
}

void CSetColorShader::Clear()
{
	m_OutTex->Clear();
}

```

CComputeShader 클래스를 부모 클래스로 설정하고

- UpdateData
- Clear

두 함수를 CComputeShader에선 가상함수로 설정하고, 자식 클래스에서 오버라이딩해서 구현해서 사용한다.<br/>

m_OutTex는 색칠할 텍스쳐, 출력 텍스쳐를 받는다.<br/>

UpdateData가 호출될 때는 CComputeShader에서 Execute함수가 호출 되었을 때 호출된다.<br/>

그리고 자식의 UpdataData 함수에서 해상도에 따른 그룹 개수 계산을 해준다.
<Br/>
<br/>

### 원하는 색을 칠하도록 만들기<br/>
<Br/>

지금까지는 오직 한 가지의 색상만 칠할 수 있었는데, 어떻게 하면 원하는 색상을 골라서 칠할 수 있을까?<br/>

원하는 색상을 상수버퍼가 들고있는 재질을 통해서 전달해주는 점을 주목해서 ComputeShader에다가 재질에 관련된 변수를 추가해주면 된다.<br/>

그리고 자식클래스에서 SetColor함수를 설정해서 원하는 값의 RGB가 설정되도록 만들면 된다.<br/>

```
// CComputeShader의 Execute 함수 중에서

static CConstBuffer* pCB = CDevice::GetInst()->GetConstBuffer(CB_TYPE::MATERIAL);
```

상수버퍼를 불러와서 상수값 혹은 색깔 데이터를 세팅한다.<br/>

그러고나서 자식 클래스를 본격적으로 실행시킨다.<br/>

한편, setcolor.fx 파일에서 함수는 아래와 같이 바꾼다.

```

#include "value.fx"



// ComputeShader 입력 Sematic
// SV_GroupThreadID      : 그룹 내에서의 인덱스(3차원)
// SV_GroupID            : 그룹 인덱스
// SV_GroupIndex         : 그룹 내에서의 인덱스(1차원)
// SV_DispatchThreadID   : 전체 그룹 기준, 스레드의 인덱스(3차원)

RWTexture2D<float4> g_Output : register(u0);
#define Color       g_vec4_0 

// 스레드 그룹 개수(HLSL 5.0 기준 1024 제한)
[numthreads(32, 32, 1)]
void CS_SetColor(int3 _ID : SV_DispatchThreadID)
{    
    g_Output[_ID.xy] = float4(Color.xyz, 1.f);
}



#endif
```
<br/>


### 적용해보기<br/>
<Br/>

```
// CLevelMgr.cpp init 함수 중에서

	// 텍스쳐 색칠하기
	// 텍스쳐 생성(UnorderedAccess)
	Ptr<CTexture> pCreateTex = CResMgr::GetInst()->CreateTexture(
								L"SampleTexture"
								, 1280, 768
								, DXGI_FORMAT::DXGI_FORMAT_R8G8B8A8_UNORM
								, D3D11_BIND_SHADER_RESOURCE | D3D11_BIND_UNORDERED_ACCESS
								, D3D11_USAGE_DEFAULT);	
	
	// ComputeShader 로 텍스쳐 색 변경하기
	Ptr<CSetColorShader> pCS = (CSetColorShader*)CResMgr::GetInst()->FindRes<CComputeShader>(L"SetColorCS").Get();
	pCS->SetTargetTexture(pCreateTex);
	pCS->SetColor(Vec3(0.f, 1.f, 1.f));
	pCS->Execute();
```
<Br/>

