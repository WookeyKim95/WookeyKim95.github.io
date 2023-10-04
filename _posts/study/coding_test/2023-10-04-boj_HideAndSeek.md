---
layout: post
title:  "[백준] 1697_숨바꼭질 C++"
subtitle:   
date: 2023-10-04 06:49:17 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1697_숨바꼭질 C++<br/>
<br/>

BFS를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>
#include <queue>
#include <algorithm>

using namespace std;

int Cost[100001] = { 99999999, };
int moving[3] = { -1, 1, 0 };

int N, M;

int BFS(int start)
{
	queue<int> q;

	q.push(start);
	Cost[start] = 0;

	while (!q.empty())
	{
		int Cur = q.front();
		q.pop();

		int Next;
		for (int i = 0; i < 3; ++i)
		{
			if (i == 2)
				Next = Cur * 2;
			else
				Next = Cur + moving[i];

			if (!(0 <= Next && Next <= 100000))
				continue;

			if (Cost[Next] > Cost[Cur] + 1)
			{
				Cost[Next] = Cost[Cur] + 1;
				q.push(Next);
			}
		}
	}

	return Cost[M];
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> M;
	
	for (int i = 0; i <= 100000; ++i)
		Cost[i] = 99999999;

	cout << BFS(N);

	return 0;
}
```

자! 목적지에 도달하는 최소의 비용을 생각해야한다.<br/>

근데 여기서 다시 문제에 대해서 생각해보자.<br/>

- 이동할 때마다 비용은 모두 같다.
- 최소의 값을 구하고 싶다.

이 말인 즉슨, 간선의 가중치 값이 모두 같을 때 최단 경로의 거리를 구하는 원리와 같다.<br/>

즉, BFS알고리즘을 사용하는 문제였던 것이다.<br/>

그러면 어떤 지점에서 갈 수 있는 다음 지점은 어떤 곳일까?<br/>

어떤 지점이 X리고 했을 때,<br/>

- X+1
- X-1
- 2*X

값으로 이동할 수 있다고 한다.<br/>

따라서 BFS알고리즘을 사용할 때 연결된 링크를 위와 같이 설정하고 방문처리를 해주면<br/>

자연스럽게 목적지에 도달하기 까지 최소 비용을 구할 수 있게 된다.