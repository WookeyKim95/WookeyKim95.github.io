---
layout: post
title:  "[WinAPI Project] BNB 제작 일지 2"
subtitle: 
date: 2022-10-27 19:18:12 +0900
categories: project
tags:
comments: true
related_posts:

---
# [WinAPI Project] BNB 제작 일지 2<br/>

개발기간 : 10월 18일 ~ 11월 24일 (예정)<br/>
<Br/>

[개발 일정표 노션 링크](https://small-fairy-d44.notion.site/0e7da7a478a8425484162dbd15138ee3?v=15d21b2d0372477ebe6de7f9cd129443)<br/>
<Br/>

## 10월 27일<br/>
<Br/>

블록을 만나면 이동을 하지 못하도록 만드는 로직을 고민하였다.<br/>

우선, 충돌을 한 객체가 블록일 경우에 충돌체간 (플레이어, 블록 중심점 간)거리 측정을 하여<br/>

블록이 위에 있는 경우, 아래에 있는 경우, 왼쪽에 있는 경우, 오른쪽에 있는 경우인지를 파악해서<br/>

해당 방향으로 더 이상 이동을 하지 못하도록 로직을 짜고자 한다.<br/>

아직 실질적인 구현은 안되었다.<br/>
<br/>

### 20시 30분 추가기록<br/>
<br/>

```
void CPlayer::BeginOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* _pObstacle = (CObstacle*)(_pOther->GetOwner());
		if (_pObstacle->GetObstacle() == true) // 이동 불가능한 곳에 접촉 했을 때
		{
			Vec2 ObsPos = _pObstacle->GetPos();
			Vec2 vPos = GetPos();


			if (vPos.y - ObsPos.y < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::UP)) // 위쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos()); // 현재 위치를 닿기 직전의 위치로 옮김.
				CanMove[0] = false; // 더이상 이동 못하게 막음.
			}

			if (ObsPos.y - vPos.y < (TILE_SIZE / 2.f) && IsPressed(KEY::DOWN)) // 아래쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos()); // 현재 위치를 닿기 직전의 위치로 옮김.
				CanMove[1] = false; // 더이상 이동 못하게 막음.
			}
			if (vPos.x - ObsPos.x < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::LEFT)) // 왼쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos());
				CanMove[2] = false; // 더이상 이동 못하게 막음.
			}

			if (ObsPos.x - vPos.x < (TILE_SIZE / 2.f) && IsPressed(KEY::RIGHT)) // 오른쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos());
				CanMove[3] = false; // 더이상 이동 못하게 막음.
			}

		}
	}
}

void CPlayer::EndOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* _pObstacle = (CObstacle*)(_pOther->GetOwner());
		if (_pObstacle->GetObstacle() == true) // 이동 불가능한 곳에 접촉 했을 때
		{
			if (!CanMove[0])
				CanMove[0] = true;
			if (!CanMove[1])
				CanMove[1] = true;
			if (!CanMove[2])
				CanMove[2] = true;
			if (!CanMove[3])
				CanMove[3] = true;
		}
	}
}
```
4개의 bool 변수를 담을 수 있는 CanMove를 선언하였고,<br/>

Obj 객체에서 바로 직전의 좌표를 저장하는 변수를 새로 생성하고 Player에 적용<br/>

tick() 함수에서 새로운 좌표를 지정하기 전에 이전 좌표를 먼저 지정해주었다.(SetPrevPos())<br/>

<br/>

BeginOverlap 함수에서 충돌이 일어났을 경우 바로 직전 좌표로 이동시킨 뒤 더이상의 이동을 막는다.<br/>

그리고 충돌 직전의 좌표로 이동했으면 자동으로 EndOverlap함수로 넘어간다.<br/>

이때 방향 이동을 막은 것을 해제해준다.<br/>

즉, 만약에 블록이나 장애물에 닿으면 위의 코드로 더 이상 이동하지 못하게 막고,<br/>

현재 좌표를 충돌하기 바로 직전의 이전 좌표로 저장하는 식으로 장애물을 통과하지 못하도록 구현하였다.<br/>

다음 목표는 블록 사이를 매끄럽게 이동할 수 있도록<br/>

블록과 충돌이 일어났을 경우 타일의 정 가운데로 옆으로 미끄러지는 상황을 구현하도록 하겠다.<br/>

### 22시 00분 추가기록<br/>
<br/>

에디터 레벨의 개선화를 진행하였다.<br/>

## 10월 28일 <br/>
<br/>

블록과 충돌이 일어났을 경우 옆으로 미끄러지는 상황을 구현하였다.<br/>

```
void CPlayer::BeginOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* _pObstacle = (CObstacle*)(_pOther->GetOwner());
		if (_pObstacle->GetObstacle() == true) // 이동 불가능한 곳에 접촉 했을 때
		{
			Vec2 ObsPos = _pObstacle->GetPos();
			Vec2 vPos = GetPos();


			if (vPos.y - ObsPos.y < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::UP)) // 위쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos()); // 현재 위치를 닿기 직전의 위치로 옮김.
				CanMove[0] = false; // 더이상 이동 못하게 막음.

				// 캐릭터가 블록보다 오른쪽에 더 치우쳐있을경우
				if (vPos.x - ObsPos.x > TILE_SIZE)
				{
					vPos.x += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 왼쪽에 더 치우쳐 있을 경우
				if (ObsPos.x - vPos.x > 0.f)
				{
					vPos.x -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}

			}

			else if (ObsPos.y - vPos.y < (TILE_SIZE / 2.f) && IsPressed(KEY::DOWN)) // 아래쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos()); // 현재 위치를 닿기 직전의 위치로 옮김.
				CanMove[1] = false; // 더이상 이동 못하게 막음.

				// 오른쪽에 더 치우쳐있을경우
				if (vPos.x - ObsPos.x > TILE_SIZE)
				{
					vPos.x += m_fSpeed * DT;
					SetPos(vPos);
				}
				// 왼쪽에 더 치우쳐 있을 경우
				if (ObsPos.x - vPos.x > 0.f)
				{
					vPos.x -= m_fSpeed * DT;
					SetPos(vPos);
				}

			}
			else if (vPos.x - ObsPos.x < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::LEFT)) // 왼쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos());
				CanMove[2] = false; // 더이상 이동 못하게 막음.


				// 캐릭터가 블록보다 위에 더 치우쳐있을경우
				if (ObsPos.y - vPos.y > 0.f)
				{
					vPos.y -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 아래에 더 치우쳐 있을 경우
				if (vPos.y - ObsPos.y > TILE_SIZE)
				{
					vPos.y += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
			}

			else if (ObsPos.x - vPos.x < (TILE_SIZE / 2.f) && IsPressed(KEY::RIGHT)) // 오른쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos());
				CanMove[3] = false; // 더이상 이동 못하게 막음


				// 캐릭터가 블록보다 위에 더 치우쳐있을경우
				if (ObsPos.y - vPos.y > 0.f)
				{
					vPos.y -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 아래에 더 치우쳐 있을 경우
				if (vPos.y - ObsPos.y > TILE_SIZE)
				{
					vPos.y += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
			}

		}
	}
}

void CPlayer::OnOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* _pObstacle = (CObstacle*)(_pOther->GetOwner());
		if (_pObstacle->GetObstacle() == true) // 이동 불가능한 곳에 접촉 했을 때
		{
			Vec2 ObsPos = _pObstacle->GetPos();
			Vec2 vPos = GetPos();
			
			if (vPos.y - ObsPos.y < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::UP)) // 캐릭터보다 위쪽에 블록이 있을 경우
			{
				// 캐릭터가 블록보다 오른쪽에 더 치우쳐있을경우
				if (vPos.x - ObsPos.x > TILE_SIZE)
				{
					SetPrevPos(GetPos());
					vPos.x += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 왼쪽에 더 치우쳐 있을 경우
				if (ObsPos.x - vPos.x > 0.f)
				{
					vPos.x -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPrevPos(GetPos()); 
					SetPos(vPos);
				}
			}

			if (ObsPos.y - vPos.y < (TILE_SIZE / 2.f) && IsPressed(KEY::DOWN)) // 아래쪽에 블록이 있을 경우
			{
				// 오른쪽에 더 치우쳐있을경우
				if (vPos.x - ObsPos.x > TILE_SIZE)
				{
					SetPrevPos(GetPos());
					vPos.x += m_fSpeed * DT;
					SetPos(vPos);
				}
				// 왼쪽에 더 치우쳐 있을 경우
				if (ObsPos.x - vPos.x > 0.f)
				{
					vPos.x -= m_fSpeed * DT;
					SetPrevPos(GetPos());
					SetPos(vPos);
				}

			}
			if (vPos.x - ObsPos.x < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::LEFT)) // 왼쪽에 블록이 있을 경우
			{
				// 캐릭터가 블록보다 위에 더 치우쳐있을경우
				if (ObsPos.y - vPos.y > 0.f)
				{
					vPos.y -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 아래에 더 치우쳐 있을 경우
				if (vPos.y - ObsPos.y > TILE_SIZE)
				{
					vPos.y += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}

			}

			if (ObsPos.x - vPos.x < (TILE_SIZE / 2.f) && IsPressed(KEY::RIGHT)) // 오른쪽에 블록이 있을 경우
			{

				// 캐릭터가 블록보다 위에 더 치우쳐있을경우
				if (ObsPos.y - vPos.y > 0.f)
				{
					vPos.y -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 아래에 더 치우쳐 있을 경우
				if (vPos.y - ObsPos.y > TILE_SIZE)
				{
					vPos.y += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}

			}
		}
	}
}
```

블록에 플레이어가 충돌할 때, 정확히는 Obstacle 여부가 true인 장애물 표시 객체에 충돌할 때 좌표를 조사하여<br/>

조건을 만족하면 x좌표, 혹은 y좌표를 이동시켜서 포지션을 재설정하도록 만들었다.<br/>

그런데 이 코드를 작성하고나서 버그가 발생하기 시작했다.<br/>

우선 첫번째로 블록 사이에서는 이동속도가 느려진다.<br/>

두번째로 EndOverlap에 있는 함수 (충돌 종료시 이동가능설정) 때문에 블록 위로 떠다녀서 이동하는 현상이 발생한다.<br/>

EndOverlap함수 재조정 및 더 나은 코드를 고민해보아야겠다.<br/>

<br/>

## 10월 30일 <br/>
<br/>

29일엔 휴식을 취하고 30일에 작업을 재시작하였다.<br/>
<Br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb2_1.png?raw=true)<br/>

<br/>

현재 위 화면과 같이 버그가 발생한 상태이다.<br/>

아무래도 블록 사이에서도 이동을 원활하게 하려고 EndOverlap함수와 OnOverlap함수의 코드를 조정하다보니 이런 버그가 생긴 것 같다.<br/>

지금까지 작업 과정 중 현재 제일 어려움을 겪고 있는 부분이다.<br/>


**계속 업데이트 중 입니다.**