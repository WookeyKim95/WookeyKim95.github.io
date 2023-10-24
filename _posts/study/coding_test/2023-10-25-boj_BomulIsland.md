---
layout: post
title:  "[백준] 2589_보물섬 C++"
subtitle:   
date: 2023-10-25 06:59:50 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2589_보물섬 C++<br/>
<br/>

오랜만에 풀어본 BFS 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <vector>
#include <cstring>
#include <queue>

using namespace std;

int N, M;
vector<string> Map;
int Dist[51][51];
bool visited[51][51];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

int MaxDist = 0;

void BFS(int a, int b)
{
    memset(visited, false, sizeof(visited));
    memset(Dist, 0, sizeof(Dist));

    queue<pair<int, int>> q;

    q.push({a, b});
    visited[b][a] = true;

    while (!q.empty())
    {
        int CurX = q.front().first;
        int CurY = q.front().second;
        int CurD = Dist[CurY][CurX];
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int Nx = CurX + dx[i];
            int Ny = CurY + dy[i];

            if (!(0 <= Nx && Nx < M && 0 <= Ny && Ny < N))
                continue;
            
            if (!visited[Ny][Nx] && Map[Ny][Nx] == 'L')
            {
                visited[Ny][Nx] = true;
                q.push({Nx, Ny});
                Dist[Ny][Nx] = CurD + 1;
            }
        }
    }

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            if (visited[i][j] && Dist[i][j] > MaxDist)
                MaxDist = Dist[i][j];
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
        string row;
        cin >> row;

        Map.push_back(row);
    }

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            if (Map[i][j] == 'L')
                BFS(j, i);
        }
    }

    cout << MaxDist;

    return 0;
}
```

문제를 읽고 BFS임을 직감했지만, 보물이 어디에 묻혀있는거라는거지? 생각했다가<br/>

문제를 다시 읽고, 가장 먼 거리에 있는 두 지점에 묻혀있다고 나와있었다.<br/>

아하! 브루트포스를 써야하는 것이다!<br/>

시간초과가 나지 않을까 걱정했지만, 다행히 범위가 가로와 세로가 최대 50으로 작았다.<br/>

그래서 1초안에 해결이 가능할 것이라 생각했다.<br/>

그리고 제출 결과, 140ms가 나오면서 정답처리 되었다.<br/>

방식은 간단하다.<br/>

2중 for문을 통해서 L인 시작점에서 일일이 돌아주면서<br/>

거리를 측정하고, 가장 긴 거리보다 큰 값이 나오면 갱신해준다.