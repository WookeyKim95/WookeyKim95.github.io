---
layout: post
title:  "[백준] 12852_1로 만들기 2 C++"
subtitle:   
date: 2023-12-24 07:29:15 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 12852_1로 만들기 2 C++<br/>
<br/>

BFS로도 푸는 거 같기도 했지만 알고보면 DP였던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int n, dp[1000001];

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

	cin >> n;

	for (int i = 1; i <= n; i++)
		dp[i] = i;

	for (int i = 2; i <= n; i++)
    {
		if (i % 2 == 0)
			dp[i] = m_min(dp[i], dp[i / 2]);

		if (i % 3 == 0)
			dp[i] = m_min(dp[i], dp[i / 3]);

		dp[i] = m_min(dp[i], dp[i - 1]) + 1;
	}

	cout << dp[n]-1 << "\n";


	while (n != 0)
    {
		cout << n << " ";
		if (dp[n] == dp[n - 1] + 1)
			n = n - 1;

		else if (n % 2 == 0 && dp[n] == dp[n / 2] + 1)
			n = n / 2;

		else if (n % 3 == 0 && dp[n] == dp[n / 3] + 1)
			n = n / 3;

	}
}
```

설마 DP겠어 했는데 DP였다.<br/>

연산 규칙에 따라서 dp 배열에다가 최소값을 갱신하고 저장해주면 된다.<br/>

시행착오도 없고, 뭐라 쓸말이 없어서 음.. 그럼 이만.<br/>