---
layout: post
title:  "[DirectX] 상수버퍼(Constant Buffer)"
subtitle:  
date: 2022-12-05 20:03:21 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] 상수버퍼(Constant Buffer)<br/>
<br/>

정점 버퍼를 직접 수정하는 방식은 사용하지 않는다.<br/>

비효율적이고 정점 버퍼를 앞으로 계속 사용할 일이 많을 것이기 때문이다.<br/>

그렇다면 정점 버퍼를 어떻게 움직일 것인가?<br/>

상수 버퍼를 사용하면 된다.<br/>
<br/>

## 상수버퍼 (Constant Buffer)<br/>
<br/>

```
// 전역변수
Vec4    g_PlayerPos; // 포지션을 조정할 대상

// 상수 버퍼 생성
// 상수버퍼는 16바이트 단위로 메모리 정렬
// 따라서, 16바이트의 배수로 메모리를 할당해야한다.

D3D11_BUFFER_DESC tBufferDesc = {};

tBufferDesc.ByteWidth = sizeof(Vec4); // 16바이트 크기
tBufferDesc.BindFlags = D3D11_BIND_CONSTANT_BUFFER; // 상수 버퍼 용도 표시

tBufferDesc.Usage = D3D11_USAGE_DYNAMIC; // 수정을 해야하므로
tBufferDesc.CPUAccessFlags = D3D11_CPU_ACCESS_WRITE;



if (FAILED(DEVICE->CreateBuffer(&tBufferDesc, nullptr, g_CB.GetAddressOf())))
{
	assert(nullptr);
}

```
<br/>

HLSL의 상수 레지스터 선언 문법
```
cbuffer TRANSFORM : register(b0)
{
    float4 vPlayerPos;
};
```
16바이트를 b0레지스터에다가 선언했다는 의미.<br/>
<br/>

파이프라인의 render 함수
```
void Render()
{
	// IA
	UINT iStride = sizeof(Vtx);
	UINT iOffset = 0;
	CONTEXT->IASetVertexBuffers(0, 1, g_VB.GetAddressOf(), &iStride, &iOffset);
	CONTEXT->IASetIndexBuffer(g_IB.Get(), DXGI_FORMAT_R32_UINT, 0);

	CONTEXT->IASetInputLayout(g_Layout.Get());
	CONTEXT->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY::D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

	CONTEXT->VSSetConstantBuffers(0, 1, g_CB.GetAddressOf());

	CONTEXT->VSSetShader(g_VS.Get(), nullptr, 0);
	CONTEXT->PSSetShader(g_PS.Get(), nullptr, 0);

	CONTEXT->DrawIndexed(6, 0, 0);
}
```

중요한 사실은 상수버퍼는 16바이트의 배수로 메모리를 잡아야 한다는 것!<br/>
<br/>

HLSL의 vertex Shader 코드

```
VS_OUT VS_Test(VS_IN _in)
{
    VS_OUT output = (VS_OUT) 0.f;
    
    // 입력으로 들어온 정점좌표를 상수버퍼 값을 더해서 출력
    float3 vPos = _in.vPos;
    vPos.xy = vPlayerPos.xy;

    output.vPosition = float4(_in.vPos, 1.f);
    output.vOutColor = _in.vColor;
    
    return output;
}
```

메모리를 생각할 때 중요한 것은

- System Memory
- GPU Memory
- Register Memory : GPU가 빠르게 접근해서 사용할 수 있는 메모리

gpu가 레지스터를 통해 렌더링을 할 수 있도록 하는 작업을 Binding이라고 함.<br/>

