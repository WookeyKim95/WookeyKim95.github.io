---
layout: post
title:  "[백준] 1520번_내리막 길 C++"
subtitle:   
date: 2023-04-06 09:19:40 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1520번_내리막 길 C++<br/>
<br/>

DFS와 다이나믹 프로그래밍을 활용하는 문제.<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>

using namespace std;

int map[500][500];
int count[500][500];

int N, M;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

void m_fill(int x, int y, int a)
{
    for (int i = y; i < N; ++i)
    {
        for (int j = x; j < M; ++j)
        {
            count[i][j] = a;
        }
    }
}

int dfs(int a, int b, int height)
{
    if (a == M-1 && b == N-1)
        return 1;
    if (count[b][a] != -1)
        return count[b][a];

    count[b][a] = 0;
    for (int i = 0; i < 4; ++i)
    {
        int nx = a + dx[i];
        int ny = b + dy[i];

        if (0 <= nx && nx < M && 0 <= ny && ny < N)
        {
            if (height > map[ny][nx])
            {
                count[b][a] += dfs(nx, ny, map[ny][nx]);   
            }
        }
    }
    return count[b][a];
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            cin >> map[i][j];
            count[i][j] = -1;
        }
            
    }

    dfs(0, 0, map[0][0]);

    cout << count[0][0];

    return 0;
}
```

긴 고민 끝에 [참고링크](https://yabmoons.tistory.com/340)를 참고하여 풀었다<br/>

오히려 이 문제는 좌측 상단부터 (x, y)까지 가는 경로가 얼마나 있는지를 보는 것이 아니라, (x, y)에서 우측 하단까지 갈 수 있는 경로가 몇개가 있는지로 접근을 해야하는 문제였다.<br/>

그래서 cout << count[0][0]; 의 의미로, 좌측 상단부터 우측 하단까지 갈 수 있는 경로를 출력하는 개념으로 만들었다.<br/>

자세한 것은 참고링크를 참조해야겠다.<br/>


<br/>

## 오답제출코드 (시간초과)<br/>
<br/>

```
#include <iostream>
#include <cstring>

using namespace std;

bool visited[500][500];
int map[500][500];
int ans = 0;
int N, M;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

void dfs(int a, int b, int height)
{
    if (a == M-1 && b == N-1)
    {
        ans++;
        return;
    }

    for (int i = 0; i < 4; ++i)
    {
        int nx = a + dx[i];
        int ny = b + dy[i];

        if (0 <= nx && nx < M && 0 <= ny && ny < N && !visited[ny][nx])
        {
            if (height > map[ny][nx])
            {
                visited[ny][nx] = true;
                dfs(nx, ny, map[ny][nx]);
                visited[ny][nx] = false;
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
            cin >> map[i][j];
    }

    memset(visited, false, sizeof(visited));

    visited[0][0] = true;
    dfs(0, 0, map[0][0]);

    cout << ans;

    return 0;
}
```

이번에는 최단 경로를 찾는 것이 아니라 오른쪽 아래로 갈 수 있는 경우를 찾는 것이므로<br/>

BFS보다는 DFS를 사용하였다.<br/>

dfs 함수의 입력 인자는 좌표값과 해당 지점의 높이 값을 input으로 입력 받았다.

그리고 내리막 길인 경우, 즉 다음 칸의 높이가 현재 칸의 높이보다 낮을 경우에만<br/>

방문 처리한 다음에 다음 dfs를 실행하도록 구현하였다.<br/>

그리고 마침내 오른쪽 아래 지점에 도착하면 return 하도록 만들었다.<br/>

그러나 시간초과가 났다.<br/>

테스트케이스의 결과가 10억이하라고 했다는 것은 경우의 수가 10억인 경우도 있다는 것인데<br/>

dfs를 쓰면 시간초과가 날 수 밖에 없긴 할 것이다.<br/>

어떻게 해결해야할지 고민을 했다.<br/>

여기서 알고리즘 분류를 보니, 다이나믹 프로그래밍이 있었다.<br/>

아! 가능한 횟수를 칸마다 기록하면서 가야하는 것이었다!<br/>

그래서 구현한 것이 정답제출코드이다.<br/>