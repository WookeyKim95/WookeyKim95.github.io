---
layout: post
title:  "[DirectX] GPGPU & Texture 클래스 재구성"
subtitle:  
date: 2023-01-19 21:22:51 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] ComputeShader & Texture 클래스 재구성<br/>
<br/>

GPGPU를 사용하기 위한 컴퓨트쉐이더(ComputeShader)에 대해서 알아보자.<br/>
<br/>

## General Purpose GPU와 ComputeShader<br/>
<br/>

작업량 자체가 많은 데이터를 병렬처리를 통해서 처리하는 것에 최적화 된 것이 GPU이다.<br/>

대표적인 작업이 바로 렌더링!<br/>

그런데 비단 렌더링말고 다른 작업에도 GPU를 사용할 수 있을 것이다.<br/>

그동안에는 DirectX를 통해 GPU에게 렌더링 작업을 시켰지만 렌더링만 아니라 다른 작업을 시킬 수 있도록 다른 명령어나 라이브러리를 사용할 것이다.<br/>

DirectX에서 그 기능을 제공한다!<br/>

이때, 일반적인 목적(General Purpose)을 GPU를 통해 달성하고자 할 때에는 컴퓨트쉐이더(ComputeShader)를 사용한다!<br/>

- Nvidia(엔비디아) 측에서는 CUDA라는 그래픽카드 제어 언어를 제공한다.
- Window는 알다시피 DirectX를 통해서 그래픽카드를 제어할 수 있다. (ComputeShader)

Nvidia 그래픽카드를 사용하면 보통 속도를 생각하면 당연히 CUDA가 더 빠르지만,<br/>
우리는 Nvidia뿐만 아니라 다른 그래픽 카드까지 사용하는 MS의 범용성을 생각해서 ComputeShader를 사용할 것 이다.<br/>
<br/>

## CTexture의 재구성<br/>
<br/>


그리고 앞으로는 Render Target Texture와 Depth Stencil Texture을 이제는
자체 텍스쳐 클래스로 전환을 할 것이다.<br/>

```
// CDevice.h 중에서

Ptr<CTexture>           m_RTTex;
Ptr<CTexture>           m_DSTex;
```

그리고 Texture 클래스 안에 RT뷰와 DS뷰가 들어와야 할 것이다.

```
// CTexture.h 중에서

private:
    ComPtr<ID3D11Texture2D>             m_Tex2D;
    ComPtr<ID3D11ShaderResourceView>    m_SRV;
    ComPtr<ID3D11RenderTargetView>	    m_RTV;
    ComPtr<ID3D11DepthStencilView>	    m_DSV;
    ComPtr<ID3D11UnorderedAccessView>   m_UAV;

```
<br/>

### CreateTexture 함수의 구현<br/>
<br/>

이제는 텍스쳐를 자체적으로 제작을 해야하므로 Texture을 로딩하는 것 뿐만 아니라

자체적으로 생성하는 기능(Create 함수)도 만들어줘야 한다.
```
// CTexture.h 중에서

public:
    int Create(UINT _Width, UINT _Height, DXGI_FORMAT _pixelformat
        , UINT _BindFlag, D3D11_USAGE _Usage);

    int Create(ComPtr<ID3D11Texture2D> _tex2D);
```
- 가로, 세로 해상도 정보를 알려준다.
- 포맷을 알려준다.(pixel format)
- 만들어지는 텍스쳐의 BindFlag를 알려준다. (이 때, 조합을 위해 uint로 받는다.)
- 사용 용도를 알려준다.

```
// CTexture.cpp 중에서 Create 함수 구현

// DepthStencil 뷰 등 새로운 텍스쳐를 만드는 함수

int CTexture::Create(UINT _Width, UINT _Height, DXGI_FORMAT _pixelformat
	, UINT _BindFlag, D3D11_USAGE _Usage)
{
	// ID3D11Texture2D 생성
	m_Desc.Format = _pixelformat;

	// 반드시 렌더타겟과 같은 해상도로 설정해야 함
	m_Desc.Width = _Width;
	m_Desc.Height = _Height;
	m_Desc.ArraySize = 1;

	m_Desc.BindFlags = _BindFlag;
	m_Desc.Usage = _Usage;

	if (D3D11_USAGE::D3D11_USAGE_DYNAMIC == _Usage)
		m_Desc.CPUAccessFlags = D3D11_CPU_ACCESS_WRITE;
	else if(D3D11_USAGE::D3D11_USAGE_STAGING == _Usage)
		m_Desc.CPUAccessFlags = D3D11_CPU_ACCESS_READ;

	m_Desc.MipLevels = 1;    // 원본만 생성
	m_Desc.SampleDesc.Count = 1;
	m_Desc.SampleDesc.Quality = 0;


	if (FAILED(DEVICE->CreateTexture2D(&m_Desc, nullptr, m_Tex2D.GetAddressOf())))
	{
		return E_FAIL;
	}

	// 바인드 플래그에 맞는 View 를 생성해준다.
	if (m_Desc.BindFlags & D3D11_BIND_DEPTH_STENCIL)
	{
		if (FAILED(DEVICE->CreateDepthStencilView(m_Tex2D.Get(), nullptr, m_DSV.GetAddressOf())))
		{
			return E_FAIL;
		}
	}
	else
	{
		if (m_Desc.BindFlags & D3D11_BIND_RENDER_TARGET)
		{
			if (FAILED(DEVICE->CreateRenderTargetView(m_Tex2D.Get(), nullptr, m_RTV.GetAddressOf())))
			{
				return E_FAIL;
			}
		}

		if (m_Desc.BindFlags & D3D11_BIND_SHADER_RESOURCE)
		{
			if (FAILED(DEVICE->CreateShaderResourceView(m_Tex2D.Get(), nullptr, m_SRV.GetAddressOf())))
			{
				return E_FAIL;
			}
		}

		if (m_Desc.BindFlags & D3D11_BIND_UNORDERED_ACCESS)
		{
			if (FAILED(DEVICE->CreateUnorderedAccessView(m_Tex2D.Get(), nullptr, m_UAV.GetAddressOf())))
			{
				return E_FAIL;
			}
		}
	}


	return S_OK;
}
```

- 사용 용도가 Dynamic일 경우, CPUAccessFlag : WRITE
- 사용 용도가 Staging일 경우, CPUAccessFlag : READ
- 둘 다 아닐 경우는 Default

```
// CTexture.cpp 중에서 Create 함수 구현

// 이미 만들어져있는 texture 2D가 통으로 들어오는 경우

int CTexture::Create(ComPtr<ID3D11Texture2D> _tex2D)
{
	m_Tex2D = _tex2D;

	m_Tex2D->GetDesc(&m_Desc);

	// 바인드 플래그에 맞는 View 를 생성해준다.
	if (m_Desc.BindFlags & D3D11_BIND_DEPTH_STENCIL)
	{
		if (FAILED(DEVICE->CreateDepthStencilView(m_Tex2D.Get(), nullptr, m_DSV.GetAddressOf())))
		{
			return E_FAIL;
		}
	}
	else
	{
		if (m_Desc.BindFlags & D3D11_BIND_RENDER_TARGET)
		{
			if (FAILED(DEVICE->CreateRenderTargetView(m_Tex2D.Get(), nullptr, m_RTV.GetAddressOf())))
			{
				return E_FAIL;
			}
		}

		if (m_Desc.BindFlags & D3D11_BIND_SHADER_RESOURCE)
		{
			if (FAILED(DEVICE->CreateShaderResourceView(m_Tex2D.Get(), nullptr, m_SRV.GetAddressOf())))
			{
				return E_FAIL;
			}
		}

		if (m_Desc.BindFlags & D3D11_BIND_UNORDERED_ACCESS)
		{
			if (FAILED(DEVICE->CreateUnorderedAccessView(m_Tex2D.Get(), nullptr, m_UAV.GetAddressOf())))
			{
				return E_FAIL;
			}
		}
	}

	return S_OK;
}
```

위 코드와 같이 상황에 맞게 바인딩을 걸어준다.<br/>

주의사항<br/>

Shader Resource 용도, Render Target 용도는 조합을 이룰 수 있으나

Depth Stencil Flag는 다른 Flag랑 조합이 될 수 없음.<br/>
<br/>

### 적용해보기<br/>
<Br/>

```
// CDevice.cpp 중에서 CreateView 함수

int CDevice::CreateView()
{
    ComPtr<ID3D11Texture2D> tex2D;
    m_SwapChain->GetBuffer(0, __uuidof(ID3D11Texture2D), (void**)tex2D.GetAddressOf());
    
    // RenderTarget 용 텍스쳐 등록
    m_RTTex = CResMgr::GetInst()->CreateTexture(L"RenderTargetTex", tex2D);

    // DepthStencil 용도 텍스쳐 생성
    m_DSTex = CResMgr::GetInst()->CreateTexture(L"DepthStencilTex"
        , (UINT)m_vRenderResolution.x, (UINT)m_vRenderResolution.y
        , DXGI_FORMAT_D24_UNORM_S8_UINT, D3D11_BIND_DEPTH_STENCIL, D3D11_USAGE_DEFAULT);        


    return S_OK;
}
```


### 본격적으로 색칠해보기<br/>
<br/>


Monster 객체를 전부 파란색이 나오도록 덮어보자.<br/>

```
// test.fx 파일 중에서

// ===============
// SetColor Shader
// mesh : RectMesh
// ===============
VS_OUT VS_SetColor(VS_IN _in)
{
    VS_OUT output = (VS_OUT) 0.f;
    
    output.vPosition = float4(_in.vPos.xy * 2.f, 0.5f, 1.f);        
    output.vOutUV = _in.vUV;
    
    return output;
}

float4 PS_SetColor(VS_OUT _in) : SV_Target
{
    float4 vColor = (float4) 0.f;   
    
    float fDist = abs(distance(_in.vOutUV, float2(0.5f, 0.5f)));
    
    if (0.5f < fDist)
        discard;
    
    vColor = float4(0.f, 1.f, 0.f, 1.f);
    
    return vColor;
}
```


```
// CLevelMgr.cpp 중에서

	// Monster
	CGameObject* pMonster = new CGameObject;
	pMonster->SetName(L"Monster");

	pMonster->AddComponent(new CTransform);
	pMonster->AddComponent(new CMeshRender);
	pMonster->AddComponent(new CCollider2D);
	pMonster->AddComponent(new CMonsterScript);

	pMonster->Transform()->SetRelativePos(Vec3(0.f, 250.f, 100.f));
	pMonster->Transform()->SetRelativeScale(Vec3(200.f, 200.f, 1.f));
	
	pMonster->MeshRender()->SetMesh(CResMgr::GetInst()->FindRes<CMesh>(L"RectMesh"));
	pMonster->MeshRender()->SetMaterial(CResMgr::GetInst()->FindRes<CMaterial>(L"Std2DMtrl"));
	pMonster->MeshRender()->GetMaterial()->SetTexParam(TEX_0, pCreateTex);

	pMonster->Collider2D()->SetAbsolute(true);
	pMonster->Collider2D()->SetOffsetScale(Vec2(100.f, 100.f));

	m_pCurLevel->AddGameObject(pMonster, L"Monster", false);
```