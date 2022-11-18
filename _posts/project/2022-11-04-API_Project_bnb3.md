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

개발기간 : 10월 18일 ~ 11월 20일<br/>
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

### 17시 40분<br/>
<br/>

방향이 바뀌면 애니메이션도 바뀌도록 구현하였다.<br/>

이제 물줄기에 맞으면 TrappedState가 되도록 구현할 차례이다.<br/>

## 11월 7일<br/>
<br/>

6일엔 휴식을 취하고<br/>

7일에 다음의 내용을 구현하였다.<br/>

- Elli의 Idle, Trapped, DeadState 구현
- 다른몬스터 (MiniBall1, MiniBall2, Orange)들의 Idle, DeadState 구현

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb3_1.png?raw=true)<br/>

Elli는 Idle 상태에서 물줄기를 맞으면 Trapped로, 그리고 Trapped에서 다시 물줄기를 맞으면 Dead로 상태가 전환된다.<br/>

나머지 몬스터들은 Idle에서 물줄기를 맞으면 바로 Dead로 상태가 전환된다.<br/>

## 11월 8일<br/>
<br/>

오늘은 물개(Seal)을 구현하였다.<br/>

- Seal의 이동
- Seal의 피격모션
- Seal의 Trapped 모션
- Seal의 사망모션

위의 내용을 구현하였다.<br/>

Seal은 보스몬스터 취급으로 다른 일반몬스터와는 달리 블록의 위치에 구애받지 않고 이동하도록 하였다.<br/>

다음 목표는 Seal의 공격모션과 패턴, 그리고 조금 더 복잡한 이동을 할 수 있도록 설계를 해야겠다.<br/>

11월 9일에 에이스타 알고리즘 (길찾기 알고리즘 중 하나)을 수업하기로 했으니 이를 참고해야지.<br/>


## 11월 9일 ~ 10일<br/>
<br/>

스테이지의 이동을 구현하였다.<br/>

- 패배조건 달성시 (플레이어 목숨이 0이 될시) 맨 처음 페이지로 이동<br/>
- 승리조건 달성시 (레벨에 몬스터가 0마리) 다음 스테이지로 이동

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb3_2.png?raw=true)<br/>
![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb3_3.png?raw=true)<br/>


아직 패배시 LOSE가 뜨는 것은 구현이 안되었다. 이것과 맨 처음 메인화면을 구현하는 것이 다음 목표이다.<br/>

## 11월 11일<br/>
<Br/>

메인화면과 패배시 LOSE가 뜨고 맨 처음 메인화면으로 이동하는 기능을 구현하였다!<br/>

우선은 메인화면에서 숫자 1을 누르면 에디터로, 숫자 2를 누르면 vs 몬스터 모드로 게임을 시작하도록 하였다.<br/>

메인화면에 안내사항은 추후 추가하도록 할 예정이다.<br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb3_4.png?raw=true)<br/>
![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb3_5.png?raw=true)<br/>

## 11월 12일<br/>
<br/>

물개의 위에 체력바가 따라다니도록 구현하였다.<br/>

## 11월 14일<br/>
<br/>

메인 화면에 버튼을 추가하였다.<br/>

일반모드는 몬스터 모드로, 경쟁모드는 2인 플레이 모드로 들어가게 된다.<br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb3_6.png?raw=true)<br/>

## 11월 15일<br/>
<Br/>

2인 플레이어 모드를 구현하였다.<br/>

- BGM 삽입
- 1P : 상하좌우키로 움직임
- 2P : WSAD키로 움직임
- 한명이 죽을 시 게임오버 구현

현재 구현 완료 상황이다.

앞으로 할 것

- 2P용 ItemUI 만들기
- 2P와 블록 충돌처리 시키기

현재 2P는 블록을 통과해서 다니는 버그가 발생하고있다.<br/>

왜 그런지 조사를 해야겠다.<br/>

![인게임 화면](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/project/bnb3_7.png?raw=true)<br/>

## 11월 16일<br/>
<br/>

완료한 것

- 2P용 ItemUI 제작
- 2P와 블록 충돌처리

왜 블록 충돌이 처리가안됐나 했더니 2P키로 눌렀을 때 충돌처리가 되도록 세팅을 해놨어야 했는데 그렇지 않았다.

아래와 같이 바꾸어주니 충돌이 된다.<br/>

```
void CPlayer2::OnOverlap(CCollider* _pOther)
{
	Vec2 vPos = GetPos();
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::OBSTACLE)
	{
		CObstacle* _pObstacle = (CObstacle*)(_pOther->GetOwner());
		Vec2 ObsPos = _pObstacle->GetPos();

		if (_pObstacle->GetObstacle() == true) // 이동 불가능한 곳에 접촉 했을 때
		{
			if (vPos.y - ObsPos.y < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::W)) // 캐릭터보다 위쪽에 블록이 있을 경우
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

			if (ObsPos.y - vPos.y < (TILE_SIZE / 2.f) && IsPressed(KEY::S)) // 아래쪽에 블록이 있을 경우
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
			if (vPos.x - ObsPos.x < (3.f * TILE_SIZE / 2.f) && IsPressed(KEY::A)) // 왼쪽에 블록이 있을 경우
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

			if (ObsPos.x - vPos.x < (TILE_SIZE / 2.f) && IsPressed(KEY::D)) // 오른쪽에 블록이 있을 경우
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
```

그리고 발차기 아이템을 구현하였다.<br/>

기존에 물풍선과 충돌중일 때 일정시간동안 계속 충돌하고 방향키를 누르면<br/>

방향키를 누른 방향으로 물풍선이 밀려나도록 구현하였다.<br/>

이 과정에서 물풍선의 이동 방향을 저장할 int변수와 발로 차졌는지 여부를 저장할 bool 변수를 추가하였다.<br/>

또한, 타일의 정중앙에 위치를 시켜야하므로 현재위치에서 가장 가까운 타일의 정중앙 위치를 저장할 변수를 추가하였다.<br/>

1p와 2p의 물풍선을 모두 찰 수 있다.<br/>

여기서 큰일날뻔했던 점이 생각난다.<br/>

```
void CKick::BeginOverlap(CCollider* _pOther)
{
	if (_pOther->GetOwner()->ReturnLayer() == LAYER::PLAYER)
	{
		CPlayer* pPlayer = (CPlayer*)_pOther->GetOwner();

		pPlayer->SetKick(true);

		CSound* pSound = CResMgr::GetInst()->FindSound(L"ItemGet");

		pSound->Play();

		SetDead();
	}
}
```

필자는 지금 CPlayer와 CPlayer2 클래스를 구현한 상태이다.<br/>

하지만 아이템 획득 관련 코드에서 CPlayer* 포인터만을 사용했는데,<br/>

만약에 CPlayer와 CPlayer2의 변수 구성이 달랐다면 <br/>

메모리 에러가 났을 것이다.<br/>

하지만 두 클래스의 변수 구성이 같기 때문에 에러가 발생하지 않은 것 같다.<br/>

다른 코딩할때에는 구분을 짓도록 주의해서 코딩해야겠다.<br/>

다이나믹 캐스팅을 활용해야지.<br/>

## 11월 18일<br/>
<br/>

전반적인 개발은 완료하였다.<br/>

테스트를 계속해보고 버그가 발생하면 디버깅을하고 리팩토링을 진행해야겠다.<br/>

그럴 때마다 기록으로 남겨놔야겠다!<br/>
