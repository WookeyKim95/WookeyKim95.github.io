---
layout: post
title:  "[백준] 10216_Count Circle Groups C++"
subtitle:   
date: 2023-09-30 06:35:58 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 10216_Count Circle Groups C++<br/>
<br/>

유니온 파인드를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>
#include <string>
#include <set>
#include <cmath>

using namespace std;

struct Tower
{
	int X;
	int Y;
	int R;
	int parent;
};

int N, T;
vector<Tower> Towers;
set<int> groups;

bool IsNear(int a, int b)
{
	double X1 = Towers[a].X;
	double Y1 = Towers[a].Y;
	double X2 = Towers[b].X;
	double Y2 = Towers[b].Y;
	double R1 = Towers[a].R;
	double R2 = Towers[b].R;

	double Dist = sqrt(pow((X2 - X1), 2) + pow((Y2 - Y1), 2));

	if (Dist <= (double)(R2 + R1))
		return true;
	else
		return false;
}

int Find(int a)
{
	if (Towers[a].parent == a)
		return a;

	return Towers[a].parent = Find(Towers[a].parent);
}


void Union(int x, int y)
{
	if (IsNear(x, y))
	{
		x = Find(x);
		y = Find(y);

        if (x == y)
            return;

		if (x < y)
			Towers[x].parent = y;

		else
			Towers[y].parent = x;
	}
}

int main()
{
	ios::sync_with_stdio(0);
	cout.tie(0);
	cin.tie(0);

	cin >> T;

	for (int tc = 1; tc <= T; ++tc)
	{
		int N;
		cin >> N;

		Towers.clear();
		groups.clear();

		Tower padding;
		Towers.push_back(padding);

		for (int i = 1; i <= N; ++i)
		{
			Tower t;
			cin >> t.X >> t.Y >> t.R;
			t.parent = i;

			Towers.push_back(t);
		}

		for (int i = 1; i <= N-1; ++i)
		{
			for (int j = i+1; j <= N; ++j)
				Union(i, j);
		}

        for (int i = 1; i <= N; ++i)
            Find(i);

		for (size_t i = 1; i < Towers.size(); ++i)
            groups.insert(Towers[i].parent);

		cout << groups.size() << '\n';
	}

	return 0;
}
```

문제에서 거리를 구하는 공식을 무엇을 써야할지 주어지지 않아서 애매했지만<br/>

본래 공식인 아래의 식으로 풀긴 했다.<br/>

```
double Dist = sqrt(pow((X2 - X1), 2) + pow((Y2 - Y1), 2));
```

통신이 가능한 진영끼리는 결국 하나의 그룹으로 묶어줘서,<br/>

묶인 그룹이 몇개인지를 세어주는 작업을 진행하면 되었다.<br/>

이 과정을 위해서 나는 유니온 파인드와 Set을 사용하였다.<br/>

```
bool IsNear(int a, int b)
{
	double X1 = Towers[a].X;
	double Y1 = Towers[a].Y;
	double X2 = Towers[b].X;
	double Y2 = Towers[b].Y;
	double R1 = Towers[a].R;
	double R2 = Towers[b].R;

	double Dist = sqrt(pow((X2 - X1), 2) + pow((Y2 - Y1), 2));

	if (Dist <= (double)(R2 + R1))
		return true;
	else
		return false;
}

int Find(int a)
{
	if (Towers[a].parent == a)
		return a;

	return Towers[a].parent = Find(Towers[a].parent);
}


void Union(int x, int y)
{
	if (IsNear(x, y))
	{
		x = Find(x);
		y = Find(y);

        if (x == y)
            return;

		if (x < y)
			Towers[x].parent = y;

		else
			Towers[y].parent = x;
	}
}
```

이 문제에서 유니온 파인드를 구성하는 코드이다.<br/>

거리 계산을 통해서 근처에 있다고 판단되는 경우에는 유니온 파인드를 진행해준다.<br/>

유니온 파인드란 간단하게 말하면 자신의 최종 부모를 상대방과 같게 해주는 작업을 말한다.<br/>

최종 부모가 같은 노드끼리가 같은 그룹이 되는 것이다.<br/>

```
// 모든 짝에 대해서 진행
for (int i = 1; i <= N-1; ++i)
{
    for (int j = i+1; j <= N; ++j)
        Union(i, j);
}

// 최종 부모를 만들어주는 작업을 진행함
for (int i = 1; i <= N; ++i)
    Find(i);

// set에다가 각자의 최종 부모들을 넣어줌.
for (size_t i = 1; i < Towers.size(); ++i)
    groups.insert(Towers[i].parent);

// set 자료구조 특성상 자동으로 중복은 없어짐.
// 즉, set의 크기가 그룹의 개수임.
cout << groups.size() << '\n';

```
다소 비효율적인 방법이긴 하지만 적군 진영의 숫자 최대 값이 3000이기에<br/>

이 방법을 써도 괜찮을 것이라고 판단되어 일일이 비교를 하는 작업을 진행하였다.<br/>