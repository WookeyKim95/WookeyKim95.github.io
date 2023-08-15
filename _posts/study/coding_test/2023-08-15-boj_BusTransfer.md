---
layout: post
title:  "[백준] 2536_버스 갈아타기 C++"
subtitle:   
date: 2023-08-15 02:57:53 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2536_버스 갈아타기 C++<br/>
<br/>

BFS를 응용해보는 구현 문제<br/>
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

int N, M, K, ans = 0;
int sx, sy, dx, dy;

struct Bus
{
	int num;
	int x1;
	int y1;
	int x2;
	int y2;
	bool IsHorizon = false;
	vector<int> link;
};

vector<Bus> Buses;

void m_swap(int& a, int& b)
{
	int temp = a;
	a = b;
	b = temp;
}

int BFS()
{
	vector<int> visited(K + 1, -1);
	vector<bool> IsDest(K + 1, false);

	queue<int> q;

	for (int i = 1; i <= K; ++i)
	{
		if (Buses[i].IsHorizon)
		{
			if (!(Buses[i].y1 != sy || Buses[i].x1 > sx || Buses[i].x2 < sx))
			{
				q.push(i);
				visited[Buses[i].num] = 1;
			}

			if (Buses[i].y1 == dy && Buses[i].x1 <= dx && dx <= Buses[i].x2)
				IsDest[Buses[i].num] = true;
		}
		else
		{
			if (!(Buses[i].x1 != sx || Buses[i].y1 > sy || Buses[i].y2 < sy))
			{
				q.push(i);
				visited[Buses[i].num] = 1;
			}

			if (Buses[i].x1 == dx && Buses[i].y1 <= dy && dy <= Buses[i].y2)
				IsDest[Buses[i].num] = true;
		}
	}

	int transfer_num, min_transfer = 1;

	while (!q.empty())
	{
		int CurBus = q.front();
		q.pop();
		transfer_num = visited[Buses[CurBus].num];

		if (IsDest[Buses[CurBus].num])
		{
			min_transfer = transfer_num;
			break;
		}

		// 교차하는 노선인지 구하는 부분
		Bus& Now = Buses[CurBus], comp;

		for (int j = 1; j <= K; ++j)
		{
			comp = Buses[j];
			if (j == CurBus)
				continue;

			// 두 노선이 평행하다면?
			if (Now.IsHorizon == comp.IsHorizon)
			{
				if (Now.IsHorizon)
				{
					if (Now.y1 != comp.y1 || comp.x2 < Now.x1 || Now.x2 < comp.x1)
						continue;
				}
				else
				{
					if (Now.x1 != comp.x1 || comp.y2 < Now.y1 || Now.y2 < comp.y1)
						continue;
				}
				
				if (visited[comp.num] == -1)
				{
					visited[comp.num] = visited[Buses[CurBus].num] + 1;
					q.push(j);
				}
			}

			// 두 노선이 서로 수직이라면?
			else
			{
				if (Now.IsHorizon)
				{
					if (Now.y2 < comp.y1 || comp.y2 < Now.y1 || Now.x1 > comp.x2 || Now.x2 < comp.x1)
						continue;
				}
				else
				{
					if (Now.y1 > comp.y2 || comp.y1 > Now.y2 || Now.x2 < comp.x1 || Now.x1 > comp.x2)
						continue;
				}

				if (visited[comp.num] == -1)
				{
					visited[comp.num] = visited[Buses[CurBus].num] + 1;
					q.push(j);
				}
			}
		}
	}

	return min_transfer;
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> M;
	cin >> K;

	Buses.resize(K+1);

	for (int i = 1; i <= K; ++i)
	{
		int index, _startx, _starty, _endx, _endy;
		cin >> index >> _startx >> _starty >> _endx >> _endy;

		if (_startx > _endx)
			m_swap(_startx, _endx);
		if (_starty > _endy)
			m_swap(_starty, _endy);
			
		Buses[i].num = index;
		Buses[i].x1 = _startx;
		Buses[i].y1 = _starty;
		Buses[i].x2 = _endx;
		Buses[i].y2 = _endy;

		if (_starty == _endy)
			Buses[i].IsHorizon = true;

	}

	cin >> sx >> sy >> dx >> dy;

	cout << BFS();

	return 0;
}
```

사실 BFS를 이용하는 부분은 크게 어렵지 않다.<br/>

연결된 노선을 타고 BFS를 이용해서 목적지 까지 가면 되는 것이다.<br/>

근데 문제는.. 목적지가 어느 노선상에 있고 어떤 노선들이 서로 연결되어있는지를 찾는 것이다.<br/>

이 부분은 어쩔 수 없다. 직접 구현할 수밖에..<br/>

```
if (_startx > _endx)
	m_swap(_startx, _endx);
if (_starty > _endy)
	m_swap(_starty, _endy);
```

우선 첫째.. start지점이 항상 end보다 작게 만들도록 swap을 진행해준다.<br/>

나중에 구현하기 용이하게 하기 위함이다.<br/>

```
if (_starty == _endy)
	Buses[i].IsHorizon = true;
```

그리고 이 노선이 수평노선인지 수직노선인지를 판별하는 코드이다.<br/>

```
// 교차하는 노선인지 구하는 부분
Bus& Now = Buses[CurBus], comp;

for (int j = 1; j <= K; ++j)
{
	comp = Buses[j];
	if (j == CurBus)
		continue;

	// 두 노선이 평행하다면?
	if (Now.IsHorizon == comp.IsHorizon)
	{
		if (Now.IsHorizon)
		{
			if (Now.y1 != comp.y1 || comp.x2 < Now.x1 || Now.x2 < comp.x1)
				continue;
		}
		else
		{
			if (Now.x1 != comp.x1 || comp.y2 < Now.y1 || Now.y2 < comp.y1)
				continue;
		}
		
		if (visited[comp.num] == -1)
		{
			visited[comp.num] = visited[Buses[CurBus].num] + 1;
			q.push(j);
		}
	}

	// 두 노선이 서로 수직이라면?
	else
	{
		if (Now.IsHorizon)
		{
			if (Now.y2 < comp.y1 || comp.y2 < Now.y1 || Now.x1 > comp.x2 || Now.x2 < comp.x1)
				continue;
		}
		else
		{
			if (Now.y1 > comp.y2 || comp.y1 > Now.y2 || Now.x2 < comp.x1 || Now.x1 > comp.x2)
				continue;
		}

		if (visited[comp.num] == -1)
		{
			visited[comp.num] = visited[Buses[CurBus].num] + 1;
			q.push(j);
		}
	}
}
```

그리고 지금까지 작성한 노선들과 새로 작성하는 노선 중에<br/>

교차하는 노선이 있는지, 있다면 서로를 링크로 연결해주는 작업을 하는 구간이다.<br/>

보통 교차하지 않는 경우는 아래와 같겠지.

- 한쪽의 start 지점이 다른 쪽의 end 지점보다 높은 값에 있을 때
- 서로 평행하는데 x좌표나 y좌표가 일치하지 않을 때

이를 생각하면서 구현하였다.<br/>

```
for (int i = 1; i <= K; ++i)
{
	if (Buses[i].IsHorizon)
	{
		if (!(Buses[i].y1 != sy || Buses[i].x1 > sx || Buses[i].x2 < sx))
		{
			q.push(i);
			visited[Buses[i].num] = 1;
		}

		if (Buses[i].y1 == dy && Buses[i].x1 <= dx && dx <= Buses[i].x2)
			IsDest[Buses[i].num] = true;
	}
	else
	{
		if (!(Buses[i].x1 != sx || Buses[i].y1 > sy || Buses[i].y2 < sy))
		{
			q.push(i);
			visited[Buses[i].num] = 1;
		}

		if (Buses[i].x1 == dx && Buses[i].y1 <= dy && dy <= Buses[i].y2)
			IsDest[Buses[i].num] = true;
	}
}
```

시작점이 어느노선에 있는지, 종료점이 어느노선에 있는지를 구하는 코드이다.<br/>

그래서 시작점에 있는 노선은 탑승횟수를 1로 맞춰주고,<br/>

종료지점이 노선상에 있으면 그 노선상에 종료점이 있는가를 true로 전환한다.<br/>

그리고 BFS를 돌면서 종료점을 가지고 있는 노선을 만나면<br/>

지금까지의 탑승 횟수를 반환하고 종료한다.<br/>

그런 시스템이다.<br/>

사실 [이분의 글](https://everenew.tistory.com/152)을 따라치기 한 코드이다.<br/>

그리고 후술할 시간초과 문제로 인해서 개조를 조금 진행하였다.<br/>
<Br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>
#include <queue>
#include <algorithm>
#include <string>

using namespace std;

int N, M, K, ans = 0;
int sx, sy, dx, dy;

struct Bus
{
	int num;
	int x1;
	int y1;
	int x2;
	int y2;
	bool IsHorizon = false;
	vector<int> link;
};

vector<Bus> Buses;

void m_swap(int& a, int& b)
{
	int temp = a;
	a = b;
	b = temp;
}

int BFS()
{
	vector<int> visited(K + 1, -1);
	vector<bool> IsDest(K + 1, false);

	queue<int> q;

	for (int i = 1; i <= K; ++i)
	{
		if (Buses[i].IsHorizon)
		{
			if (Buses[i].y1 != sy || Buses[i].x1 > sx || Buses[i].x2 < sx)
				continue;
		}
		else
		{
			if (Buses[i].x1 != sx || Buses[i].y1 > sy || Buses[i].y2 < sy)
				continue;
		}

		q.push(Buses[i].num);
		visited[Buses[i].num] = 1;
	}

	for (int i = 1; i <= K; ++i)
	{
		if (Buses[i].IsHorizon)
		{
			if (Buses[i].y1 == dy && Buses[i].x1 <= dx && dx <= Buses[i].x2)
				IsDest[Buses[i].num] = true;
		}
		else
		{
			if (Buses[i].x1 == dx && Buses[i].y1 <= dy && dy <= Buses[i].y2)
				IsDest[Buses[i].num] = true;
		}
	}

	int transfer_num, min_transfer;

	while (!q.empty())
	{
		int CurBus = q.front();
		q.pop();
		transfer_num = visited[CurBus];

		if (IsDest[CurBus])
		{
			min_transfer = transfer_num;
			break;
		}

		int NextBus;
		for (size_t i = 0; i < Buses[CurBus].link.size(); ++i)
		{
			NextBus = Buses[CurBus].link[i];

			if (visited[NextBus] == -1)
			{
				q.push(NextBus);
				visited[NextBus] = transfer_num + 1;
			}
		}
	}

	return min_transfer;
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> M;
	cin >> K;

	Buses.resize(K+1);

	for (int i = 1; i <= K; ++i)
	{
		int index, _startx, _starty, _endx, _endy;
		cin >> index >> _startx >> _starty >> _endx >> _endy;

		if (_startx > _endx)
			m_swap(_startx, _endx);
		if (_starty > _endy)
			m_swap(_starty, _endy);
			
		Buses[i].num = index;
		Buses[i].x1 = _startx;
		Buses[i].y1 = _starty;
		Buses[i].x2 = _endx;
		Buses[i].y2 = _endy;

		if (_starty == _endy)
			Buses[i].IsHorizon = true;


		// 교차하는 노선인지 구하는 부분
		Bus& Now = Buses[i], comp;

		for (int j = 1; j < i; ++j)
		{
			comp = Buses[j];

			// 두 노선이 평행하다면?
			if (Now.IsHorizon == comp.IsHorizon)
			{
				if (Now.IsHorizon)
				{
					if (Now.y1 != comp.y1 || comp.x2 < Now.x1 || Now.x2 < comp.x1)
						continue;
				}
				else
				{
					if (Now.x1 != comp.x1 || comp.y2 < Now.y1 || Now.y2 < comp.y1)
						continue;
				}
			}

			// 두 노선이 서로 수직이라면?
			else
			{
				if (Now.IsHorizon)
				{
					if (Now.y2 < comp.y1 || comp.y2 < Now.y1 || Now.x1 > comp.x2 || Now.x2 < comp.x1)
						continue;
				}
				else
				{
					if (Now.y1 > comp.y2 || comp.y1 > Now.y2 || Now.x2 < comp.x1 || Now.x1 > comp.x2)
						continue;
				}
			}

			Buses[Now.num].link.push_back(comp.num);
			Buses[comp.num].link.push_back(Now.num);
		}
	}

	cin >> sx >> sy >> dx >> dy;

	cout << BFS();

	return 0;
}

```

시간초과가 발생하였다.<br/>

원인은 버스를 추가할 때 마다 그동안 추가됐던 버스를 일일이 탐색하는 과정에 있었다.<br/>

이를 해결하고자 교자하는 노선을 찾는 과정을 BFS를 진행할 때 한번만으로 줄여서<br/>

이 과정의 시간복잡도를 O(N^2)에서 O(N)로 감소시켰다.<br/>

즉, 오답코드에서 밑쪽에 있는 교차선분을 찾는 코드를<br/>

정답코드에선 BFS 안으로 옮긴 것이다.<br/>