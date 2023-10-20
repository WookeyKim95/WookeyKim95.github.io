---
layout: post
title:  "[백준] 1051_숫자 정사각형 C++"
subtitle:   
date: 2023-10-21 07:02:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1051_숫자 정사각형 C++<br/>
<br/>

간단한 완전탐색 구현 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    int N, M;
    cin >> N >> M;
    int Max;
    char Map[51][51];
    
    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= M; ++j)
            cin >> Map[i][j];
    }
    
    int Len;
    if (M > N)
        Len = N;
    else
        Len = M;
    
    for (int i = 0; i < Len; ++i)
    {
        for (int j = 1; j <= N - i; ++j)
        {
            for (int k = 1; k <= M - i; ++k)
            {
                if (Map[j+i][k] == Map[j][k] &&
                    Map[j][k+i] == Map[j][k] &&
                    Map[j+i][k+i] == Map[j][k])
                    Max = i+1;
            }
        }
    }
    
    cout << Max * Max;
    
    return 0;
}
```

쉬어가는 느낌으로 난이도가 낮은 문제를 풀고 있었는데<br/>

범위가 작아서 안심하고 3중 for문을 쓸 수 있는 문제가 나와서 살짝 낮설었다.<br/>

물론 높은 난이도 문제로 가면 3중 for문은 물론 2중 for문을 쓰는 것도 자제해야하지만..<br/>

N과 M중 짧은 길이를 최대 탐색 길이로 정하고, 탐색 길이를 0부터 시작해서 점차 늘려가면서<br/>

정사각형을 찾아내는 방식으로 구현하였다.<br/>

이 방식이면 시간복잡도는 O(N^3)이지만, N과 M의 최대 길이가 50이기 때문에<br/>

안심하고 3중 for문을 써도 되겠다.