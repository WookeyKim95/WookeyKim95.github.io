---
layout: post
title:  "[백준] 1939_중량제한 C++"
subtitle:   
date: 2023-05-25 08:32:25 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1939_중량제한 C++<br/>
<br/>

BFS와 이분탐색을 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

struct Island
{
    int num;
    vector<pair<int, int>> bridge;
    bool visited = false;

    Island(int _num) : num(_num) {};
};

int N, M;
vector<Island> Islands;
int S, E;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

bool BFS(int CurWeight)
{
    queue<int> q;
    q.push(S);
    Islands[S].visited = true;

    while (!q.empty())
    {
        int CurIsland = q.front();
        q.pop();

        if (CurIsland == E)
            return true;

        for (size_t i = 0; i < Islands[CurIsland].bridge.size(); ++i)
        {
            int NextIsland = Islands[CurIsland].bridge[i].first;
            int BridgeWeight = Islands[CurIsland].bridge[i].second;

            if (!Islands[NextIsland].visited && BridgeWeight >= CurWeight)
            {
                Islands[NextIsland].visited = true;
                q.push(NextIsland);
            }
        }
    }

    return false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    Island padding(0);
    Islands.push_back(padding);

    for (int i = 1; i <= N; ++i)
    {
        Island I(i);
        Islands.push_back(I);
    }

    int MaxWeight = 0;

    for (int i = 0; i < M; ++i)
    {
        int Start, End, Weight;
        cin >> Start >> End >> Weight;

        Islands[Start].bridge.push_back({End, Weight});
        Islands[End].bridge.push_back({Start, Weight});

        MaxWeight = m_max(Weight, MaxWeight);
    }

    int S, E;
    cin >> S >> E;

    int Low = 0;
    int High = MaxWeight;

    while (Low <= High)
    {
        for (size_t i = 1; i < Islands.size(); ++i)
            Islands[i].visited = false;
        
        int Mid = (Low + High) / 2;

        if(BFS(Mid))
            Low = Mid + 1;
        else
            High = Mid - 1;
    }
    
    cout << High;


    return 0;
}
```
<br/>

우선 경로를 찾아야 하기 때문에 BFS를 응용하는 것은 맞았다.<br/>

다만 주의할 것은 최단 경로를 구하는 것이 아니라 최대 중량을 구하는 것이다.<br/>

즉, 경로가 여러개 나올 경우, 최단 경로를 구하는 것이 아니라 최대 중량을 구하는 경로를 선택해야한다.<br/>

이것을 어떻게 구할지 고민을 많이 했던 것 같다.<br/>

힌트를 얻고자 알고리즘 분류를 살펴보니 이분탐색이 있었다.<br/>

아하! 특정 무게를 가지고 BFS를 진행해서 유효한 경로가 존재하는지 살펴보는 방식으로 구현하면 되는 것이다.<br/>

그렇게 무게의 범위를 점점 좁혀나가서 High 값을 도출해내면 성공할 수 있다.<br/>