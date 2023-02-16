---
layout: post
title:  "[DirectX] Particle Spawn Module"
subtitle:  
date: 2023-02-16 17:31:15 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] Particle Spawn Module<br/>
<br/>

현재 파티클을 다루는 모듈들은 스폰 모듈, 색상 변경 모듈, 크기 변경 모듈이 있다.<br/>

여기서, 스폰 모듈을 다루고, 파티클의 모듈 활성화 여부에 따라 작동되도록 해보자.<br/>
<br/>

## Spawn Module<br/>
<Br/>

### particle_update.fx 파일<br/>
<br/>

```
#ifndef _PARTICLE_UPDATE
#define _PARTICLE_UPDATE

#include "value.fx"
#include "struct.fx"
#include "func.fx"

RWStructuredBuffer<tParticle>       ParticleBuffer : register(u0);
RWStructuredBuffer<int4>            ParticleSpawnCount : register(u1);
StructuredBuffer<tParticleModule>   ParticleModuleData : register(t20);
Texture2D                           NoiseTexture : register(t21);

#define NoiseTexResolution  g_vec2_0

#define SpawnCount          ParticleSpawnCount[0].x
#define ModuleData          ParticleModuleData[0]
#define ParticleMaxCount    ParticleModuleData[0].iMaxParticleCount

#define SpawnModule         ParticleModuleData[0].Spawn
#define ColorChangeModule   ParticleModuleData[0].ColorChange
#define ScaleChangeModule   ParticleModuleData[0].ScaleChange


[numthreads(128, 1, 1)]
void CS_ParticleUpdate(int3 _ID : SV_DispatchThreadID)
{
    // 스레드 ID 가 파티클버퍼 최대 수를 넘긴경우 or 스레드 담당 파티클이 비활성화 상태인 경우
    if (ParticleMaxCount <= _ID.x)
        return;
        
    tParticle particle = ParticleBuffer[_ID.x];
           
    if (SpawnModule)
    {
        // 파티클이 비활성화 상태인 경우
        if (particle.Age < 0.f)
        {
            // SpawnCount 를 확인
            // 만약 SpawnCount 가 0 이상이라면, 파티클을 활성화시킴      
            while (0 < SpawnCount)
            {
                int orgvalue = SpawnCount;
                int outvalue = 0;
                InterlockedCompareExchange(SpawnCount, orgvalue, SpawnCount - 1, outvalue);
            
                if (orgvalue == outvalue)
                {                   
                    // 랜덤 결과를 받을 변수
                    float3 vOut = (float3) 0.f;
                    
                    // 전체 유효 스레드의 아이디를 0 ~ 1 로 정규화
                    float fNormalizeThreadID = (float) _ID.x / (float) ParticleMaxCount;
                    GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID, vOut);
                    
                    particle.Age = 0.f;
                    particle.LifeTime = 10.f;
                    break;
                }
            }
        }
    }
           
    
    // 파티클이 활성화인 경우
    if(0.f <= particle.Age)
    {
        // 속도에 따른 파티클위치 이동
        particle.vWorldPos += particle.vVelocity * g_DT;
        
        // 파티클의 Age 에 시간을 누적시킴
        particle.Age += g_DT;
        
        // 파티클의 수명이 끝나면, 다시 비활성화 상태로 되돌림
        if (particle.LifeTime <= particle.Age)
        {
            particle.Age = -1.f;
        }
    }    
    
    // 변경점 적용
    ParticleBuffer[_ID.x] = particle;
}

#endif
```

### CParticleSystem.cpp 파일<br/>
<br/>

```
#include "pch.h"
#include "CParticleSystem.h"

#include "CDevice.h"
#include "CStructuredBuffer.h"

#include "CResMgr.h"
#include "CTransform.h"

#include "CTimeMgr.h"

CParticleSystem::CParticleSystem()
	: CRenderComponent(COMPONENT_TYPE::PARTICLESYSTEM)
	, m_ParticleBuffer(nullptr)
	, m_RWBuffer(nullptr)
	, m_ModuleData{}
	, m_AccTime(0.f)
{
	m_ModuleData.iMaxParticleCount = 100;
	m_ModuleData.SpawnRate = 1;
	m_ModuleData.ModuleCheck[(UINT)PARTICLE_MODULE::PARTICLE_SPAWN] = true;

	// 입자 메쉬
	SetMesh(CResMgr::GetInst()->FindRes<CMesh>(L"PointMesh"));

	// 파티클 전용 재질
	SetMaterial(CResMgr::GetInst()->FindRes<CMaterial>(L"ParticleRenderMtrl"));

	// 파티클 업데이트 컴퓨트 쉐이더	
	m_UpdateCS = (CParticleUpdateShader*)CResMgr::GetInst()->FindRes<CComputeShader>(L"ParticleUpdateCS").Get();

	// 파티클 버퍼 초기 데이터
	tParticle arrParticle[100] = { };
	float fAngle = XM_2PI / 100.f;
	float fRadius = 20.f;
	float fSpeed = 100.f;

	for (UINT i = 0; i < 100; ++i)
	{
		arrParticle[i].vWorldPos = Vec3(fRadius * cosf(fAngle * (float)i), fRadius * sinf(fAngle * (float)i), 100.f);
		arrParticle[i].vVelocity = arrParticle[i].vWorldPos;
		arrParticle[i].vVelocity.z = 0.f;
		arrParticle[i].vVelocity.Normalize();
		arrParticle[i].vVelocity *= fSpeed;
		arrParticle[i].vWorldScale = Vec3(10.f, 10.f, 1.f);
		arrParticle[i].Age = -1.f;		
	}
	
	m_ParticleBuffer = new CStructuredBuffer;
	m_ParticleBuffer->Create(sizeof(tParticle), m_ModuleData.iMaxParticleCount, SB_TYPE::READ_WRITE, false, arrParticle);

	m_RWBuffer = new CStructuredBuffer;
	m_RWBuffer->Create(sizeof(tRWParticleBuffer), 1, SB_TYPE::READ_WRITE, true);

	m_ModuleDataBuffer = new CStructuredBuffer;
	m_ModuleDataBuffer->Create(sizeof(tParticleModule), 1, SB_TYPE::READ_ONLY, true);
}

CParticleSystem::~CParticleSystem()
{
	if (nullptr != m_ParticleBuffer)
		delete m_ParticleBuffer;

	if (nullptr != m_RWBuffer)
		delete m_RWBuffer;

	if (nullptr != m_ModuleDataBuffer)
		delete m_ModuleDataBuffer;
}


void CParticleSystem::finaltick()
{
	// 스폰 레이트 계산
	// 1개 스폰 시간
	float fTimePerCount = 1.f / (float)m_ModuleData.SpawnRate;
	m_AccTime += DT;

	// 누적시간이 개당 생성시간을 넘어서면
	if (fTimePerCount < m_AccTime)
	{
		// 초과 배율 ==> 생성 개수
		float fData = m_AccTime / fTimePerCount;

		// 나머지는 남은 시간
		m_AccTime = fTimePerCount * (fData - floor(fData));

		// 버퍼에 스폰 카운트 전달
		tRWParticleBuffer rwbuffer = { (int)fData, };		
		m_RWBuffer->SetData(&rwbuffer);
	}


	// 파티클 업데이트 컴퓨트 쉐이더
	m_ModuleDataBuffer->SetData(&m_ModuleData);

	m_UpdateCS->SetParticleBuffer(m_ParticleBuffer);
	m_UpdateCS->SetRWParticleBuffer(m_RWBuffer);
	m_UpdateCS->SetModuleData(m_ModuleDataBuffer);
	m_UpdateCS->SetNoiseTexture(CResMgr::GetInst()->FindRes<CTexture>(L"Noise_03"));

	m_UpdateCS->Execute();
}

void CParticleSystem::render()
{
	Transform()->UpdateData();

	// 파티클버퍼 t20 에 바인딩
	m_ParticleBuffer->UpdateData(20, PIPELINE_STAGE::PS_ALL);

	// Particle Render	
	GetMaterial()->UpdateData();
	GetMesh()->render_particle(m_ModuleData.iMaxParticleCount);

	// 파티클 버퍼 바인딩 해제
	m_ParticleBuffer->Clear();
}

```

아래 코드를 true, false 값을 지정해서 모듈을 켜고 끌 수 있다.<br/>

```
m_ModuleData.ModuleCheck[(UINT)PARTICLE_MODULE::PARTICLE_SPAWN] = true;
```

<br/>

## HLSL의 Random 기능 부여<br/>
<br/>

func.fx 파일에다가 랜덤함수를 구현해보자.<br/>
<br/>

### HLSL에서의 Random 사용<br/>
<Br/>

보통 상황에서 난수를 쓸 때에는 시간을 이용한다. (time.h 라이브러리)<br/>

시간을 키로하는 랜덤 값을 불러와서 시간에 따라서 다른 수가 출력되는 난수를 이용한다.<br/>

문제는 컴퓨트 쉐이더, 병렬 처리는 동 시간대에 진행돼서 같은 시간에는 모든 쓰레드가 같은 값을 참조하게 되는 것이다.<br/>

같은 시간이라도 쓰레드 별로 다른 값을 참조하도록 만들어줘야 한다.<br/>

노이즈 텍스쳐를 이용할 것이다.<br/>

노이즈 텍스쳐는 각 픽셀 별로 rgba 값이 불규칙하게 분포되어 있다.<br/>

이 텍스쳐의 특정 UV좌표에 있는 픽셀의 값을 추출해서 랜덤한 값을 가지고 올 수 있도록 만들 것이다.<br/>
<br/>

우선, 노이즈 텍스쳐부터 로딩해보자.<br/>

```
// CResMgr.cpp 중에서

// NoiseTexture 로딩
Load<CTexture>(L"Noise_01", L"texture\\Noise\\noise_01.png");
Load<CTexture>(L"Noise_02", L"texture\\Noise\\noise_02.png");
Load<CTexture>(L"Noise_03", L"texture\\Noise\\noise_03.jpg");

```

```
// CParticleUpdateShader.h 중에서

private:
    Ptr<CTexture>   m_NoiseTex;

public:
    void SetNoiseTexture(Ptr<CTexture> _NoiseTex) { m_NoiseTex = _NoiseTex; }
```

```
// CParticleUpdateShader.cpp 중에서

m_NoiseTex->UpdateData_CS(21, true); // 21번 레지스터에 바인딩
```

```
// CParticleSystem.cpp 중에서

// 파티클 업데이트 컴퓨트 쉐이더
	m_ModuleDataBuffer->SetData(&m_ModuleData);

	m_UpdateCS->SetParticleBuffer(m_ParticleBuffer);
	m_UpdateCS->SetRWParticleBuffer(m_RWBuffer);
	m_UpdateCS->SetModuleData(m_ModuleDataBuffer);
	m_UpdateCS->SetNoiseTexture(CResMgr::GetInst()->FindRes<CTexture>(L"Noise_03"));

	m_UpdateCS->Execute();

```

<br/>

그리고, 필터를 만들어보면 아래와 같다.<br/>

```
// ======
// Random
// ======
static float GaussianFilter[5][5] =
{
    0.003f,  0.0133f, 0.0219f, 0.0133f, 0.003f,
    0.0133f, 0.0596f, 0.0983f, 0.0596f, 0.0133f,
    0.0219f, 0.0983f, 0.1621f, 0.0983f, 0.0219f,
    0.0133f, 0.0596f, 0.0983f, 0.0596f, 0.0133f,
    0.003f,  0.0133f, 0.0219f, 0.0133f, 0.003f,
};

```

위와 같이 가우시안 필터가 나타나있다. 위의 값을 모두 합하면 1(100%)이 나오며, 중심을 기준으로 저 비율을 따라서 샘플링을 진행하겠다는 의미이다.<br/>

- 정 가운데 값을 약 16퍼센트 참조할 것이다. (가장 큰 값)
- 가장자리로 갈 수록 적은 비율로 참조할 것이다.
<br/>
<br/>

위의 필터를 이용해서 샘플링을 하는 함수를 구현해보자.<br/>

```
// particle_update.fx 중 에서

Texture2D NoiseTexture : register(t21);

#define NoiseTexResolution g_vec2_0
```
우선, 노이즈 텍스쳐와 바인딩할 레지스터 번호를 위와 같이 등록한다.<br/>

그리고 NoiseTexResolution 을 통해 텍스쳐의 해상도정보를 받아올 수 있도록 매크로를 지정한다.<br/>

그리고 func.fx에 함수를 구현한다.<br/>

필요한 입력 인자는 노이즈 텍스쳐, 해상도정보, 쓰레드의 ID 값, 아웃풋 정보이다.<br/>

```
// func.fx 중에서

void GaussianSample(in Texture2D _NoiseTex, float2 _vResolution, float _NomalizedThreadID, out float3 _vOut)
{
    float2 vUV = float2(_NomalizedThreadID, 0.5f);       
    
    vUV.x += g_AccTime * 0.5f;
    
    // sin 그래프로 텍스쳐의 샘플링 위치 UV 를 계산
    vUV.y -= (sin((_NomalizedThreadID - (g_AccTime/*그래프 우측 이동 속도*/)) * 2.f * 3.1415926535f * 10.f/*반복주기*/) / 2.f);
    
    if( 1.f < vUV.x)
        vUV.x = frac(vUV.x);
    else if(vUV.x < 0.f)
        vUV.x = 1.f + frac(vUV.x);
    
    if( 1.f < vUV.y)
        vUV.y = frac(vUV.y);
    else if (vUV.y < 0.f)
        vUV.y = 1.f + frac(vUV.y);
        
    int2 pixel = vUV * _vResolution;           
    int2 offset = int2(-2, -2);
    float3 vOut = (float3) 0.f;    
    
    for (int i = 0; i < 5; ++i)
    {
        for (int j = 0; j < 5; ++j)
        {            
            vOut += _NoiseTex[pixel + offset + int2(j, i)] * GaussianFilter[i][j];
        }
    }        
    
    _vOut = vOut;    
}

```

그리고 particle_update.fx 파일에서 아래와 같이 적용해본다.<br/>

```
    if (orgvalue == outvalue)
    {   
        particle.Active = 1;
        
        // 랜덤 결과를 받을 변수
        float3 vOut1 = (float3) 0.f;
        float3 vOut2 = (float3) 0.f;
        
        // 전체 유효 스레드의 아이디를 0 ~ 1 로 정규화
        float fNormalizeThreadID = (float) _ID.x / (float) ParticleMaxCount;
        GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID, vOut1);
        GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID + 0.1f, vOut2);
        
        // 반지름이 500인 원 이내에 랜덤으로 생성되도록 만들기
        float fRadius = 500.f; //vOut1.r * 200.f;
        float fAngle = vOut2.r * 2 * 3.1415926535f;                    
        //particle.vWorldPos.xyz = float3(fRadius * cos(fAngle), fRadius * sin(fAngle), 100.f);                    
        
        particle.vWorldPos.xyz = float3(fRadius * vOut1.r - fRadius * 0.5f, fRadius * vOut2.r - fRadius * 0.5f, 100.f);
        particle.vWorldScale.xyz = float3(10.f, 10.f, 1.f);
        
        particle.Age = 0.f;
        particle.LifeTime = 10.f;
        break;
    }
```

쓰레드 자신의 고유한 아이디 값을 최대 파티클의 수로 나누어서 정규화를 한다.<br/>

그리고 가우시안 샘플에서 정규화된 ID값을 이용해서 UV값을 랜덤하게 추출한다.<br/>

추출 하는 원리는 샘플링 위치를 누적 시간 값과 사인(Sin) 그래프를 이용해서 시간에 따라서 다르게 지정을 하는 것이다.<br/>

좌표 상에서 X 값은 고정이지만, Y 값은 달라지는 것이다.<br/>

그래서 시간마다 랜덤한 위치를 지정해서 샘플링을 진행할 수 있다.<br/>

또한, UV좌표가 1을 초과하거나, 0보다 작아질 경우 상황에 따라서 UV좌표를 조정해준다.<br/>

그리고 텍스쳐의 해당 UV 좌표에 있는 r, g, b 값을 랜덤하게 불러낼 수 있게 된다.<br/>

그리고 텍스쳐의 색상을 참조해서 난수를 추출하므로 텍스쳐의 색상이 픽셀 별로 고르게 분포되어 있지 않다면 랜덤값이 의도와 다르게 특정 구간에 쏠려서 나오게 된다.<br/>
<br/>

## Spawn Module의 개선<br/>
<br/>

Age가 아니라 활성화 여부를 Particle의 속성에 추가해주자.<br/>

```
// struct.h 중에서

// Particle
struct tParticle
{
    Vec4    vLocalPos;      // 파티클의 로컬 포지션
	Vec4	vWorldPos;		// 파티클의 최종 위치
	Vec4	vWorldScale;	// 파티클 크기
	Vec4	vColor;			// 파티클 색상
	Vec4	vVelocity;		// 파티클 현재 속도
	Vec4	vForce;			// 파티클에 주어진 힘

	float   Age;			// 생존 시간
	float   NomalizedAge;	// 수명대비 생존시간을 0~1로 정규화 한 값
	float	LifeTime;		// 수명
	float	Mass;			// 질량

	int     Active;			// 파티클 활성화 여부
	int     pad[3];
};

```

Active의 값이 0인가 여부에 따라서 비활성화, 활성화를 결정할 것이다.<br/>

또한, 파티클의 크기도 랜덤으로 배율로 결정을 해줄것이다.<br/>

그래서 vStartScale, vEndScale이 Vec4로부터 float로 자료형을 바꿔준다.<br/>

```
// struct.h 중에서

struct tParticleModule
{
	// 스폰 모듈
	Vec4    vSpawnColor;
	Vec4	vSpawnScaleMin;
	Vec4	vSpawnScaleMax;
	Vec3	vBoxShapeScale;	
	float	fSphereShapeRadius;
	int		SpawnShapeType;		// 0 : Box, 1 : Sphere 
	int		SpawnRate;			// 초당 생성 개수
    int     Space;              // 파티클 업데이트 좌표계 (0 : World, 1 : Local)
	int     spawnpad[1];

	// Color Change 모듈
	Vec4	vStartColor;		// 초기 색상
	Vec4	vEndColor;			// 최종 색상

	// Scale Change 모듈
	float	vStartScale;		// 초기 배율
	float	vEndScale;			// 최종 배율	

	// Module Check
	int		ModuleCheck[(UINT)PARTICLE_MODULE::END];

	// 버퍼 최대크기
	int		iMaxParticleCount;
	int		ipad;
};

```
그리고 SpawnShapeType가 있는데 이 스폰 모양에 따라서 원형으로 스폰될지, 네모모양으로 스폰될지를 결정할 것이다.<br/>

그래서 particle_update.fx의 hlsl코드는 아래와 같이 바뀔 수 있다.<br/>

```
// particle_update.fx 중에서

if (SpawnModule)
    {
        // 파티클이 비활성화 상태인 경우
        if (particle.Active == 0)
        {
            // SpawnCount 를 확인
            // 만약 SpawnCount 가 0 이상이라면, 파티클을 활성화시킴      
            while (0 < SpawnCount)
            {
                int orgvalue = SpawnCount;
                int outvalue = 0;
                InterlockedCompareExchange(SpawnCount, orgvalue, SpawnCount - 1, outvalue);
            
                if (orgvalue == outvalue)
                {   
                    particle.Active = 1;
                    
                    // 랜덤 결과를 받을 변수
                    float3 vOut1 = (float3) 0.f;
                    float3 vOut2 = (float3) 0.f;
                    float3 vOut3 = (float3) 0.f;
                    
                    // 전체 유효 스레드의 아이디를 0 ~ 1 로 정규화
                    float fNormalizeThreadID = (float) _ID.x / (float) ParticleMaxCount;
                    GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID, vOut1);
                    GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID + 0.1f, vOut2);
                    GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID + 0.2f, vOut3);
                    
                    // Box 스폰
                    if (ModuleData.SpawnShapeType == 0)
                    {                           
                        particle.vLocalPos.xyz = float3(ModuleData.vBoxShapeScale.x * vOut1.r - ModuleData.vBoxShapeScale.x * 0.5f
                                                      , ModuleData.vBoxShapeScale.y * vOut2.r - ModuleData.vBoxShapeScale.y * 0.5f
                                                      , ModuleData.vBoxShapeScale.z * vOut3.r - ModuleData.vBoxShapeScale.z * 0.5f);                        
                        particle.vWorldPos.xyz = particle.vLocalPos.xyz + ObjectPos.xyz;
                        
                        
                        // 스폰 크기 범위내에서 랜덤 크기로 지정 (Min, Max 가 일치하면 고정크기)
                        float4 vSpawnScale = ModuleData.vSpawnScaleMin + (ModuleData.vSpawnScaleMax - ModuleData.vSpawnScaleMin) * vOut3.x;                                                
                        particle.vWorldScale.xyz = vSpawnScale.xyz;
                    }      
                    
                    // Sphere 스폰
                    else if (ModuleData.SpawnShapeType == 1)
                    {
                        float fRadius = 500.f; //vOut1.r * 200.f;
                        float fAngle = vOut2.r * 2 * 3.1415926535f;
                        //particle.vWorldPos.xyz = float3(fRadius * cos(fAngle), fRadius * sin(fAngle), 100.f);
                    }
                    
                    
                    particle.vColor = ModuleData.vSpawnColor;                    
                                      
                    particle.Age = 0.f;
                    particle.LifeTime = ModuleData.MinLifeTime + (ModuleData.MaxLifeTime - ModuleData.MinLifeTime) * vOut2.r;
                    break;
                }
            }
        }
    }

```

그리고 오브젝트의 위치에 따라서 파티클이 스폰될 수 있도록 만들어줄 필요가 있다.<br/>

그래서 오브젝트의 위치, 특히 부모 오브젝트의 값을 받아와야 한다.<br/>

```
#define ObjectPos       g_vec4_0
```

그리고 CParticleUpdateShader 클래스에서 포지션을 세팅해주고, CParticleSystem 클래스에서 부모 오브젝트의 WorldPosition을 추출해서 세팅을 해줄 것이다.<br/>

```
// CParticleUpdateShader.h 중에서

#pragma once
#include "CComputeShader.h"

#include "ptr.h"
#include "CTexture.h"

class CStructuredBuffer;

class CParticleUpdateShader :
    public CComputeShader
{
private:
    CStructuredBuffer*  m_ParticleBuffer;
    CStructuredBuffer*  m_RWBuffer;
    CStructuredBuffer*  m_ModuleData;
    Ptr<CTexture>       m_NoiseTex;


public:
    void SetParticleBuffer(CStructuredBuffer* _Buffer);
    void SetRWParticleBuffer(CStructuredBuffer* _Buffer) {m_RWBuffer = _Buffer;}
    void SetModuleData(CStructuredBuffer* _Buffer) {m_ModuleData = _Buffer;}
    void SetNoiseTexture(Ptr<CTexture> _tex)
    { 
        m_NoiseTex = _tex; 
        m_Const.arrV2[0] = Vec2(m_NoiseTex->Width(), m_NoiseTex->Height());
    }

    void SetParticleObjectPos(Vec3 _vPos) { m_Const.arrV4[0] = _vPos; }

public:
    virtual void UpdateData() override;
    virtual void Clear() override;

public:
    CParticleUpdateShader(UINT _iGroupPerThreadX, UINT _iGroupPerThreadY, UINT _iGroupPerThreadZ);
    ~CParticleUpdateShader();
};
```

```
// CParticleSystem.cpp 중에서 finaltick 함수

// 파티클 업데이트 컴퓨트 쉐이더
	m_ModuleDataBuffer->SetData(&m_ModuleData);

	m_UpdateCS->SetParticleBuffer(m_ParticleBuffer);
	m_UpdateCS->SetRWParticleBuffer(m_RWBuffer);
	m_UpdateCS->SetModuleData(m_ModuleDataBuffer);
	m_UpdateCS->SetNoiseTexture(CResMgr::GetInst()->FindRes<CTexture>(L"Noise_01"));
	m_UpdateCS->SetParticleObjectPos(Transform()->GetWorldPos());

	m_UpdateCS->Execute();
```
<br/>

## 파티클에 여러 효과를 입혀보기<br/>
<br/>

### 파티클에다가 텍스쳐를 적용해보자.<br/>
<br/>

```
// CParticleSystem.cpp 중에서 render 함수

void CParticleSystem::render()
{
	Transform()->UpdateData();

	// 파티클버퍼 t20 에 바인딩
	m_ParticleBuffer->UpdateData(20, PIPELINE_STAGE::PS_ALL);

	// Particle Render	
	Ptr<CTexture> pParticleTex = CResMgr::GetInst()->Load<CTexture>(L"Particle_0", L"texture\\particle\\AlphaCircle.png");
	GetMaterial()->SetTexParam(TEX_0, pParticleTex);

	GetMaterial()->UpdateData();
	GetMesh()->render_particle(m_ModuleData.iMaxParticleCount);

	// 파티클 버퍼 바인딩 해제
	m_ParticleBuffer->Clear();
}
```

```
// Particle_render.fx 중에서 텍스쳐 체크 구간

float4 PS_ParticleRender(GS_OUT _in) : SV_Target
{   
    float4 vOutColor = float4(1.f, 0.f, 1.f, 1.f);
    
    if(g_btex_0)
    {
        vOutColor = g_tex_0.Sample(g_sam_0, _in.vUV);        
        vOutColor.rgb *= ParticleBuffer[_in.iInstID].vColor.rgb;
    }
    
    return vOutColor;
}

```
<br/>

### 파티클의 크기변화 모듈을 켜보자.<br/>
<br/>


```
// CParticleSystem.cpp 중에서 생성자 함수

	m_ModuleData.vSpawnScaleMin = Vec3(15.f, 15.f, 1.f);
	m_ModuleData.vSpawnScaleMax = Vec3(50.f, 50.f, 1.f);

	m_ModuleData.ModuleCheck[(UINT)PARTICLE_MODULE::SCALE_CHANGE] = true;
	m_ModuleData.StartScale = 2.f;
	m_ModuleData.EndScale = 0.1f;
```

```
// particle_update.fx 중에서

	// 크기 변화 모듈이 활성화 되어있으면
	if(ModuleData.ScaleChange)
		particle.ScaleFactor = ModuleData.StartScale + particle.NomalizedAge * (ModuleData.EndScale - ModuleData.StartScale);                    
	else
		particle.ScaleFactor = 1.f;
```
<br/>

### 파티클의 색상변화 모듈을 켜보자<br/>
<Br/>

```
// CParticleSystem.cpp 중에서 생성자 함수

	m_ModuleData.ModuleCheck[(UINT)PARTICLE_MODULE::COLOR_CHANGE] = true;
	m_ModuleData.vStartColor = Vec3(0.2f, 0.3f, 1.0f);
	m_ModuleData.vEndColor = Vec3(0.4f, 1.f, 0.4f);
```

```
// particle_update.fx 중에서

	// 색상 변화모듈이 활성화 되어있으면
	if(ModuleData.ColorChange)
	{
		particle.vColor = ModuleData.vStartColor + particle.NomalizedAge * (ModuleData.vEndColor - ModuleData.vStartColor);
	}        
```
<br/>

### 파티클에 속도를 적용해보자<br/>
<br/>

```
// define.h 중에서 PARTICLE_MODULE enum class

enum class PARTICLE_MODULE
{
	PARTICLE_SPAWN,
	COLOR_CHANGE,
	SCALE_CHANGE,
	ADD_VELOCITY,
	END,
};

```

```
// struct.h 중에서

	// Add Velocity 모듈
	Vec4	vVelocityDir;
	int     AddVelocityType;	// 0 : From Center, 1 : Fixed Direction	
	float	OffsetAngle;		
	float	Speed;
	int     addvpad;
```

```
// struct.fx 중에서

    // Add Velocity 모듈
    float4  vVelocityDir;
    int     AddVelocityType; // 0 : From Center, 1 : Fixed Direction	
    float   OffsetAngle;
    float   Speed;
    int     addvpad;
```


```
// CParticleSystem.cpp 중에서

	m_ModuleData.ModuleCheck[(UINT)PARTICLE_MODULE::ADD_VELOCITY] = true;
	m_ModuleData.AddVelocityType = 0; // From Center
	m_ModuleData.Speed = 200.f;
	m_ModuleData.vVelocityDir;
	m_ModuleData.OffsetAngle;
```

```
// particle_update.fx 중에서

    // 속도에 따른 파티클위치 이동
    // Sim 좌표계에 따라서 이동방식 분기
    if (ModuleData.Space == 0)
    {                        
        particle.vWorldPos += particle.vVelocity * g_DT;
    }
    else if(ModuleData.Space == 1)
    {
        particle.vLocalPos += particle.vVelocity * g_DT;
        particle.vWorldPos.xyz = particle.vLocalPos.xyz + ObjectPos.xyz;
    }
```

또한, 질량을 세팅하고, 힘을 가하면, 즉 가속도가 있다면 방향을 바꾸게 만들수도 있다.<br/>

```
// particle_update.fx 중에서

    // 파티클 질량 설정
    particle.Mass = 1.f;

    // 파티클에 힘이 적용된 경우, 힘에 의한 속도의 변화량 계산
    float3 vAccel = particle.vForce / particle.Mass;
    particle.vVelocity += vAccel * g_DT;

    // 속도에 따른 파티클 위치 이동
    if (ModuleData.Space == 0)
    {
        particle.vWorldPos += particle.vVelocity * g_DT;
    }

```
<br/>

아래 코드는 랜덤으로 힘을 가했을 때의 모듈(NoiseForce 모듈)이다.<br/>


```
// struct.h 중에서

// 파티클 입자 속성
struct tParticle
{
	Vec4	vLocalPos;		// 오브젝트로부터 떨어진 거리
	Vec4	vWorldPos;		// 파티클 최종 월드위치
	Vec4	vWorldScale;	// 파티클 크기
	Vec4	vColor;			// 파티클 색상
	Vec4	vVelocity;		// 파티클 현재 속도
	Vec4	vForce;			// 파티클에 주어진 힘
	Vec4	vRandomForce;	// 파티클에 적용되는 랜덤방향 힘

	float   Age;			// 생존 시간
	float   PrevAge;		// 이전 프레임 생존 시간
	float   NomalizedAge;	// 수명대비 생존시간을 0~1로 정규화 한 값
	float	LifeTime;		// 수명
	float	Mass;			// 질량
	float   ScaleFactor;	// 추가 크기 배율

	int     Active;			// 파티클 활성화 여부
	int     pad;
};

    // NoiseForce 모듈 - 랜덤 힘 적용
    float   fNoiseTerm;  // 랜덤 힘을 적용하는 쿨타임, 텀.
    float   fNoiseForce; // 랜덤힘의 크기
```

```
// struct.fx 중에서

// Particle
struct tParticle
{
    float4  vLocalPos;
    float4  vWorldPos; // 파티클 위치
    float4  vWorldScale; // 파티클 크기
    float4  vColor; // 파티클 색상
    float4  vVelocity; // 파티클 현재 속도
    float4  vForce; // 파티클에 주어진 힘
    float4  vRandomForce; // 파티클에 적용되는 랜덤 힘

    float   Age; // 생존 시간
    float   PrevAge;  // 이전 프레임 생존시간
    float   NomalizedAge; // 수명대비 생존시간을 0~1로 정규화 한 값
    float   LifeTime; // 수명
    float   Mass; // 질량
    float   ScaleFactor; // 추가 크기 배율
    
    int     Active;
    int     pad;
};

// NoiseForce 모듈
    float fNoiseTerm;
    float fNoiseForce;
```

```
// CParticleSystem.cpp 의 생성자 함수 중에서<br/>

	m_ModuleData.ModuleCheck[(UINT)PARTICLE_MODULE::NOISE_FORCE] = true;
	m_ModuleData.fNoiseTerm = 0.3f;
	m_ModuleData.fNoiseForce = 50.f;

```


```
// particle_update.fx 중에서

    // NoiseForce 모듈 (랜덤으로 힘) 적용 모듈
    if (ModuleData.NoiseForce)
    {            
        if (particle.PrevAge == 0.f)
        {
                // 랜덤 결과를 받을 변수
            float3 vOut1 = (float3) 0.f;
            float3 vOut2 = (float3) 0.f;
            float3 vOut3 = (float3) 0.f;
                
            // 전체 유효 스레드의 아이디를 0 ~ 1 로 정규화
            float fNormalizeThreadID = (float) _ID.x / (float) ParticleMaxCount;
            GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID, vOut1);
            GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID + 0.1f, vOut2);
            GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID + 0.2f, vOut3);
            
            float3 vForce = normalize(float3(vOut1.x, vOut2.x, vOut1.z));
            particle.vRandomForce.xyz = vForce * ModuleData.fNoiseForce;
        }
        else
        {
            int Age = int(particle.Age * (1.f / ModuleData.fNoiseTerm));
            int PrevAge = int(particle.PrevAge * (1.f / ModuleData.fNoiseTerm));

            // 지정한 간격을 넘어간 순간, 새로운 랜덤 Force 를 준다.
            if (Age != PrevAge)
            {
                // 랜덤 결과를 받을 변수
                float3 vOut1 = (float3) 0.f;
                float3 vOut2 = (float3) 0.f;
                float3 vOut3 = (float3) 0.f;
                
                // 전체 유효 스레드의 아이디를 0 ~ 1 로 정규화
                float fNormalizeThreadID = (float) _ID.x / (float) ParticleMaxCount;
                GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID, vOut1);
                GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID + 0.1f, vOut2);
                GaussianSample(NoiseTexture, NoiseTexResolution, fNormalizeThreadID + 0.2f, vOut3);
            
                float3 vForce = normalize(float3(vOut1.x, vOut2.x, vOut1.z) * 2.f - 1.f);
                particle.vRandomForce.xyz = vForce * ModuleData.fNoiseForce;
            }
        }   
        
        particle.vForce.xyz += particle.vRandomForce.xyz;
    }                
```
<br/>

### 파티클의 속도를 제한하는 모듈 (Drag Module)<br/>
<br/>

시간이 지날수록 파티클의 속도가 줄어들게 해보자.<br/>

```
// define.h 중에서

enum class PARTICLE_MODULE
{
	PARTICLE_SPAWN,
	COLOR_CHANGE,
	SCALE_CHANGE,
	ADD_VELOCITY,

	DRAG,
	DUMMY_1,
	DUMMY_2,
	DUMMY_3,

	END,
};
```


```
// struct.h 중에서 tParticleModule 구조체 중

    // Drag 모듈 - 속도 제한
	float StartDrag;
	int	  DragPad;

```

```
// struct.fx 중에서 tParticleModule 구조체 중

    // Drag 모듈
    float StartDrag;
    float EndDrag;
    int dragpad;
```


```
// CParticleSystem.cpp 중에서 CParticleSystem의 생성자함수

	m_ModuleData.ModuleCheck[(UINT)PARTICLE_MODULE::DRAG] = true;
	m_ModuleData.StartDrag = 200.f;
	m_ModuleData.EndDrag = 0.f;
```

```
// particle_update.fx 중에서

    // 속도 제한(Drag) 모듈
    if (ModuleData.Drag)
    {
        // 파티클의 현재 속력
        float Speed = length(particle.vVelocity);
        float fDrag = ModuleData.StartDrag + (ModuleData.EndDrag - ModuleData.StartDrag) * particle.NomalizedAge;
        
        // 빠른 감속을 위해서 설정한 fDrag가 음수일 경우 예외처리
        if (fDrag < 0.f)
            fDrag = 0.0001f;

        if (fDrag < Speed)
        {
            particle.vVelocity = normalize(particle.vVelocity) * fDrag;
        }
    }
```
<br/>

여기서 Drag 모듈은 감속을 위한 모듈로, 속도 값이 음수로 떨어져서 반대로 이동하도록 하면 안된다.

이를 방지하기 위한 예외처리도 해주자.<br/>

```
if (fDrag < 0.f)
    fDrag = 0.0001f;
```
0.0001을 준 이유는 멈춘 것 처럼 보이게 하기 위해서이다.<br/>

정확하게 0을 줘버리면 속도벡터가 0이 되어버리기 때문에 파티클을 렌더링 하는 과정에서 오류가 생긴다. 그래서 멈춘것처럼 보이게 하되, Normalize가 가능하도록 0에 가까운 값을 주었다.<br/>

<br/>

### 렌더링 관련 모듈<br/>
<br/>

```
// define.h 중에서 PARTICLE_MODULE 목록

enum class PARTICLE_MODULE
{
	PARTICLE_SPAWN,
	COLOR_CHANGE,
	SCALE_CHANGE,
	ADD_VELOCITY,

	DRAG,
	NOISE_FORCE,
	RENDER,
	DUMMY_3,

	END,
};

```


```
// struct.h 중에서

// Render 모듈

int     VelocityAlignment; // 속도 정렬, 속도의 방향으로 이미지를 회전시킨다.
                           // 값이 1이라면 속도정렬을 사용한다.
                           // 값이 0이라면 쓰지 않는다.
int     VelocityScale;     // 속도에 따른 크기 변화
Vec4    vMaxVelocityScale; // 속력에 따른 크기 변화량 최대치
float   vMaxSpeed;         // 최대 크기에 도달하는 속력

int     renderpad;
```

```
// struct.fx 중에서

// Render 모듈
    int     VelocityAlignment;  // 1 : 속도정렬 사용(이동 방향으로 회전) 0 : 사용 안함
    int     VelocityScale;      // 1 : 속도에 따른 크기 변화 사용, 0 : 사용 안함	
    float   vMaxSpeed;          // 최대 크기에 도달하는 속력
    float4  vMaxVelocityScale;  // 속력에 따른 크기 변화량 최대치
    int     renderpad;


// Module check
    int Spawn;
    int ColorChange;
    int ScaleChange;
    int AddVelocity;    
    
    int Drag;
    int NoiseForce;
    int Render;
    int modulepad;    

```

```
// CParticleSystem.cpp 파일 중에서

// 생성자함수에서 Render모듈을 켜주기
// 진행 방향으로 크기를 20배 키워줌.

	m_ModuleData.ModuleCheck[(UINT)PARTICLE_MODULE::RENDER] = true;
	m_ModuleData.VelocityAlignment = true;
	m_ModuleData.VelocityScale = true;
	m_ModuleData.vMaxVelocityScale = Vec3(20.f, 1.f, 1.f);
	m_ModuleData.vMaxSpeed = 500.f;

// Render 함수 중에서

// 파티클버퍼 t20 에 바인딩
	m_ParticleBuffer->UpdateData(20, PIPELINE_STAGE::PS_ALL);


// 모듈 데이터 t21에 바인딩
	m_ModuleDataBuffer->UpdateData(21, PIPELINE_STAGE::PS_GEOMETRY);

```

```
// particle_render.fx 중에서

// t21번 레지스터에 바인딩
StructuredBuffer<tParticleModule>   ParticleModuleData : register(t21);

// 연산 개시
[maxvertexcount(6)]
void GS_ParticleRender (point VS_OUT _in[1], inout TriangleStream<GS_OUT> _outstream)
{    
    uint id = _in[0].iInstID;
    
    if (0 == ParticleBuffer[id].Active)
        return;

    float3 vParticleViewPos = mul(float4(ParticleBuffer[id].vWorldPos.xyz, 1.f), g_matView).xyz;
    float2 vParticleScale = ParticleBuffer[id].vWorldScale.xy * ParticleBuffer[id].ScaleFactor;
   
    // 0 -- 1
    // |    |
    // 3 -- 2
    float3 NewPos[4] =
    {
        float3(-vParticleScale.x / 2.f, +vParticleScale.y / 2.f, 0.f),
        float3(+vParticleScale.x / 2.f, +vParticleScale.y / 2.f, 0.f),
        float3(+vParticleScale.x / 2.f, -vParticleScale.y / 2.f, 0.f),
        float3(-vParticleScale.x / 2.f, -vParticleScale.y / 2.f, 0.f)
    };
    
    
    if (ModuleData.Render)
    {        
        if (ModuleData.VelocityScale)
        {
            // 현재 파티클의 속력을 알아낸다.
            float fCurSpeed = length(ParticleBuffer[id].vVelocity);            
            if (ModuleData.vMaxSpeed < fCurSpeed)
                fCurSpeed = ModuleData.vMaxSpeed;
            
            // 최대속도 대비 현재 속도의 비율을 구한다.
            float fRatio = saturate(fCurSpeed / ModuleData.vMaxSpeed);            
          
            // 비율에 맞는 크기변화량을 구한다.
            float3 vDefaultScale = float3(1.f, 1.f, 1.f);
            float3 fScale = vDefaultScale + (ModuleData.vMaxVelocityScale.xyz - vDefaultScale) * fRatio;
                      
            NewPos[0] = NewPos[0] * fScale;
            NewPos[3] = NewPos[3] * fScale;            
        }
        
        
        if (ModuleData.VelocityAlignment)
        {
            // 파티클 월드 기준 속도를 View 공간으로 변환
            float3 vVelocity = normalize(ParticleBuffer[id].vVelocity);
            vVelocity = mul(float4(vVelocity, 0.f), g_matView).xyz;
                       
            // 파티클 Right 방향과 이동 방향을 내적해서 둘 사이의 각도를 구한다.
            float3 vRight = float3(1.f, 0.f, 0.f);
            float fTheta = acos(dot(vRight, vVelocity));
            
            // 내적의 결과가 코사인 예각을 기준으로 하기 때문에, 2파이 에서 반대로 뒤집어 준다.
            if (vVelocity.y < vRight.y)
            {
                fTheta = (2.f * 3.1415926535f) - fTheta;
            }
            
            // 구한 각도로 Z 축 회전 행렬을 만든다.
            float3x3 matRotZ =
            {
                cos(fTheta),  sin(fTheta),      0,
                -sin(fTheta), cos(fTheta),      0,
                          0,            0,    1.f,
            };
            
            // 4개의 정점을 회전시킨다.
            for (int i = 0; i < 4; ++i)
            {
                NewPos[i] = mul(NewPos[i], matRotZ);
            }
        }        
    }
    
    
    GS_OUT output[4] = { (GS_OUT) 0.f, (GS_OUT) 0.f, (GS_OUT) 0.f, (GS_OUT) 0.f };
    
    output[0].vPosition = mul(float4(NewPos[0] + vParticleViewPos, 1.f), g_matProj);
    output[0].vUV = float2(0.f, 0.f);
    output[0].iInstID = id;
    
    output[1].vPosition = mul(float4(NewPos[1] + vParticleViewPos, 1.f), g_matProj);
    output[1].vUV = float2(1.f, 0.f);
    output[1].iInstID = id;
    
    output[2].vPosition = mul(float4(NewPos[2] + vParticleViewPos, 1.f), g_matProj);
    output[2].vUV = float2(1.f, 1.f);
    output[2].iInstID = id;
    
    output[3].vPosition = mul(float4(NewPos[3] + vParticleViewPos, 1.f), g_matProj);
    output[3].vUV = float2(0.f, 1.f);
    output[3].iInstID = id;
    
    
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
    float4 vOutColor = float4(1.f, 0.f, 1.f, 1.f);
    
    if(g_btex_0)
    {
        vOutColor = g_tex_0.Sample(g_sam_0, _in.vUV);        
        vOutColor.rgb *= ParticleBuffer[_in.iInstID].vColor.rgb;
    }
    
    return vOutColor;
}
```

VelocityScale에서는 파티클의 속력을 알아내어 비율(fRatio)을 구해준다.<br/>

이 때, saturate를 이용해서 0~1사이의 값이 나오도록 한다.<br/>

이 부분이 작동한다면 속력에 따라서 파티클이 늘어지는 크기가 달라지게 된다.<br/>

속력이 빠를수록 타원형이나 막대기에 가깝고, 느려지면 점점 원형이나 정사각형 모양에 가깝게 나올 것이다.<br/>

<br/>

VelocityAlignment에서는 파티클을 회전시키기 위해서 각도(fTheta)를 추출한다.<Br/>

여기서 문제는 내적을 한 값에서 acos를 할 경우 항상 작은 각도 쪽으로 fTheta가 반환된다는 점이다.<br/>

예를 들어, 벡터1과 벡터2 사이의 각이 120도, 240도 일 경우 240도를 반환받고 싶어도 120도를 반환받게 된다.<br/>

그래서 예외조건을 걸어줘야한다.<br/>

속도의 y값이 더 낮을 경우, 정확히 말하면 속도벡터가 더 아래쪽으로 치우쳐져있을 경우 예외처리를 해주면 된다.<br/>

```
// 내적의 결과가 코사인 예각을 기준으로 하기 떄문에, 2파이 에서 반대로 뒤집어 준다.

if (vVelocity.y < vRight.y)
{
    fTheta = (2.f * 3.1415926535f) - fTheta;
}
```

또 다른 문제는 회전 연산, 시뮬레이션은 ViewSpace에서 하는데 비해,<Br/>

속도 시뮬레이션, 연산을 World좌표계에서 진행중이다.<br/>

그래서 속도 벡터를 뷰 스페이스로 끌고와야한다.<br/>

그렇기 때문에 아래와 같은 코드가 작성이 된다.<br/>

```
vVelocity = mul(float4(vVelocity, 0.f), g_matView).xyz;
```

이 과정을 거치고 나서 파티클 좌표계의 Right 방향과 이동 방향을 내적해서 둘 사이의 각도를 구하고 z축으로 회전시킨다.<br/>

float3x3을 이용해서 회전행렬을 만들 수 있다.<br/>

```
float3x3 matRotZ =
{
    cos(fTheta), sin(fTheta), 0,
   -sin(fTheta), cos(fTheta), 0,
              0,           0, 1.f
};
```
