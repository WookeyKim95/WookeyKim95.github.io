---
layout: post
title:  "[백준] 1261번_알고스팟 C++"
subtitle:   
date: 2023-03-26 11:13:41 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1261번_알고스팟 C++<br/>
<br/>

BFS와 우선순위 큐를 활용하는 문제.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <algorithm>

using namespace std;

int BreakCount[100][100];
bool visited[100][100] = {false};
int N, M;
vector<string> map;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

// 0과 연결된 곳에 초기 값 세팅
void SettingBFS()
{
    int x, y;
    queue<pair<int, int>> q;

    q.push(make_pair(0, 0));
    BreakCount[0][0] = 0;
    visited[0][0] = true;

    while(!q.empty())
    {
        x = q.front().first;
        y = q.front().second;

        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int nx = x + dx[i];
            int ny = y + dy[i];

            if (0 <= nx && nx < M && 0 <= ny && ny < N && !visited[ny][nx])
            {
                if (map[ny][nx] == '0')
                {
                    BreakCount[ny][nx] = 0;
                    visited[ny][nx] = true;
                    q.push(make_pair(nx, ny));
                }
            }
        }
    }
}

// 본격적으로 벽을 부수며 탐색 시작
void BreakWallBFS()
{
    int x, y, CurBreakCount;
    priority_queue<pair<int, pair<int, int>>> q;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            if (BreakCount[i][j] == 0)
            {
                q.push(make_pair(0, make_pair(j, i)));
                BreakCount[i][j] = 0;
            }
        }
    }

    // 벽을 부수면서 탐색
    while(!q.empty())
    {
        CurBreakCount = q.top().first;
        x = q.top().second.first;
        y = q.top().second.second;

        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int nx = x + dx[i];
            int ny = y + dy[i];

            if (0 <= nx && nx < M && 0 <= ny && ny < N)
            {
                if (map[ny][nx] == '1')
                {
                    if (BreakCount[ny][nx] > CurBreakCount + 1)
                    {
                        BreakCount[ny][nx] = CurBreakCount + 1;
                        q.push(make_pair(CurBreakCount+1, make_pair(nx, ny)));
                    }

                }
                else if (map[ny][nx] == '0')
                {
                    if (BreakCount[ny][nx] > CurBreakCount)
                    {
                        BreakCount[ny][nx] = CurBreakCount;
                        q.push(make_pair(CurBreakCount, make_pair(nx, ny)));
                    }
                }
            }
        }
    }
}

int main()
{
    cin >> M >> N;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        map.push_back(_input);
    }

    fill(&BreakCount[0][0], &BreakCount[0][0] + 100*100, 1000);

    SettingBFS();
    fill(&visited[0][0], &visited[0][0] + 100*100, false);
    BreakWallBFS();

    cout << BreakCount[N-1][M-1];

    return 0;
}
```

우선순위 큐를 사용하였다.<br/>

우선순위 큐에는 3개의 int가 들어가는데, 구성은 아래와 같다.

- (벽을 부순 횟수, (x좌표, y좌표))

두번째 bfs (BreakWallBFS)에서 visited 여부를 따져버리면 최소경로는 보장되지만<br/>

벽을 부순횟수를 최소로 보장하지는 않는다.<br/>

따라서 다음 노드의 벽을 부순 횟수가 이전 노드보다 클 경우,<br/>

이전 노드의 횟수 + 1이나 이전 노드의 값과 같은 것으로 바꾼 다음에 큐에 넣어주도록 하였다.<br/>

이렇게 하면 계속 검사를 하면서, 최소로 벽을 부순 횟수가 출력된다.<br/>

<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <algorithm>

using namespace std;

int BreakCount[100][100];
bool visited[100][100] = {false};
int N, M;
vector<string> map;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

// 0과 연결된 곳에 초기 값 세팅
void SettingBFS()
{
    int x, y;
    queue<pair<int, int>> q;

    q.push(make_pair(0, 0));
    BreakCount[0][0] = 0;

    while(!q.empty())
    {
        x = q.front().first;
        y = q.front().second;
        visited[y][x] = true;

        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int nx = x + dx[i];
            int ny = y + dy[i];

            if (0 <= nx && nx < M && 0 <= ny && ny < N && !visited[ny][nx])
            {
                if (map[ny][nx] == '0')
                {
                    BreakCount[ny][nx] = 0;
                    q.push(make_pair(nx, ny));
                }
            }
        }
    }
}

// 본격적으로 벽을 부수며 탐색 시작
void BreakWallBFS()
{
    int x, y;
    queue<pair<int, int>> q;

    q.push(make_pair(0, 0));
    BreakCount[0][0] = 0;

    // 벽을 부수면서 탐색
    while(!q.empty())
    {
        x = q.front().first;
        y = q.front().second;
        visited[y][x] = true;

        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int nx = x + dx[i];
            int ny = y + dy[i];

            if (0 <= nx && nx < M && 0 <= ny && ny < N && !visited[ny][nx])
            {
                if (map[ny][nx] == '1')
                {
                    if (BreakCount[y][x] + 1 < BreakCount[ny][nx])
                        BreakCount[ny][nx] = BreakCount[y][x] + 1;
                }
                else if (map[ny][nx] == '0')
                {
                    if (BreakCount[y][x] < BreakCount[ny][nx])
                        BreakCount[ny][nx] = BreakCount[y][x];
                }

                q.push(make_pair(nx, ny));
            }
        }
    }
}

int main()
{
    cin >> M >> N;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        map.push_back(_input);
    }

    fill(&BreakCount[0][0], &BreakCount[0][0] + 100*100, 1000);

    SettingBFS();
    fill(&visited[0][0], &visited[0][0] + 100*100, false);
    BreakWallBFS();

    cout << BreakCount[N-1][M-1];

    return 0;
}
```
<br/>

어떤 위치에 도달할 때 까지 여러가지의 방법이 있는데 그 방법들 중에서<br/>

최소로 벽을 부순 방법을 구현하는데 조금 애를 먹었다.<br/>

우선 나의 해법은 이렇다.<br/>

- 첫번째 BFS를 통해 시작점부터 뚫려있는 곳 까지 벽을 부순 개수를 0을 체크한다.
- 두번째 BFS를 통해 벽을 부수며 탐색한다.
- 벽을 만났을 경우 벽을 부순 횟수를 카운트 하기 전에 더 작은 값인지 여부를 파악한다.
- 자신이 가지고 있던 것이 더 작다면 현재까지 벽을 부순수 + 1을 넣어준다.
- 그렇지 않다면 생략한다.

3번 ~ 5번 과정을 구현한 코드가 아래 코드이다.

```
if (0 <= nx && nx < M && 0 <= ny && ny < N && !visited[ny][nx])
{
    if (map[ny][nx] == '1')
    {
        if (BreakCount[y][x] + 1 < BreakCount[ny][nx])
            BreakCount[ny][nx] = BreakCount[y][x] + 1;
    }
    else if (map[ny][nx] == '0')
    {
        if (BreakCount[y][x] < BreakCount[ny][nx])
            BreakCount[ny][nx] = BreakCount[y][x];
    }

    q.push(make_pair(nx, ny));
}
```

하지만 이 방법은 BFS를 사용하며 벽을 부순 횟수와 상관없이 무조건 최소경로로만 가기 때문에<br/>

벽을 부순 횟수로는 최소값을 보장하지 않는다.<br/>

따라서 다익스트라 알고리즘이나 DFS를 써야했다.<br/>

하지만 DFS는 시간초과가 날 것 같아서 다익스트라로 방향을 잡았다.<br/>

그래서 우선순위 큐를 사용하였고, 정답처리 된 코드가 위의 정답제출코드이다.<br/>