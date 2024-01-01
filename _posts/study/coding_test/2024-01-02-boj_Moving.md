---
layout: post
title:  "[백준] 11048_이동하기 C++"
subtitle:   
date: 2024-01-02 07:11:03 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11048_이동하기 C++<br/>
<br/>

DFS인줄 알았더니 DP였던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

#define MAX 1001

using namespace std;

int N, M;
int Map[MAX][MAX];
int DP[MAX][MAX];

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;
    
    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= M; ++j)
            cin >> Map[i][j];
    }

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= M; ++j)
        {
            int Add = m_max(DP[i-1][j], m_max(DP[i][j-1], DP[i-1][j-1]));
            DP[i][j] = Add + Map[i][j];
        }
    }

    cout << DP[N][M];

    return 0;
}
```

<br/>

## 오답제출코드 (시간초과)<br/>
<br/>

```
#include <iostream>

#define MAX 1001

using namespace std;

int N, M;
int Map[MAX][MAX];
int Max = 0;

int dx[3] = {1, 0, 1};
int dy[3] = {0, 1, 1};

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void DFS(int x, int y, int count)
{
    if (!(1 <= x && x <= M && 1 <= y && y <= N))
    {
        Max = m_max(Max, count);
        return;
    }

    for (int i = 0; i < 3; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        DFS(Nx, Ny, count + Map[y][x]);
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;
    
    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= M; ++j)
            cin >> Map[i][j];
    }

    DFS(1, 1, 0);

    cout << Max;

    return 0;
}
```

처음엔 DFS로 풀 수 있겠다 생각했다.<br/>

그래서 위와 같은 코드로 제출을 했는데 시간초과가 났다.<br/>

그러고 나서 생각했다. 아! DP구나.<br/>

그래서 정답제출코드로 변환하였다.<br/>

DFS로 풀 때 이상했던 점이 visited를 굳이 안써도 됐고,<br/>

종료 시점을 어떻게 정해야할지 애매했던 것이었는데 DFS가 아니라는 신호였나보다.<br/>

DP에 대한 감이 많이 줄었다. 문제를 다시 많이 풀어야겠다.<br/>