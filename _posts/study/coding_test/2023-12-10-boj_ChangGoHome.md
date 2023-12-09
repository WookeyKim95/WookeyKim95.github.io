---
layout: post
title:  "[백준] 22116_창영이와 퇴근 C++"
subtitle:   
date: 2023-12-10 06:39:14 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 22116_창영이와 퇴근 C++<br/>
<br/>

다익스트라 혹은 MST를 이용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>
#include <cmath>

#define MAX (1 << 63) - 1

using namespace std;

typedef long long ll;

int N;
vector<vector<ll>> Map;
vector<vector<ll>> Accul;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

ll m_max(ll a, ll b)
{
    if (a > b)
        return a;
    else
        return b;
}

ll Dijkstra()
{
    priority_queue<pair<ll, pair<int, int>>> pq;
    pq.push({-(abs(Map[0][1] - Map[0][0])), {1, 0}});
    pq.push({-(abs(Map[1][0] - Map[0][0])), {0, 1}});
    Accul[0][0] = 0;

    ll ret = 0;

    while (!pq.empty())
    {
        int CurX = pq.top().second.first;
        int CurY = pq.top().second.second;
        ll CurAccul = -pq.top().first;
        pq.pop();
        
        if (Accul[CurY][CurX] != MAX)
            continue;
        
        Accul[CurY][CurX] = CurAccul;
        ret = m_max(ret, CurAccul);

        if (CurY == N-1 && CurX == N-1)
            break;

        for (int i = 0; i < 4; ++i)
        {
            int Nx = CurX + dx[i];
            int Ny = CurY + dy[i];

            if (!(0 <= Nx && Nx < N && 0 <= Ny && Ny < N))
                continue;

            ll NextCost = abs(Map[Ny][Nx] - Map[CurY][CurX]);

            if (Accul[Ny][Nx] == MAX)
                pq.push({-(NextCost), {Nx, Ny}});
        }
    }

    return ret;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        vector<ll> row;
        vector<ll> Accul_row;
        for (int j = 0; j < N; ++j)
        {
            ll _input;
            cin >> _input;
            row.push_back(_input);
            Accul_row.push_back(MAX);
        }
        Map.push_back(row);
        Accul.push_back(Accul_row);
    }

    if (N == 1)
    {
        cout << 0;
        return 0;
    }

    ll ans = Dijkstra();

    cout << ans;


    return 0;
}
```

문제를 풀면서 다익스트라 알고리즘을 사용하였다.<br/>

어려웠던 점은 누적해서 이동거리를 구하는 것이 아니라<br/>

각 노드 까지 가는데 최소 경사를 구하는 것이 목적인지라<br/>

많이 헷갈렸던 것 같다.<br/>

구글링을 해서 [참고](https://kau-algorithm.tistory.com/204)를 해보니, 격자 노드가 아니라, 간선을 pq에 넣어야하는 것으로 나타났다.<br/>

즉, 문제를 애초에 설계를 잘못해서 내가 많이 힘들었던 것 같다.<br/>
