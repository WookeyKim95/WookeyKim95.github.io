---
layout: post
title:  "[백준] 2602_돌다리 건너기 C++"
subtitle:   
date: 2023-09-23 07:12:58 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2602_돌다리 건너기 C++<br/>
<br/>

DFS인줄 알았더니 DP써야 했던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>
#include <vector>
#include <string>

using namespace std;

string roll;
string Bridge[2];
int DP[2][101][21];

int DFS(int col, int curBridge, int curBridgeCol)
{
	if (col == roll.length())
		return 1;

	if (DP[curBridge][curBridgeCol][col] != -1)
		return DP[curBridge][curBridgeCol][col];
	
	DP[curBridge][curBridgeCol][col] = 0;

	for (size_t i = curBridgeCol; i < Bridge[0].length(); ++i)
	{
		if (curBridge == 1)
		{
			if (roll[col] == Bridge[1][i])
				DP[curBridge][curBridgeCol][col] += DFS(col+1, 0, i+1);
		}
				
		else if (curBridge == 0)
		{
			if (roll[col] == Bridge[0][i])
				DP[curBridge][curBridgeCol][col] += DFS(col+1, 1, i+1);
		}
	}

	return DP[curBridge][curBridgeCol][col];
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	memset(DP, -1, sizeof(DP));

	cin >> roll;

	cin >> Bridge[0];
	cin >> Bridge[1];

	DP[0][0][0] = DFS(0, 0, 0);
	DP[1][0][0] = DFS(0, 1, 0);

	cout << DP[0][0][0] + DP[1][0][0];

	return 0;
}
```

<br/>

## 오답제출코드 (시간 초과)<br/>
<br/>

```
#include <iostream>
#include <cstring>
#include <vector>
#include <string>

using namespace std;

int cnt = 0;
string roll;
string Bridge[2];

void DFS(int col, int curBridge, int curBridgeCol)
{
	if (col == roll.length())
	{
		cnt++;
		return;
	}
	for (size_t i = curBridgeCol + 1; i < Bridge[0].length(); ++i)
	{
		if (curBridge == 1)
		{
			if (roll[col] == Bridge[0][i])
				DFS(col + 1, 0, i);
		}
				
		else if (curBridge == 0)
		{
			if (roll[col] == Bridge[1][i])
				DFS(col + 1, 1, i);
		}
	}
}

int main()
{
	cin >> roll;

	cin >> Bridge[0];
	cin >> Bridge[1];

	for (int i = 0; i < 2; ++i)
	{
		for (size_t j = 0; j < Bridge[0].length(); ++j)
		{
			if (Bridge[i][j] == roll[0])
			{
				DFS(1, i, j);
			}
		}
	}

	cout << cnt;

	return 0;
}
```

백트래킹, DFS로 풀어보는 문제였던 것 같다.<br/>

사실 번갈아가는 경우를 구현하기가 힘들거라 생각했었는데, 의외로 간단했다.<br/>

그냥 2행짜리 문자열을 담는 배열을 선언해주고,<br/>

0번을 악마의 돌다리, 1번을 천사의 돌다리로 지정해주고 현재 0번이면 1번으로, 현재 1번이면 0번으로 건너가게 해주면 된다.<br>

```
void DFS(int col, int curBridge, int curBridgeCol)
{
	if (col == roll.length())
	{
		cnt++;
		return;
	}
	for (size_t i = curBridgeCol + 1; i < Bridge[0].length(); ++i)
	{
		if (curBridge == 1)
		{
			if (roll[col] == Bridge[0][i])
				DFS(col + 1, 0, i);
		}
				
		else if (curBridge == 0)
		{
			if (roll[col] == Bridge[1][i])
				DFS(col + 1, 1, i);
		}
	}
}
```
여기서 col은 현재 두루마리의 몇번째 인덱스인지, curBridgeCol은 다리의 몇번째 인덱스인지를 가리킨다.<br/>

이렇게 해서 두루마리의 순서를 모두 진행했다면 cnt를 1 늘려주면 된다.<br/>

하지만 이렇게 하니까 46%에서 시간초과가 뜬다.<br/>

가지치기가 필요하다는 것인데 어떻게 하면 될까?<br/>
<br/>

## 정답코드로의 변환<br/>
<Br/>

아뿔싸! 시간초과의 발생에다가 경우의 수를 따진다?<br/>

설마설마 하는 마음에 알고리즘 분류를 보았다.<br/>

그랬다. 알고리즘 설계 자체가 잘못됐다.<br/>

DFS, 백트래킹 인줄 알았던 문제가 사실은 다이나믹 프로그래밍 이었던 것이다.<br/>

```
#include <iostream>
#include <cstring>
#include <vector>
#include <string>

using namespace std;

string roll;
string Bridge[2];
int DP[2][101][21];

int DFS(int col, int curBridge, int curBridgeCol)
{
	if (col == roll.length())
		return 1;

	if (DP[curBridge][curBridgeCol][col] != -1)
		return DP[curBridge][curBridgeCol][col];
	
	DP[curBridge][curBridgeCol][col] = 0;

	for (size_t i = curBridgeCol; i < Bridge[0].length(); ++i)
	{
		if (curBridge == 1)
		{
			if (roll[col] == Bridge[1][i])
				DP[curBridge][curBridgeCol][col] += DFS(col+1, 0, i+1);
		}
				
		else if (curBridge == 0)
		{
			if (roll[col] == Bridge[0][i])
				DP[curBridge][curBridgeCol][col] += DFS(col+1, 1, i+1);
		}
	}

	return DP[curBridge][curBridgeCol][col];
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	memset(DP, -1, sizeof(DP));

	cin >> roll;

	cin >> Bridge[0];
	cin >> Bridge[1];

	DP[0][0][0] = DFS(0, 0, 0);
	DP[1][0][0] = DFS(0, 1, 0);

	cout << DP[0][0][0] + DP[1][0][0];

	return 0;
}
```

기존에 작성한 코드를 조금 개조해서 DP코드로 전환하였다.<br/>

다행히 Top-Down 방식을 적용해서 풀 수 있는 문제였다.