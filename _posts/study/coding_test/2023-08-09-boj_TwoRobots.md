---
layout: post
title:  "[백준] 15971_두 로봇 C++"
subtitle:   
date: 2023-08-09 06:26:44 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 15971_두 로봇 C++<br/>
<br/>

알고보면 DFS를 사용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

struct Node
{
	vector<pair<int, int>> link;
	bool visited = false;
};

int N, Robot1, Robot2;
bool flag = false;
vector<Node> Cave;

int m_max(int a, int b)
{
	if (a > b)
		return a;
	else
		return b;
}

void DFS(int R1, int acculPrice, int Max_dist)
{
	if (flag)
		return;
	if (R1 == Robot2)
	{
		cout << acculPrice - Max_dist;
		flag = true;
		return;
	}
	
	for (size_t i = 0; i < Cave[R1].link.size(); ++i)
	{
		int Next = Cave[R1].link[i].first;
		int NextPrice = Cave[R1].link[i].second;

		if (!Cave[Next].visited)
		{
			Cave[Next].visited = true;
			DFS(Next, acculPrice + NextPrice, m_max(NextPrice, Max_dist));
		}
	}

	return;

}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> Robot1 >> Robot2;

	for (int i = 0; i <= N; ++i)
	{
		Node n;
		Cave.push_back(n);
	}

	for (int i = 0; i < N - 1; ++i)
	{
		int a, b, dist;
		cin >> a >> b >> dist;

		Cave[a].link.push_back({ b, dist });
		Cave[b].link.push_back({ a, dist });
	}

	Cave[Robot1].visited = true;	
	DFS(Robot1, 0, 0);

	return 0;
}
```

오답을 제출하고 아이디어가 떠오르지 않아서 구글링을 하고 풀었다.<br/>

아하.. 결국엔 같은 통로상 끝점에 있으면 되는 것이기 때문에<br/>

두 로봇 사이의 거리 - 가장 긴 간선의 길이 <br/>

를 해주면 결국엔 두 로봇이 움직인 거리의 최솟값이 되는 것이었다.<br/>

이를 구현한 코드가 위의 코드이다.<br/>

<br/>

## 오답제출코드<br/>
<Br/>

```
#include <iostream>
#include <cstring>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

struct Node
{
	vector<pair<int, int>> link;
	bool visited1 = false;
	bool visited2 = false;
};

int N, Robot1, Robot2;
int Dist = 999999999;

vector<Node> Cave;

int FindLink(int a, int b)
{
	if (a == b)
		return true;

	for (size_t i = 0; i < Cave[a].link.size(); ++i)
	{
		int Connected = Cave[a].link[i].first;

		if (Connected == b)
			return true;
	}
	return false;
}

void DFS(int R1, int R2, int acculPrice, int flag)
{
	if (FindLink(R1, R2))
	{
		if (Dist > acculPrice)
			Dist = acculPrice;
		return;
	}

	if (flag == 1)
	{
		for (size_t i = 0; i < Cave[R1].link.size(); ++i)
		{
			int Next1 = Cave[R1].link[i].first;
			int NextPrice1 = Cave[R1].link[i].second;

			if (!Cave[Next1].visited1)
			{
				for (int j = 1; j <= 2; ++j)
				{
					Cave[Next1].visited1 = true;
					DFS(Next1, R2, acculPrice + NextPrice1, j);
					Cave[Next1].visited1 = false;
				}
				
			}
		}
	}

	else if (flag == 2)
	{
		for (size_t i = 0; i < Cave[R2].link.size(); ++i)
		{
			int Next2 = Cave[R2].link[i].first;
			int NextPrice2 = Cave[R2].link[i].second;

			if (!Cave[Next2].visited2)
			{
				for (int j = 1; j <= 2; ++j)
				{
					Cave[Next2].visited2 = true;
					DFS(R1, Next2, acculPrice + NextPrice2, j);
					Cave[Next2].visited2 = false;
				}
			}
		}
	}
	
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> Robot1 >> Robot2;

	for (int i = 0; i <= N; ++i)
	{
		Node n;
		Cave.push_back(n);
	}

	for (int i = 0; i < N - 1; ++i)
	{
		int a, b, dist;
		cin >> a >> b >> dist;

		Cave[a].link.push_back({ b, dist });
		Cave[b].link.push_back({ a, dist });
	}

	for (int i = 1; i <= 2; ++i)
	{
		Cave[Robot1].visited1 = true;
		Cave[Robot2].visited2 = true;
		DFS(Robot1, Robot2, 0, i);
	}
		

	cout << Dist;

	return 0;
}
```

처음에 두 로봇을 모두 움직이는 방법을 생각했다.<br/>

하지만 두 로봇을 모두 움직이면 결국 시간초과가 나온다.<br/>

시간초과를 줄이는 방법은 무엇이 있을까 생각하다가 [참고링크](https://dingcoding.tistory.com/116)를 참고하였다.<br/>

그리고 상술한 방법을 찾아낼 수 있었다.<br/>