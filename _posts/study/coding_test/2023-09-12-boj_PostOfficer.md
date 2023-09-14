---
layout: post
title:  "[백준] 2842_집배원 한상덕 C++"
subtitle:   
date: 2023-09-12 07:16:15 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2842_집배원 한상덕 C++<br/>
<br/>

투 포인터를 업그레이드 해서 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstring>
#include <queue>

#define MAX 51

using namespace std;

int N, Kcnt = 0;
char Map[MAX][MAX];
int Tire[MAX][MAX];
bool visited[MAX][MAX];
int dx[8] = { -1, 0, 1, 1, 1, 0, -1, -1 };
int dy[8] = { -1, -1, -1, 0, 1, 1, 1, 0 };
int Sx, Sy;

vector<int> TireGroup;

int m_min(int a, int b)
{
	if (a < b)
		return a;
	else
		return b;
}

bool IsThere(int a)
{
	for (size_t i = 0; i < TireGroup.size(); ++i)
	{
		if (TireGroup[i] == a)
			return true;
	}
	return false;
}

bool BFS(int Low, int High)
{
	if (!(Tire[Sy][Sx] <= High && Tire[Sy][Sx] >= Low))
		return false;

	queue<pair<int, int>> q;
	q.push( { Sx, Sy } );
	visited[Sy][Sx] = true;

	while (!q.empty())
	{
		int CurX = q.front().first;
		int CurY = q.front().second;
		q.pop();

		for (int i = 0; i < 8; ++i)
		{
			int Nx = CurX + dx[i];
			int Ny = CurY + dy[i];

			if (!(0 <= Nx && Nx < N && 0 <= Ny && Ny < N))
				continue;

			if (!visited[Ny][Nx] && Tire[Ny][Nx] >= Low && Tire[Ny][Nx] <= High)
			{
				visited[Ny][Nx] = true;
				q.push({ Nx, Ny });
			}
		}
	}

	for (int i = 0; i < N; ++i)
	{
		for (int j = 0; j < N; ++j)
		{
			if (Map[i][j] == 'K' && !visited[i][j])
				return false;
		}
	}
	return true;
}

int main()
{
	ios::sync_with_stdio(0);
	cout.tie(0);
	cin.tie(0);

	cin >> N;

	for (int i = 0; i < N; ++i)
	{
		for (int j = 0; j < N; ++j)
		{
			cin >> Map[i][j];
			if (Map[i][j] == 'K')
				Kcnt++;
			if (Map[i][j] == 'P')
			{
				Sx = j;
				Sy = i;
			}
		}
	}

	for (int i = 0; i < N; ++i)
	{
		for (int j = 0; j < N; ++j)
		{
			cin >> Tire[i][j];

			if (!IsThere(Tire[i][j]))
				TireGroup.push_back(Tire[i][j]);
		}
	}

	sort(TireGroup.begin(), TireGroup.end());

	int Left = 0;
	int Right = 0;
	int Ans = 2147483647;

	while (Left < TireGroup.size())
	{
		memset(visited, false, sizeof(visited));

		if (BFS(TireGroup[Left], TireGroup[Right]))
		{
			Ans = m_min(Ans, TireGroup[Right] - TireGroup[Left]);
			Left++;
		}
		else
		{
			if (Right < TireGroup.size() - 1)
				Right++;
			else
				break;
		}
	}

	cout << Ans;

	return 0;
}

```

이 문제를 접하고 DFS와 투 포인터를 써야겠다는 생각은 들었다.<br/>

다만, 투 포인터를 어떻게 활용해야할지 아이디어가 떠오르지 않았다.<br/>

30분 고민해도 풀리지 않아서 구글링을 해보기로 했다.<br/>

이번에 참고한 글은 [이분의 글](https://junseok.tistory.com/101)이다.<br/>

아하! 내가 설계부터 잘못한 것 같다.<br/>

BFS와 투 포인터를 사용하는 문제였고,<br/>

투 포인터를 사용하는 부분은 경로 상에서 투 포인터를 사용하는 것이 아니라,<br/>

최대, 최소 값을 설정한 다음에 그 값 내의 범위에서 모든 K를 들를 수 있는지<br/>

체크하는 것이었다.<br/>

즉,

```
P(2) 1 4
3 K(3) 5
2 2 K(2)
```
가 있다고 할 때, Left를 1, Right를 5로 둔 다음에<br/>

이 포인터의 간격을 서서히 좁혀나가면서 차이의 최솟값을 구하는 것이다.<br/>

위의 맵 같은 경우에는 경로 상에서 최소 값은 1, 최대 값은 3이 될 것이다.<br/>

따라서 답은 2가 된다.<br/>