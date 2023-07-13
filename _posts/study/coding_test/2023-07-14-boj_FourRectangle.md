---
layout: post
title:  "[백준] 2669_직사각형 네개의 합집합의 면적 구하기 C++"
subtitle:   
date: 2023-07-14 07:20:42 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2669_직사각형 네개의 합집합의 면적 구하기 C++<br/>
<br/>

조금만 생각하면 쉽게 풀 수 있었던 구현 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <cstring>

using namespace std;

bool Painted[101][101];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    memset(Painted, false, sizeof(Painted));

    for (int i = 0; i < 4; ++i)
    {
        int x1, y1, x2, y2;
        cin >> x1 >> y1 >> x2 >> y2;

        for (int j = x1; j < x2; ++j)
        {
            for (int k = y1; k < y2; ++k)
                Painted[j][k] = true;
        }
    }

    int ans = 0;
    for (int i = 1; i <= 100; ++i)
    {
        for (int j = 1; j <= 100; ++j)
        {
            if (Painted[i][j])
                ans++;
        }
    }
    
    cout << ans;

    return 0;
}
```

IM을 대비하기 위해서 연습차 오랜만에 풀어본 실버 문제이다.<br/>

맨 처음에 생각했던 풀이는 직사각형을 더할 때마다 겹치는 부분이 있는지를 판정하고<br/>

겹치는 부분을 빼는 방법을 생각했다.<br/>

하지만 이렇게 하면 나중에 직사각형을 추가할 때<br/>

그 전전의 직사각형이랑도 겹쳤는지를 따져야해서 복잡하게 느껴졌다.<br/>

그러다 생각난 것은 그냥 입력을 받고 해당 범위를 칠해주는 것이다.<br/>

```
for (int i = 0; i < 4; ++i)
{
    int x1, y1, x2, y2;
    cin >> x1 >> y1 >> x2 >> y2;

    for (int j = x1; j < x2; ++j)
    {
        for (int k = y1; k < y2; ++k)
            Painted[j][k] = true;
    }
}
```

그리고 다 입력 받고 나면 칠해진 범위의 개수를 세어주는 것이다.<br/>

```
int ans = 0;
for (int i = 1; i <= 100; ++i)
{
    for (int j = 1; j <= 100; ++j)
    {
        if (Painted[i][j])
            ans++;
    }
}
```

여기서 주의할 점은 길이 = 점의개수 - 1 이므로 for문이 돌아가는 범위도<br/>

위에 나온 코드대로 해주었다.

다시 말해

```
for (int j = x1; j <= x2; ++j)
{
    for (int k = y1; k <= y2; ++k)
        Painted[j][k] = true;
}
```

가 아니라

```
for (int j = x1; j < x2; ++j)
{
    for (int k = y1; k < y2; ++k)
        Painted[j][k] = true;
}
```

가 맞는 것이다.<br/>