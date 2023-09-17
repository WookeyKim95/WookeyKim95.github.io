---
layout: post
title:  "[백준] 10538_빅 픽쳐 C++"
subtitle:   
date: 2023-09-17 06:34:15 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 10538_빅 픽쳐 C++<br/>
<br/>

해시를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <string>
#include <cstring>

#define mod 1000000000
#define MAX 2001

typedef long long ll;

using namespace std;
int N, M, H, W;
vector<string> Map;

ll row_hash[MAX][MAX];
ll hash_store[MAX][MAX];

ll MOD(ll v)
{
	if (v >= 0)
		return v % mod;
	else
		return ((-v / mod + 1) * mod + v) % mod;
}

void RowHash(string s, int m, int line)
{
	int n = s.length();
	ll power = 1;
	ll hash_val = 0;

	for (int i = 0; i <= n - m; ++i)
	{
		if (i == 0)
		{
			for (int j = 0; j < m; ++j)
			{
				hash_val = MOD(MOD(hash_val * 37) + s[i + j]);
				if (j != m - 1)
					power = MOD(power * 37);
			}
		}
		else
			hash_val = MOD(MOD(37 * MOD(hash_val - MOD(s[i - 1] * power))) + s[i + m - 1]);
		row_hash[line][i] = hash_val;
	}
}

void ColHash(int h, int w, int n, int m)
{
	for (int i = 0; i <= m - w; ++i)
	{
		ll power = 1;
		ll hash_val = 0;
		for (int k = 0; k <= n - h; ++k)
		{
			if (k == 0)
			{
				for (int j = 0; j < h; ++j)
				{
					hash_val = MOD(hash_val + MOD(row_hash[h - j - 1][i] * power));
					if (j != h - 1)
						power = MOD(power * 5831);
				}
			}
			else
				hash_val = MOD(MOD(5831 * MOD((hash_val - MOD(row_hash[k - 1][i] * power)))) + row_hash[k + h - 1][i]);
			hash_store[k][i] = hash_val;
		}
	}
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N >> M >> H >> W;

	for (int i = 0; i < N; ++i)
	{
		string p;
		cin >> p;

		RowHash(p, M, i);
	}
	ColHash(N, M, N, M);

	ll cmp = hash_store[0][0];

	for (int i = 0; i < H; ++i)
	{
		string p;
		cin >> p;

		RowHash(p, M, i);
	}
	ColHash(N, M, H, W);

	ll ans = 0;
	for (int i = 0; i <= H - N; ++i)
	{
		for (int j = 0; j <= W - M; ++j)
		{
			if (cmp == hash_store[i][j])
				ans++;
		}
	}

	cout << ans;

	return 0;
}
```

라면사기 (Small)을 제외하고 풀었던 문제 중(물론 내 힘이 아니고 구글링...)에서<br/>

제일 어려웠던 문제였던 것 같다. 그도 그럴 것이 플레티넘 1짜리 문제이니..<br/>

과제로 나와서 풀게 됐는데 해시를 이용해보는 문제였다.<br/>

과제로 풀었던 문제를 그대로 제출했는데 시간초과가 났다.<br/>

아이디어가 떠오르지 않아서 구글링을 해서 [본문](https://viyoung.tistory.com/387)을 참고했다.<br/>

구글링을 해서 이해하는데도 한참 걸렸던 것 같다.<br/>

요약하자면 가로 세로까지 2차원 패턴을 고유 값으로 만들어버리는 것이었다.<br/>

그래서 우선, 처음 주어지는 패턴을 고유값으로 해시화 시키고,<br/>

다음에 주어지는 2차원 배열도 각 시작점을 고유값으로 해시화 시키는 것이다.<br/>

그 작업을 해주고 순회를 해주면서 2차원 패턴 해시값이 똑같으면 값을 더해주는 것이다.<br/>

<br/>

그래서 그런 것일까? 나는 참고한 분의 글을 따라치기 하느라고 37, 5831이라는 값을 적었지만,<br/>

사실 2자리, 4자리, 혹은 3자리 든 랜덤한 아무 큰 값을 써도 상관이 없는 것 같다.<br/>

해시 충돌을 방지하기 위한 것이니까!