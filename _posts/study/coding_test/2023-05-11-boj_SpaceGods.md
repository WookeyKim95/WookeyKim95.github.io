---
layout: post
title:  "[백준] 1774_우주신과의 교감 C++"
subtitle:   
date: 2023-05-11 10:19:25 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1774_우주신과의 교감 C++<br/>
<br/>

유니온 파인드와 크루스칼 알고리즘을 사용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>
#include <tuple>
#include <algorithm>

using namespace std;

struct God
{
    int X;
    int Y;
    int parent;
};

int N, M;
double Dist = 0;
vector<God> Gods;
vector<tuple<double, int, int>> DistList;

int Parentfind(int u)
{
	if (Gods[u].parent == u)
        return u;
	else
        return Gods[u].parent = Parentfind(Gods[u].parent);
}

bool SameParent(int u, int v)
{
	u = Parentfind(u);
	v = Parentfind(v);
	
	if (u == v)
		return true;
	else
		return false;
}

void UnionNode(int u, int v)
{
	u = Parentfind(u);
	v = Parentfind(v);

	Gods[u].parent = v;
}

double DistCal(int a, int a2, int b, int b2)
{
	double da = (double)(a - a2);
	double db = (double)(b - b2);
    return sqrt(pow(da, 2) + pow(db, 2));
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

    cin >> N >> M;

    God Padding;
    Gods.push_back(Padding);

    for (int i = 1; i <= N; ++i)
    {
        God SpaceGod;
        cin >> SpaceGod.X >> SpaceGod.Y;
        SpaceGod.parent = i;
        Gods.push_back(SpaceGod);
    }

	for (int i = 1; i <= M; ++i)
	{
        int u, v;
		cin >> u >> v;
		UnionNode(u, v);
	}
	
	for (int i = 1; i <= N - 1; ++i)
	{
		for (int j = i + 1; j <= N; ++j)
		{
			double r = DistCal(Gods[i].X, Gods[j].X, Gods[i].Y, Gods[j].Y);
			DistList.push_back({ r, i, j });
		}
	}

	sort(DistList.begin(), DistList.end());

	for (size_t i = 0; i < DistList.size(); ++i)
	{
        int x, y, d;
		x = get<1>(DistList[i]);
		y = get<2>(DistList[i]);
		d = get<0>(DistList[i]);
		if (!SameParent(x, y))
		{
			UnionNode(x, y);
			Dist += d;
		}
	}



    return 0;
}
```

<br/>

아직 크루스칼 알고리즘을 사용하기 익숙하지 않아서 [참고링크](https://cocoon1787.tistory.com/417)를 참고하였다.<br/>

이번 문제는 크루스칼 알고리즘을 사용하는 MST문제였다.<br/>

미리 연결되어 있는 노드를 찾기 위해서 유니온 파인드를 사용하였다.<br/>

그리고 유니온 파인드를 사용한 뒤에도 아직 연결되어 있지 않은 노드는<br/>

연결을 하면서 거리를 계산해야하는 노드이다.<br/>

이 때, 아직 연결되어 있지 않다는 것을 판단하기 위해서<br/>

유니온 파인드에서 부모가 같은 경우에 false를 반환하도록 구현하였다.<br/>

다를 경우에만 true를 반환하면서 부모를 같게 만드는 것이다.<br/>

그리고 이 과정이 곧 새로 연결을 하는 것이므로 연결을 하면서 거리를 계산하도록 하였다.<br/>

그리고 이 문제를 풀고 참고링크를 참고하면서 tuple을 처음으로 알게 되었다.<br/>

더 이상 아래와 같이 안써도 되겠다. 야호!<br/>

```
pair<int, pair<int, int>>
```
<br/>

### 주의사항<br/>
<br/>

반올림에 주의해야한다.<br/>

cout가 아니라 printf를 써야했다..<br/>

```
// 오답

    cout << fixed;
    cout.precision(2);
    cout << Dist;

// 정답
	printf("%.2lf", Dist);

```

이것 때문에 1시간을 낭비했다.<br/>

무슨 차이인고 챗 GPT에게 물어보니,<br/>

```
네, 실수를 출력할 때 반올림하는 방법에 차이가 있습니다.

cout에서 cout.precision(2)는 출력할 실수를 소수점 둘째 자리까지만 표시하도록 설정하는 것입니다. 이 때, 기본적으로는 반올림하지 않고 버림(rounding mode)을 사용합니다. 즉, 출력하려는 실수가 1.235일 경우 1.23으로 출력됩니다.

반면에 printf("%.2lf", Dist)는 printf 함수를 사용하여 소수점 이하 두 자리까지 반올림하여 출력하는 것입니다. 따라서 출력하려는 실수가 1.235일 경우 1.24로 출력됩니다.

즉, cout에서는 기본적으로 버림을 사용하고, printf에서는 기본적으로 반올림을 사용한다는 차이가 있습니다.
```

반올림 차이였다.<br/>

cout는 객체, printf는 함수라는 소소한 차이도 알 수 있었다.<br/>

그렇다면 실험을 해보자.<Br/>

```
	cout.precision(2);
	cout << fixed;
	cout << Dist;
```

이 코드는 정답처리되었을까?

답은 YES다. precision을 통해서 소수 3째자리에서 반올림하고,<br/>

fixed를 통해 3째자리 부터는 버리는 역할을 진행해서<br/>

```
printf("%.2lf", Dist);
```

와 같은 효과를 낼 수 있는 것이다.<br/>

앞으로 저 순서로 써야겠다.<br/>