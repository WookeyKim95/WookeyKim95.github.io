---
layout: post
title:  "[백준] 1256_사전 C++"
subtitle:   
date: 2023-05-28 09:12:00 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1256_사전 C++<br/>
<br/>

다이나믹 프로그래밍을 이용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int N, M, K;
long long dp[101][101];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M >> K;

    for (int i = 1; i <= 100; ++i)
    {
        dp[i][0] = 1;
        dp[0][i] = 1;
    }

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= M; ++j)
        {
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1];

			if (dp[i][j] > 1000000000)
                dp[i][j] = 1000000000;
        }
    }

    if (dp[N][M] < K)
    {
        cout << -1;
        return 0;
    }

    int a_count = N;
    int z_count = M;

    for (int i = 0; i < N + M; ++i)
    {
		int as = dp[a_count - 1][z_count];
		
		if (a_count == 0)
        {
			cout << 'z';
			z_count--;
		}
		else if (z_count == 0)
        {
			cout << 'a';
			a_count--;
		}
		else if (K <= as)
        {
			cout << 'a';
			a_count--;
		}
		else
        {
			K = K - as;
			cout << 'z';
			z_count--;
		}
	}

    return 0;
}
```
<br/>

i개의 a와 j개의 z를 활용해서 만들 수 있는 문자열 조합의 개수는<br/>

아래와 같은 규칙으로 증가한다.<br/>

```
dp[i][j] = dp[i-1][j] + dp[i][j-1]
```

그리고 dp[i][0] 및 dp[0][j]의 경우는 1이므로<br/>

1행과 1열 각 자리에는 1을 채워넣어준다<br/>

그리고 주의할 점은 a의 알파벳 개수 100, z의 알파벳 개수 100으로 하면<br/>

경우의수가 int 범위를 넘어가는 일이 발생한다.<br/>

그런데 어차피 K는 최대값이 1000000000이므로<br/>

해당 값을 넘어가면 1000000000으로 만들어 주도록 했다.<br/>

