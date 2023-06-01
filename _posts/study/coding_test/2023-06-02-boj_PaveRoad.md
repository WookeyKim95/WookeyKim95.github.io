---
layout: post
title:  "[백준] 1162_도로포장 C++"
subtitle:   
date: 2023-05-22 08:26:40 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1162_도로포장 C++<br/>
<br/>

다익스트라 알고리즘에다가 DP를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>
#include <cstring>

#define INF INT64_MAX

typedef long long ll;

using namespace std;

struct City
{
    int num;
    vector<pair<int, int>> link;

    City(int _num) : num(_num) {};
};

struct Road
{
    ll cost;
    ll idx;
    ll paved;

    bool operator < (const Road& a) const
    {
        return cost > a.cost;
    }
};

int N, M, K;
vector<City> Cities;

ll m_min(ll a, ll b)
{
    if (a < b)
        return a;
    else
        return b;
}

ll dijkstra(int start)
{
    ll dist[10001][21];
    memset(dist, 0x3f, sizeof(dist));

    priority_queue<Road> pq;
    pq.push({0, start, 0});
    dist[start][0] = 0;

    while (!pq.empty())
    {
        ll CurNode = pq.top().idx;
        ll CurCost = pq.top().cost;
        ll paved = pq.top().paved;
        pq.pop();

        if (dist[CurNode][paved] < CurCost)
            continue;
        
        for (size_t i = 0; i < Cities[CurNode].link.size(); ++i)
        {
            ll NextNode = Cities[CurNode].link[i].first;
            ll NextCost = CurCost + Cities[CurNode].link[i].second;

            if (NextCost < dist[NextNode][paved])
            {
                pq.push({NextCost, NextNode, paved});
                dist[NextNode][paved] = NextCost;
            }

            if (paved < K && CurCost < dist[NextNode][paved+1])
            {
                pq.push({CurCost, NextNode, paved+1});
                dist[NextNode][paved+1] = CurCost;
            }
        }
    }

    ll min_cost = INF;
    for (int i = 0; i <= K; ++i)

        min_cost = m_min(min_cost, dist[N][i]);
        
    
    return min_cost;
}

int main()
{
    cin >> N >> M >> K;

    City padding(0);
    Cities.push_back(padding);

    for (int i = 1; i <= N; ++i)
    {
        City C(i);
        Cities.push_back(C);
    }

    for (int i = 0; i < M; ++i)
    {
        int S, E, T;
        cin >> S >> E >> T;

        Cities[S].link.push_back({E, T});
        Cities[E].link.push_back({S, T});
    }

    cout << dijkstra(1);

    return 0;
}
```
<br/>

거리 계산에 다익스트라 알고리즘을 사용하고,<br/>

각 도로를 포장했을 경우를 별도로 산정하기 위해서<br/>

DP를 사용하는 것으로 보인다.<br/>

그래서 이번에는 도로 객체와 도시 객체를 따로 만들어서<br/>

다익스트라 알고리즘이 돌아가도록 구현하였다.<br/>

구체적인 풀이는 [이분의 글](https://everenew.tistory.com/155)을 참고하였다.<Br/>

