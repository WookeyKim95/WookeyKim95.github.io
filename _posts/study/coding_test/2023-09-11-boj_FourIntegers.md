---
layout: post
title:  "[백준] 7453_합이 0인 네 정수 C++"
subtitle:   
date: 2023-09-11 06:59:58 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 7453_합이 0인 네 정수 C++<br/>
<br/>

투 포인터를 업그레이드 해서 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

#define MAX 4001

using namespace std;
typedef long long ll;

int N;
int Arr[4][MAX], AB[MAX * MAX], CD[MAX * MAX];

int main()
{
	ios::sync_with_stdio(0);
	cout.tie(0);
	cin.tie(0);

	cin >> N;

	for (int i = 0; i < N; ++i)
	{
		for (int j = 0; j < 4; ++j)
			cin >> Arr[j][i];
	}

	int Idx = 0;
	for (int i = 0; i < N; ++i)
	{
		for (int j = 0; j < N; ++j)
		{
			AB[Idx] = Arr[0][i] + Arr[1][j];
			CD[Idx] = Arr[2][i] + Arr[3][j];
			Idx++;
		}
	}

	sort(AB, AB + Idx);
	sort(CD, CD + Idx);

	int Idx_AB = 0;
	int Idx_CD = Idx - 1;
	ll Ans = 0;

	while (Idx_AB < Idx && Idx_CD >= 0)
	{
		if (AB[Idx_AB] + CD[Idx_CD] == 0)
		{
			int Orig = Idx_AB;
			ll SameAB = 0, SameCD = 0;

			while (AB[Idx_AB] + CD[Idx_CD] == 0)
			{
				// Idx_AB  배열 수색
				if (Idx_AB >= Idx)
					break;
				SameAB++;
				Idx_AB++;
			}

			while (AB[Orig] + CD[Idx_CD] == 0)
			{
				// Idx_CD 배열 수색
				if (Idx_CD < 0)
					break;
				SameCD++;
				Idx_CD--;
			}

			// SameAB와 SameCD를 곱해서 Ans에 더하기.
			Ans += SameAB * SameCD;
		}

		else if (AB[Idx_AB] + CD[Idx_CD] < 0)
			Idx_AB++;
		else
			Idx_CD--;
	}

	cout << Ans;

	return 0;
}
```

이 문제를 접했을 때 든 생각은 네 정수의 합을 구한다?<br/>

그렇다면 투 포인터도 아니고 4 포인터를 사용해야 할 거 같은 느낌!<br/>

그런데 사실상 4 포인터를 사용할 수 있는 알고리즘은 없으니 고민에 빠졌었다.<br/>

아이디어가 떠오르지 않아 구글링을 하고 [이분의 글](https://yabmoons.tistory.com/290)을 참고하였다.<br/>

오호라! 네 그룹 중에서 두 개씩 그룹을 짓고,<br/>

2중 for문을 이용해서 각각 그룹에서 나올 수 있는 합을 배열에 담고 정렬한 다음,<br/>

그 합이 0인 경우를 투 포인터를 이용해서 찾는 아이디어였다.<br/>

다시 설명하자면, A배열, B배열을 AB 그룹으로 묶고 여기서 나올 수 있는 합을 배열에 담는다.<br>

반대로 C배열, D배열을 CD 그룹으로 묶고 여기서 나올 수 있는 합을 배열에 담는다.<br/>

여기서 합을 투 포인터로 담는 것이다.<br/>

4포인터를 사용하거나, 4중 for문을 사용하기는 사실상 어렵기 때문에<br/>

2중 포문 2개를 돌리고 나머지 연산은 2 포인터로 계산하는 아이디어인 것이다!<br/>

```
int Orig = Idx_AB;
ll SameAB = 0, SameCD = 0;

while (AB[Idx_AB] + CD[Idx_CD] == 0)
{
	// Idx_AB  배열 수색
	if (Idx_AB >= Idx)
		break;
	SameAB++;
	Idx_AB++;
}

while (AB[Orig] + CD[Idx_CD] == 0)
{
	// Idx_CD 배열 수색
	if (Idx_CD < 0)
		break;
	SameCD++;
	Idx_CD--;
}

// SameAB와 SameCD를 곱해서 Ans에 더하기.
Ans += SameAB * SameCD;
```

그래서 Orig에다가 원래 가리키고 있던 Idx_AB를 지정해주는 것이고,<br/>

SameAB와 SameCD 에다가 조건이 맞는 인덱스의 개수를 저장해주는 것이다.<br/>

다만, 두 배열의 합 + 두 배열의 합 구조이기 때문에<br>

Ans에다가는 SameAB + SameCD가 아닌

SameAB * SameCD을 더해준다.<br/>