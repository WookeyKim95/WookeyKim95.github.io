---
layout: post
title:  "[백준] 2146_다리 만들기 C++"
subtitle:   
date: 2023-07-29 07:50:23 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2146_다리 만들기 C++<br/>
<br/>

BFS를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <cstring>
#include <queue>

using namespace std;

int N;
int map[100][100];
bool visited[100][100];
int land[100][100];
int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

void BFS_Land(int Sx, int Sy, int num)
{
    queue<pair<int, int>> q;

    q.push({Sx, Sy});
    visited[Sy][Sx] = true;
    land[Sy][Sx] = num;

    while (!q.empty())
    {
        int CurX = q.front().first;
        int CurY = q.front().second;
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int Nx = CurX + dx[i];
            int Ny = CurY + dy[i];

            if (0 <= Nx && Nx < N && 0 <= Ny && Ny < N)
            {
                if (!visited[Ny][Nx] && map[Ny][Nx] == 1)
                {
                    visited[Ny][Nx] = true;
                    land[Ny][Nx] = num;
                    q.push({Nx, Ny});
                }
            }
        }
    }
}

int BFS_Bridge(int num)
{
    queue<pair<int, int>> q;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            if (land[i][j] == num)
                q.push({j, i});
        }
    }

    int Bridge = 0;

    while (!q.empty())
    {
        int L = q.size();
        for (int i = 0; i < L; ++i)
        {
            int CurX = q.front().first;
            int CurY = q.front().second;
            q.pop();

            for (int j = 0; j < 4; ++j)
            {
                int Nx = CurX + dx[j];
                int Ny = CurY + dy[j];

                if (0 <= Nx && Nx < N && 0 <= Ny && Ny < N)
                {
                    if (map[Ny][Nx] == 1 && land[Ny][Nx] != num)
                        return Bridge;

                    else if (!visited[Ny][Nx] && map[Ny][Nx] == 0)
                    {
                        visited[Ny][Nx] = true;
                        q.push({Nx, Ny});
                    }
                }
            }
        }
        Bridge++;
    }
    return Bridge;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
            cin >> map[i][j];
    }

    int ans = 99999999;
    int land_num = 0;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            if (map[i][j] == 1 && !visited[i][j])
            {
                land_num++;
                BFS_Land(j, i, land_num);
            }
        }
    }

    memset(visited, false, sizeof(visited));

    for (int i = 1; i < land_num; ++i)
    {
        ans = m_min(ans, BFS_Bridge(i));
        memset(visited, false, sizeof(visited));
    }

    cout << ans;

    return 0;
}
```

예전엔 땅따먹기류의 문제를 잘 풀었던 것 같은데<br/>

오랜 시간 안풀어서 그런지 좀 녹슬은 것 같다.<br/>

flag를 사용할까 하다가 아이디어가 안떠올라서 [참고링크](https://yabmoons.tistory.com/71)를 참고하여 문제를 풀었다.<br/>

이분의 풀이에 따르면 대륙 별로 번호를 먹인 다음에<br/>

번호가 다른 대륙까지 거리를 구하는 형식이었다.<br/>

내가 제일 고민이었던 점은 시작점을 어떻게 정하는 것이었나 였는데<br/>

대륙의 번호를 비교하면서, 그리고 큐에다가 같은 번호의 땅을 미리 담으면서 해결이 가능하였다.<br/>

그렇게 시작점을 정하고 난 뒤 bfs를 활용해서 문제를 풀었다.<br/>

다시 서술하면 과정은 크게 아래와 같다.<br/>

1. 대륙 별로 번호를 책정한다.
2. 서로 다른 대륙을 만나면 다리의 길이를 반환시킨다.
3. 기존의 다리 길이와 비교를 한다.