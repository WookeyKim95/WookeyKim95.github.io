---
layout: post
title:  "[백준] 17396_백도어 C++"
subtitle:   
date: 2023-11-26 07:18:08 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 17396_백도어 C++<br/>
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

<br/>

## 오답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <queue>

#define MAX 100000000000

using namespace std;

struct Node
{
    long long Accul = MAX;
    bool CanGo = false;
    vector<pair<int, int>> link;
};

int N, M;
vector<Node> Nodes;

long long Dijkstra(int a)
{
    priority_queue<pair<int, int>> pq;
    pq.push({0, a});
    Nodes[a].Accul = 0;

    while (!pq.empty())
    {
        long long CurCost = -pq.top().first;
        int CurNode = pq.top().second;
        pq.pop();

        if (CurCost > Nodes[CurNode].Accul)
            continue;
        
        for (size_t i = 0; i < Nodes[CurNode].link.size(); ++i)
        {
            int NextNode = Nodes[CurNode].link[i].second;
            long long Cost = Nodes[CurNode].link[i].first;

            if (Nodes[NextNode].Accul > CurCost + Cost && Nodes[NextNode].CanGo)
            {
                Nodes[NextNode].Accul = CurCost + Cost;
                pq.push({-(CurCost + Cost), NextNode});
            }
        }
    }

    return Nodes[N-1].Accul;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        Node n;
        int _input;
        cin >> _input;

        if (_input == 0)
            n.CanGo = true;
        else
        {   
            n.CanGo = false;
            if (i == N-1)
                n.CanGo = true;
        }
            

        Nodes.push_back(n);
    }

    for (int i = 0; i < M; ++i)
    {
        int S, E, C;
        cin >> S >> E >> C;

        Nodes[S].link.push_back({C, E});
        Nodes[E].link.push_back({C, S});
    }

    long long ans = Dijkstra(0);

    if (ans == MAX)
        cout << -1;
    else
        cout << ans;

    return 0;
}
```

처음에는 18%에서 오답이 나오길래 값의 범위를 살펴봤는데 int 범위를 넘어갔다.<br/>

그래서 long long으로 고쳐주고 MAX도 99999999에서 더 큰 값으로 바꿔주니<br/>

67%까지 올라갔지만 여전히 틀렸다고 나오더라.<br/>

왜 그런가 연구를 해보니 priorty_queue에서 Cost를 담는 부분을 long long으로 안고쳤다.<br/>

아.. 안돼!!<br/>

고쳐주니까 바로 해결되었다.<br/>

Cost 이부분은 long long으로 다 통일하자. ㅠㅠ