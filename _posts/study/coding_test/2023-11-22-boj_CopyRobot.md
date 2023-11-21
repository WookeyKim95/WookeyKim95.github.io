---
layout: post
title:  "[백준] 1944_복제 로봇 C++"
subtitle:   
date: 2023-11-22 06:32:19 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1944_복제 로봇 C++<br/>
<br/>

BFS와 MST를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <cstring>
#include <queue>

using namespace std;

typedef pair<int, pair<int, int>> piii;

int N, M;
vector<string> Map;
bool visited[51][51];
int Num[51][51];
int Parent[252];
int Dist[251][51][51];
int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
vector<piii> Edges;
vector<pair<int, int>> Coord;

int FindParent(int a)
{
    if (Parent[a] == a)
        return a;
    else
        return Parent[a] = FindParent(Parent[a]);
}

void BFS(int y, int x, int count)
{
    queue<piii> q;
    q.push({0, {x, y}});
    visited[y][x] = true;

    while(!q.empty())
    {
        int CurX = q.front().second.first;
        int CurY = q.front().second.second;
        int CurDist = q.front().first;
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int Nx = CurX + dx[i];
            int Ny = CurY + dy[i];

            if (visited[Ny][Nx])
                continue;
            
            if (Map[Ny][Nx] != '1')
            {
                q.push({CurDist + 1, {Nx, Ny}});
                visited[Ny][Nx] = true;
                
                int NextNum = Num[Ny][Nx];
                if (NextNum != 0)
                    Edges.push_back({CurDist+1, {count, NextNum}});
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
        string row;
        cin >> row;
        Map.push_back(row);
    }

    memset(Num, 0, sizeof(Num));
    int count = 1;
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            if (Map[i][j] == 'S' || Map[i][j] == 'K')
            {
                Coord.push_back({i, j});
                Num[i][j] = count;
                count++;
            }
        }
    }

    for (size_t i = 0; i < Coord.size(); ++i)
    {
        memset(visited, false, sizeof(visited));
        BFS(Coord[i].first, Coord[i].second, i+1);

        if (i == 0)
        {
            for (size_t i = 1; i < Coord.size(); ++i)
            {
                if (!visited[Coord[i].first][Coord[i].second])
                {
                    cout << -1;
                    return 0;
                }
            }
        }
    }
    
    for (int i = 1; i <= M; ++i)
        Parent[i] = i;

    sort(Edges.begin(), Edges.end());

    int Sum = 0;
    for (size_t i = 0; i < Edges.size(); ++i)
    {
        int C = Edges[i].first;
        int S = Edges[i].second.first;
        int E = Edges[i].second.second;

        int A = FindParent(S);
        int B = FindParent(E);

        if (A != B)
        {
            Parent[A] = B;
            Sum += C;
        }
    }

    cout << Sum;

    return 0;
}
```
<br/>

MST와 BFS를 응용해보는 문제이다.<br/>

이 문제를 풀면서 어려웠던 점은<br/>

각 S와 K가 있는 지점을 어떻게 Edges에 넣을 수 있을까 생각해보는 과정이었다.<br/>

나의 해결방법은 이랬다.<br/>

- S와 K에다가 Num을 부여한다.
- 각 Num에 Parent를 부여한다.
- BFS를 이용해서 S와 K 끼리의 거리를 구한다.
- 구한 각각의 간선을 Edges에 저장한다.
- MST를 적용한다.

```
int count = 1;
for (int i = 0; i < N; ++i)
{
    for (int j = 0; j < N; ++j)
    {
        if (Map[i][j] == 'S' || Map[i][j] == 'K')
        {
            Coord.push_back({i, j});
            Num[i][j] = count;
            count++;
        }
    }
}
```

이런 코드가 있는데, 각각의 S와 K에 넘버를 부여하는 과정이다.<br/>

이 넘버가 있어야 Parent를 적용해서 크루스칼 알고리즘을 적용할 수 있다고 생각했다.<br/>

그리고 각 S와 K의 좌표를 출발점으로 지정해서 BFS를 돌려주면 된다.<br/>

그리고 Edge들을 구해서 크루스칼 알고리즘을 적용하면 끝.

```
for (size_t i = 0; i < Coord.size(); ++i)
{
    memset(visited, false, sizeof(visited));
    BFS(Coord[i].first, Coord[i].second, i+1);

    if (i == 0)
    {
        for (size_t i = 1; i < Coord.size(); ++i)
        {
            if (!visited[Coord[i].first][Coord[i].second])
            {
                cout << -1;
                return 0;
            }
        }
    }
}
```

그리고 저기 if 코드는 맨 처음에 S지점에서 BFS를 했더니 못 찾는 K가 있다면<br/>

볼 것도 없이 바로 -1을 리턴하기 위한 예외처리 코드이다.<br/>