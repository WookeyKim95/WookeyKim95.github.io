---
layout: post
title:  "[백준] 2276_물 채우기 C++"
subtitle:   
date: 2023-08-17 06:27:57 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2276_물 채우기 C++<br/>
<br/>

BFS와 우선순위 큐를 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <string>
#include <cstring>

#define MAX 302

using namespace std;

int N, M;
int map[MAX][MAX];
bool visited[MAX][MAX];
int dx[4] = { -1, 1, 0, 0 };
int dy[4] = { 0, 0, -1, 1 };
int ans = 0;

struct Square
{
    int x, y;
    bool operator<(Square i) const
    {
        return map[y][x] > map[i.y][i.x];
    }
};

priority_queue<Square> pq;

void BFS(int x, int y, int h)
{
    queue<pair<int, int>> q;
    q.push({ x, y });
    visited[y][x] = true;

    while (!q.empty())
    {
        int CurX = q.front().first;
        int CurY = q.front().second;
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int nx = CurX + dx[i];
            int ny = CurY + dy[i];

            if (0 <= nx && nx < M && 0 <= ny && ny < N && !visited[ny][nx])
            {
                visited[ny][nx] = true;

                if (map[ny][nx] > h)
                    pq.push({ nx, ny });
                else
                {
                    ans += h - map[ny][nx];
                    q.push({ nx, ny });
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

    cin >> M >> N;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            cin >> map[i][j];

            if (i == 0 || j == 0 || i == N - 1 || j == M - 1)
            {
                pq.push({ j, i });
                visited[i][j] = true;
            }
               
        }
    }

    while (!pq.empty())
    {
        Square s = pq.top();
        pq.pop();
        BFS(s.x, s.y, map[s.y][s.x]);
    }

    cout << ans;

    return 0;
}
```

어떻게 보면 [수영장 만들기](https://www.acmicpc.net/problem/1113)랑 비슷한 종류의 문제지만<br/>

테스트 케이스가 훨씬더 많아져서 같은 방법으로 했다가는 시간초과가 나는 문제였다.<br/>

물의 높이를 조사하는데 있어서 우선순위 큐를 활용해야 했던 문제다.<br/>
<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <string>
#include <cstring>

#define MAX 302

using namespace std;

int N, M;
int map[MAX][MAX];
bool visited[MAX][MAX];
int dx[4] = { -1, 1, 0, 0 };
int dy[4] = { 0, 0, -1, 1 };
int ans = 0;

void BFS(int h)
{
    queue<pair<int, int>> q;

    map[0][0] = h;
    q.push({ 0, 0 });

    while (!q.empty())
    {
        int x = q.front().first;
        int y = q.front().second;
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int nx = x + dx[i];
            int ny = y + dy[i];

            if (0 <= nx && nx <= M + 1 && 0 <= ny && ny <= N + 1 && map[ny][nx] < h && !visited[ny][nx])
            {
                visited[ny][nx] = true;
                map[ny][nx] = h;
                q.push({ nx, ny });
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> M >> N;

    int maximum = 0;
    int minimum = 999999999;

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= M; ++j)
        {
            cin >> map[i][j];

            if (map[i][j] > maximum)
                maximum = map[i][j];
            if (map[i][j] < minimum)
                minimum = map[i][j];
        }
    }

    for (int k = minimum; k <= maximum; ++k)
    {
        memset(visited, false, sizeof(visited));
        BFS(k);

        for (int i = 1; i <= N; ++i)
        {
            for (int j = 1; j <= M; ++j)
            {
                if (map[i][j] < k)
                {
                    ans++;
                    map[i][j]++;
                }
            }
        }
    }

    cout << ans;

    return 0;
}
```

이 방법은 좌표를 일일이 돌면서 BFS를 걸어주는 방식이다.<br/>

상술하였듯, 적은 테스트케이스로는 유효하지만<br/>

테스트케이스가 커지면 시간초과를 마주하게 된다.<br/>

이를 해결하기 위해서 우선순위 큐를 써줘야 했다.<br/>

방법은 [이분의 글](http://codersbrunch.blogspot.com/2016/12/2276.html)을 참고하였다.<br/>

수면이 점점 올라갈 때, 잠기는 구역이 새로 생기게 되며, 물의 높이 - 잠기는 구역의 높이를 누적시키면 답을 구할 수 있다.<br/>

이 때, 물의 높이를 저장하는데 우선순위 큐를 사용하는 것이다.<br/>