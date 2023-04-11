---
layout: post
title:  "[백준] 1600번_말이 되고픈 원숭이 C++"
subtitle:   
date: 2023-04-11 09:08:04 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1600번_말이 되고픈 원숭이 C++<br/>
<br/>

BFS를 활용하는 문제.<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <queue>

#define INF 99999999

using namespace std;

int W, H, K;
bool visited[200][200][31] = {false};
int map[200][200] = {0};

int HorseDx[8] = {-2, -2, -1, -1, 1, 1, 2, 2};
int HorseDy[8] = {-1, 1, -2, 2, -2, 2, -1, 1};

int NormalDx[4] = {-1, 1, 0, 0};
int NormalDy[4] = {0, 0, -1, 1};

int result[200][200][31];

void fill_result()
{
    for (int i = 0; i < H; ++i)
    {
        for (int j = 0; j < W; ++j)
        {
            for (int k = 0; k <= K; ++k)
                result[i][j][k] = -1;
        }
    }
}

void bfs()
{
    queue<pair<pair<int, int>, int>> q;

    q.push({{0, 0}, 0});
    visited[0][0][0] = true;
    result[0][0][0] = 0;

    while(!q.empty())
    {
        int x = q.front().first.first;
        int y = q.front().first.second;
        int CurHorseCount = q.front().second;

        q.pop();

        if (CurHorseCount < K)
        {
            for (int i = 0; i < 8; ++i)
            {
                int nx = x + HorseDx[i];
                int ny = y + HorseDy[i];

                if (0 <= nx && nx < W && 0 <= ny && ny < H && 
                !visited[ny][nx][CurHorseCount] && map[ny][nx] != 1)
                {
                    int CurResult = result[y][x][CurHorseCount] + 1;
                    if (result[ny][nx][CurHorseCount+1] == -1 ||
                    CurResult < result[ny][nx][CurHorseCount+1])
                    {
                        result[ny][nx][CurHorseCount+1] = CurResult;
                        q.push(make_pair(make_pair(nx, ny), CurHorseCount + 1));
                        visited[ny][nx][CurHorseCount+1] = true;
                    }
                }
            }
        }
        
        for (int i = 0; i < 4; ++i)
        {
            int nx = x + NormalDx[i];
            int ny = y + NormalDy[i];

            if (0 <= nx && nx < W && 0 <= ny && ny < H &&
            !visited[ny][nx][CurHorseCount] && map[ny][nx] != 1)
            {
                int CurResult = result[y][x][CurHorseCount] + 1;
                if (result[ny][nx][CurHorseCount] == -1 ||
                CurResult < result[ny][nx][CurHorseCount])
                {
                    result[ny][nx][CurHorseCount] = CurResult;
                    q.push(make_pair(make_pair(nx, ny), CurHorseCount));
                    visited[ny][nx][CurHorseCount] = true;
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

    cin >> K;
    cin >> W >> H;

    for (int i = 0; i < H; ++i)
    {
        for (int j = 0; j < W; ++j)
            cin >> map[i][j];
    }

    fill_result();
    bfs();

    int ans = INF;
    for (int i = 0; i <= K; ++i)
    {
        int RightDown = result[H-1][W-1][i];
        if (ans > RightDown && RightDown != -1)
            ans = RightDown;
    }

    if (ans == INF)
        cout << -1;
    else
        cout << ans;

    return 0;
}
```

최단 경로를 구하는 것이므로 BFS를 사용해서 풀었다.<br/>

말처럼 이동하는 dx는 아래와 같다.<br/>

```
int HorseDx[8] = {-2, -2, -1, -1, 1, 1, 2, 2};
int HorseDy[8] = {-1, 1, -2, 2, -2, 2, -1, 1};
```
이 점에 주의하자!<br/>

```
if (0 <= nx && nx < W && 0 <= ny && ny < H && 
!visited[ny][nx][CurHorseCount] && map[ny][nx] != 1)
{
    int CurResult = result[y][x][CurHorseCount] + 1;
    if (result[ny][nx][CurHorseCount+1] == -1 ||
    CurResult < result[ny][nx][CurHorseCount+1])
    {
        result[ny][nx][CurHorseCount+1] = CurResult;
        q.push(make_pair(make_pair(nx, ny), CurHorseCount + 1));
        visited[ny][nx][CurHorseCount+1] = true;
    }
}
```

큐에는 {x좌표, y좌표, 현재까지 말처럼 이동한 횟수}를 저장하도록 구현하였다.<br/>

그리고 그 값들에 따라서 배열의 서로 다른 지점에 결과값을 기록하도록 구현하였다.<br/>

이 때, 기록조건은 기존에 있던 값이 -1이거나(즉, 첫 방문이거나)<br/>

result값이 기존에 있던 값보다 작을 경우에만 기록하도록 하였다.<br/>

그리고 기록을 했을 때에만 큐에 새로운 값을 넣도록 구현하였다.<br/>

조건을 달아놓지 않고 방문 지점을 모두 큐에 넣으면 메모리초과가 뜬다.<br/>

```
    int ans = INF;
    for (int i = 0; i <= K; ++i)
    {
        int RightDown = result[H-1][W-1][i];
        if (ans > RightDown && RightDown != -1)
            ans = RightDown;
    }

    if (ans == INF)
        cout << -1;
    else
        cout << ans;
```

그리고 탐색이 모두 끝난 뒤에 값이 오른쪽 아래쪽에 기록된 result 값들 중<br/>

-1이 아니면서 가장 작은 값을 출력하도록 했다.<br/>
<br/>

## 오답제출코드<br>
<br/>

```
#include <iostream>
#include <queue>

using namespace std;

int W, H, K;
bool visited[200][200] = {false};
int map[200][200] = {0};

int HorseDx[8] = {-2, -2, -1, -1, 1, 1, 2, 2};
int HorseDy[8] = {-1, 1, -2, 2, -2, 2, -1, 1};

int NormalDx[4] = {-1, 1, 0, 0};
int NormalDy[4] = {0, 0, -1, 1};

int result[200][200];

void fill_result(int x, int y)
{
    for (int i = 0; i < y; ++i)
    {
        for (int j = 0; j < x; ++j)
            result[i][j] = -1;
    }
}

void bfs()
{
    queue<pair<pair<int, int>, int>> q;

    q.push({{0, 0}, 0});
    visited[0][0] = true;
    result[0][0] = 0;

    while(!q.empty())
    {
        int x = q.front().first.first;
        int y = q.front().first.second;
        int CurHorseCount = q.front().second;

        q.pop();

        if (CurHorseCount < K)
        {
            for (int i = 0; i < 8; ++i)
            {
                int nx = x + HorseDx[i];
                int ny = y + HorseDy[i];

                if (0 <= nx && nx < W && 0 <= ny && ny < H && !visited[ny][nx] && map[ny][nx] != 1)
                {
                    result[ny][nx] = result[y][x] + 1;
                    q.push(make_pair(make_pair(nx, ny), CurHorseCount + 1));
                    visited[ny][nx] = true;

                }
            }
        }
        
        for (int i = 0; i < 4; ++i)
        {
            int nx = x + NormalDx[i];
            int ny = y + NormalDy[i];

            if (0 <= nx && nx < W && 0 <= ny && ny < H && !visited[ny][nx] && map[ny][nx] != 1)
            {
                result[ny][nx] = result[y][x] + 1;
                q.push(make_pair(make_pair(nx, ny), CurHorseCount));
                visited[ny][nx] = true;
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> K;
    cin >> W >> H;

    for (int i = 0; i < H; ++i)
    {
        for (int j = 0; j < W; ++j)
            cin >> map[i][j];
    }

    fill_result(W, H);
    bfs();

    cout << result[H-1][W-1];

    return 0;
}
```

되도록이면 말처럼 이동해야 더 먼거리를 갈 수 있기 때문에 최단 경로를 구하는데 유리할 것이라 생각했다.<br/>

그래서 현재까지 말처럼 이동하는 횟수 즉 CurHorseCount가 K미만이면 우선적으로 말처럼 이동하도록 구현했다.<br/>

말 처럼 이동하고 난 다음에 나머지 보통처럼 이동했을 경우를 구하였다.<br/>

그리고 첫 방문이 곧 최소한 동작으로 이동을 한 것이므로 result에다가 지금까지 행동한 횟수를 기록하도록 구현하였다.<br/>

이렇게 구현하고 난 뒤에 오른쪽 아래의 result를 출력하면 될 거라 생각했다.<br/>

하지만 결과는 틀렸습니다가 떴다.<br/>

이유를 분석해보니, 말처럼 이동한 횟수와 상관없이 result값을 덮어써버리기 때문이었다.<br/>

초반에 했던 생각이 잘못되었던 것 같다.<br/>

그래서 말처럼 이동한 횟수에 따른 result값을 기록하기 위해서 result배열 선언을 아래와 같이 분리하고,<br/>

visited배열도 말처럼 이동한 횟수에 따라서 기록하도록 따로 분리하도록 조치하였다.<br/>

```
int result[200][200][31];
bool visited[200][200][31] = {false};
```