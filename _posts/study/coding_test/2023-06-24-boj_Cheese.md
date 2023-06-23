---
layout: post
title:  "[백준] 2638_치즈 C++"
subtitle:   
date: 2023-06-14 08:38:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2638_치즈 C++<br/>
<br/>

BFS를 사용해보는 시뮬레이션 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>
#include <cstring>

using namespace std;

int N, M;
vector<vector<int>> map;
bool visited[100][100];
bool OutSide[100][100];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

bool IsCheese()
{
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            if (map[i][j] == 1)
                return true;
        }
    }
    return false;
}

void BFSAir()
{
    queue<pair<int, int>> q;
    q.push({0, 0});
    visited[0][0] = true;
    OutSide[0][0] = true;

    while (!q.empty())
    {
        int CurX = q.front().second;
        int CurY = q.front().first;
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int NewX = CurX + dx[i];
            int NewY = CurY + dy[i];

            if (NewX < 0 || NewX >= M || NewY < 0 || NewY >= N)
                continue;

            if (map[NewY][NewX] == 0 && !visited[NewY][NewX])
            {
                q.push({NewY, NewX});
                visited[NewY][NewX] = true;
                OutSide[NewY][NewX] = true;
            }
        }
    }
}

void BFS()
{
    queue<pair<int, int>> q;
    queue<pair<int, int>> Melt;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            if (map[i][j] == 1)
            {
                q.push({i, j});
                visited[i][j] = true;
            }
        }
    }

    while (!q.empty())
    {
        int CurX = q.front().second;
        int CurY = q.front().first;
        q.pop();

        int AirCount = 0;
        for (int i = 0; i < 4; ++i)
        {
            int NewX = CurX + dx[i];
            int NewY = CurY + dy[i];

            if (NewX < 0 || NewX >= M || NewY < 0 || NewY >= N)
                continue;

            if (map[NewY][NewX] == 0 && OutSide[NewY][NewX])
                AirCount++;
        }

        if (AirCount >= 2)
            Melt.push({CurY, CurX});
    }

    while (!Melt.empty())
    {
        int X = Melt.front().second;
        int Y = Melt.front().first;
        Melt.pop();

        map[Y][X] = 0;
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
        vector<int> row;
        for (int j = 0; j < M; ++j)
        {
            int _input;
            cin >> _input;

            row.push_back(_input);
        }
        map.push_back(row);
    }

    int ans = 0;

    while (IsCheese())
    {
        memset(visited, false, sizeof(visited));
        memset(OutSide, false, sizeof(OutSide));
        BFSAir();
        BFS();
        ans++;
    }

    cout << ans;

    return 0;
}
```
<br/>

BFS를 이용해서 풀어보기로 생각하였다.<br/>

우선 공기가 안쪽의 공기인지 바깥쪽의 공기인지 어떻게 구분을 할지 고민을 했는데<br/>

다행히 N과 M의 범위가 5 이상 100 이하이고, 가장자리에는 치즈가 놓이지 않는다는 조건이 있었다.<br/>

그래서 BFS를 이용해 바깥 공기가 어디인지를 조사하는 로직을 구현하였다.<br/>

```
bool OutSide[100][100];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

...

void BFSAir()
{
    queue<pair<int, int>> q;
    q.push({0, 0});
    visited[0][0] = true;
    OutSide[0][0] = true;

    while (!q.empty())
    {
        int CurX = q.front().second;
        int CurY = q.front().first;
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int NewX = CurX + dx[i];
            int NewY = CurY + dy[i];

            if (NewX < 0 || NewX >= M || NewY < 0 || NewY >= N)
                continue;

            if (map[NewY][NewX] == 0 && !visited[NewY][NewX])
            {
                q.push({NewY, NewX});
                visited[NewY][NewX] = true;
                OutSide[NewY][NewX] = true;
            }
        }
    }
}

...

memset(OutSide, false, sizeof(OutSide));
BFSAir();
```

이 함수를 통해 바깥 공기가 어디인지를 먼저 체크한다.<br/>

그리고 다음 BFS 함수를 통해서 녹아야할 치즈를 선별한다.<br/>

```
void BFS()
{
    queue<pair<int, int>> q;
    queue<pair<int, int>> Melt;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            if (map[i][j] == 1)
            {
                q.push({i, j});
                visited[i][j] = true;
            }
        }
    }

    while (!q.empty())
    {
        int CurX = q.front().second;
        int CurY = q.front().first;
        q.pop();

        int AirCount = 0;
        for (int i = 0; i < 4; ++i)
        {
            int NewX = CurX + dx[i];
            int NewY = CurY + dy[i];

            if (NewX < 0 || NewX >= M || NewY < 0 || NewY >= N)
                continue;

            if (map[NewY][NewX] == 0 && OutSide[NewY][NewX])
                AirCount++;
        }

        if (AirCount >= 2)
            Melt.push({CurY, CurX});
    }

    while (!Melt.empty())
    {
        int X = Melt.front().second;
        int Y = Melt.front().first;
        Melt.pop();

        map[Y][X] = 0;
    }
}
```

한꺼번에 녹아야 하기 때문에 Melt라는 큐에다가 담아준 다음에<br/>

Melt 큐에 있는 치즈가 녹도록 구현하였다.<br/>

그리고 이 과정을 치즈가 없어질 때 까지 반복한다.<br/>

```
while (IsCheese())
{
    memset(visited, false, sizeof(visited));
    memset(OutSide, false, sizeof(OutSide));
    BFSAir();
    BFS();
    ans++;
}
```

시간 초과를 걱정했지만 다행히 N과 M의 값의 범위가 적어서 이 방법을 사용하였다.<br/>