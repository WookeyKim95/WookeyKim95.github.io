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

개발기간 : 10월 18일 ~ 11월 20일 (예정)<br/>
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

### 17시 50분<br/>
<br/>

```
	if (vPos.x < 40.f || 600.f < vPos.x || vPos.y < 60.f || vPos.y > 540.f)
	{
		SetPos(GetPrevPos());
		vPos = GetPrevPos();
	}
```
캐릭터가 맵 밖으로 벗어나지 못하게 하는 아이디어를 짜는 과정에서 해결방안을 찾아내었다.<br/>

만약에 왼쪽이 막혀있다면? 그냥 굳이 변수를 설정해 줄 것이 아니라 위처럼<br/>

바로 직전 포지션으로 SetPos해주면 될 일이었다.<br/>

```
void CPlayer::BeginOverlap(CCollider* _pOther)
{
	Vec2 vPos = GetPos();
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* _pObstacle = (CObstacle*)(_pOther->GetOwner());
		Vec2 ObsPos = _pObstacle->GetPos();

		if (_pObstacle->GetObstacle() == true) // 이동 불가능한 곳에 접촉 했을 때
		{
			if (vPos.y - ObsPos.y < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::UP)) // 위쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos()); // 현재 위치를 닿기 직전의 위치로 옮김.

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
	Vec2 vPos = GetPos();
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* _pObstacle = (CObstacle*)(_pOther->GetOwner());
		Vec2 ObsPos = _pObstacle->GetPos();

		if (_pObstacle->GetObstacle() == true) // 이동 불가능한 곳에 접촉 했을 때
		{
			if (vPos.y - ObsPos.y < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::UP)) // 캐릭터보다 위쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos());
				// 캐릭터가 블록보다 오른쪽에 더 치우쳐있을경우
				if (vPos.x - ObsPos.x > TILE_SIZE)
				{
					SetPrevPos(GetPos());
					vPos.x += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 왼쪽에 더 치우쳐 있을 경우
				else if (ObsPos.x - vPos.x > 0.f)
				{
					vPos.x -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPrevPos(GetPos()); 
					SetPos(vPos);
				}
			}

			if (ObsPos.y - vPos.y < (TILE_SIZE / 2.f) && IsPressed(KEY::DOWN)) // 아래쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos());
				// 오른쪽에 더 치우쳐있을경우
				if (vPos.x - ObsPos.x > TILE_SIZE)
				{
					SetPrevPos(GetPos());
					vPos.x += m_fSpeed * DT;
					SetPos(vPos);
				}
				// 왼쪽에 더 치우쳐 있을 경우
				else if (ObsPos.x - vPos.x > 0.f)
				{
					vPos.x -= m_fSpeed * DT;
					SetPrevPos(GetPos());
					SetPos(vPos);
				}

			}
			if (vPos.x - ObsPos.x < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::LEFT)) // 왼쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos());
				// 캐릭터가 블록보다 위에 더 치우쳐있을경우
				if (ObsPos.y - vPos.y > 0.f)
				{
					vPos.y -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 아래에 더 치우쳐 있을 경우
				else if (vPos.y - ObsPos.y > TILE_SIZE)
				{
					vPos.y += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}

			}

			if (ObsPos.x - vPos.x < (TILE_SIZE / 2.f) && IsPressed(KEY::RIGHT)) // 오른쪽에 블록이 있을 경우
			{
				SetPos(GetPrevPos());
				// 캐릭터가 블록보다 위에 더 치우쳐있을경우
				if (ObsPos.y - vPos.y > 0.f)
				{
					vPos.y -= m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}
				// 캐릭터가 블록보다 아래에 더 치우쳐 있을 경우
				else if (vPos.y - ObsPos.y > TILE_SIZE)
				{
					vPos.y += m_fSpeed * DT; // 미끄러 지듯이 이동
					SetPos(vPos);
				}

			}
		}
	}
}

void CPlayer::EndOverlap(CCollider* _pOther)
{

}

```

코드를 위와 같이 바꿔주었고, 그리고 Obstacle 객체의 크기도 39 x 39로 바꾸어주었다.<br/>

조금 야매이긴하지만 블록 사이를 지나갈 때 속도가 느려지는 원인을 해결하기 위해서 <br/>

위와 같은 조치를 취해주었다.<br/>

이렇게 조치를 해주니 매끄럽게 지나가지며, 블록 위로 지나가는 버그도 발생하지 않는다.<br/>

### 19시 10분<br/>
<br/>

물풍선의 물줄기를 구현하였다.<br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb2_2.png?raw=true)<br/>

```
CWave::CWave(int _WaveCount, int _Dir) :
	m_WaveCount(_WaveCount),
	m_Dir(_Dir),
	m_BOOM(false)
{
	CreateCollider();
	CreateAnimator();

	GetCollider()->SetOffsetPos(Vec2(0.f, 0.f));
	GetCollider()->SetScale(Vec2(40.f, 40.f));

	// Animator 에서 사용할 Image 로딩
	if (_Dir == 0)
	{
		if (_WaveCount == 1)
		{
			CTexture* pWaveUp = CResMgr::GetInst()->LoadTexture(L"WaveUp", L"Texture\\Flow\\Flow_Up.bmp");
			GetAnimator()->CreateAnimation(L"WAVE_UP", pWaveUp, Vec2(0.f, 0.f), Vec2(40.f, 40.f), Vec2(0.f, 0.f), 4, 0.125f);
			GetAnimator()->Play(L"WAVE_UP", false);
		}
		else if (_WaveCount > 1)
		{
			CTexture* pWaveUpM = CResMgr::GetInst()->LoadTexture(L"WaveUpM", L"Texture\\Flow\\Flow_UpM.bmp");
			GetAnimator()->CreateAnimation(L"WAVE_UPM", pWaveUpM, Vec2(0.f, 0.f), Vec2(40.f, 40.f), Vec2(0.f, 0.f), 4, 0.125f);
			GetAnimator()->Play(L"WAVE_UPM", false);
		}
	}

	else if (_Dir == 1)
	{
		if (_WaveCount == 1)
		{
			CTexture* pWaveDown = CResMgr::GetInst()->LoadTexture(L"WaveDown", L"Texture\\Flow\\Flow_Down.bmp");
			GetAnimator()->CreateAnimation(L"WAVE_DOWN", pWaveDown, Vec2(0.f, 0.f), Vec2(40.f, 40.f), Vec2(0.f, 0.f), 4, 0.125f);
			GetAnimator()->Play(L"WAVE_DOWN", false);
		}
		else if (_WaveCount > 1)
		{
			CTexture* pWaveDownM = CResMgr::GetInst()->LoadTexture(L"WaveDownM", L"Texture\\Flow\\Flow_DownM.bmp");
			GetAnimator()->CreateAnimation(L"WAVE_DOWNM", pWaveDownM, Vec2(0.f, 0.f), Vec2(40.f, 40.f), Vec2(0.f, 0.f), 4, 0.125f);
			GetAnimator()->Play(L"WAVE_DOWNM", false);
		}
	}

	else if (_Dir == 2)
	{
		if (_WaveCount == 1)
		{
			CTexture* pWaveLeft = CResMgr::GetInst()->LoadTexture(L"WaveLeft", L"Texture\\Flow\\Flow_Left.bmp");
			GetAnimator()->CreateAnimation(L"WAVE_LEFT", pWaveLeft, Vec2(0.f, 0.f), Vec2(40.f, 40.f), Vec2(0.f, 0.f), 4, 0.125f);
			GetAnimator()->Play(L"WAVE_LEFT", false);
		}
		else if (_WaveCount > 1)
		{
			CTexture* pWaveLeftM = CResMgr::GetInst()->LoadTexture(L"WaveLeftM", L"Texture\\Flow\\Flow_LeftM.bmp");
			GetAnimator()->CreateAnimation(L"WAVE_LEFTM", pWaveLeftM, Vec2(0.f, 0.f), Vec2(40.f, 40.f), Vec2(0.f, 0.f), 4, 0.125f);
			GetAnimator()->Play(L"WAVE_LEFTM", false);
		}
	}

	else if (_Dir == 3)
	{
		if (_WaveCount == 1)
		{
			CTexture* pWaveRight = CResMgr::GetInst()->LoadTexture(L"WaveRight", L"Texture\\Flow\\Flow_Right.bmp");
			GetAnimator()->CreateAnimation(L"WAVE_RIGHT", pWaveRight, Vec2(0.f, 0.f), Vec2(40.f, 40.f), Vec2(0.f, 0.f), 4, 0.125f);
			GetAnimator()->Play(L"WAVE_RIGHT", false);
		}
		else if (_WaveCount > 1)
		{
			CTexture* pWaveRightM = CResMgr::GetInst()->LoadTexture(L"WaveRightM", L"Texture\\Flow\\Flow_RightM.bmp");
			GetAnimator()->CreateAnimation(L"WAVE_RIGHTM", pWaveRightM, Vec2(0.f, 0.f), Vec2(40.f, 40.f), Vec2(0.f, 0.f), 4, 0.125f);
			GetAnimator()->Play(L"WAVE_RIGHTM", false);
		}
	}

}
```

```
if (!m_BOOM)
	{
		if (m_WaveCount > 1)
		{
			CWave* pWave = new CWave(--m_WaveCount, m_Dir);
			pWave->SetScale(Vec2(40.f, 40.f));

			if (m_Dir == 0)
				Instantiate(pWave, Vec2(GetPos().x, GetPos().y - TILE_SIZE), LAYER::WAVE);
			else if (m_Dir == 1)
				Instantiate(pWave, Vec2(GetPos().x, GetPos().y + TILE_SIZE), LAYER::WAVE);
			else if (m_Dir == 2)
				Instantiate(pWave, Vec2(GetPos().x - TILE_SIZE, GetPos().y), LAYER::WAVE);
			else if (m_Dir == 3)
				Instantiate(pWave, Vec2(GetPos().x + TILE_SIZE, GetPos().y), LAYER::WAVE);
		}
	}
	m_DurationTime += DT;

	if (m_DurationTime > 0.5f)
	{
		SetDead();
		m_DurationTime = 0;
	}
```

생성자 인자에 방향을 나타낼 정수와 몇 번째 물줄기인지를 넣었다.<br/>

그리고 tick 함수에서 처음에 터지지 않았을 경우에<br/>

상속받은 방향과 몇번째 물줄기인지 카운트에 따라서 다음 물줄기에<br/>

물줄기 카운트 - 1과 자신의 방향을 물려주고 새로운 객체를 생성하는 식으로 만들었다.<br/>

그리고 물줄기는 0.5초뒤에 사라지게 하였다.<br/>

다음 목표는 물줄기가 블록에 부딪히거나 장애물여부가 true인 곳에서는 막히도록 코드를 조정하는 것이다.<br/>

## 10월 31일<br/>
<br/>

제일 고민되는 점은 그거다.<br/>

Wave객체에게 어떻게 이 칸에 블록이 있는지, 장애물이있는지 정보를 넘기고 조작할까?<br/>

현재 떠오른 방법은 wave가 생성되고 나서 Obstacle과 충돌하면서 정보를 얻어오는 것이다.<br/>

하지만 tick을 돌고 애니메이션이 재생되는 동안 OnOverlap함수나 BeginOverlap함수에 들어가지 않는다.<br/>

이를 어떻게 해결하면 좋을까?<br/>

### 14시 45분<br/>
<br/>

해결방안은 간단했다.<br/>

우선, "하지만 tick을 돌고 애니메이션이 재생되는 동안 OnOverlap함수나 BeginOverlap함수에 들어가지 않는다."<br/>

원인은 해당 Level에서 Obstacle과 Wave의 충돌설정을 해주지 않아서 발생하는 문제였다.<br/>

설정을 해주니 BeginOverlap이 된다.<br/>

그리고<br/>

```
void CWave::BeginOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* pObstacle = (CObstacle*)_pOther->GetOwner();
		if (pObstacle->GetObstacle() == false)
		{
			m_MoreBOOM = true;
		}
	}
}
```

위와 같이 코딩을 해주니<br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb2_3.png?raw=true)<br/>

좋았어! 원하는 부분까지만 물줄기가 생성된다!<br/>

다음으로는 물줄기가 박스에 닿으면 사라지는 것을 구현해야겠다.<br/>

### 17시 <br/>
<br/>

물줄기가 박스에 닿으면 사라지는 것을 구현하였다.(SetDead사용)<br/>

그리고 물풍선을 여러개 놓았을 때 다른 물줄기에 물풍선이 닿으면 터지도록 하는 과정도 구현하였다.<br/>

```
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::WAVE)
	{
		CWaveCenter* pWaveCenter = new CWaveCenter(m_WhoUsed->GetWaveCount());
		pWaveCenter->SetScale(Vec2(40.f, 40.f));
		Instantiate(pWaveCenter, GetPos(), LAYER::WAVE_CENTER);

		SetDead();
		int BombCount = m_WhoUsed->GetBombCount();
		if (m_WhoUsed->GetBombCount() < m_WhoUsed->GetMaxBombCount())
			m_WhoUsed->SetBombCount(++BombCount);
		m_DurationTime = 0;
	}
```
이 코드를 BeginOverlap 함수에 넣어주었다.<br/>

같은 코드가 반복되는 것 같으니 함수화 시켜주면 될 듯 하다.<br/>

```
void CBomb::BOOM()
{
	CWaveCenter* pWaveCenter = new CWaveCenter(m_WhoUsed->GetWaveCount());
	pWaveCenter->SetScale(Vec2(40.f, 40.f));
	Instantiate(pWaveCenter, GetPos(), LAYER::WAVE_CENTER);

	SetDead();
	int BombCount = m_WhoUsed->GetBombCount();
	if (m_WhoUsed->GetBombCount() < m_WhoUsed->GetMaxBombCount())
		m_WhoUsed->SetBombCount(++BombCount);
	m_DurationTime = 0;
}

```
오케이. BOOM()이라는 함수로 따로 빼내주었다.<br/>

다음 고민거리는 지금 같은 칸 내에 풍선을 두 개 놓을 수 있게 되는 버그가 있다.<br/>

이를 해결해보아야겠다.<br/>
<br/>

### 18시 30분<br/>
<Br/>

새로운 고민거리를 발견하고 해결했다.<br/>

물풍선을 놓은 직후에는 물풍선과 겹쳐도 이동할 수 있어야하지만,<br/>

물풍선과 떨어지고 나서 다시 충돌할 때에는 물풍선이 있는 방향으로 이동해선 안된다.<br/>

고민 끝에, player에 변수를 하나 추가했다.<br/>

```
    bool        m_BombFirst; // 폭탄을 놓고난 직후인지 여부
```

폭탄을 놓고난 직후엔 저 변수값은 true이지만,<br/>

```
void CPlayer::EndOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::BOMB)
	{
		if (!CanMove[0])
		{
			CanMove[0] = true;
		}

		if (!CanMove[1])
		{
			CanMove[1] = true;
		}

		if (!CanMove[2])
		{
			CanMove[2] = true;
		}

		if (!CanMove[3])
		{
			CanMove[3] = true;
		}

	}
	m_BombFirst = false;
}
```

EndOverlap함수를 거치고 난 뒤엔 false로 바뀐다.<br/>

그리고 다시 접촉했을 때 BeginOverlap함수를 만난다면<br/>

```
else if (_pOther->GetOwner()->ReturnLayer() == LAYER::BOMB)
	{
		CBomb* _pBomb = (CBomb*)(_pOther->GetOwner());
		Vec2 BombPos = _pBomb->GetPos();

		if (vPos.y - BombPos.y < (TILE_SIZE) && IsPressed(KEY::UP) && !m_BombFirst) // 위쪽에 물풍선이 있을 경우
		{
			CanMove[0] = false;
		}

		else if (BombPos.y - vPos.y < (TILE_SIZE) && IsPressed(KEY::DOWN) && !m_BombFirst) // 아래쪽에 물풍선이 있을 경우
		{
			CanMove[1] = false;
		}
		else if (vPos.x - BombPos.x < (TILE_SIZE) && IsPressed(KEY::LEFT) && !m_BombFirst) // 왼쪽에 물풍선이 있을 경우
		{
			CanMove[2] = false;
		}

		else if (BombPos.x - vPos.x < (TILE_SIZE) && IsPressed(KEY::RIGHT) && !m_BombFirst) // 오른쪽에 물풍선이 있을 경우
		{
			CanMove[3] = false;
		}
	}
```

이 코드로 인해서 해당방향으론 이동할 수 없게 만들어진다.<br/>

변수명 짓기가 슬슬 힘들어지고있다.<br/>

### 18시 50분<br/>
<br/>

계속해서, 고민이었던 같은 칸에 물폭탄이 2개 놓여지는 현상에 대해서 생각했다.<br/>

해결방안은 또 변수 추가이다.<br/>

```
    bool        m_CanSetBomb; // 폭탄을 설치할 수 있는지 여부
```

그리고 OnOverlap함수에 아래의 코드를 설치했다.<br/>

```
else if (_pOther->GetOwner()->ReturnLayer() == LAYER::BOMB)
	{
		Vec2 BombPos = _pOther->GetOwner()->GetPos();

		if (m_TileCenter == BombPos)
			m_CanSetBomb = false;
	}
```

생각해보면 플레이어의 위치에 따라 타일의 가운데 좌표를 조사, 저장하고,<br/>

물풍선은 타일의 가운데에 설치하도록 구현했다.<br/>

그렇다면 물풍선의 위치랑 현재 타일 가운데의 위치가 같다면 설치를 못하게 막으면 되는 것이었다.<br/>

```
	if (m_CanSetBomb == false)
		m_CanSetBomb = true;
```
물풍선과 떨어지면 다시 true로 바꿔주고,<br/>

```
if (IsTap(KEY::SPACE) && m_BombCount > 0 && m_CanSetBomb)
```

물풍선 설치 조건문에다가 변수를 추가해주었다.<br/>

이렇게 하니 버그가 해결되었다.<br/>

계속해서 다음 고민은 State패턴 생성, 그리고 물웅덩이에 물줄기가 닿으면 이동할 수 있도록 하는 버그를<br/>

어떻게 해결할 것 인가를 고민해야겠다.<br/>

<br/>

### 19시 20분<br/>

물 웅덩이에 물줄기가 닿으면 이동할 수 있게 되는 버그를 해결했다.<br/>

의외로 간단했다.<br/>

바로 또 변수추가!<br/>

```
    bool    is_AbsObstacle; //절대 부숴지지 않는 장애물인가?
```

박스로 인해서 임시적으로 장애물일 수 있으나,<br/>

부숴지지 않는 박스라던지, 물웅덩이라던지가 있는 경우에는 영구적인 장애물이라는 것을 표시한다.<br/>

```
void CObstacle::EndOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::WAVE && !is_AbsObstacle)
	{
		is_Obstacle = false;
	}
}
```
이렇게 한 뒤에, 물줄기를 만났을 때 영구적인 장애물이 아닌 경우에만 Obstacle을 false로 변환한다.<br/>

이렇게하니까 더 자연스러워졌다.<br/>
<br/>

### 20시 30분<br/>
<br/>

렌더 순서를 조절하였다.<br/>
(타일 - 블록 - 물줄기 - 캐릭터)<br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb2_4.png?raw=true)<br/>
<br/>

## 11월 1일<br/>
<br/>

플레이어의 State를 구성하는 중이다.<br/>

기존의 CPlayer의 tick에서 연산하던 코드를 CPIdleState 클래스를 생성해 그곳에 있는 tick으로 옮겼고,<br/>

TrappedState클래스를 새로 생성해주었다.<br/>

```
void CPTrappedState::Enter()
{
	CPlayer* pPlayer = dynamic_cast<CPlayer*>(GetOwnerPlayer());
	assert(pPlayer);

	pPlayer->GetAnimator()->Play(L"TRAPPED", false);
}
```

TrappedState 클래스의 일부로, 여기서 물줄기에 플레이어가 닿으면 물방울 안에 갇히도록 구현하였다.<br/>

그리고 느린속도로 이동할 수 있도록 tick함수에 세팅하였다.<br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb2_5.png?raw=true)<br/>

위 사진과 같이 이제 물줄기에 닿으면 물방울 안에 갇히게 된다.<br/>

다음 목표는 사망모션과 부활모션을 생성할 것이다.<br/>

### 오후 3시<br/>
<br/>

같은 방법으로 DeadState를 생성해서 Trapped 상태가 된지 5초가 지나면 죽는 모션을 만들었고,<br/>

또한, 우선은 TrappedState에서 Q를 누르면 부활을 하도록 구현하였다.<br/>

나중에 바늘아이템이 있는지 여부에 따라서 부활 가능인지 여부를 넣으려고 한다.<br/>
<br/>

### 오후 7시 30분<br/>
<br/>

아이템을 구현 중이다. 현재 물줄기 증가, 물풍선 개수 증가, 이동속도 증가, 물줄기 최대화를 구현하였다.<br/>

```
void CObstacle::EndOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::WAVE && !is_AbsObstacle) // 절대 장애물이 아닌 경우에!
	{

		if (is_Obstacle) // is_Obstacle은 박스가 있는 곳.
		{
			// 박스가 부숴지고 확률적으로 아이템 생성
			int random_item = rand() % 100;

			if (0 <= random_item && random_item < 10)
			{
				CBombPlus* pBombPlus = new CBombPlus;
				pBombPlus->SetScale(Vec2(38.f, 38.f));
				Instantiate(pBombPlus, GetPos(), LAYER::ITEM);
			}

			else if (10 <= random_item && random_item < 20)
			{
				CWavePlus* pWavePlus = new CWavePlus;
				pWavePlus->SetScale(Vec2(38.f, 38.f));
				Instantiate(pWavePlus, GetPos(), LAYER::ITEM);
			}

			else if (20 <= random_item && random_item < 25)
			{
				CMaxWave* pMaxWave = new CMaxWave;
				pMaxWave->SetScale(Vec2(38.f, 38.f));
				Instantiate(pMaxWave, GetPos(), LAYER::ITEM);
			}

			else if (25 <= random_item && random_item < 35)
			{
				CSpeedUp* pSpeedUp = new CSpeedUp;
				pSpeedUp->SetScale(Vec2(38.f, 38.f));
				Instantiate(pSpeedUp, GetPos(), LAYER::ITEM);
			}
		is_Obstacle = false;
	}
}
```

박스가 부숴지고나서 아이템이 확률적으로 생성되도록 구현하였다.<br/>

나중에 여유가 된다면 물풍선을 발로차는 아이템도 구현하고자 한다.<br/>

### 20시<br/>
<br/>

중대한 문제가 발생했다.<br/>

**설치한 물풍선이 많고, 최대 물줄기가 길어지면 프레임 드랍이 발생한다.**<br/>

물줄기를 생성하는 원리가 도미노처럼 물줄기하나가 다음 물줄기를 형성하는 형태로 생성하는 것인데,<br/>

이 과정에서 프레임 드랍이 발생하는 것 같다.<br/>

최적화가 필요해보인다. 어떻게 해결할까?<br/>

## 11월 2일<br/>
<br/>

다행히 해결하였다.<br/>

원인을 파악해보니, 물줄기가 new로 계속 생성될 때마다 애니메이션 로드를 계속해주었기 때문에 발생하는 문제였다.<br/>

물줄기와 애니메이션의 복사생성자를 생성하여<br/>

물줄기 가운데와 가운데와 맞닿아있는 첫 칸은 new로 생성, 나머지는 복사생성자를 통해 생성해주어<br/>

로딩 과정을 줄였다.<br/>

이를 통해 프레임 드랍이 감소한 것을 확인하였다.<br/>

### 18시<br/>
<Br/>

바늘의 기능을 구현하였고, 특정 아이템(바늘 등)을 먹으면 사진과 같이<br/>

소유 아이템이 표시되도록 구현하였다.<br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb2_6.png?raw=true)<br/>

## 11월 3일<br/>
<Br/>

코딩과 구현 보다는 몬스터의 State 설계를 진행하였다.<br/>

기본적인 MonsterState를 구성하고, 각 몬스터마다 이를 어떻게 적용할지 고민 중이다.<br/>

그리고 컨디션이 좋지 않은지 진전이 느껴지지 않는다.<br/>

조금만 하고 재충전을 해야할 것 같다.<br/>