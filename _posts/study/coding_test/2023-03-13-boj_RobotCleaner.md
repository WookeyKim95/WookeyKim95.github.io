---
layout: post
title:  "[백준] 14503번_로봇 청소기 C++"
subtitle:   
date: 2023-03-13 09:06:37 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 14503번_로봇 청소기 C++<br/>
<br/>

문제에 쓰여있는 조건을 그대로 구현하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>

using namespace std;

int N, M;
int dir = 0;
int map[50][50];
int Robot[2]; //index 0 : 세로좌표 index 1 : 가로좌표
bool cleared[50][50] = {false};

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

// 주변 4칸에 청소하지 않은 칸이 있는지 검사하는 함수
bool isNotClearNearby(int y, int x)
{
    for (int i = 0; i < 4; ++i)
    {
        int new_x = x + dx[i];
        int new_y = y + dy[i];

        if (0 <= new_x && new_x < M && 0 <= new_y && new_y < N)
        {
            if (!cleared[new_y][new_x])
                return true;
        }
    }
    return false;
}

int main()
{
    // 초기화
    memset(cleared, false, sizeof(cleared));

    // 입력받기
    cin >> N >> M;
    cin >> Robot[0] >> Robot[1] >> dir;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            int _input = 0;
            cin >> _input;

            map[i][j] = _input;

            // 벽이 있는 곳은 청소했다고 취급하기
            if (_input == 1)
                cleared[i][j] = true;
        }
    }

    int count = 0;

    while (true)
    {
        // 현재 칸이 청소 안되어 있을 경우 청소
        if (!cleared[Robot[0]][Robot[1]])
        {
            cleared[Robot[0]][Robot[1]] = true;
            count++;
        }
            

        // 북쪽을 바라보며
        if (dir == 0)
        {
            // 청소 안된 곳이 있을 경우 회전
            if (isNotClearNearby(Robot[0], Robot[1]))
            {
                dir = 3; // 서쪽으로 회전

                // 앞에 있는 칸이 청소가 안됐을 경우 전진
                if (0 <= Robot[1]-1 && !cleared[Robot[0]][Robot[1]-1])
                    Robot[1] -= 1;
            }
            
            else
            {
                // 모두 청소된경우 후진을 위해(남쪽으로)
                if (N > Robot[0]+1 && map[Robot[0]+1][Robot[1]] == 0)
                    Robot[0] += 1;
                    
                else
                    break;
            }
        }

        // 동쪽을 바라보며
        else if (dir == 1)
        {
            // 청소 안된 곳이 있을 경우 회전
            if (isNotClearNearby(Robot[0], Robot[1]))
            {
                dir = 0; // 북쪽으로 회전

                // 앞에 있는 칸이 청소가 안됐을 경우 전진
                if (0 <= Robot[0]-1 && !cleared[Robot[0]-1][Robot[1]])
                    Robot[0] -= 1;
            }
            
            else
            {
                // 모두 청소된경우 후진을 위해(서쪽으로)
                if (0 <= Robot[1]-1 && map[Robot[0]][Robot[1]-1] == 0)
                    Robot[1] -= 1;
                    
                else
                    break;
            }
        }

        // 남쪽을 바라보며
        else if (dir == 2)
        {
            // 청소 안된 곳이 있을 경우 회전
            if (isNotClearNearby(Robot[0], Robot[1]))
            {
                dir = 1; // 동쪽으로 회전

                // 앞에 있는 칸이 청소가 안됐을 경우 전진
                if (M > Robot[1]+1 && !cleared[Robot[0]][Robot[1]+1])
                    Robot[1] += 1;
            }
            
            else
            {
                // 모두 청소된경우 후진을 위해(북쪽으로)
                if (0 <= Robot[0]-1 && map[Robot[0]-1][Robot[1]] == 0)
                    Robot[0] -= 1;
                    
                else
                    break;
            }
        }

        // 서쪽을 바라보며
        else if (dir == 3)
        {
            // 청소 안된 곳이 있을 경우 회전
            if (isNotClearNearby(Robot[0], Robot[1]))
            {
                dir = 2; // 남쪽으로 회전

                // 앞에 있는 칸이 청소가 안됐을 경우 전진
                if (N > Robot[0]+1 && !cleared[Robot[0]+1][Robot[1]])
                    Robot[0] += 1;
            }
            
            else
            {
                // 모두 청소된경우 후진을 위해(동쪽으로)
                if (M > Robot[1]+1 && map[Robot[0]][Robot[1]+1] == 0)
                    Robot[1] += 1;
                    
                else
                    break;
            }
        }            
        // 검증용
        // cout << Robot[0] << ' ' << Robot[1] << ' ' << dir << '\n';
    }

    cout << count;

    return 0;
}
```
<br/>

문제에 쓰여있는 조건을 그대로 구현해야한다.<br/>

순서도 똑같이<br/>

```
// 1. 현재 칸이 청소되지 않은 경우 청소를 한다.

if (!cleared[Robot[0]][Robot[1]])
{
    cleared[Robot[0]][Robot[1]] = true;
    count++;
}
```

```
// 2. 주변 4칸이 청소되어 있지 않은 칸이 있는지 검사

bool isNotClearNearby(int y, int x)
{
    for (int i = 0; i < 4; ++i)
    {
        int new_x = x + dx[i];
        int new_y = y + dy[i];

        if (0 <= new_x && new_x < M && 0 <= new_y && new_y < N)
        {
            if (!cleared[new_y][new_x])
                return true;
        }
    }
    return false;
}

// 2-1. 있는 경우

// 청소 안된 곳이 있을 경우 회전
// dir = 2(남쪽) 일때

if (isNotClearNearby(Robot[0], Robot[1]))
{
    dir = 1; // 동쪽으로 회전

    // 앞에 있는 칸이 청소가 안됐을 경우 전진
    if (M > Robot[1]+1 && !cleared[Robot[0]][Robot[1]+1])
        Robot[1] += 1;
}

// 2-2 없는 경우

else
{
    // 벽이 없으면 후진하고
    // 모두 청소된경우 후진을 위해(북쪽으로)
    if (0 <= Robot[0]-1 && map[Robot[0]-1][Robot[1]] == 0)
        Robot[0] -= 1;
    
    // 뒤쪽이 벽이면 작동을 멈춘다.
    else
        break;
}
```

문제에 있는 조건을 순서대로 그대로 구현하였다.<br/>

헷갈렸던게, **0을 모두 청소할 때까지 로봇 청소기를 계속 작동시키는 것이 아니다.**<br/>

모두 청소가 안되었다고 하더라도 **주변 4칸이 모두 청소되었고, 바라보는 방향 뒤쪽이 벽일 경우 무조건 작동이 멈춘다.**<br/>

이 점을 주의하면서 문제에 있는 내용을 그대로 구현하도록 유의하자.<br/>