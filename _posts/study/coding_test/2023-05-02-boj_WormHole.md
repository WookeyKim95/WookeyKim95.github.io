---
layout: post
title:  "[백준] 1865_웜홀 C++"
subtitle:   
date: 2023-05-02 10:43:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1865_웜홀 C++<br/>
<br/>

벨만포드 알고리즘을 사용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

#define INF 999999999

using namespace std;

struct Edge
{
    int start;
    int end;
    int time;

    Edge(int _S, int _E, int _T) :
        start(_S), end(_E), time(_T) {}
};

struct Node
{
    int num;
    int AcculTime = INF;

    Node(int _num) : num(_num) {}
};

vector<Node> Nodes;
vector<Edge> Edges;

bool Bellman_Ford(int n)
{
    for (int i = 1; i <= n; ++i)
    {
        for (size_t j = 0; j < Edges.size(); ++j)
        {
            int s = Edges[j].start;
            int e = Edges[j].end;
            int t = Edges[j].time;

            if (Nodes[s].AcculTime + t < Nodes[e].AcculTime)
                Nodes[e].AcculTime = Nodes[s].AcculTime + t;
        }
    }

    for (size_t j = 0; j < Edges.size(); ++j)
    {
        int s = Edges[j].start;
        int e = Edges[j].end;
        int t = Edges[j].time;

        if (Nodes[s].AcculTime + t < Nodes[e].AcculTime)
            return true;
    }

    return false;
}


int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int TC, N, M, W;

    cin >> TC;

    for (int i = 0; i < TC; ++i)
    {
        cin >> N >> M >> W;

        Nodes.clear();
        Edges.clear();
        
        Node Padding(0);
        Nodes.push_back(Padding);
        for (int j = 1; j <= N; ++j)
        {
            Node n(j);

            Nodes.push_back(n);
        }

        for (int j = 1; j <= M; ++j)
        {
            int S, E, T;
            cin >> S >> E >> T;

            // 도로는 무방향
            Edge Road1(S, E, T);
            Edge Road2(E, S, T);

            Edges.push_back(Road1);
            Edges.push_back(Road2);
        }

        for (int j = 1; j <= W; ++j)
        {
            int S, E, T;
            cin >> S >> E >> T;

            // 간선은 방향성
            Edge Worm(S, E, -T);

            Edges.push_back(Worm);
        }

        if (Bellman_Ford(N))
            cout << "YES\n";
        else
            cout << "NO\n";
    }

    return 0;
}
```

벨만포드 알고리즘을 처음 접했기 때문에 구글링을 통해 이해할 필요가 있었다.<br/>

[참고링크](https://yabmoons.tistory.com/365)를 통해 벨만포드 알고리즘에 대해서 공부해보았다.<br/>

벨만 포드 알고리즘 또한 다익스트라 알고리즘과 마찬가지로 최소 경로를 구하는 과정이다.<br/>

다만, 우선순위 큐를 사용하는 다익스트라 알고리즘과는 달리,<br/>

벨만 포드 알고리즘은 완전탐색, 즉 모든 경우의 수를 다 따져서 탐색을 해 최소 경로를 구한다.<br/>

그리고, 다익스트라 알고리즘은 우선순위 큐를 사용하기 때문에<br/>

경로 상 비용 값이 음수가 있으면 사용할 수 없으므로 이 때는 벨만 포드 알고리즘을 사용해야 한다.<br/>

내가 정리한 다익스트라 알고리즘과의 차이점은 아래와 같다.<br/>

- 경로 상의 cost가 음수 값이 있을 때에 사용하면 된다.
- 시간복잡도는 O(V*E)로, 완전 탐색 기법을 사용하기 때문에 다익스트라 알고리즘에 비해 오래 걸린다.

<br/>

그렇다면 이 문제에서 벨만 포드 알고리즘은 어떻게 구현되었을까?<br/>

이번 문제에서 구현을 할 때는 [이 링크](https://velog.io/@nacean/%EB%B0%B1%EC%A4%801865-%EC%9B%9C%ED%99%80-C-%ED%92%80%EC%9D%B4)를 참고하였다.<br/>

궁금했던 것은, 왜 두번째 탐색을 진행할 때 다시 한번 갱신이 되면 true를 return하게 되는 것인지 궁금했는데<br/>

가중치가 음수인 경로가 있다면 값이 변해버려서 한번 더 계산이 될 것이기 때문으로 판단된다.<br/>

그래서 한번 계산을 완료하고 난 뒤, 다시 한번 계산을 하면서 값이 변하는게 있는지를 판단하는 것이다.<br/>
