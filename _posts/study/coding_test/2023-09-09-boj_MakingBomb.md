---
layout: post
title:  "[백준] 2977_폭탄제조 C++"
subtitle:   
date: 2023-09-09 06:56:30 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2977_폭탄제조 C++<br/>
<br/>

이분 탐색을 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <tuple>

#define MAX 2147483640

using namespace std;

int N, M;
vector<tuple<int, int, int, int, int, int>> Bomb;

int m_max(int a, int b)
{
	if (a > b)
		return a;
	else
		return b;
}

int m_min(int a, int b)
{
	if (a < b)
		return a;
	else
		return b;
}

bool IsPossible(int a)
{
	int CurMoney = M;

	for (int i = 0; i < N; ++i)
	{
		int Money = MAX;
		int Need = a * get<0>(Bomb[i]) - get<1>(Bomb[i]);

		int Range = Need / get<2>(Bomb[i]);

		if (Need % get<2>(Bomb[i]) > 0)
			Range++;

		for (int j = 0; j <= Range; ++j)
		{
			int LeftNeed = Need - get<2>(Bomb[i]) * j;

			int Bigger;
			if (LeftNeed <= 0)
				Bigger = 0;
			else
				Bigger = LeftNeed / get<4>(Bomb[i]);

			if (LeftNeed > 0 && (LeftNeed % get<4>(Bomb[i])) > 0)
				Bigger++;

			Money = m_min(Money, j * get<3>(Bomb[i]) + Bigger * get<5>(Bomb[i]));
		}

		if (Range >= 0)
			CurMoney -= Money;

		if (CurMoney < 0)
			return false;
	}

	if (CurMoney < 0)
		return false;
	else
		return true;
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N;
	cin >> M;

	for (int i = 0; i < N; ++i)
	{
		int a, b, c, d, e, f;
		cin >> a >> b >> c >> d >> e >> f;
		Bomb.push_back({ a, b, c, d, e, f });
	}

	int Left = 0;
	int Right = 0;
	int ans = -1;

	for (int i = 0; i <= M / get<3>(Bomb[0]); ++i)
	{
		int LeftMoney = M - get<3>(Bomb[0]) * i;
		int j = LeftMoney / get<5>(Bomb[0]);

		int Count = i * get<2>(Bomb[0]) + j * get<4>(Bomb[0]);
		Right = m_max(Right, (Count + get<1>(Bomb[0]))/ get<0>(Bomb[0]));
	}

	while (Left <= Right)
	{
		int Mid = (Left + Right) / 2;

		if (IsPossible(Mid))
		{
			Left = Mid + 1;
			ans = Mid;
		}

		else
			Right = Mid - 1;
	}
	
	cout << ans;

	return 0;
}
```

이분 탐색 문제를 푸는데 있어서 제일 중요한 것은<br/>

목표치를 Mid로 설정하고 새로운 Mid 값을 어떻게 설정할 것인지<br/>

조건을 구현하는 것이다. 이번 문제에서도 그 점이 까다로웠던 것 같다.<br/>

특히, Mid가 움직이는 조건을 구성하는 과정이 까다로웠다.<br/>

사실, 아래 코드가 없어도 Right를 큰수로 해주면 되긴 하지만 최대한 몇개를 만들 수 있는지 조사를 해보고자 했다.<br/>

```
for (int i = 0; i <= M / get<3>(Bomb[0]); ++i)
{
    int LeftMoney = M - get<3>(Bomb[0]) * i;
    int j = LeftMoney / get<5>(Bomb[0]);

    int Count = i * get<2>(Bomb[0]) + j * get<4>(Bomb[0]);
    Right = m_max(Right, (Count + get<1>(Bomb[0]))/ get<0>(Bomb[0]));
}
```

그리고 주어진 돈으로 만들어야하는 폭탄을 만들 수 있는지 여부이다.<br/>

```
bool IsPossible(int a)
{
	int CurMoney = M;

	for (int i = 0; i < N; ++i)
	{
		int Money = MAX;
		int Need = a * get<0>(Bomb[i]) - get<1>(Bomb[i]);

		int Range = Need / get<2>(Bomb[i]);

		if (Need % get<2>(Bomb[i]) > 0)
			Range++;

		for (int j = 0; j <= Range; ++j)
		{
			int LeftNeed = Need - get<2>(Bomb[i]) * j;

			int Bigger;
			if (LeftNeed <= 0)
				Bigger = 0;
			else
				Bigger = LeftNeed / get<4>(Bomb[i]);

			if (LeftNeed > 0 && (LeftNeed % get<4>(Bomb[i])) > 0)
				Bigger++;

			Money = m_min(Money, j * get<3>(Bomb[i]) + Bigger * get<5>(Bomb[i]));
		}

		if (Range >= 0)
			CurMoney -= Money;

		if (CurMoney < 0)
			return false;
	}

	if (CurMoney < 0)
		return false;
	else
		return true;
}
```

만들어야하는 작은 폭탄과 큰 폭탄의 개수를 구해준 다음,<br/>

그 범위 내 만큼 for문을 돌려 소비되는 Money를 계산해주었다.<br/>

다만, Range가 음수가 나오는 경우가 있었다.<br/>

이 경우에는 Money를 계산해주지 않아서 오류가 나서 Range가 0 이상일 때 돈을 계산하도록 구현하였다.<br/>

코드를 보면서 느낀 것은 tuple을 써서 가독성이 굉장히 떨어져보인다.<br/>

구조체로 만들어서 풀었으면 더 좋았을 것 같다.<br/>