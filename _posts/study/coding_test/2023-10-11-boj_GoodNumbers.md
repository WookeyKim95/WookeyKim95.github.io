---
layout: post
title:  "[백준] 2661_좋은수열 C++"
subtitle:   
date: 2023-10-11 07:09:59 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2661_좋은수열 C++<br/>
<br/>

백트래킹을 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>

using namespace std;

int arr[80] = { 0 };
int N;
int min_arr[80] = { 0 };
int save[80][80] = { 0 };

bool isGood(int a, int idx)
{
	for (int i = 0; i < a / 2; ++i)
	{
		if (arr[idx - i] != arr[idx - (a / 2) - i])
			return true;
	}
	return false;
}

void backtrack(int cnt)
{
	if (cnt == N)
	{
		for (int i = 0; i < N; ++i)
			cout << arr[i];
		exit(0);
	}
	for (int i = 1; i <= 3; ++i)
	{
		arr[cnt] = i;
		bool flag = true;
		for (int j = 2; j <= cnt+1; j += 2)
		{
			if (j % 2 == 0)
			{
				if (!isGood(j, cnt))
					flag = false;
			}
		}

		if (flag)
			backtrack(cnt + 1);
	}
}

int main()
{
	cin >> N;
	backtrack(0);

	return 0;
}
```

[문제 링크](https://www.acmicpc.net/problem/2661)

최대 N이 다행히 80으로 낮아서 백트래킹으로 풀어도 된다고 생각했다.<br/>

사실.. 풀이는 그렇게 자세히 적을 것은 없는 것 같다.<br/>

인덱스 0부터 시작해서 1, 2, 3을 일일이 넣어준 다음에<br/>

좋은 수열인지 아닌지를 판단하면 되는 것이다.<br/>