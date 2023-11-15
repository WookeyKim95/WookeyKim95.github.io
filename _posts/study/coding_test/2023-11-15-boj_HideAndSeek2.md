---
layout: post
title:  "[백준] 12851_숨바꼭질 2 C++"
subtitle:   
date: 2023-11-15 07:11:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 12851_숨바꼭질 2 C++<br/>
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

#define MAX 99999999

using namespace std;

int Cost[100001] = { MAX, };
int moving[3] = { -1, 1, 0 };

int N, M;
int Ans = 0;

void BFS(int start)
{
	queue<pair<int, int>> q;

	q.push({start, 0});
	Cost[start] = 0;

	while (!q.empty())
	{
		int Cur = q.front().first;
        int CurCost = q.front().second;
		q.pop();

        if (Cur == M)
        {
            if (Cost[Cur] == MAX)
            {
                Ans = 1;
                Cost[Cur] = CurCost;
            }
            else
            {
                if (CurCost == Cost[Cur])
                    Ans++;
            }
        }

		int Next;
		for (int i = 0; i < 3; ++i)
		{
			if (i == 2)
				Next = Cur * 2;
			else
				Next = Cur + moving[i];

			if (!(0 <= Next && Next <= 100000))
				continue;

			if (Cost[Next] >= Cost[Cur] + 1)
			{
				Cost[Next] = Cost[Cur] + 1;
				q.push({Next, CurCost + 1});
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
	
	for (int i = 0; i <= 100000; ++i)
		Cost[i] = 99999999;

	BFS(N);

    cout << Cost[M] << '\n';
    cout << Ans;

	return 0;
}
```
<br/>

[숨바꼭질1](https://www.acmicpc.net/problem/1697) 문제에서 응용해볼 수 있는 문제이다.<br/>

여기서 추가로 필요한 것은 최단 거리로 갔을 경우 경우의 수이니<br/>

현 위치와 함께 소모된 비용을 함께 큐에다가 넣어주면 되는 것이다.<br/>

```
void BFS(int start)
{
	queue<pair<int, int>> q;

	q.push({start, 0});
	Cost[start] = 0;

	while (!q.empty())
	{
		int Cur = q.front().first;
        int CurCost = q.front().second;
		q.pop();

        if (Cur == M)
        {
            if (Cost[Cur] == MAX)
            {
                Ans = 1;
                Cost[Cur] = CurCost;
            }
            else
            {
                if (CurCost == Cost[Cur])
                    Ans++;
            }
        }

		int Next;
		for (int i = 0; i < 3; ++i)
		{
			if (i == 2)
				Next = Cur * 2;
			else
				Next = Cur + moving[i];

			if (!(0 <= Next && Next <= 100000))
				continue;

			if (Cost[Next] >= Cost[Cur] + 1)
			{
				Cost[Next] = Cost[Cur] + 1;
				q.push({Next, CurCost + 1});
			}
		}
	}
}
```

그래서 BFS의 큐에다가 정수쌍을 넣어주는 구조로 개조했고, <br/>

첫번째로 도착을 하게 된다면 그것이 최소비용이니<br/>

MAX에서 값을 갱신해준다.<br/>

그리고 같은 비용이 다시 들어온다면 Ans를 증가시키는 식으로 구현하였다.<br/>

이 때, 주의할 점은

```
if (Cost[Next] >= Cost[Cur] + 1)
{
    Cost[Next] = Cost[Cur] + 1;
    q.push({Next, CurCost + 1});
}
```

큐에다가 값을 넣어줄 때 값이 같은 경우도 탐색해야하기 때문에<br/>

부등호에다가 등호를 같이 넣어주자. 안그럼 틀리더라.<br/>