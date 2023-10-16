---
layout: post
title:  "[백준] 2470_두 용액 C++"
subtitle:   
date: 2023-10-17 03:24:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2470_두 용액 C++<br/>
<br/>

이분 탐색 및 정렬을 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>

#define MAX 2147483647

using namespace std;

int main()
{
	int N;
	vector<int> v;
	cin >> N;

	for (int i = 0; i < N; ++i)
	{
		int _input;
		cin >> _input;
		v.push_back(_input);
	}

	sort(v.begin(), v.end());

	int Diff = MAX;

	int Left = 0;
	int Right = N-1;

	int Ans[2];

	while (Left < Right)
	{
		int Sum = v[Left] + v[Right];

		if (abs(Sum) < Diff)
		{
			Ans[0] = v[Left];
			Ans[1] = v[Right];
			Diff = abs(Sum);
		}

		if (Sum < 0)
			Left++;
		else
			Right--;
	}

	cout << Ans[0] << ' ' << Ans[1];

	return 0;
}
```

정렬을 한 뒤에 투 포인터를 가지고 이분탐색을 하는 방법을 이용하면 된다.<br/>

한쪽 포인터는 맨 왼쪽 값에, 다른 포인터는 맨 오른쪽 값에 둔다.<br/>

이렇게 해서 합칠 때마다 차이 값이 작은 것으로 값을 갱신해주면 자연스럽게<br/>

Ans[0]와 Ans[1]에 값이 저장된다. 그 값을 출력하면 된다.<br/>