---
layout: post
title:  "[DirectX] 삼각형 움직이기, 그리고 보간"
subtitle:  
date: 2022-12-02 19:37:48 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [DirectX] 삼각형 움직이기, 그리고 보간<br/>
<br/>

Engine에서 키 매니저를 추가하자.<br/>

```
#include "pch.h"
#include "CEngine.h"

#include "CDevice.h"
#include "CPathMgr.h"
#include "CKeyMgr.h"
#include "CTimeMgr.h"


CEngine::CEngine()
	: m_hWnd(nullptr)
{
}

CEngine::~CEngine()
{
}

int CEngine::init(HWND _hWnd, UINT _iWidth, UINT _iHeight)
{
	// 메인 윈도우 핸들
	m_hWnd = _hWnd;
	m_vResolution = Vec2((float)_iWidth, (float)_iHeight);

	// 해상도에 맞는 작업영역 크기 조정
	RECT rt = { 0, 0, (int)_iWidth, (int)_iHeight};
	AdjustWindowRect(&rt, WS_OVERLAPPEDWINDOW, false);
	SetWindowPos(m_hWnd, nullptr, 10, 10, rt.right - rt.left, rt.bottom - rt.top, 0);
	ShowWindow(m_hWnd, true);


	// Device 초기화
	if (FAILED(CDevice::GetInst()->init(m_hWnd, _iWidth, _iHeight)))
	{
		MessageBox(nullptr, L"Device 초기화 실패", L"에러", MB_OK);
		return E_FAIL;
	}

	// Manager 초기화
	CPathMgr::GetInst()->init();
	CKeyMgr::GetInst()->init();
	return S_OK;
}

void CEngine::progress()
{
	tick();

	render();
}

void CEngine::tick()
{
	CTimeMgr::GetInst()->tick();
	CKeyMgr::GetInst()->tick();

    Tick();
}

void CEngine::render()
{
    // 렌더 시작 및 코드

	// 렌더 종료
	CDevice::GetInst()->Present();
}
```

삼각형을 움직이는 것 처럼 보이게 할려면<br/>
정점의 위치를 변경시켜서 새로 세팅을 해주어야 한다.<Br/>

KEY_PRESSSED 와 DT는 매크로로 설정해준 것이다.<br/>

```
#define DT CTimeMgr::GetInst()->GetDeltaTime();

#define KEY_PRESSED(Key) CKeyMgr::GetInst()->GetKeyState(Key) == PRESSED;
#define KEY_TAP(Key) CKeyMgr::GetInst()->GetKeyState(Key) == TAP;
#define KEY_RELEASE(Key) CKeyMgr::GetInst()->GetKeyState(Key) == RELEASE;
```

```
void Tick()
{
    if (KEY_PRESSED(KEY::UP))
    {
        for (int i = 0; i < 3; ++i)
        {
            g_arrVtx[i].vPos.y += DT * 1.f;
        }
    }

    if (KEY_PRESSED(KEY::DOWN))
    {
        for (int i = 0; i < 3; ++i)
        {
            g_arrVtx[i].vPos.y -= DT * 1.f;
        }
    }

    if (KEY_PRESSED(KEY::LEFT))
    {
        for (int i = 0; i < 3; ++i)
        {
            g_arrVtx[i].vPos.y -= DT * 1.f;
        }
    }

    if (KEY_PRESSED(KEY::RIGHT))
    {
        for (int i = 0; i < 3; ++i)
        {
            g_arrVtx[i].vPos.y += DT * 1.f;
        }
    }

    // g_arrVtx --> g_VB 버텍스 버퍼로 계속 새로운 정보를 보내야한다.

    D3D11_MAPPED_SUBRESOURCE tSubRes = {};

    CONTEXT->Map(g_VB.Get(), 0, D3D11_MAP::MAP_WRITE_DISCARD, 0, &tSubRes);

    // memcpy(Dest, Src, Size);
    memcpy(tSubRes.pData, g_arrVtx, sizeof(Vtx) * 3);

    CONTEXT->Unmap(g_VB.Get(), 0);
}
```

<br/>

## 정점에 다른 색깔을 부여한다면? (보간에 대해)<br/>
<br/>

```
	g_arrVtx[0].vPos = Vec3(0.f, 1.f, 0.5f);
	g_arrVtx[0].vColor = Vec4(1.f, 0.f, 0.f, 1.f);

	g_arrVtx[1].vPos = Vec3(1.f, -1.f, 0.5f);;
	g_arrVtx[1].vColor = Vec4(0.f, 1.f, 0.f, 1.f);

	g_arrVtx[2].vPos = Vec3(-1.f, 1.f, 0.5f);;
	g_arrVtx[2].vColor = Vec4(0.f, 0.f, 1.f, 1.f);
```

이 코드 처럼 vColor 값을 다르게 부여하면 어떻게될까?<br/>

이 코드대로 라면 위쪽 정점은 빨간색, 오른쪽 하단 정점은 녹색, 왼쪽 하단 정점은 파란색으로 나올 것이다.<br/>

그렇다면 나머지 픽셀들의 색깔은 어떻게 될까?<br/>

빨간색 정점에 가까울 수록 빨간색으로, 파란색 정점에 가까울 수록 파란색으로, 녹색 정점에 가까울 수록 녹색으로 출력된다.<br/>

세 정점에서 같은 거리에 있는 가운데 지점에서는 회색이 출력된다.<br/>
(R : 0.333.., G : 0.333.., B, 0.333..) = 회색

즉, 거리에 반비례해서 그 정점에 가까운 데이터가 출력되는 것이다.<br/>

이 과정을 **보간(InterPolation)** 이라고 한다.<br/>

**색상 뿐만 아니라 모든 데이터가** 보간되어 들어가게 된다.<br/>
<br/>
