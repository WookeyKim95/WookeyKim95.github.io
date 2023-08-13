---
layout: post
title:  "[백준] 5214_환승 C++"
subtitle:   
date: 2023-08-14 02:53:12 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 5214_환승 C++<br/>
<br/>

BFS와 더미노드를 응용해보는 문제<br/>
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

struct Station
{
	bool visited = false;
	vector<int> link;
	int Dist = 0;
};

int N, M, K;
vector<Station> Stations;

int BFS(int start)
{
	queue<int> q;

	q.push({start});
	Stations[start].visited = true;
	Stations[start].Dist = 1;

	while (!q.empty())
	{
		int Cur = q.front();
		q.pop();

		int CurDist = Stations[Cur].Dist;
		
		if (Cur == N)
			return CurDist;

		for (size_t i = 0; i < Stations[Cur].link.size(); ++i)
		{
			int Next = Stations[Cur].link[i];

			if (Stations[Next].Dist == 0)
			{
				if (Next > N)
					Stations[Next].Dist = Stations[Cur].Dist;
				else
					Stations[Next].Dist = Stations[Cur].Dist + 1;

				q.push(Next);
			}
		}
	}

	return -1;
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> K >> M;

	Stations.resize(N + M + 1);

	for (int i = 1; i <= M; ++i)
	{
		for (int j = 0; j < K; ++j)
		{
			int a;
			cin >> a;
			Stations[a].link.push_back(i + N);
			Stations[i + N].link.push_back(a);
		}
	}

	cout << BFS(1);

	return 0;
}
```

무수한 시간초과의 악수요청이 들어왔고,<br/>

더 이상 시간을 단축시킬 아이디어가 떠오르지 않아 [이분의 글](https://yabmoons.tistory.com/260)을 참고하였다.<br/>

아하! 하이퍼튜브도 일종의 노드로 선정을 해주고, 하이퍼튜브를 지나는 동안에는 카운트를 하지 않으면 되는 것이었다.<br/>

<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>
#include <queue>
#include <algorithm>

using namespace std;

struct Station
{
	bool visited = false;
	vector<int> link;
	int Dist = 1;
};

int N, M, K;
vector<Station> Stations;

int BFS(int start)
{
	queue<int> q;

	q.push({start});
	Stations[start].visited = true;

	while (!q.empty())
	{
		int Cur = q.front();
		q.pop();

		int CurDist = Stations[Cur].Dist;

		for (size_t i = 0; i < Stations[Cur].link.size(); ++i)
		{
			int Next = Stations[Cur].link[i];

			if (Next == N)
				return CurDist + 1;
				
			if (!Stations[Next].visited)
			{
				Stations[Next].Dist = CurDist + 1;
				q.push({ Next });
				Stations[Next].visited = true;
			}
		}
	}

	return -1;
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> K >> M;

	Stations.resize(N + 1);

	for (int i = 0; i < M; ++i)
	{
		vector<int> Tube;
		for (int j = 0; j < K; ++j)
		{
			int a;
			cin >> a;
			Tube.push_back(a);
		}

		for (int j = 0; j < K-1; ++j)
		{
			for (int k = j + 1; k < K; ++k)
			{
				Stations[Tube[j]].link.push_back(Tube[k]);
				Stations[Tube[k]].link.push_back(Tube[j]);
			}
		}
	}

	cout << BFS(1);

	return 0;
}
```

처음에 생각했던 아이디어는<br/>

같은 하이퍼튜브에 연결된 노드를 더미노드 없이 곧바로 연결해주는 것이었다.<br/>

그런데 문제는 이부분이었다.<br/>

```
for (int j = 0; j < K-1; ++j)
{
    for (int k = j + 1; k < K; ++k)
    {
        Stations[Tube[j]].link.push_back(Tube[k]);
        Stations[Tube[k]].link.push_back(Tube[j]);
    }
}
```

같은 하이퍼튜브끼리 연결된 노드들을 서로 찾는 동안 시간이 많이 낭비된다.<br/>

더미노드라는 개념을 생각하지 못한 것이 아쉬웠다.<br/>

시간을 줄이려고 곧바로 연결하는 작업이 오히려 시간을 잡아먹었다.<br/>

그리고 나머지는 정석적인 BFS를 통해서 해결하였다.<br/>