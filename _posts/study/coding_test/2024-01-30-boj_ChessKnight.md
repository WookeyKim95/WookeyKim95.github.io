---
layout: post
title:  "[백준] 7562_나이트의 이동 C++"
subtitle:   
date: 2024-01-30 06:32:45 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 7562_나이트의 이동 C++<br/>
<br/>

BFS를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <queue>
#include <cstring>

#define MAX 301

using namespace std;

int N;
int Map[MAX][MAX];
bool visited[MAX][MAX];
int Sx, Sy, Fx, Fy;

int dx[8] = {-2, -1, 1, 2, 2, 1, -1, -2};
int dy[8] = {-1, -2, -2, -1, 1, 2, 2, 1};

void init()
{
    cin >> N;
    cin >> Sx >> Sy;
    cin >> Fx >> Fy;
    memset(visited, false, sizeof(visited));
}

int BFS(int Startx, int Starty)
{
    queue<pair<int, pair<int, int>>> q;
    q.push({0, {Startx, Starty}});
    visited[Starty][Startx] = true;

    while (!q.empty())
    {
        int CurX = q.front().second.first;
        int CurY = q.front().second.second;
        int CurCount = q.front().first;
        q.pop();

        if (CurX == Fx && CurY == Fy)
            return CurCount;

        for (int i = 0; i < 8; ++i)
        {
            int Nx = CurX + dx[i];
            int Ny = CurY + dy[i];

            if (!(0 <= Nx && Nx < N && 0 <= Ny && Ny < N))
                continue;
            
            if (!visited[Ny][Nx])
            {
                visited[Ny][Nx] = true;
                q.push({CurCount+1, {Nx, Ny}});
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int tc;
    cin >> tc;

    for (int i = 0; i < tc; ++i)
    {
        init();
        cout << BFS(Sx, Sy) << '\n';
    }

    return 0;
}
```

<br/>

BFS를 까먹지 않기 위해서 몸풀기 용으로 풀었다.<br/>

dx, dy를 통해서 나이트가 다음에 이동할 칸을 정해주었고,<br/>

init() 함수에서 초기화 과정을 거쳤다.<br/>

그러고나서 BFS를 통해서 구해주면 된다.<br/>

한 가지 우려했던 점은 나이트가 영원히 도달하지 못하는 경우가 나올까?<br/>

생각했지만 목적지에 다다를 때 까지 계속 무한루프로 돌고, 방문하지 않은 곳은 다시 방문하지 않기 때문에<br/>

결국에는 모든 영역에 방문을 하게 되므로 영원히 도달하지 못하는 경우는 없다고 보면 되겠다.