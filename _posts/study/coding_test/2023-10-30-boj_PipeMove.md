---
layout: post
title:  "[백준] 17070_파이프 옮기기 1 C++"
subtitle:   
date: 2023-10-30 06:39:44 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 17070_파이프 옮기기 1 C++<br/>
<br/>

완전탐색을 활용해볼 수 있는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstring>

using namespace std;

int N;
int Map[17][17];
int dx[3] = {1, 0, 1};
int dy[3] = {0, 1, 1};
int ans = 0;

bool check(int x, int y)
{
    if (!(1 <= x && x <= N && 1 <= y && y <= N) || Map[y][x] == 1)
        return false;
    else
        return true;
}

void Solve(int x, int y, int dir)
{
    if (x == N && y == N)
    {
        ans++;
        return;
    }

    for (int i = 0; i < 3; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        if((dir == 0 && i == 1) || (dir == 1 && i == 0))
            continue;

        if (!check(Nx, Ny))
            continue;
        
        if (i == 2 && (Map[y+1][x] == 1 || Map[y][x+1] == 1))
            continue;
        
        Solve(Nx, Ny, i);
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
            cin >> Map[i][j];
    }

    Solve(2, 1, 0);

    cout << ans;
}
```

처음엔 DP를 이용하려고 하다가 시간 때문에 나중에 풀어보기로 하고 완전탐색으로 선회하였다.<br/>

DFS를 이용해서 완전탐색으로 풀 수 있었다.<br/>

<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstring>

using namespace std;

int N;
int Map[17][17];
int dx[3] = {0, 1, 1};
int dy[3] = {1, 0, 1};
int DP[17][17];

bool check(int x, int y)
{
    if (!(1 <= x && x <= N && 1 <= y && y <= N) || Map[y][x] == 1)
        return false;
    else
        return true;
}

int Solve(int x, int y, int dir)
{
    if (x == N && y == N)
        return 1;
    else if (DP[y][x] != -1)
        return DP[y][x];
    
    DP[y][x] = 0;

    for (int i = 0; i < 3; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        if (!check(Nx, Ny))
            continue;
        
        if (i == 2 && (Map[y+1][x] == 1 || Map[y][x+1] == 1))
            continue;
        
        DP[y][x] = Solve(Nx, Ny, i) + DP[y][x];
    }

    return DP[y][x];
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
            cin >> Map[i][j];
    }

    memset(DP, -1, sizeof(DP));

    int Ans = Solve(1, 2, 0);

    cout << Ans;
}
```

처음엔 DP를 이용해서 풀어보려고 했지만<br/>

뜻대로 되지 않았다. 왜냐하면 for문을 돌면서<br/>

같은 지점에 다시 한번 방문하게 되는데<br/>

이 과정에서 if(DP[y][x] != -1)에 걸려서 값을 또 반환하게 되는 것이다.<br/>

이를 어떻게 고치면 DP로 풀 수 있을지 생각해봐야겠다.<br/>

시간이 없어서 완전탐색으로 선회했다.<br/>