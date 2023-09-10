---
layout: post
title:  "[백준] 2461_대표 선수 C++"
subtitle:   
date: 2023-09-10 06:58:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2461_대표 선수 C++<br/>
<br/>

투 포인터를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <map>

#define MAX 2147483647

using namespace std;

int N, M;
vector<pair<int, int>> v;
map<int, int> Check;

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
	cout.tie(0);
	cin.tie(0);

	cin >> N >> M;

	for (int i = 1; i <= N; ++i)
	{
		for (int j = 0; j < M; ++j)
		{
			int _input;
			cin >> _input;
			v.push_back({ _input, i });
		}
	}

	sort(v.begin(), v.end());

	int Left = 0;
	int Right = 0;
	int Ans = MAX;

	while (true)
	{
		if (Check.size() == N)
		{
			if (v[Right - 1].second != v[Left].second)
				Ans = m_min(Ans, v[Right - 1].first - v[Left].first);

			Check[v[Left].second]--;

			if (Check[v[Left].second] == 0)
				Check.erase(v[Left].second);
			Left++;
		}

		else
		{
			if (Right >= v.size())
				break;

			if (Check.count(v[Right].second) == 0)
				Check.insert({ v[Right].second, 1 });

			else
				Check[v[Right].second]++;

			Right++;
		}
	}

	cout << Ans;

	return 0;
}
```

세 반을 통틀어서 제일 작은 값의 반과 제일 큰 값의 반이 다를 경우 나는 차이 중

가장 작은 값을 조사해주면 되는 것이었다.<br/>

투 포인터를 이용해서 푸는 문제였다.<br/>

우선, int 쌍으로 (능력치, 반) 순서대로 입력을 받았다.<br/>

벡터의 정렬은 첫번째 값을 기준으로 정렬이 되기 때문에<br/>

(능력치, 반) 순서대로 입력을 받고 오름차순으로 정렬을 시켜주었다.<br/>

그리고 벡터에서 값을 뽑아서 map( Key : 반, Value : 값 해시)에 넣어준다.<br/>

이 때, 규칙은 아래와 같다.

- 해시에 모든 반 학생이 있을 경우
	+ Right 포인터와 Left 포인터에 있는 학생이 다른 반일 때 Ans 갱신
	+ Left 포인터를 옮겨줌.

- 해시에 모든 반 학생이 있지 않을 경우
	+ 오른쪽 포인터에 있는 학생의 수치를 해시에 넣어줌.
	+ Right 포인터를 옮겨줌.

이 과정을 거치면서 차이 값을 조사를 해주면 된다.<br/>