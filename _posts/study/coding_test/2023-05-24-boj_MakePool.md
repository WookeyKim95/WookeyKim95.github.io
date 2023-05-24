---
layout: post
title:  "[백준] 1113_수영장 만들기 C++"
subtitle:   
date: 2023-05-24 10:21:36 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1113_수영장 만들기 C++<br/>
<br/>

BFS를 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <string>

#define MAX 52

using namespace std;

int N, M;
int map[MAX][MAX];
int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
int ans = 0;

void BFS(int h)
{
    queue<pair<int, int>> q;

    map[0][0] = h;
    q.push({0, 0});

    while (!q.empty())
    {
        int x = q.front().first;
        int y = q.front().second;
        q.pop();

        for (int i = 0; i < 4; ++i)
        {
            int nx = x + dx[i];
            int ny = y + dy[i];

            if (0 <= nx && nx <= M+1 && 0 <= ny && ny <= N+1 && map[ny][nx] < h)
            {
                map[ny][nx] = h;
                q.push({nx, ny});
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

    int maximum = 0;

    for (int i = 1; i <= N; ++i)
    {
        string _input;
        cin >> _input;
        for (int j = 1; j <= M; ++j)
        {
            map[i][j] = _input[j-1] - '0';

            if (map[i][j] > maximum)
                maximum = map[i][j];
        }
    }

    for (int k = 1; k <= maximum; ++k)
    {
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
<br/>

처음에는 구조체를 구성하고, 높이와 물의 높이를 구성하는 등 난리를 쳤지만<br/>

시뮬레이션을 돌리니 허점이 너무 많아서 결국 구글링을 하였다.<br/>

[안산학생님의 블로그 글](https://haejun0317.tistory.com/157)을 참고하여 풀었다.<br/>

감사드립니다. 꾸벅(__)<br/>

여기서 겪은 문제는 안산학생님의 의도가 살짝 쿵 이해가 안되는 것이었는데 해석을 해볼필요가 있었다.<br/>

신기했던 점은 배열의 바깥 테두리 공간을 만들어주신다는 것이었다.<br/>

그래서 MAX값이 50이 아니라 52이다.<br/>

<br/>

해석을 해보니 시뮬레이션을 하는 방법이<br/>

물의 수위를 계속 높여서 풀장을 이루는 부분 내에 물의 부피를 구하는 방식이었다.<br/>

나는 특정한 지점에서 물이 흐르게 해서 물이 넘치고, 차고, 넘치고를 반복하는 방법으로 시뮬레이션을 구성했는데<br/>

이분은 그냥 전체적으로 바닥에서부터 물이 차올라서 물이 찬 부분의 부피를 구하도록 시뮬레이션을 진행하셨다.<br/>

그래서 나온 코드가 위의 코드인 것이었다.<br/>

