---
layout: post
title:  "[백준] 4963_섬의 개수 C++"
subtitle:   
date: 2023-12-23 07:15:54 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 4963_섬의 개수 C++<br/>
<br/>

기초적인 BFS, 플러드 필 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <cstring>

using namespace std;

vector<vector<int>> Map;
bool visited[50][50];
int dx[8] = {-1, 1, 0, 0, -1, -1, 1, 1};
int dy[8] = {0, 0, -1, 1, -1, 1, -1, 1};
int W, H;

void BFS(int x, int y)
{
    queue<pair<int, int>> q;
    q.push({x, y});
    visited[y][x] = true;

    while (!q.empty())
    {
        int CurX = q.front().first;
        int CurY = q.front().second;
        q.pop();

        for (int i = 0; i < 8; ++i)
        {
            int Nx = CurX + dx[i];
            int Ny = CurY + dy[i];

            if (!(0 <= Nx && Nx < W && 0 <= Ny && Ny < H))
                continue;
            
            if (!visited[Ny][Nx] && Map[Ny][Nx] == 1)
            {
                visited[Ny][Nx] = true;
                q.push({Nx, Ny});
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    while (true)
    {
        cin >> W >> H;
        if (W == 0 && H == 0)
            break;

        memset(visited, false, sizeof(visited));
        int Count = 0;
        Map.clear();

        for (int i = 0; i < H; ++i)
        {
            vector<int> row;
            for (int j = 0; j < W; ++j)
            {
                int _input;
                cin >> _input;
                row.push_back(_input);
            }
            Map.push_back(row);
        }

        for (int i = 0; i < H; ++i)
        {
            for (int j = 0; j < W; ++j)
            {
                if (!visited[i][j] && Map[i][j] == 1)
                {
                    BFS(j, i);
                    Count++;
                }
            }
        }

        cout << Count << '\n';

    }
    return 0;
}
```

기초적인 플러드필 문제이다.<br/>

플러드필은, 서로 붙어있는 그룹이 몇개인지를 세는 알고리즘으로,<br/>

BFS를 통해서 구현할 수 있다.<br/>

방문하지 않은, 땅인 곳에서 BFS를 시작한다음에 BFS를 하면서 방문처리를 해주고 BFS를 마치면<br/>

카운트를 증가하는 식으로 구현하였다.<br/>