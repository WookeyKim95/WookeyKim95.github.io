---
layout: post
title:  "[백준] 2887_행성 터널 C++"
subtitle:   
date: 2023-08-31 07:18:38 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2887_행성 터널 C++<br/>
<br/>

최소 스패닝 트리를 이용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <tuple>

using namespace std;

struct Planet
{
    int parent;
    int X;
    int Y;
    int Z;
};

int N, result = 0;
vector<Planet> Planets;
vector<pair<int, int>> Xord;
vector<pair<int, int>> Yord;
vector<pair<int, int>> Zord;
vector<tuple<int, int, int>> Edges;

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int find(int x)
{
    if (Planets[x].parent == x)
        return x;
    else
        return Planets[x].parent = find(Planets[x].parent);
}

bool union_node(int x, int y)
{
    x = find(x);
    y = find(y);

    if (x==y)
        return false;
    else
    {
        Planets[y].parent = x;
        return true;
    }
    
}

int main()
{
    ios::sync_with_stdio(0);
    cout.tie(0);
    cin.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        Planet p;
        cin >> p.X >> p.Y >> p.Z;
        p.parent = i;
        Planets.push_back(p);

        Xord.push_back({p.X, i});
        Yord.push_back({p.Y, i});
        Zord.push_back({p.Z, i});
    }

    sort(Xord.begin(), Xord.end());
    sort(Yord.begin(), Yord.end());
    sort(Zord.begin(), Zord.end());

    for (int i = 0; i < N-1; ++i)
    {
        Edges.push_back({Xord[i+1].first - Xord[i].first, Xord[i+1].second, Xord[i].second});
        Edges.push_back({Yord[i+1].first - Yord[i].first, Yord[i+1].second, Yord[i].second});
        Edges.push_back({Zord[i+1].first - Zord[i].first, Zord[i+1].second, Zord[i].second});
    }

    sort(Edges.begin(), Edges.end());

    for (size_t i = 0; i < Edges.size(); ++i)
    {
        int dist = get<0>(Edges[i]);
        int u = get<1>(Edges[i]);
        int v = get<2>(Edges[i]);

        if (union_node(u, v))
            result += dist;
    }

    cout << result;

    return 0;
}
```

최소비용으로 모든 행성을 연결하라는 문제를 보고<br/>

최소 스패닝 트리를 이용하는 문제라는 것은 알았다.<br/>

다만 문제점이 음.. 프림 알고리즘을 이용할지, 크루스칼 알고리즘을 이용할지 고민 되었다.<br/>

- 크루스칼 알고리즘 : 모든 간선에 대해서 사이클이 생기는지 여부를 판단.
- 프림 알고리즘 : 정점 하나에서 트리를 점차 확장시켜나가는 구조.

그리고 이 문제의 최대 문제점은 간선 정보가 없다는 것이다.<br/>

즉, 여태 간선정보가 주어졌던 지금까지의 문제와는 달리,<br/>

간선을 직접 찾아가면서 직접 연결해야한다는 것이다.<br/>

그런데 주어진 시간은 1초이며, 행성의 개수는 최대 10만개이다.<br/>

간선을 어떻게 연결할지 경우의 수를 따지는 데에만 O(N^2)이니 타임오버가 날 것이 뻔했다.<br/>

뭔가 좌표를 정렬해서 어떻게 해야했던 것이었을까?<br/>

30분 고민 결과 정확한 아이디어가 떠오르지 않아서 구글링을 하기로 결정했다.<br/>

이번엔 [이분의 글](https://cocoon1787.tistory.com/478)을 참고하였다.<br/>

그런데 처음엔 이분의 풀이를 봐도 이해가 안가서 잠시 뇌정지가 왔었다.<br/>

행성의 좌표의 차이들을 벡터에 담아서 유니온 파인드에 담아서 MST를 찾는다라..<br/>

아하! sort를 간선의 dist를 기준으로 오름차순으로 하면 자동으로 작은 값들부터 정렬이 될 것이므로<br/>

그리고 어떤 행성끼리 연결되어있는지 저장을 해두니 자동으로 시간복잡도도 줄어들고 MST가 완성된다.<br/>

알고리즘 분류에 정렬이 같이 있었던 이유다.<br/>

아마..도? 시간 복잡도는 O(N*N)이라서 걱정을 했는데 이제는 O(3*N)이 되지 않을까 생각이 든다.<br/>