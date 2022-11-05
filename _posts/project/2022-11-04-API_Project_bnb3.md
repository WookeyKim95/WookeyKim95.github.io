---
layout: post
title:  "[WinAPI Project] BNB 제작 일지 3"
subtitle: 
date: 2022-11-04 12:14:17 +0900
categories: project
tags:
comments: true
related_posts:

---
# [WinAPI Project] BNB 제작 일지 3<br/>

개발기간 : 10월 18일 ~ 11월 20일 (예정)<br/>
<Br/>

[개발 일정표 노션 링크](https://small-fairy-d44.notion.site/0e7da7a478a8425484162dbd15138ee3?v=15d21b2d0372477ebe6de7f9cd129443)<br/>
<Br/>

## 11월 4일<br/>
<Br/>

몬스터가 움직일 AI를 설계하기 위해서 상태를 설계하는 중이다.<br/>

개인적인 생각으로 이 부분이 포트폴리오 전체를 통틀어 가장 어려운 구간이 아닐까 생각이 든다.<br/>

아마도 오늘도 코딩자체에 진전 보다는 설계 위주로 하지 않을까 생각이 든다.<br/>

### 18시<br/>

몬스터가 화면에 나오고, 스스로 움직일 수 있게는 하였다.<br/>

하지만 원하는 조건에 움직이도록 더 세부적인 설계를 해야할 것 같다.<br/>

아직은 오류가 많다.<br/>

뭐.. 예를 들어 블록을 통과해서 움직이다던가..<br/>

블록을 만나면 다른 방향으로 틀어야하는데 말이지.<br/>

## 11월 5일<br/>
<br/>

절반은 성공한 것 같다.<br/>

```
void CElli::BeginOverlap(CCollider* _pOther)
{
	Vec2 vPos = GetPos();

	if (_pOther->GetOwner()->ReturnLayer() == LAYER::WAVE || _pOther->GetOwner()->ReturnLayer() == LAYER::WAVE_CENTER)
	{
		if (m_BTrapped == false)
		{
			m_BTrapped = true;
		}
	}

	else if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* pObstacle = (CObstacle*)_pOther->GetOwner();
		Vec2 ObsPos = pObstacle->GetPos();
		if (pObstacle->GetObstacle() || pObstacle->GetAbsObstacle()) // 장애물을 만나면 방향전환
		{
			int dice = rand() % 3;

			if (0 < vPos.y - ObsPos.y && vPos.y - ObsPos.y < (3.f * TILE_SIZE / 2.f)) // 위쪽에 블록이 있을 경우 m_dir = 0
			{
				if (dice == 0)
					m_dir = 2;
				else if (dice == 1)
					m_dir = 3;
				else
					m_dir = 1;

				SetPos(GetPrevPos()); // 현재 위치를 닿기 직전의 위치로 옮김.
			}

			else if (0 < ObsPos.y - vPos.y && ObsPos.y - vPos.y < (TILE_SIZE / 2.f)) // 아래쪽에 블록이 있을 경우 m_dir = 1
			{
				if (dice == 0)
					m_dir = 2;
				else if (dice == 1)
					m_dir = 3;
				else
					m_dir = 0;

				SetPos(GetPrevPos()); // 현재 위치를 닿기 직전의 위치로 옮김.

			}
			else if (0 < vPos.x - ObsPos.x && vPos.x - ObsPos.x < (3.f * TILE_SIZE / 2.f)) // 왼쪽에 블록이 있을 경우 m_dir = 2
			{
				if (dice == 0)
					m_dir = 0;
				else if (dice == 1)
					m_dir = 1;
				else
					m_dir = 3;

				SetPos(GetPrevPos());
			}

			else if (0 < ObsPos.x - vPos.x && ObsPos.x - vPos.x < (TILE_SIZE / 2.f)) // 오른쪽에 블록이 있을 경우 m_dir = 3
			{
				if (dice == 0)
					m_dir = 0;
				else if (dice == 1)
					m_dir = 1;
				else
					m_dir = 2;
			}
        SetPos(m_TileCenter);
		}
	}
}
```

위 코드를 통해서 블록을 만나면 타일의 정중앙으로 이동하고, 직전 진행방향을 제외한 나머지 방향 중 한 부분으로 움직인다.<br/>

그리고 충돌체를 Obstacle보다 조금 작게 설정했기 때문에 바꾼 방향이 막혀있더라도 바로 다른 방향으로 바꿀 수 있도록 구현하였다.<br/>

방향이 바뀌면 애니메이션도 바뀌도록 구현을 해야겠다.<br/>


**계속 업데이트 중입니다.**
