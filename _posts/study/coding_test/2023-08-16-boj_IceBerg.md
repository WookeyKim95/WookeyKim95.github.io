---
layout: post
title:  "[백준] 2573_빙산 C++"
subtitle:   
date: 2023-08-16 06:57:57 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2573_빙산 C++<br/>
<br/>

BFS를 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>
#include <queue>
#include <algorithm>
#include <string>

using namespace std;

bool visited[76][76];
int map[76][76];
int newmap[76][76];
int dx[4] = { -1, 1, 0, 0 };
int dy[4] = { 0, 0, -1, 1 };

int x, y, x2, y2;
int N, M;


void BFS(int startx, int starty, int num)
{
	queue<pair<pair<int, int>, int>> q;
	q.push({ {startx, starty}, num });
	visited[starty][startx] = true;

	while (!q.empty())
	{
		int CurX = q.front().first.first;
		int CurY = q.front().first.second;
		int CurNum = q.front().second;
		q.pop();

		int meltcount = 0;
		for (int i = 0; i < 4; ++i)
		{
			int Nx = CurX + dx[i];
			int Ny = CurY + dy[i];

			if (!(0 <= Nx && Nx < M && 0 <= Ny && Ny < N))
				continue;

			if (map[Ny][Nx] <= 0)
				meltcount++;
		}

		newmap[CurY][CurX] -= meltcount;

		for (int i = 0; i < 4; ++i)
		{
			int Nx = CurX + dx[i];
			int Ny = CurY + dy[i];

			if (!(0 <= Nx && Nx < M && 0 <= Ny && Ny < N))
				continue;

			if (!visited[Ny][Nx] && map[Ny][Nx] > 0)
			{
				visited[Ny][Nx] = true;
				q.push({ {Nx, Ny}, CurNum});
			}
		}
	}
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> M;

	for (int i = 0; i < N; ++i)
	{
		for (int j = 0; j < M; ++j)
		{
			cin >> map[i][j];
			newmap[i][j] = map[i][j];
		}
	}

	int Counter = 0;

	while (true)
	{
		memset(visited, false, sizeof(visited));

		int num = 0;
		for (int i = 0; i < N; ++i)
		{
			for (int j = 0; j < M; ++j)
			{
				if (!visited[i][j] && map[i][j] > 0)
				{
					num++;
					BFS(j, i, num);
				}
			}
		}

		if (num >= 2)
		{
			cout << Counter;
			break;
		}

		if (num == 0)
		{
			cout << 0;
			break;
		}

		for (int i = 0; i < N; ++i)
		{
			for (int j = 0; j < M; ++j)
				map[i][j] = newmap[i][j];
		}
		Counter++;
	}

	return 0;
}
```

이 문제의 관건은 한꺼번에 녹는것을 어떻게 구현할지 막막했다.<br/>

그런데 알고보니 답은 간단명료했던게<br/>

num이 1이었다가 갑자기 0이 되어버리면 그냥 한꺼번에 녹았다는 것 아닌가?<br/>

그래서 무한 루프를 계속 돌려주다가 num이 0이 되어버리면<br/>

0을 출력하고 바로 break를 하도록 구현하였다.<br/>

그리고 또 다른 관건이 있다.<br/>

BFS를 도는 과정에서 맵을 하나만 써서 갱신해버리면<br/>

```
0 0 0 0
0 4 1 0
0 0 0 0
```

위와 같은 상황이라고 해보자.<br/>

여기서 4보다 1을 먼저 갱신하면<br/>

```
0 0 0 0
0 4 0 0
0 0 0 0
```
이렇게 된 상태에서 4를 계산해줘야 하는데<br/>

삼면이 바다로 둘러싸여 계산했어야 했는데<br/>

map을 하나만 써버리면 사면이 바다로 둘러싸인채 계산하게 된다.<br/>

그래서 갱신용 newmap을 따로 만들어줘서 갱신용 기록을 거기다가 남긴 다음에<br/>

마지막에 map에다가 옮기는 방식으로 구현하였다.<br/>