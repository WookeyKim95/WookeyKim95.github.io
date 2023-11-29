---
layout: post
title:  "[백준] 13424_비밀 모임 C++"
subtitle:   
date: 2023-11-30 07:35:15 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 13424_비밀 모임 C++<br/>
<br/>

다익스트라 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

#define MAX 99999999

using namespace std;

struct Node
{
    int Accul = MAX;
    vector<pair<int, int>> link;
};

int N, M;
vector<Node> Nodes;
vector<vector<int>> Distances;
vector<int> Where;

vector<int> Dijkstra(int a)
{
    for (size_t i = 1; i <= Nodes.size(); ++i)
        Nodes[i].Accul = MAX;

    priority_queue<pair<int, int>> pq;
    pq.push({0, a});
    Nodes[a].Accul = 0;

    while (!pq.empty())
    {
        int CurNode = pq.top().second;
        int CurAccul = -pq.top().first;
        pq.pop();

        if (CurAccul > Nodes[CurNode].Accul)
            continue;

        for (size_t i = 0; i < Nodes[CurNode].link.size(); ++i)
        {
            int NextNode = Nodes[CurNode].link[i].second;
            int Cost = Nodes[CurNode].link[i].first;

            if (Nodes[NextNode].Accul > CurAccul + Cost)
            {
                Nodes[NextNode].Accul = CurAccul + Cost;
                pq.push({-(CurAccul + Cost), NextNode});
            }
        }
    }

    vector<int> row;
    for (int i = 1; i <= N; ++i)
        row.push_back(Nodes[i].Accul);

    return row;
}

void Init()
{
    cin >> N >> M;

    Nodes.clear();
    Where.clear();
    Distances.clear();

    for (int i = 0; i <= N; ++i)
    {
        Node n;
        Nodes.push_back(n);
    }

    for (int i = 0; i < M; ++i)
    {
        int S, E, C;
        cin >> S >> E >> C;

        Nodes[S].link.push_back({C, E});
        Nodes[E].link.push_back({C, S});
    }

    int W;
    cin >> W;

    for (int i = 0; i < W; ++i)
    {
        int _input;
        cin >> _input;
        Where.push_back(_input);
    }
}

int vectorSum(vector<int> a)
{
    int ret = 0;
    for (size_t i = 0; i < a.size(); ++i)
        ret += a[i];
    return ret;
}

void Solution()
{
    for (size_t i = 0; i < Where.size(); ++i)
        Distances.push_back(Dijkstra(Where[i]));

    int Min = MAX;
    int idx = 0;
    for (size_t i = 0; i < Distances[0].size(); ++i)
    {
        int comp = 0;
        for (size_t j = 0; j < Distances.size(); ++j)
            comp += Distances[j][i];

        if (Min > comp)
        {
            idx = i + 1;
            Min = comp;
        }
    }

    cout << idx << '\n';
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int T;
    cin >> T;

    for (int tc = 0; tc < T; ++tc)
    {
        Init();
        Solution();
    }

    return 0;
}
```

아무래도 출제자가 해리포터와 불사조 기사단을 감명깊게 보셨나보다 ㅋㅋ<br/>

아무튼 이번 문제는 다익스트라 알고리즘을 활용하면서<br/>

각 목적지까지 거리를 어떻게 구하냐가 관건이었다.<br/>

나는 그냥

```
vector<vector<int>> Distances;
```
여기다가 각 노드 까지의 거리를 저장했다.<br/>

즉, 출발지점은 Where에 들어있는 인덱스에서 출발해서 각 지점까지의 최소거리를 벡터로 저장한다음<br/>

2차원 배열에서 종방향으로 합을 구하도록 했다.<br/>

이렇게 구한 값을 비교하면서 인덱스 값을 구했다.<br/>