---
layout: post
title:  "[백준] 2225_합분해 C++"
subtitle:   
date: 2023-10-09 06:59:35 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2225_합분해 C++<br/>
<br/>

DP를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

#define MOD 1000000000

using namespace std;

int DP[201][201];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N, K;
    cin >> N >> K;

    for (int i = 0; i <= N; ++i)
        DP[1][i] = 1;
    
    for (int i = 1; i <= K; ++i)
    {
        for (int j = 0; j <= N; ++j)
        {
            for (int k = 0; k <= j; ++k)
                DP[i][j] = (DP[i][j] + DP[i-1][j-k]) % MOD;
        }
    }

    cout << DP[K][N];

    return 0;
}
```

범위가 높을까 걱정했지만 다행히 최대 범위가 N, K 둘다 200으로 작아서<br/>

3중 for문을 사용해도 무방할 것이라 생각했다.<br/>

그래서 과감하게 3중 for문을 사용해 구현하였다.<br/>

DP 배열에는 각각 경우의 수를 저장해준다.<br/>

우선, 각자 자연수 1개만 있을 경우를 1로 저장해주고 나서<br/>

DP에다가 이전 경우의 수를 가져와서 더하는 식으로 구현하였다.<br/>

조금 특이한 부분이 있다. 왜 돼지? 싶은 부분이있었는데<br/>

```
DP[i][j] = (DP[i][j] + DP[i-1][j-k]) % MOD;
```

생각해보면 배열을 선언할 때 0으로 초기화를 하는 것으로 알고 있다.<br/>

그래서 처음 더하는 부분인데도 위와 같이 식을 써도 값이 올바르게 들어가는 것 같다.<br/>