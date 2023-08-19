---
layout: post
title:  "[백준] 13907_세금 C++"
subtitle:   
date: 2023-08-19 07:07:34 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 13907_세금 C++<br/>
<br/>

다익스트라 알고리즘을 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <queue>

#define INF 999999999

using namespace std;

int N, M, K;
struct Node
{
    int num;
    vector<int> acculCost;
    vector<pair<int, int>> link;

    Node()
    {
        for (int i = 0; i <= N; ++i)
            acculCost.push_back(INF);
    }
};

vector<Node> Nodes;
priority_queue<pair<int, pair<int, int>>> pq;
int Start, End;

void Dijkstra(int s, int e)
{
    Nodes[s].acculCost[0] = 0;
    pq.push({ 0, {s, 0} });

    while (!pq.empty())
    {
        int Cur = pq.top().second.first;
        int CurPassed = pq.top().second.second;
        int dist = -pq.top().first;
        pq.pop();

        if (CurPassed >= N)
            continue;

        if (Nodes[Cur].acculCost[CurPassed] < dist)
            continue;

        for (size_t i = 0; i < Nodes[Cur].link.size(); ++i)
        {
            int Next = Nodes[Cur].link[i].second;
            int cost = Nodes[Cur].link[i].first + dist;

            if (cost < Nodes[Next].acculCost[CurPassed+1])
            {
                Nodes[Next].acculCost[CurPassed+1] = cost;
                pq.push({ -cost, {Next, CurPassed + 1 } });
            }
        }
    }
}

int Sum(int add)
{
    int ret = INF;
    for (int i = 0; i < N; ++i)
        ret = min(ret, Nodes[End].acculCost[i] + (i * add));

    return ret;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M >> K;

    for (int i = 0; i <= N; ++i)
    {
        Node n;
        n.num = i;
        Nodes.push_back(n);
    }

    cin >> Start >> End;

    for (int i = 0; i < M; ++i)
    {
        int s, e, c;
        cin >> s >> e >> c;

        Nodes[s].link.push_back({ c, e });
        Nodes[e].link.push_back({ c, s });
    }

    Dijkstra(Start, End);

    int Result;

    int hap = 0;
    Result = Sum(hap);

    cout << Result << '\n';

    for (int i = 0; i < K; ++i)
    {
        int Increase;
        cin >> Increase;

        hap += Increase;

        Result = Sum(hap);
        cout << Result << '\n';
    }

    return 0;
}
```

다익스트라를 매년 돌리지 않고 한 번만 돌려야 하는 것이<br/>

이번 문제의 핵심이었다.<br/>

결론부터 말하자면 다익스트라를 한번만 돌리는 방법은 아래와 같다.

- 도착점까지의 경로들을 거쳐온 길의 갯수와 같이 기록해준다.
- 매년 세금을 올릴 때마다 거쳐온 길의 갯수를 더해준다.
- 매년 마다 min을 이용해서 비교하고, 작은 값으로 갱신한다.

이 아이디어는 동료와 [이분의 글](https://geniusjo-story.tistory.com/476)을 통해서 얻을 수 있었다.<br/>

```
vector<int> acculCost;
```
노드를 보면 위와 같은 벡터가 구성요소로 있는 것을 알 수 있는데,<br/>

거쳐온 길의 갯수에 해당하는 인덱스에다가 acculCost를 기록해주는 것이다.<br/>

그리고 Sum 함수를 이용해서 acculCost를 순회하면서 도착점까지 거쳐온 길의 갯수만큼 합친 값과<br/>

기존에 기록되어 있던 값을 기록해서 더 작은 값을 ans로 산출하는 작업을 진행한다.<br/>

```
int Sum(int add)
{
    int ret = INF;
    for (int i = 0; i < N; ++i)
        ret = min(ret, Nodes[End].acculCost[i] + (i * add));

    return ret;
}

```

```
for (int i = 0; i < K; ++i)
    {
        int Increase;
        cin >> Increase;

        hap += Increase;

        Result = Sum(hap);
        cout << Result << '\n';
    }
```

이 작업을 매년 하는 것이다.<br/>

시간을 비교해봤는데, 특이했던 점은 간선의 개수가 많다면<br/>

K값이 작다면 다익스트라를 여러번 돌리는 것이 오히려 유리했다.<br/>

하지만 K값이 크다면 무조건! 다익스트라를 한번만 돌리는 것이 좋다.<br/>
<br/>

## 오답제출코드(시간초과)<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <queue>

#define INF 99999999

using namespace std;

struct Node
{
    int num;
    int acculCost = INF;
    vector<pair<int, int>> link;
};

int N, M, K;
vector<Node> Nodes;
priority_queue<pair<int, int>> pq;
int Start, End;

int Dijkstra(int s, int e)
{
    Nodes[s].acculCost = 0;
    pq.push({ 0, s });

    while (!pq.empty())
    {
        int Cur = pq.top().second;
        int dist = -pq.top().first;
        pq.pop();

        if (Nodes[Cur].acculCost < dist)
            continue;

        for (size_t i = 0; i < Nodes[Cur].link.size(); ++i)
        {
            int Next = Nodes[Cur].link[i].second;
            int cost = Nodes[Cur].link[i].first + dist;

            if (cost < Nodes[Next].acculCost)
            {
                Nodes[Next].acculCost = cost;
                pq.push({ -cost, Next });
            }
        }
    }

    if (Nodes[e].acculCost != INF)
        return Nodes[e].acculCost;
    else
        return -1;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M >> K;

    for (int i = 0; i <= N; ++i)
    {
        Node n;
        n.num = i;
        Nodes.push_back(n);
    }

    cin >> Start >> End;

    for (int i = 0; i < M; ++i)
    {
        int s, e, c;
        cin >> s >> e >> c;

        Nodes[s].link.push_back({ c, e });
        Nodes[e].link.push_back({ c, s });
    }

    cout << Dijkstra(Start, End) << '\n';

    for (int i = 0; i < K; ++i)
    {
        int Increase;
        cin >> Increase;

        for (int j = 1; j <= N; ++j)
        {
            for (size_t k = 0; k < Nodes[j].link.size(); ++k)
                Nodes[j].link[k].first += Increase;
            Nodes[j].acculCost = INF;
        }

        cout << Dijkstra(Start, End) << '\n';
    }

    return 0;
}
```

나도 처음에는 매년마다 다익스트라 알고리즘을 돌렸다.<br/>

염려했던대로 시간초과가 났다.<br/>

하긴.. 다익스트라 여러번 돌려서 풀릴거였으면<br/>

플레티넘 4 등급의 문제는 아니었겠지..<br/>

다익스트라를 매년 돌리지 않으려면 갯수를 기록해야한다는 아이디어를<br/>

구글링과 동료를 통해서 알 수 있었다.<br/>