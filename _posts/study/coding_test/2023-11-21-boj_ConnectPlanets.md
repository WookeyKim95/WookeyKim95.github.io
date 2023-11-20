---
layout: post
title:  "[백준] 16398_행성 연결 C++"
subtitle:   
date: 2023-11-21 07:19:28 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 16398_행성 연결 C++<br/>
<br/>

MST를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, pair<int, int>> piii;

int N;
vector<piii> Edges;
vector<int> Parents;

int FindParent(int a)
{
    if (Parents[a] == a)
        return a;
    else
        return Parents[a] = FindParent(Parents[a]);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    Parents.push_back(0);

    for (int i = 1; i <= N; ++i)
        Parents.push_back(i);

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
        {
            int cost;
            cin >> cost;

            if (j >= i)
                continue;

            Edges.push_back({cost, {i, j}});
        }
    }

    sort(Edges.begin(), Edges.end());

    long long Sum = 0;
    for (size_t i = 0; i < Edges.size(); ++i)
    {
        int S, E;

        S = Edges[i].second.first;
        E = Edges[i].second.second;

        int A = FindParent(S);
        int B = FindParent(E);

        if (A != B)
        {
            Parents[A] = B;
            Sum += Edges[i].first;
        }
    }

    cout << Sum;

    return 0;
}
```
<br/>

전형적인 MST 문제이다.<br/>

다만 주의할 점은 입력을 인접 행렬로 받는 다는 점을 주의하면 되겠다.<br/>

나 같은 경우는 정렬시간이나 순회시간을 줄이기 위해, 즉, Edges의 크기를 줄이기 위해서<br/>

중복되는 경로나 Cost 값이 0 인 경우는 넣지 않았다.<br/>

```
if (j >= i)
    continue;
```

그리고 Cost의 최대값은 1억이기 떄문에 long long을 써주지 않으면 오답처리되니 주의하자.