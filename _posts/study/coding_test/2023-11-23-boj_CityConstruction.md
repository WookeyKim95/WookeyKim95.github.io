---
layout: post
title:  "[백준] 21924_도시 건설 C++"
subtitle:   
date: 2023-11-23 07:16:32 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 21924_도시 건설 C++<br/>
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

typedef long long ll;
typedef pair<int, pair<int, int>> pii;

int Parent[100001];

int FindParent(int a)
{
    if (Parent[a] == a)
        return a;
    else
        return Parent[a] = FindParent(Parent[a]);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N, M;
    cin >> N >> M;

    vector<pii> Edges;

    for (int i = 0; i <= N; ++i)
        Parent[i] = i;
    
    ll Sum1 = 0;
    for (int i = 0; i < M; ++i)
    {
        int S, E, C;
        cin >> S >> E >> C;
        Sum1 += C;
        Edges.push_back({C, {S, E}});
    }

    sort(Edges.begin(), Edges.end());

    ll Sum2 = 0;
    for (size_t i = 0; i < Edges.size(); ++i)
    {
        int S = Edges[i].second.first;
        int E = Edges[i].second.second;
        int C = Edges[i].first;

        int A = FindParent(S);
        int B = FindParent(E);

        if (A != B)
        {
            Sum2 += C;
            Parent[A] = B;
        }
    }

    bool flag = true;

    for (int i = 1; i <= N-1; ++i)
    {
        int A = FindParent(i);
        int B = FindParent(i+1);

        if (A != B)
        {
            flag = false;
            break;
        }
    }

    if (flag)
        cout << Sum1 - Sum2;
    else
        cout << -1;
    return 0;
}
```
<br/>

이 문제는 MST를 활용해볼 수 있는 문제이다.<br/>

다만, 주어진 엣지를 모두 연결하고 나서 비용과 최소 스패닝 트리의 비용 차이를 계산하고,<br/>

도시가 모두 연결되었는지를 파악하는 것이 중요하다.<br/>

비용차이는 뭐.. 모두 연결했을 경우 - 최소 스패닝 트리 비용을 해주면 되는데,<br/>

모든 도시가 연결되었는지 여부를 어떻게 파악할까? 생각했다.<br/>

생각해보니 간단했던게,<br/>

```
for (int i = 1; i <= N-1; ++i)
{
    int A = FindParent(i);
    int B = FindParent(i+1);

    if (A != B)
    {
        flag = false;
        break;
    }
}
```

위 코드와 같은 방식으로 그냥 모든 건물의 부모가 같은지 확인하면 되는 문제이다.<br/>

이것을 생각하는게 관건이었던 것 같다.<br/>