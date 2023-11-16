---
layout: post
title:  "[백준] 6497_전력난 C++"
subtitle:   
date: 2023-11-17 06:41:43 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 6497_전력난 C++<br/>
<br/>

최소 스패닝 트리를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, pair<int, int>> pii;

int N, M;
vector<pii> Edges;
vector<int> Parent;

int FindParent(int a)
{
    if (Parent[a] == a)
        return a;
    return Parent[a] = FindParent(Parent[a]);
}

bool SameParent(int a, int b)
{
    int A = FindParent(a);
    int B = FindParent(b);

    if (A == B)
        return true;
    else
        return false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    while (true)
    {
        cin >> N >> M;
        
        if (N == 0 && M == 0)
            break;

        Parent.clear();
        Edges.clear();

        Parent.resize(N+1);

        int Total = 0, Result = 0, Ans;

        for (int i = 1; i <= N; ++i)
            Parent[i] = i;

        for (int i = 0; i < M; ++i)
        {
            int S, E, C;
            cin >> S >> E >> C;

            Edges.push_back({C, {S, E}});
            Total += C;
        }

        sort(Edges.begin(), Edges.end());

        for (size_t i = 0; i < Edges.size(); ++i)
        {
            int Cost = Edges[i].first;
            int Start = Edges[i].second.first;
            int End = Edges[i].second.second;

            if (!SameParent(Start, End))
            {
                int A = FindParent(Start);
                int B = FindParent(End);

                Parent[A] = B;
                Result += Cost;
            }
        }

        Ans = Total - Result;

        cout << Ans << '\n';
    }

    return 0;
}
```
<br/>

최소 스패닝 트리를 활용해서 풀어보는 문제였다.<br/>

나는 크루스칼 알고리즘을 사용해서 풀었다.<br/>

크루스칼 알고리즘은 유니온 파인드를 기반으로 최소 스패닝 트리를 구하는 문제이다.<br/>

각 노드가 연결되어 있다면 냅두고,<br/>

연결되어 있지 않다면 연결 비용을 추가하면서 연결을 해주는 것이다.<br/>

최소 스패닝 트리를 구하는 방법에는 프림 알고리즘도 있다.<br/>

이 문제를 프림 알고리즘으로 푸는 방법은 [여기 이쪽에](https://yabmoons.tistory.com/402).<br/>