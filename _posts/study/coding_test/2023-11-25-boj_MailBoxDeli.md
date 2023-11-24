---
layout: post
title:  "[백준] 5972_택배 배송 C++"
subtitle:   
date: 2023-11-25 07:08:18 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 5972_택배 배송 C++<br/>
<br/>

기초 다익스트라 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

#define MAX 99999999

using namespace std;

int N, M;

struct Node
{
    vector<pair<int, int>> link;
    int Accul = MAX;
};

vector<Node> Nodes;

int Dijkstra(int S, int E)
{
    priority_queue<pair<int, int>> pq;
    pq.push({0, S});

    while (!pq.empty())
    {
        int CurNode = pq.top().second;
        int CurCost = -pq.top().first;
        pq.pop();

        if (CurCost > Nodes[CurNode].Accul)
            continue;
        
        for (size_t i = 0; i < Nodes[CurNode].link.size(); ++i)
        {
            int NextCost = CurCost + Nodes[CurNode].link[i].second;
            int NextNode = Nodes[CurNode].link[i].first;

            if (NextCost < Nodes[NextNode].Accul)
            {
                Nodes[NextNode].Accul = NextCost;
                pq.push({-NextCost, NextNode});
            }
        }
    }

    return Nodes[E].Accul;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i <= N; ++i)
    {
        Node n;
        Nodes.push_back(n);
    }

    for (int i = 0; i < M; ++i)
    {
        int S, E, C;
        cin >> S >> E >> C;

        Nodes[S].link.push_back({E, C});
        Nodes[E].link.push_back({S, C});
    }

    int ans = Dijkstra(1, N);

    cout << ans;

    return 0;
}
```
<br/>

다익스트라 알고리즘을 까먹을까봐 풀어보았다.<br/>

이 문제는 다익스트라 알고리즘 골격을 그대로 따라가면 풀 수 있는 문제였다.<br/>

게다가, 모든 노드는 반드시 연결되어있기 때문에 부담을 가질 필요도 없었을 것이다.<br/>

오랜만에 다익스트라를 만져서 그런지 구현속도는 예전보다 느려진 것 같다.<br/>

연습 좀 해야겠다.