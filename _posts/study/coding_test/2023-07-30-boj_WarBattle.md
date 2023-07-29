---
layout: post
title:  "[백준] 1303_전쟁 - 전투 C++"
subtitle:   
date: 2023-07-30 07:10:50 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1303_전쟁 - 전투 C++<br/>
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

using namespace std;

int N, M;
char map[100][100];
bool visited[100][100];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

int BFS(int Sx, int Sy, char a)
{
    queue<pair<int, int>> q;
    q.push({Sx, Sy});
    visited[Sy][Sx] = true;
    
    int count = 0;

    while (!q.empty())
    {
        int Cx = q.front().first;
        int Cy = q.front().second;
        count++;
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int Nx = Cx + dx[i];
            int Ny = Cy + dy[i];

            if (0 <= Nx && Nx < N && 0 <= Ny && Ny < M)
            {
                if (!visited[Ny][Nx] && map[Ny][Nx] == a)
                {
                    visited[Ny][Nx] = true;
                    q.push({Nx, Ny});
                }
            }
        }
    }

    return count * count;
}

int main()
{
    cin >> N >> M;
    memset(visited, false, sizeof(visited));

    for (int i = 0; i < M; ++i)
    {
        for (int j = 0; j < N; ++j)
            cin >> map[i][j];
    }

    int B = 0, W = 0;

    for (int i = 0; i < M; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            if (!visited[i][j])
            {
                if (map[i][j] == 'W')
                    W += BFS(j, i, 'W');
                else if (map[i][j] == 'B')
                    B += BFS(j, i, 'B');
            }
        }
    }

    cout << W << ' ' << B;

    return 0;
}
```

잠시 쉬어가는 차원에서 기본적인 BFS, DFS 문제를 풀어보았다.<br/>

DFS를 활용할 수도있고, BFS를 활용할 수도있는 문제였는데<br/>

난 BFS를 활용하기로 했다.<br/>

구성은 아래와 같다.

- 맵을 입력받는다.
- 각 자리마다 for문을 돌면서 방문하지 않은 곳이라면 시작점으로 선정해서 BFS를 진행한다.
- BFS를 돌면서 몇 칸인지 세주고, 칸의 개수 ^ 2를 반환받는다.
- 각 문자에 해당하는 값에 더한다.

자주하는 실수인데,<br/>

BFS를 시작하면서 시작점을 큐에 넣을 때 시작점을 방문처리를 해주지 않는 실수를 자주 범하는 것 같다.<br/>

이것 때문에 10분을 날려먹은듯 ㅎㅎ;;<br/>

조심해야할텐데 말이지.<br/>