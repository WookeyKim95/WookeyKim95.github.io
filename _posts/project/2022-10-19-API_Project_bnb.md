---
layout: post
title:  "[WinAPI Project] BNB 제작 일지"
subtitle: 
date: 2022-10-19 16:19:09 +0900
categories: project
tags:
comments: true
related_posts:

---
# [WinAPI Project] BNB 제작 일지<br/>

개발기간 : 10월 18일 ~ 11월 24일 (예정)<br/>
<Br/>

[개발 일정표 노션 링크](https://small-fairy-d44.notion.site/0e7da7a478a8425484162dbd15138ee3?v=15d21b2d0372477ebe6de7f9cd129443)<br/>
<Br/>

## 10월 18일<br/>
<Br/>

게임 구성을 위해서 필요한 매니저와 엔진의 구조를 구상하였다.<br/>

본격적인 개발에 들어가기에 앞선 전 단계, 즉 OutputFile경로와 미리 컴파일된 헤더로 사용될 pch파일, 그리고 사용자 지정함수, 지정 자료구조를 만들어 주었다.<br/>

ex) Vec2 : 2차원 벡터를 저장하기 위한 자료구조이다.<br/>
<Br/>

## 10월 19일<br/>
<br/>

본격적인 게임 구성을 위한 엔진과 매니저 설계를 완료하였다.

BnB에서 구성한 매니저는 아래와 같다.

- Engine : 게임이 돌아가기 위한 코어. 각 매니저들을 init하고 프레임마다의 논리계산, 렌더링을 진행하도록 유도한다.

<Br/>

- KeyMgr : 게임 내 에서 키의 상태를 정의하기 위한 매니저이다.
	+ 게임에서 눌리는 키를 정의한다.
	+ 현재 해당 윈도우를 가리키고 있을 경우 이전에 눌렸는지 아닌지 여부, 그리고 현재 논리 연산단계(tick)에서 눌렸는지 아닌지 여부에 따라서 tap, released, pressed 상태인지를 정의하고 판별한다.
	+ 현재 해당 윈도우를 가리키고 있지 않은 경우 released로 키를 해제한다.

<br/>

- TimeMgr : 1프레임이 진행된 시간과 1초당 진행된 프레임 수를 측정하기 위한 장치이다. 여기서 측정된 값을 매크로를 통해서 뽑아내서 1초 당 일정한 속도로 이동할 수 있도록 조절한다.<br/> 이 매니저가 없으면 컴퓨터 성능에 따라서 1초당 프레임이 달라지므로 이동 속도 등이 달라지게 된다.

<br/>

- LevelMgr : 어떤 레벨(스테이지 및 에디터 상태 등)이 있는지를 관리하고 1프레임 당 연산과 렌더링을 관할한다.

<br/>

- CollisionMgr : 충돌을 관리한다. 객체로 구현된 Collider가 어떤 상태이어야 충돌로 처리할지, 비충돌로 처리할지를 관할한다.

<br/>

- EventMgr : Level에서 발생하는 이벤트들을 관할한다. 삭제 예정인 오브젝트를 먼저 삭제하고, 그 다음에 순차적으로 들어오는 이벤트르 들을 종류에 따라서 처리한다.

- PathMgr : 리소스, 텍스쳐의 저장경로를 관할한다. 절대경로가 아니라 상대 경로를 이용하도록 돕는다.

- ResMgr : 객체가 어떤 리소스나 텍스쳐를 이용할지 관할한다. PathMgr의 도움을 받아 상대경로를 찾아서 객체에게 텍스쳐를 넘긴다.

- UIMgr : 게임 내의 UI를 관할한다. 이때, 인벤토리와 같은 경우처럼 부모 UI - 자식 UI의 관계를 가지는 경우가 있는데 어떤 UI가 클릭되었는지를 구분할 때 bfs알고리즘을 사용한다.

- SoundMgr : Bgm 재생을 관할한다. 0~100까지 %단위를 사용하여 어디서 재생할지를 결정할 수 있다.

<br/>

## 10월 20일<br/>
<br/>

코딩보다는 리소스 작업에 집중했다.<br/>

리소스를 받아서 png파일을 bmp파일로 바꾸고 투명화를 위한 색깔인 마젠타(255, 0, 255)를 배경에 넣는 작업을 진행하였다.<br/>

그리고 매니저의 구조와 Tile객체를 다시 공부하면서 필요하다면 어떻게 재구성할지를 고민하고자 한다.<br/>

<br/>

![에디터 기본 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb_1.png?raw=true)

<br/>

Eidtor의 기본 화면을 위와 같이 화면을 구성하는데 성공하였다.<br/>

앞으로의 목표는 아래와 같다.

- 타일 별로 텍스쳐를 다르게 저장할 수 있게 구현하기
- 타일 맵 저장, 로드 구현하기

## 10월 21일<br/>
<br/>

![에디터 기본 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb_2.png?raw=true)

<br/>

위와 같이 팔레트를 만들려고 하는 중이었다.<br/>

팔레트에 있는 타일을 클릭하면 해당 타일이 선택되며, 이를 통해 맵에다가 타일을 설치할 수 있는 것이다.

```
if (IsTap(KEY::LBTN))
	{
		// 범위 내에서 마우스 좌클릭을 실행하였을 경우
		// 마우스 위치를 받아와서 실제좌표로 변환	
		Vec2 vMousePos = MOUSE_POS;

		int iCol = (int)(vMousePos.x - 20.f) / TILE_SIZE;
		int iRow = (int)(vMousePos.y - 40.f) / TILE_SIZE;

		if (0.f <= vMousePos.x && iCol < GetTileXCount()
			&& 0.f <= vMousePos.y && iRow < GetTileYCount())
		{
			int iIdx = iRow * GetTileXCount() + iCol;
			const vector<CObj*>& vecTile = GetLayer(LAYER::TILE);
			//((CTile*)vecTile[iIdx])->SetAtlas();
		}
	}
```
타일을 업데이트 하는 함수 내에 있는 코드이다. 여기서 m_AtlasNum의 값을 이용하여
```
((CTile*)vecTile[iIdx])->SetAtlas();
```

타일을 클릭하면 m_AtlasNum 값을 바꾸며 다른 타일이 맵에 입력되도록 하는 방법을 연구 중이다.<br/>

### 오후 7시<br/>
<Br/>

```
if (IsTap(KEY::LBTN))
	{
		// 범위 내에서 마우스 좌클릭을 실행하였을 경우
		// 마우스 위치를 받아와서 실제좌표로 변환	
		Vec2 vMousePos = MOUSE_POS;

		int iCol = (int)(vMousePos.x - 20.f) / TILE_SIZE;
		int iRow = (int)(vMousePos.y - 40.f) / TILE_SIZE;

		if (0.f <= vMousePos.x && iCol < GetTileXCount()
			&& 0.f <= vMousePos.y && iRow < GetTileYCount())
		{
			int iIdx = iRow * GetTileXCount() + iCol;
			const vector<CObj*>& vecTile = GetLayer(LAYER::TILE);
			((CTile*)vecTile[iIdx])->SetAtlas(m_Tilelist[m_AtlasNum]);
		}
	}
```

```
    vector<CTexture*>   m_Tilelist; // 타일의 종류를 저장하는 벡터
    UINT            m_AtlasNum; // 현재 선택한 타일의 아틀라스를 선택하기 위한 것.
                                // 팔레트 출력 목록
```

```
    void SetAtlasNum0() { m_AtlasNum = 0; };
    void SetAtlasNum1() { m_AtlasNum = 1; };
    void SetAtlasNum2() { m_AtlasNum = 2; };
    void SetAtlasNum3() { m_AtlasNum = 3; };
    void SetAtlasNum4() { m_AtlasNum = 4; };
    void SetAtlasNum5() { m_AtlasNum = 5; };
    void SetAtlasNum6() { m_AtlasNum = 6; };
```

우선 위와 같이 하드코딩을 해서 타일 팔레트를 구현하였다.<br/>
타일 UI 7개에 각기 다른 함수를 넣어주고(기능 : 인덱스를 바꿈), <br/>
m_AtlasNum을 인덱스로 활용하여 현재 m_AtlasNum값에 따라서 CTexture*을 저장한 벡터에서<br/>
몇번째 인덱스를 타일에 저장할지를 마우스 클릭을 통해서 입력하는 것이다.<br/>

저장 기능, 로드 기능 까지는 잘 작동하지만 나중을 위해서 조금 코드를 고칠 필요가 있다고 보인다.<br/>

그리고 다음 목표는

- 오브젝트모드, 타일모드에 따라서 각기 다른 팔레트가 우측에 나오도록 설정하는 것
- 타일에 따라서 지나갈 수 있는 곳인지 없는 곳인지 정보를 저장하고 로드하는 과정 구현

이렇게 설정해야겠다.<br/>

## 10월 22일<br/>
<Br/>

```
// EditorLevel의 tick() 함수

void CEditorLevel::tick()
{
	CLevel::tick();

	if (IsTap(KEY::ENTER))
	{
		ChangeLevel(LEVEL_TYPE::START);
	}

	if (IsTap(KEY::_1))
	{
		m_eMode = EDITOR_MODE::TILE;
		// 기존 팔레트 지우기
		for (int i = 0; i < m_UIList[m_curPalette].size(); ++i)
		{
			m_UIList[m_curPalette][i]->SetDead();
		}
		m_curPalette = 0;

		// 타일 팔레트 출력
		for (int i = 0; i < m_UIList[m_curPalette].size(); ++i)
		{
			m_UIList[m_curPalette][i]->SetPos(Vec2(660.f, 50.f + 50.f * i));
			AddObject(m_UIList[m_curPalette][i], LAYER::UI);
		}

	}

	if (IsTap(KEY::_2))
	{
		m_eMode = EDITOR_MODE::BOX;
		// 기존 팔레트 지우기
		for (int i = 0; i < m_UIList[m_curPalette].size(); ++i)
		{
			m_UIList[m_curPalette][i]->SetDead();
		}
		m_curPalette = 1;

		// 박스 팔레트 출력
		for (int i = 0; i < m_UIList[m_curPalette].size() / 2; ++i)
		{
			m_UIList[m_curPalette][i]->SetPos(Vec2(660.f, 50.f + 50.f * i));
			AddObject(m_UIList[m_curPalette][i], LAYER::UI);
		}

		for (int i = 0; i < m_UIList[m_curPalette].size() / 2; ++i)
		{
			m_UIList[m_curPalette][i + m_UIList[m_curPalette].size() / 2]->SetPos(Vec2(710.f, 50.f + 50.f * i));
			AddObject(m_UIList[m_curPalette][i], LAYER::UI);
		}
		
	}

	if (IsTap(KEY::_0))
	{
		m_eMode = EDITOR_MODE::NONE;
		m_curPalette = 2;
	}

	update();
}
```

위의 코드를 요약하면

- 1번을 누르면 우측에 있던 기존 팔레트를 지우고 (SetDead()함수) 타일 팔레트를 출력한다.

- 2번을 누르면 우측에 있던 기존 팔레트를 지우고 박스 팔레트를 출력한다.

그런데 2번을 눌렀다가 1번을 누르면 오류가 발생한다.<br/>

아! 팔레트 구성이 잘못되었나??<br/>

현재 팔레트를 바꿀 때마다 delete하고 push_back하는 과정으로 해야하는데 delete만 계속하니 오류가 발생하는 것이라 추측했다.<br/>

팔레트 구성이나 팔레트 출력 구성을 바꿔봐야겠다.<br/>

SetDead를 쓰지 않고 순수 팔레트만 출력하는 구조로 바꾸기로 했다.<br/>

아니면 SetDead가 필수라면 팔레트를 보관하는 구조를 다시 고민해보아야 할 것 같다.<br/>
<Br/>

### 12시 30분<br/>

SetDead함수를 없애고 이것 저것 만지고 스테이지 UI를 씌우니까 그럴듯 하게 보이고있다.<br/>

하지만 클릭을 할 때마다 UI그림이 다시 그려지는 듯한 느낌을 지울 수가 없다.<br/>

우선은.. 박스까지 저장하는 구조와 특정 타일에서는 박스를 지우는 방법을 고민해야겠다.<br/>

아니면.. 맵의 구조 로직 자체를 새로 고민해야할지도 모르겠다.<br/>

## 10월 24일<br/>
<Br/>

10월 23일엔 휴식 및 리소스 작업을 한 뒤, 오늘은 맵 에디터에서 박스 삭제를 구현하였다.<br/>

![에디터 기본 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb_3.png?raw=true)
<br/>

에디터에서 삭제가 적힌 흰색 사각형을 클릭하면 박스를 지울 수 있도록 만들었다.<br/>

하지만 기본 원리는 그냥 텍스쳐를 nullptr로 바꾸는 것이기 때문에 나중에 내부 박스 클래스가 구현되면 구조를 바꿀 필요가 있다고 사료된다.<br/>

### 21시 30분<br/>

우선은 한 칸에 블록, 타일, 장애물여부 총 3개의 자료가 존재하는 구조로 설계하였다.<br/>

## 10월 25일<br/>
<br/>

오늘은 코드 설계보다는 리소스 정리를 진행하였다.<br/>

## 10월 26일<br/>
<br/>

![에디터 기본 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb_4.png?raw=true)

에디터에서 편집한 맵을 스테이지에서 불러올 수 있도록 했다.<br/>

그리고 플레이어 캐릭터에다가 상, 하, 좌, 우로 움직이는 경우 애니메이션을 부여하였다.<br/>

```

	// 상하좌우 키로 움직이기
	if (IsPressed(KEY::RIGHT))
	{
		Vec2 vPos = GetPos();

		vPos.x += m_fSpeed * DT;
		SetPos(vPos);
	}

	if (IsPressed(KEY::LEFT))
	{
		Vec2 vPos = GetPos();

		vPos.x -= m_fSpeed * DT;
		SetPos(vPos);
	}

	if (IsPressed(KEY::DOWN))
	{
		Vec2 vPos = GetPos();

		vPos.y += m_fSpeed * DT;
		SetPos(vPos);
	}

	if (IsPressed(KEY::UP))
	{
		Vec2 vPos = GetPos();

		vPos.y -= m_fSpeed * DT;
		SetPos(vPos);
	}

	// 가다가 섰을 때 애니메이션 재생
	if (IsRelease(KEY::RIGHT))
	{
		GetAnimator()->Play(L"IDLE_RIGHT", true);
	}

	if (IsRelease(KEY::LEFT))
	{
		GetAnimator()->Play(L"IDLE_LEFT", true);
	}

	if (IsRelease(KEY::UP))
	{
		GetAnimator()->Play(L"IDLE_UP", true);
	}

	if (IsRelease(KEY::DOWN))
	{
		GetAnimator()->Play(L"IDLE_DOWN", true);
	}

	// 걷는 중 애니메이션 재생
	if (IsTap(KEY::RIGHT))
	{
		GetAnimator()->Play(L"WALK_RIGHT", true);
	}

	if (IsTap(KEY::UP))
	{
		GetAnimator()->Play(L"WALK_UP", true);
	}

	if (IsTap(KEY::LEFT))
	{
		GetAnimator()->Play(L"WALK_LEFT", true);
	}

	if (IsTap(KEY::DOWN))
	{
		GetAnimator()->Play(L"WALK_DOWN", true);
	}
```

IsTap은 누른 순간에 발동되는 조건문이고, IsRelease는 키보드에서 손을 뗀 순간에 발동되는 조건문이다.<br/>

그리고 IsPressed는 눌려있는 동안에 발동되는 조건문이다.<br/>

애니메이션 재생 시작 자체는 처음 한번만 일어나야하기 때문에 IsTap에다가 걷는 모션의 애니메이션을 배치하였다.<br/>

여기서 발생한 문제는 두 키를 동시에 눌렀다가 뗐을 경우에 애니메이션이 잘못 재생되는 문제가 발생한다.<br/>

- 예를 들어, 왼쪽과 아래키를 동시에 눌렀다가 아래키를 떼는 경우<br/>
아래키를 뗐기 때문에 왼쪽으로 움직이지만 애니메이션은 아래쪽을 바라보고 서있는 모션이 재생된다.

이를 해결하기 위해서 코드 배치를 다시 생각해보아야겠다.<brb>


### 19시 40분<br/>
<br/>

![에디터 기본 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb_5.png?raw=true)

위와 같이 타일에 걸쳐서 물풍선(검은색 사각형)을 놓는 경우 근처 타일의 중앙에 놓이도록 구현하였다.<br/>

```
for (int i = 0; i < 15; ++i)
	{
		for (int j = 0; j < 13; ++j)
		{
			Vec2 Candi_TileCenter = { 20.f + TILE_SIZE / 2 + TILE_SIZE * i
									, 40.f + TILE_SIZE / 2 + TILE_SIZE * j };
			if ((abs(GetPos().x - m_TileCenter.x) > abs(GetPos().x - Candi_TileCenter.x)))
				m_TileCenter.x = Candi_TileCenter.x;
			if ((abs(GetPos().y - m_TileCenter.y) > abs(GetPos().y - Candi_TileCenter.y)))
				m_TileCenter.y = Candi_TileCenter.y;
		}
	}
```

2중 for문 완전 탐색을 이용해서 15열 13행 총 195개의 타일 중에서 가장 가까운 타일의 중앙에 놓이도록 조정을 했다.<br/>

### 20시 20분<br/>
<br/>

- 예를 들어, 왼쪽과 아래키를 동시에 눌렀다가 아래키를 떼는 경우<br/>
아래키를 뗐기 때문에 왼쪽으로 움직이지만 애니메이션은 아래쪽을 바라보고 서있는 모션이 재생된다.

이 문제를 해결하였다.<br/>

```
// 가다가 섰을 때 애니메이션 재생
	if (IsRelease(KEY::RIGHT) && !(IsPressed(KEY::DOWN)) && !(IsPressed(KEY::UP)) && !(IsPressed(KEY::LEFT)))
	{
		GetAnimator()->Play(L"IDLE_RIGHT", true);
	}

	if (IsRelease(KEY::LEFT) && !(IsPressed(KEY::DOWN)) && !(IsPressed(KEY::UP)) && !(IsPressed(KEY::RIGHT)))
	{
		GetAnimator()->Play(L"IDLE_LEFT", true);
	}

	if (IsRelease(KEY::UP) && !(IsPressed(KEY::DOWN)) && !(IsPressed(KEY::RIGHT)) && !(IsPressed(KEY::LEFT)))
	{
		GetAnimator()->Play(L"IDLE_UP", true);
	}

	if (IsRelease(KEY::DOWN) && !(IsPressed(KEY::RIGHT)) && !(IsPressed(KEY::UP)) && !(IsPressed(KEY::LEFT)))
	{
		GetAnimator()->Play(L"IDLE_DOWN", true);
	}
```

위와 같이 오직 한쪽의 방향키가 눌려있있다가 뗐을 때에만 멈추는 모션이 재생되도록 하였다.<br/>

그런데 문제는 애시당초 크레이지 아케이드는 대각선으로 움직이면 안된다.<br/>

음.. 그렇다면 위에 이동을 관장하는 코드도 바꿀 필요가 있겠다.<br/>

```
// 상하좌우 키로 움직이기
	if (IsPressed(KEY::RIGHT) && !(IsPressed(KEY::DOWN)) && !(IsPressed(KEY::UP)) && !(IsPressed(KEY::LEFT)))
	{
		Vec2 vPos = GetPos();

		vPos.x += m_fSpeed * DT;
		SetPos(vPos);
	}

	if (IsPressed(KEY::LEFT) && !(IsPressed(KEY::DOWN)) && !(IsPressed(KEY::UP)) && !(IsPressed(KEY::RIGHT)))
	{
		Vec2 vPos = GetPos();

		vPos.x -= m_fSpeed * DT;
		SetPos(vPos);
	}

	if (IsPressed(KEY::DOWN) && !(IsPressed(KEY::UP)) && !(IsPressed(KEY::RIGHT)) && !(IsPressed(KEY::LEFT)))
	{
		Vec2 vPos = GetPos();

		vPos.y += m_fSpeed * DT;
		SetPos(vPos);
	}

	if (IsPressed(KEY::UP) && !(IsPressed(KEY::RIGHT)) && !(IsPressed(KEY::DOWN)) && !(IsPressed(KEY::LEFT)))
	{
		Vec2 vPos = GetPos();

		vPos.y -= m_fSpeed * DT;
		SetPos(vPos);
	}
```

상하좌우로 움직이는 부분의 코드를 위와 같이 수정하였다.<br/>

하지만 이렇게 하면 움직임을 전환할 때 뻣뻣한 느낌을 준다.<br/>

더 좋은 방법을 연구할 필요가 있겠어.<br/>

**계속 업데이트 중 입니다.**