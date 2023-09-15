---
layout: post
title:  "[백준] 2629_양팔저울 C++"
subtitle:   
date: 2023-09-16 07:15:16 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2629_양팔저울 C++<br/>
<br/>

DP를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>
#include <cmath>

using namespace std;

int N, Q, Max;
bool DP[31][15001] = {false};
int Chu[31] = { 0 };

int m_max(int a, int b)
{
	if (a > b)
		return a;
	else
		return b;
}

void Solve(int i, int w)
{
	if (i > N || DP[i][w])
		return;

	DP[i][w] = true;

	Solve(i + 1, w + Chu[i]);
	Solve(i + 1, abs(w - Chu[i]));
	Solve(i + 1, w);
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N;

	for (int i = 0; i < N; ++i)
	{
		cin >> Chu[i];
		Max += Chu[i];
	}
	memset(DP, false, sizeof(DP));

	Solve(0, 0);

	cin >> Q;

	for (int i = 0; i < Q; ++i)
	{
		int _input;
		cin >> _input;

		if (_input > Max)
			cout << 'N' << ' ';
		else if (DP[N][_input])
			cout << 'Y' << ' ';
		else
			cout << 'N' << ' ';
	}

	return 0;
}

```

우선 이렇게 문제를 입력받을 때마다 결과를 출력하는 문제는<br/>

전처리(?)를 다 하고 나서 문제를 입력받아야 하는 것이 이치.<br/>

이번 문제는 [이분의 글](https://cocoon1787.tistory.com/360)을 참고하였다.<br/>

처음 아이디어는 이렇다.<br/>

주어진 추를 통해서 잴 수 있는 몸무게는 모두 true로 만들어주는 것이다.<br/>

그런데 DP배열을 어떻게 설계해야할지 아이디어가 떠오르지 않아서 구글링을 했다.<br/>

배열은 이런것이다.<br/>

```
DP[추의 개수][무게]
```

일단은 추의 개수가 M일 때 무게를 이룰 수 있다면 M+1부터 N까지의 같은 무게 배열도 true로 해준다.<br/>

그래야 개수에 구애받지 않고 DP[N][_input]의 값을 잘 출력할 수 있으니까.<br/>

그리고 다음으로는 어떻게 재귀를 구성할까?<br/>

```
Solve(i + 1, w + Chu[i]);
Solve(i + 1, abs(w - Chu[i]));
Solve(i + 1, w);
```

순서대로 아래와 같다. 
- 추의 개수를 증가시키고 다음 추를 달아주는 경우
- 뺄셈을 이용, 추의 무게의 차이를 이용해서 무게를 재는 경우
- 같은 무게이며, 채워주는 목적으로 i+1만 해주는 경우

이런 구조를 통해서 Top-Down DP를 통해서 풀어주는 문제였다.<br/>