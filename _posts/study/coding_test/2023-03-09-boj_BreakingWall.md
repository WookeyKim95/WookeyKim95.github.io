---
layout: post
title:  "[백준] 2206번_벽 부수고 이동하기 C++"
subtitle:   
date: 2023-03-09 09:31:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2206번_벽 부수고 이동하기 C++<br/>
<br/>

BFS를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <string>
#include <vector>
#include <cstring>

using namespace std;

int N, M;
int visited[1001][1001][2];
int dist[1001][1001];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

vector<string> map;

void bfs()
{
    queue<pair<pair<int, int>, int>> que;

    int startx = 0;
    int starty = 0;
    int Crashed = 0;

    dist[0][0] = 1;

    que.push(make_pair(make_pair(startx, starty), Crashed));

    while(!que.empty())
    {
        // 저장할 데이터 : x, y 좌표 및 벽을 부쉈는가 여부
        int x = que.front().first.first;
        int y = que.front().first.second;
        int Crashed = que.front().second;

        if (x == M-1 && y == N-1)
            return;

        visited[y][x][Crashed] = 1;

        que.pop();

        for (int i = 0; i < 4; ++i)
        {
            int new_x = x + dx[i];
            int new_y = y + dy[i];

            if (0 <= new_x && new_x < M && 0 <= new_y && new_y < N && !visited[new_y][new_x][Crashed])
            {   
                visited[new_y][new_x][Crashed] = 1;

                if (map[new_y][new_x] == '0')
                {
                    // 빈칸인 경우
                    dist[new_y][new_x] = dist[y][x] + 1;
                    que.push(make_pair(make_pair(new_x, new_y), Crashed));
                }

                else if (map[new_y][new_x] == '1')
                {
                    if (!Crashed)
                    {
                        // 벽을 안부쉈다면 부수고 한칸 이동 (새로운 좌표랑 Crashed를 true로 해서 큐에 추가)
                        dist[new_y][new_x] = dist[y][x] + 1;
                        que.push(make_pair(make_pair(new_x, new_y), 1));
                    }
                }
            }
        }   
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        map.push_back(_input);
    }

    // 초기화
    memset(visited, 0, sizeof(visited));
    memset(dist, -1, sizeof(dist));

    bfs();

    cout << dist[N-1][M-1];

    return 0;
}
```
<br/>

문제를 풀기까지 많은 착오가 있었다.<br/>

이 문제를 푸느라 정답률이 많이 내려갔을 듯 하하..<br/>

내가 겪었던 시행착오는 아래에 기술한다.<br/>
<br/>

## 오답제출코드(메모리 초과)<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <string>
#include <vector>
#include <cstring>

using namespace std;

int N, M;
bool visited[1001][1001];
int dist[1001][1001];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

vector<string> map;

void bfs()
{
    queue<pair<pair<int, int>, bool>> que;

    int startx = 0;
    int starty = 0;
    bool Crashed = false;

    dist[0][0] = 1;

    que.push(make_pair(make_pair(startx, starty), Crashed));

    while(!que.empty())
    {
        // 저장할 데이터 : x, y 좌표 및 벽을 부쉈는가 여부
        int x = que.front().first.first;
        int y = que.front().first.second;
        bool Crashed = que.front().second;
        visited[y][x] = true;

        que.pop();

        for (int i = 0; i < 4; ++i)
        {
            int new_x = x + dx[i];
            int new_y = y + dy[i];

            if (0 <= new_x && new_x < M && 0 <= new_y && new_y < N && !visited[new_y][new_x])
            {   
                if (map[new_y][new_x] == '0')
                {
                    // 빈칸인 경우
                    dist[new_y][new_x] = dist[y][x] + 1;
                    que.push(make_pair(make_pair(new_x, new_y), Crashed));
                }

                else if (map[new_y][new_x] == '1')
                {
                    if (!Crashed)
                    {
                        // 벽을 안부쉈다면 부수고 한칸 이동 (새로운 좌표랑 Crashed를 true로 해서 큐에 추가)
                        dist[new_y][new_x] = dist[y][x] + 1;
                        que.push(make_pair(make_pair(new_x, new_y), true));
                    }
                }
            }
        }   
    }
}

int main()
{
    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        map.push_back(_input);
    }

    // 초기화
    memset(visited, false, sizeof(visited));
    memset(dist, -1, sizeof(dist));

    bfs();

    cout << dist[N-1][M-1];

    return 0;
}
```
<br/>

우선, 내가 생각할 수 있는 제일 편한 방법은 큐를 두개 쓰는 것이었다.<br>

좌표를 저장하는 큐와 벽을 부쉈는지 여부를 저장하는 큐를 지정했다.<br/>

하지만 큐를 두개 쓰니까 메모리 초과가 나는 것 같았다.<br/>

이를 해결하는데 애를 먹었다.<br/>

특히, 2개였던 큐 중에서 벽을 부쉈는지 여부를 저장하는 큐를 없애고,<br/>

배열로 저장을 해놓았는데도 메모리 초과가 떠서 굉장히 애를 먹었다.<br/>

큐를 쓰지 말라는건지.. 생각이 들었다.<br/>

그런데..<br/>
<br/>

```
if (0 <= new_x && new_x < M && 0 <= new_y && new_y < N && !visited[new_y][new_x])
            {   
                visited[new_y][new_x] = true;

                if (map[new_y][new_x] == '0')
                ...
```

visited 바로 다음에 방문한 칸에다가 true를 넣어주니 해결되었다.<br/>

아무래도 큐에 불필요한 데이터를 더 이상 넣어주지 않기 때문에 메모리 초과 문제가 해결되는 것으로 보인다.<br/>

하지만 기껏 해결했다고 생각했더니 아래 처럼 **틀렸습니다**가 떴다.<br/>

이제는 아래 문제를 해결해야지..<br/>
<br/>

## 오답제출코드(틀렸습니다)<br/>
<Br/>

```
#include <iostream>
#include <queue>
#include <string>
#include <vector>
#include <cstring>

using namespace std;

int N, M;
bool visited[1001][1001];
int dist[1001][1001];
bool Crashed[1001][1001];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

vector<string> map;

void bfs()
{
    // 좌표를 저장할 큐와 벽을 부쉈는지 여부를 저장할 큐 생성
    queue<pair<int, int>> CoordiQue;

    int startx = 0;
    int starty = 0;

    dist[0][0] = 1;

    // 맨 처음 좌표, 그리고 벽을 부수지 않았다는 표시를 큐의 처음에 추가
    CoordiQue.push(make_pair(startx, starty));

    while(!CoordiQue.empty())
    {
        // 저장할 데이터 : x, y 좌표 및 벽을 부쉈는가 여부
        int x = CoordiQue.front().first;
        int y = CoordiQue.front().second;
        visited[y][x] = true;

        CoordiQue.pop();

        for (int i = 0; i < 4; ++i)
        {
            int new_x = x + dx[i];
            int new_y = y + dy[i];

            if (0 <= new_x && new_x < M && 0 <= new_y && new_y < N && !visited[new_y][new_x])
            {   
                visited[new_y][new_x] = true;

                if (map[new_y][new_x] == '0')
                {
                    // 빈칸인 경우
                    dist[new_y][new_x] = dist[y][x] + 1;
                    CoordiQue.push(make_pair(new_x, new_y));
                    Crashed[new_y][new_x] = Crashed[y][x];
                }

                else if (map[new_y][new_x] == '1')
                {
                    if (!Crashed[y][x])
                    {
                        // 벽을 안부쉈다면 부수고 한칸 이동
                        dist[new_y][new_x] = dist[y][x] + 1;
                        CoordiQue.push(make_pair(new_x, new_y));
                        Crashed[new_y][new_x] = true;
                    }
                }

                // 도착지에 도착하고 기록을 했다면 그냥 끝내기.
                // 더 이상 필요 없는 탐색을 방지해서 메모리 초과 방지
                if (new_y == N-1 && new_x == M-1)
                {
                    CoordiQue = queue<pair<int, int>>();
                    return;
                }
            }
        }   
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    
    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        map.push_back(_input);
    }

    // 초기화
    memset(visited, false, sizeof(visited));
    memset(dist, -1, sizeof(dist));
    memset(Crashed, false, sizeof(Crashed));

    bfs();

    cout << dist[N-1][M-1];

    return 0;
}

```

그런데 방문을 중복해서 하지 못하면 최단경로를 탐색해야하는데 최단경로를 보장하지 못하니<br/>

틀렸습니다가 뜰 수 밖에..<br/>

불필요한 방문을 줄이면 틀렸습니다..<br/>

허용하면 메모리초과..<br/>

흠.. 뭐가문제지 싶었는데<br/>

```
        if (x == M-1 && y == N-1)
            return;

```

이번 좌표가 목적지에 도착한 경우면 그냥 리턴하는 경우를 추가했다.<br/>

그랬더니 정답처리되었다.<br/>

아하! 목적지에 도착했음에도 불구하고 불필요한 탐색을 진행하는 바람에<br/>

최소경로가 아닌곳이 나오거나 메모리 초과가 나왔나보다.<br/>

오케이. 후.. 수많은 착오 끝에 해결이 되었다.<br/>

