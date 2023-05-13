---
layout: post
title:  "[백준] 1022_소용돌이 예쁘게 출력하기 C++"
subtitle:   
date: 2023-05-13 10:45:18 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1022_소용돌이 예쁘게 출력하기 C++<br/>
<br/>

오랜만에 풀어본 빡구현 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cmath>

using namespace std;

int r1, r2, c1, c2;
int map[50][5] = { 0, };

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void Write()
{
	int MAX = m_max(m_max(abs(r1), abs(c1)), m_max(abs(r2), abs(c2)));

	int val = 1;
	if (0 >= r1 && 0 >= c1 && 0 <= r2 && 0 <= c2)
		map[0 - r1][0 - c1] = val;

	int cnt = 0;
	int x = 0, y = 0;

	for (int i = 1; i <= MAX + 1; ++i)
    {
		for (int j = 1; j <= 1 + cnt * 2; ++j)
        {
            // 오른쪽 방향
			val++;
			x++;
			if (y >= r1 && x >= c1 && y <= r2 && x <= c2)
				map[y - r1][x - c1] = val;
		}
		for (int j = 1; j <= 1 + cnt * 2; ++j)
        {
            // 위쪽 방향
			val++;
			y--;
			if (y >= r1 && x >= c1 && y <= r2 && x <= c2)
				map[y - r1][x - c1] = val;
		}
		for (int j = 1; j <= 2 + cnt * 2; ++j)
        {
            // 왼쪽 방향
			val++;
			x--;
			if (y >= r1 && x >= c1 && y <= r2 && x <= c2)
				map[y - r1][x - c1] = val;
		}
		for (int j = 1; j <= 2 + cnt * 2; ++j)
        {
			val++;
			y++;
			if (y >= r1 && x >= c1 && y <= r2 && x <= c2)
				map[y - r1][x - c1] = val;
		}

		cnt++;
	}
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> r1 >> c1 >> r2 >> c2;

    Write();

    int h = abs(r2 - r1);
	int w = abs(c2 - c1);

	int MAX = 0;
	
	for (int i = 0; i <= h; i++)
    {
		for (int j = 0; j <= w; j++)
			MAX = m_max(MAX, map[i][j]);
	}
	
	int max_digit = 0;
	for (int i = 1; i <= MAX; i *= 10)
		max_digit++;

	for (int i = 0; i <= h; i++)
    {
		for (int j = 0; j <= w; j++)
        {
			int current_digit = 0;
			for (int k = 1; k <= map[i][j]; k *= 10)
				current_digit++;

			for (int k = current_digit; k < max_digit; k++)
				cout << ' ';

			cout << map[i][j] << ' ';
		}
		cout << '\n';
	}

    return 0;
}
```

<br/>

수학적 규칙을 찾고 구현을 하는 문제이다.<br/>

하지만 모두 저장한 다음에 출력을 하려고 했다는 실수를 범했다는 점과<br/>

예쁘게 출력해야 한다는 점이 아이디어가 안 떠올라서 (그리고 솔직히 당시에 조금 귀찮아서)<br/>

[참고링크](https://gusdnr69.tistory.com/93)를 참조하여 문제를 풀었다.<br/>

정말로 다행스럽게도, 출력해야 하는 열의 수는 최대 4개, 행의 수는 49개라는 점이다.<br/>

더 많이 출력해야 했으면 구현하기 힘들었을지도 몰라..<br/>

값을 모두 map 배열에 저장해서 메모리 초과를 일으키지 말고<br/>

val 값을 계속 더해주는 과정에서 범위 안에 들어가는 것만 map에 저장을 하도록 구현하자.<br/>

그리고 출력하는 과정에서 줄 맞춤 해주는 부분도 문제다.<br/>

최대 자리수 까지 있는지(max_digit) 구하고 차이 만큼 띄어쓰기를 하면 된다.<br/>

```
int current_digit = 0;
for (int k = 1; k <= map[i][j]; k *= 10)
    current_digit++;

for (int k = current_digit; k < max_digit; k++)
    cout << ' ';
```

이렇게 이해하고 구현하였다.<br/>
