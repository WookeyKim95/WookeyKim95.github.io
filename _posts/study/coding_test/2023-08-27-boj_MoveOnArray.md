---
layout: post
title:  "[백준] 1981_배열에서 이동 C++"
subtitle:   
date: 2023-08-27 07:17:28 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1981_배열에서 이동 C++<br/>
<br/>

이분탐색과 BFS의 활용에 대해서 알아보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <cstring>

using namespace std;

int N;
int map[101][101];
bool visited[101][101];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

int Min = 201;
int Max = -1;

bool BFS(int Diff)
{
    for (int i = Min; i <= Max; ++i)
    {
        memset(visited, true, sizeof(visited));
        
        for (int j = 0; j < N; ++j)
        {
            for (int k = 0; k < N; ++k)
            {
                if (i <= map[j][k] && map[j][k] <= i + Diff)
                    visited[j][k] = false;
            }
        }

        queue<pair<int, int>> q;
        
        if (!visited[0][0])
        {
            q.push({0, 0});
            visited[0][0] = true;
        }

        while (!q.empty())
        {
            int CurX = q.front().first;
            int CurY = q.front().second;
            q.pop();

            if (CurX == N-1 && CurY == N-1)
                return true;

            for (int i = 0; i < 4; ++i)
            {
                int Nx = CurX + dx[i];
                int Ny = CurY + dy[i];

                if (!(0 <= Nx && Nx < N && 0 <= Ny && Ny < N))
                    continue;
                
                if (!visited[Ny][Nx])
                {
                    visited[Ny][Nx] = true;
                    q.push({Nx, Ny});
                }
            }
        }
    }

    return false;
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
        {
            cin >> map[i][j];

            if (map[i][j] > Max)
                Max = map[i][j];
            if (map[i][j] < Min)
                Min = map[i][j];
        }
            
    }

    int Left = 0;
    int Right = Max - Min;
    int Mid;

    while (Left <= Right)
    {
        Mid = (Left + Right) / 2;
        if (BFS(Mid))
            Right = Mid - 1;
        else
            Left = Mid + 1;
    }
    cout << Right + 1;


    return 0;
}
```

알고리즘 분류를 펼쳐보니 이분탐색 및 BFS라고 하길래<br/>

우선 algorithm과 cmath, 그리고 cstring, vector을 include 시켰었다.<br/>

구글링을 하긴 했지만, 그러기 전에 BFS를 최대한 줄이는 것이 좋겠지?<br/>

생각을 하며 어떻게 BFS 횟수를 줄이면서 루트를 짤 수 있을까 생각했다.<br/>

처음 생각했던 방법은 이랬다.<br/>

- BFS를 한다.
- route를 받는다.
- route를 정렬한다.
- 가운데에 있는 값들의 차이를 측정한다.

근데 이러면 다시 BFS를 해야하고 시간복잡도가 늘어나게 되니..<br/>

N의 범위가 작다고 해도 이것은 아닐거라 생각했다.<br/>

이분탐색과 BFS라.. 아이디어가 떠오르지 않아 구글링을 했다.<br/>

[얍문님의 글](https://yabmoons.tistory.com/101)을 참고하였다.<br/>

<br/>

이 분의 글을 따르면 알고보니 추출한 루트에서 이분탐색을 하는 것이 아니라<br/>

값의 차이를 통해서 이분탐색을 해야하는 것이었다.<br/>

- 값을 받으며 최대값, 최소값을 측정한다.
- 두 값의 차이를 right, 0을 left로 지정한다.
- 이 두 값을 통해 이분탐색을 실시한다.

그리고 BFS는 아래와 같다.<br/>

- Max와 Min의 차이를 인자로 받는다.
- map에서 Max와 Min 사이에 있는 값들만 탐색할 수 있도록 방문여부를 false로 만든다.
- BFS를 해서 목적지에 도달하면 true를 반환한다.
- 그렇지 않으면 false를 반환한다.

그래서 BFS 결과에 따라서 이분탐색의 범위를 좁혀가는 것이다.<br/>

이렇게 하면 BFS의 횟수를 최대한 줄일 수 있다.<br/>

아마 NlogN * logN 정도가 나오지 않을까?<br/>

난 오늘도 감탄할뿐..