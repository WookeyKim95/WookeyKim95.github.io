---
layout: post
title:  "[백준] 1405번_미친 로봇 C++"
subtitle:   
date: 2023-03-30 10:02:07 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1405번_미친 로봇 C++<br/>
<br/>

백트래킹을 활용하는 문제.<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <cstring>
#include <stack>

using namespace std;

int Num;
long double EWSN[4] = {0.f, 0.f, 0.f, 0.f};
long double Simple = 0.f;

bool visited[29][29] = {false};
int dx[4] = {1, -1, 0, 0};
int dy[4] = {0, 0, 1, -1};

void backtrack(long double CurPer, int x, int y, int depth)
{
    if (depth == Num)
    {
        Simple += CurPer;
        return;
    }

    for (int i = 0; i < 4; ++i)
    {
        int nx = x + dx[i];
        int ny = y + dy[i];

        if (EWSN[i] != 0.f && !visited[ny][nx])
        {
            visited[ny][nx] = true;
            backtrack(CurPer * EWSN[i], nx, ny, depth+1);
            visited[ny][nx] = false;
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> Num;

    for (int i = 0; i < 4; ++i)
    {
        long double _input;
        cin >> _input;
        _input /= 100.f;

        EWSN[i] = _input;
    }

    memset(visited, false, sizeof(visited));

    visited[14][14] = true;
    backtrack(1.f, 14, 14, 0);


    cout << fixed;
    cout.precision(11);
    cout << Simple;

    return 0;
}
```

모든 경우를 탐색하는게 아니라 방문했던 곳을 다시 방문하지 않는 경우만 탐색하면 되므로<br/>

!visited[ny][nx]일 경우에만 backtrack를 계속 실행해준다.<br/>

이전에 제출한 오답코드는 모든 경우를 다 탐색해버려서 시간초과가 났다.<br/>

backtrack과 사실 dfs가 같이 섞인 문제였다.<br/>

그리고 depth가 N에 도달하면 그때 확률을 추가하는 식으로 구현하였다.<br/>

초반 구현과정은 아래에 서술한다.<br/>

<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <cstring>

using namespace std;

int Num;
long double EWSN[4] = {0.f, 0.f, 0.f, 0.f};
long double Notsimple = 0.f;

bool visited[29][29] = {false};

void backtrack(long double CurPer, int x, int y, int depth, bool crazy)
{
    if (depth == Num)
    {
        if (crazy)
            Notsimple += CurPer;

        return;
    }

    for (int i = 0; i < 4; ++i)
    {
        if (EWSN[i] != 0.f)
        {
            if (i == 0)
            {
                if (visited[x+1][y])
                {
                    backtrack(CurPer * EWSN[i], x+1, y, depth+1, true);
                }
                else
                {
                    visited[x+1][y] = true;
                    backtrack(CurPer * EWSN[i], x+1, y, depth+1, crazy);
                    visited[x+1][y] = false;
                }

            }
            else if (i == 1)
            {
                if (visited[x-1][y])
                {
                    backtrack(CurPer * EWSN[i], x-1, y, depth+1, true);
                }
                else
                {
                    visited[x-1][y] = true;
                    backtrack(CurPer * EWSN[i], x-1, y, depth+1, crazy);
                    visited[x-1][y] = false;
                }
            }
            else if (i == 2)
            {
                if (visited[x][y+1])
                {
                    backtrack(CurPer * EWSN[i], x, y+1, depth+1, true);
                }
                else
                {
                    visited[x][y+1] = true;
                    backtrack(CurPer * EWSN[i], x, y+1, depth+1, crazy);
                    visited[x][y+1] = false;
                }
            }
            else if (i == 3)
            {
                if (visited[x][y-1])
                {
                    backtrack(CurPer * EWSN[i], x, y-1, depth+1, true);
                }
                else
                {
                    visited[x][y-1] = true;
                    backtrack(CurPer * EWSN[i], x, y-1, depth+1, crazy);
                    visited[x][y-1] = false;
                }
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> Num;

    for (int i = 0; i < 4; ++i)
    {
        long double _input;
        cin >> _input;
        _input /= 100.f;

        EWSN[i] = _input;
    }

    memset(visited, false, sizeof(visited));

    visited[14][14] = true;
    backtrack(1.f, 14, 14, 0, false);


    cout << fixed;
    cout.precision(11);
    cout << 1.f - Notsimple;

    return 0;
}
```

실수로 계산을 해야하고, 오차의 허용범위가 10^(-9)까지 이므로 long double을 선언하였다.<br/>

그리고 N번 이동하는 모든 경우를 탐색해야하므로 백트래킹을 사용하였다.<br/>

그리고 최대 이동횟수는 14이고, 평면에서 가운데에서 시작하도록 하기 위해서<br/>

visited[29][29] 배열로 선언하고, 정 가운데인 (14, 14)에서 시작하도록 하였다.<br/>

backtrack함수에서는 현재까지 누적된 확률과 좌표, crazy(재방문 이력), 그리고 이동 횟수를 입력인자로 받는다.<br/>

for문을 돌면서 동서남북으로 이동하는데, 다음 칸이 이미 방문했다면<br/>

재방문을 하는 것이므로 crazy자리에 true를 넣는다. 그렇지 않다면 기존에 가지고 있던 crazy값을 계속 가지고 간다.

만약에 이동 횟수가 N이면 지금까지 누적된 확률을 더한다.<br/>

그리고 마지막까지 탐색을 마치면 전체확률(100%)에서 누적된 확률을 합한 값을 빼도록 하였다.<br/>

그리고 소숫점 10자리까지 확인을 위해서 아래 코드를 넣어주었다.<br/>

```
    cout << fixed;
    cout.precision(11);
```

하지만 시간초과가 떴다.<br/>

이유를 생각해보니 모든 경우를 탐색해서였다.<br/>

탐색하는 경우를 줄이는 과정을 위에 서술하였다.<br/>