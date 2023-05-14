---
layout: post
title:  "[백준] 1937_욕심쟁이 판다 C++"
subtitle:   
date: 2023-05-14 09:14:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1937_욕심쟁이 판다 C++<br/>
<br/>

DFS와 다이나믹 프로그래밍 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>

using namespace std;

int N;
int map[500][500];
int DP[500][500];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0 ,0, -1, 1};

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int dfs(int x, int y)
{
    if (DP[y][x] != 0)
        return DP[y][x];
    
    DP[y][x] = 1;

    for (int i = 0; i < 4; ++i)
    {
        int nx = x + dx[i];
        int ny = y + dy[i];

        if (0 <= nx && nx < N && 0 <= ny && ny < N)
        {
            if (map[y][x] < map[ny][nx])
                DP[y][x] = m_max(DP[y][x], dfs(nx, ny) + 1);
        }
    }
    return DP[y][x];
}

int main()
{
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
            cin >> map[i][j];
    }

    memset(DP, 0, sizeof(DP));

    int ans = 0;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
            ans = m_max(dfs(j, i), ans);
    }

    cout << ans;

    return 0;
}
```

<br/>

모든 지점에서 DFS 탐색을 해야했기에 시간초과가 나지 않을까 걱정했다.<br/>

하지만 다이나믹 프로그래밍을 사용해서 그런지 시간초과는 나지 않았다.<br/>

기본적인 DFS + 다이나믹 프로그래밍 코드에다가 조건을 추가하였다.<br/>

```
int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int dfs(int x, int y)
{
    if (DP[y][x] != 0)
        return DP[y][x];
    
    DP[y][x] = 1;

    for (int i = 0; i < 4; ++i)
    {
        int nx = x + dx[i];
        int ny = y + dy[i];

        if (0 <= nx && nx < N && 0 <= ny && ny < N)
        {
            if (map[y][x] <= map[ny][nx])
                DP[y][x] = m_max(DP[y][x], dfs(nx, ny) + 1);
        }
    }
    return DP[y][x];
}
```

- 첫 방문일 경우 DP 값을 1로 지정
- 처음 방문하는 지점이 아닌경우, 즉 DP값이 0이 아닌 경우 이미 갖고 있는 DP값 반환
- 범위 안에 들어가있으며, 이전 지점보다 다음 지점의 값이 클 경우에 DP값 갱신

DFS를 이용한 다이나믹 프로그래밍에 대한 틀이 이제 잡혀가는 것 같다.<br/>

조금만 더 연습하면 될 것 같다.<br/>