---
layout: post
title:  "[백준] 14500_테트로미노 C++"
subtitle:   
date: 2023-11-11 07:09:31 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 14500_테트로미노 C++<br/>
<br/>

알고보면 완전탐색을 써보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>

using namespace std;

int N, M;
int Map[501][501];
bool visited[501][501];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
int Max = 0;

void TShape(int x, int y)
{
    int Sum = Map[y][x];
    int count = 1;

    for (int i = 0; i < 4; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        if (!(0 <= Ny && Ny < N && 0 <= Nx && Nx < M))
            continue;
        
        Sum += Map[Ny][Nx];
        count++;
    }

    if (count < 4)
        return;

    if (count == 5)
    {
        for (int i = 0; i < 4; ++i)
        {
            int Nx = x + dx[i];
            int Ny = y + dy[i];

            if (!(0 <= Ny && Ny < N && 0 <= Nx && Nx < M))
                continue;
            
            if (Max < Sum - Map[Ny][Nx])
                Max = Sum - Map[Ny][Nx];
        }
    }
    
    else if (count == 4)
    {
        if (Max < Sum)
            Max = Sum;
    }
}

void backtrack(int a, int x, int y, int Sum)
{
    if (a == 4)
    {
        if (Max < Sum)
            Max = Sum;
        return;
    }

    for (int i = 0; i < 4; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        if (!(0 <= Ny && Ny < N && 0 <= Nx && Nx < M))
            continue;
        
        if (!visited[Ny][Nx])
        {
            visited[Ny][Nx] = true;
            backtrack(a+1, Nx, Ny, Sum + Map[Ny][Nx]);
            visited[Ny][Nx] = false;
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
            cin >> Map[i][j];
    }

    memset(visited, false, sizeof(visited));

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            visited[i][j] = true;
            backtrack(0, j, i, 0);
            visited[i][j] = false;

            TShape(j, i);
        }
    }

    cout << Max;

    return 0;
}
```

## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>

using namespace std;

int N, M;
int Map[501][501];
bool visited[501][501];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
int Max = 0;

void TShape(int x, int y)
{
    int Sum = Map[y][x];

    for (int i = 0; i < 4; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        if (!(0 <= Ny && Ny < N && 0 <= Nx && Nx < M))
            continue;
        
        Sum += Map[Ny][Nx];
    }

    for (int i = 0; i < 4; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        if (!(0 <= Ny && Ny < N && 0 <= Nx && Nx < M))
            continue;
        
        if (Max < Sum - Map[Ny][Nx])
            Max = Sum - Map[Ny][Nx];
    }
}

void backtrack(int a, int x, int y, int Sum)
{
    if (a == 4)
    {
        if (Max < Sum)
            Max = Sum;
        return;
    }

    for (int i = 0; i < 4; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        if (!(0 <= Ny && Ny < N && 0 <= Nx && Nx < M))
            continue;
        
        if (!visited[Ny][Nx])
        {
            visited[Ny][Nx] = true;
            backtrack(a+1, Nx, Ny, Sum + Map[Ny][Nx]);
            visited[Ny][Nx] = false;
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
            cin >> Map[i][j];
    }

    memset(visited, false, sizeof(visited));

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            visited[i][j] = true;
            backtrack(0, j, i, 0);
            visited[i][j] = false;

            TShape(j, i);
        }
    }

    cout << Max;

    return 0;
}
```

N, M 의 범위가 500까지 늘어나서 살짝 아리송했지만 완전탐색 문제였다.<br/>

2중 for문으로 돌면서 일일이 대입해서 합을 구하는 방식으로 탐색을 했다.<br/>

DFS 방식 백트래킹으로 4칸을 돌면서 합을 구해주면 된다고 생각했다.<br/>

여기서 문제는 T모양.<br/>

T모양으로는 DFS를 사용할 수 없어서 살짝 문제가 있었다.<br/>

근데 알고보면 T모양은 그냥 DFS 쓰지말고 따로 탐색해주면 되는 문제였다.<br/>

그래서 함수를 따로 빼주어서 구현하였다.<br/>

ㅗ, ㅜ, ㅓ, ㅏ 모양으로 함수를 4개 만들어서 합을 구해서 탐색하는 방법도 있지만<br/>

그런 함수가 너무 많아지는 것은 싫어서 제출코드에 있는 대로 함수 하나로 구현하였다.<br/>

내가 생각한 원리는, 십자가 모양으로 합을 구한 다음,<br/>

5칸 중 동, 서, 남, 북 에 있는 칸의 수를 하나 빼면 그것이 곧<br/>

나머지 네 칸의 합이 아니겠는가?<br/>

그래서 그렇게 구현하였다.<br/>

그런데 주의해야 할 반례가 하나 있다.<br/>

```
5 5

10000 1 2 2 2
1 2 2 2 2
2 2 2 2 2
3 3 3 3 3
4 4 4 4 4
```

위와 같은 상황이 있다고 하면 칸의 개수가 3개인데도 불구하고 10000 1 1 의 조합이 합이 제일 크기 때문에<br/>

오답이 출력되어버리는 반례가 발생한다.<br/>

그래서 오답제출코드로 부터 아래와 같은 조치를 취했다.<br/>

```
if (count < 4)
        return;

if (count == 5)
{
    for (int i = 0; i < 4; ++i)
    {
        int Nx = x + dx[i];
        int Ny = y + dy[i];

        if (!(0 <= Ny && Ny < N && 0 <= Nx && Nx < M))
            continue;
        
        if (Max < Sum - Map[Ny][Nx])
            Max = Sum - Map[Ny][Nx];
    }
}

else if (count == 4)
{
    if (Max < Sum)
        Max = Sum;
}
```

칸을 더할 때마다 칸의 개수를 증가시키고,<br/>

3칸일 경우는 return, 4칸일 경우는 바로 비교,<br/>

십자 모양으로 5칸일 경우에는 빼면서 비교하기.<br/>

나누어 주니까 정답이 되었다!<br/>