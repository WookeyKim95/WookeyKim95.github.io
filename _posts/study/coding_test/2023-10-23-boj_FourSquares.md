---
layout: post
title:  "[백준] 17626_Four Squares C++"
subtitle:   
date: 2023-10-23 07:05:50 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 17626_Four Squares C++<br/>
<br/>

다이나믹 프로그래밍에 브루트포스 알고리즘을 쓰는 독특했던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string.h>

using namespace std;

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    int DP[50001];
    memset(DP, 0, sizeof(DP));
    cin >> N;

    for (int i = 1; i <= N; ++i)
    {
        DP[i] = DP[i-1] + 1;

        for (int j = 1; j * j <= i; ++j)
            DP[i] = m_min(DP[i], DP[i - j * j] + 1);
    }

    cout << DP[N];

    return 0;
}
```

문제 제한 시간이 0.5초인 것을 보고 바로 DP임을 직감했다.<br/>

하지만 카테고리를 보니 역시 DP였지만, 브루트포스를 같이 쓰기도 했다.<br/>

음? 그렇다면 2중 for문을 써도 되는건가? 싶었는데<br/>

다행히 최대 N의 값이 50000이라서 써도 되겠다고 판단했다.<br/>

제한 시간이 0.5초인 것이 맘에 걸리긴 했지만..<br/>