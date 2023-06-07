---
layout: post
title:  "[백준] 1916_최소비용 구하기 C++"
subtitle:   
date: 2023-06-07 09:29:19 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1916_최소비용 구하기 C++<br/>
<br/>

다익스트라 알고리즘을 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>

#define INF 999999999

using namespace std;

struct City
{
    int num;
    vector<pair<int, int>> link;
    int acculCost = INF;

    City(int _num) : num(_num) {};
};

int N, M, Start, End;
vector<City> Cities;

int Dijkstra()
{
    priority_queue<pair<int, int>> pq;
    pq.push({0, Start});
    Cities[Start].acculCost = 0;

    while (!pq.empty())
    {
        int Cur = pq.top().second;
        int CurCost = -pq.top().first;
        pq.pop();

        if (Cur == End)
            break;

        for (size_t i = 0; i < Cities[Cur].link.size(); ++i)
        {
            int Next = Cities[Cur].link[i].first;
            int NextCost = Cities[Cur].link[i].second;

            if (Cities[Next].acculCost > CurCost + NextCost)
            {
                Cities[Next].acculCost = CurCost + NextCost;
                pq.push({-Cities[Next].acculCost, Next});
            }
        }
    }

    return Cities[End].acculCost;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;
    cin >> M;

    for (int i = 0; i <= N; ++i)
    {
        City C(i);
        Cities.push_back(C);
    }

    for (int i = 0; i < M; ++i)
    {
        int S, E, C;
        cin >> S >> E >> C;
        
        Cities[S].link.push_back({E, C});
    }

    cin >> Start >> End;

    cout << Dijkstra();

    return 0;
}
```
<br/>

기초적인 다익스트라 알고리즘을 이용해서 푸는 문제였다.<br/>

City라는 구조체를 만들고 그 구조체를 벡터에 담아서 풀어주었다.<br/>

City에는 도시의 번호, 누적 비용, 그리고 연결된 링크를 담아주었다.<br/>

```
struct City
{
    int num;
    vector<pair<int, int>> link;
    int acculCost = INF;

    City(int _num) : num(_num) {};
};
```

이렇게 하면 배열을 여러개 선언하지 않고 벡터 하나로만 관리를 할 수 있어서 편한거 같다.<br/>