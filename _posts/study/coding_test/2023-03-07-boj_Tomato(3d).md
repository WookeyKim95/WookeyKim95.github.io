---
layout: post
title:  "[백준] 7569번_토마토 C++"
subtitle:   
date: 2023-03-07 06:24:32 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 7569번_토마토 C++<br/>
<br/>

큐와 bfs를 활용하는 문제<br/>

7576번 토마토의 3차원 버전<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

int N, M, H;
int box[100][100][100];

int dx[6] = {-1, 1, 0, 0, 0, 0};
int dy[6] = {0, 0, -1, 1, 0, 0};
int dz[6] = {0, 0, 0, 0, -1, 1};

// 안익은 토마토가 있는지 확인하는 함수
bool isRare()
{
    for (int k = 0; k < H; ++k)
    {
        for (int i = 0; i < M; ++i)
        {
            for (int j = 0; j < N; ++j)
            {
                // 0이 하나라도 있다면 다 익지 않은 것.
                if (box[k][i][j] == 0)
                    return true;
            }
        }
    }
    
    return false;
}

int main()
{
    cin >> N >> M >> H;

    // 박스에 토마토를 담음.
    for (int k = 0; k < H; ++k)
    {
        for (int i = 0; i < M; ++i)
        {
            for (int j = 0; j < N; ++j)
            {
                int _input;
                cin >> _input;

                box[k][i][j] = _input;
            }
        }
    }    

    int days = 0;

    if (!isRare())
    {
        // 처음부터 모두 익었을 경우 0을 출력하고 함수를 종료한다.
        cout << 0;
        return 0;
    }

    // 날짜별로 좌표 방문을 기록할 큐
    queue<queue<vector<int>>> q;

    // 첫날, 맨 처음 토마토가 익은 곳에서 방문 개시.
    queue<vector<int>> CoordinatePerDay;

    for (int k = 0; k < H; ++k)
    {
        for (int i = 0; i < M; ++i)
        {
            for (int j = 0; j < N; ++j)
            {
                if (box[k][i][j] == 1)
                    CoordinatePerDay.push({i, j, k});
            }
        }
    }
    

    q.push(CoordinatePerDay);

    while (!q.empty())
    {
        // 날짜별로 다음날 방문할 좌표 기록
        CoordinatePerDay = {};

        queue<vector<int>> nowday = q.front();
        q.pop();

        while(!nowday.empty())
        {
            int x = nowday.front()[1];
            int y = nowday.front()[0];
            int z = nowday.front()[2];

            nowday.pop();

            for (int k = 0; k < 6; ++k)
            {
                int new_x = x + dx[k];
                int new_y = y + dy[k];
                int new_z = z + dz[k];

                // segmentation false 방지
                if (0 <= new_x && new_x < N && 0 <= new_y && new_y < M && 0 <= new_z && new_z < H)
                {
                    if (box[new_z][new_y][new_x] == 0)
                    {
                        // 토마토가 아직 익지 않은 경우 익은것으로 반환
                        box[new_z][new_y][new_x] = 1;  

                        // 다음날 방문할 노드 기록
                        CoordinatePerDay.push({new_y, new_x, new_z}); 
                    }
                }
            }
        }

        // 다음날도 익을 토마토가 있을 경우 큐에 넣고 하루를 추가한다.
        if (!CoordinatePerDay.empty())
        {
            q.push(CoordinatePerDay);
            days++;
        }
    }

    if (isRare())
    {
        // bfs를 모두 완료하고 나서 안익은 토마토가 있을 경우 -1을 출력한다.
        cout << -1;
    }
    else
        cout << days;

    return 0;
}
```
<br/>

int형을 담는 벡터, 그리고 그를 담는 큐, 그리고 그 큐를 또 담는 큐를 활용해서 구현하였다.<br/>

날짜별로 어떤 좌표를 방문해야하는지 기록을 할 필요가 있었기 때문에 벡터와 이중 큐를 사용하였다.<br/>

아쉬운 점은 이중큐를 사용해서 그런지 메모리를 많이 잡아먹는것 같다.<br/>

그리고 3차원이기 떄문에 dx, dy에 이어 dz를 추가하였고,

```
for (int k = 0; k < 6; ++k)
{
    int new_x = x + dx[k];
    int new_y = y + dy[k];
    int new_z = z + dz[k];

    // segmentation false 방지
    if (0 <= new_x && new_x < N && 0 <= new_y && new_y < M && 0 <= new_z && new_z < H)
    {
        if (box[new_z][new_y][new_x] == 0)
        {
            // 토마토가 아직 익지 않은 경우 익은것으로 반환
            box[new_z][new_y][new_x] = 1;  

            // 다음날 방문할 노드 기록
            CoordinatePerDay.push({new_y, new_x, new_z}); 
        }
    }
}

```

bfs의 for문도 다음과 같이 만들었으며, 큐에는 한번에 3개의 인자(x, y, z)를 담도록 조치하였다.<br/>