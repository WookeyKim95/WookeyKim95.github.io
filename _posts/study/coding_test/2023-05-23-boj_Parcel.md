---
layout: post
title:  "[백준] 1719_택배 C++"
subtitle:   
date: 2023-05-23 10:36:39 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1719_택배 C++<br/>
<br/>

다익스트라 알고리즘과 경로를 출력해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

#define INF 99999999

using namespace std;

struct Station
{
    int num;
    int Parent = 0;
    vector<pair<int, int>> Link;
    int acculTime = INF;

    Station(int _num) : num(_num) {};
};

int N, M;
vector<Station> Stations;

void Dijkstra(int Start, int End)
{
    priority_queue<pair<int, int>> pq;
    pq.push({0, Start});
    Stations[Start].acculTime = 0;

    vector<int> Route;

    while (!pq.empty())
    {
        int Cost = -pq.top().first;
        int Cur = pq.top().second;
        pq.pop();

        if (Cur == End)
            break;
 
        for (size_t i = 0; i < Stations[Cur].Link.size(); i++)
        {
            int Next = Stations[Cur].Link[i].first;
            int nCost = Stations[Cur].Link[i].second;
 
            if (Stations[Next].acculTime > Cost + nCost)
            {
                Stations[Next].acculTime = Cost + nCost;
                pq.push(make_pair(-Stations[Next].acculTime, Next));
                Stations[Next].Parent = Cur;
            }
        }
    }
}

void TracePath(int Child)
{
    if (Stations[Child].Parent == 0)
        return;
    else if (Stations[Stations[Child].Parent].Parent == 0)
    {
        cout << Child << ' ';
        return;
    }
    TracePath(Stations[Child].Parent);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    Station Padding(0);
    Stations.push_back(Padding);

    for (int i = 1; i <= N; ++i)
    {
        Station St(i);
        Stations.push_back(St);
    }

    for (int i = 0; i < M; ++i)
    {
        int S, E, D;
        
        cin >> S >> E >> D;
        
        Stations[S].Link.push_back({E, D});
        Stations[E].Link.push_back({S, D});
    }

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
        {
            for (int k = 1; k <= N; ++k)
            {
                Stations[k].acculTime = INF;
                Stations[k].Parent = 0;
            }
            if (i == j)
            {
                cout << '-' << ' ';
                continue;
            }
            Dijkstra(i, j);
            TracePath(j);
        }
        cout << '\n';
    }

    return 0;
}
```
<br/>

최단 경로라고는 생각을 했어서 다익스트라 알고리즘을 채택했다.<br/>

하지만 경로를 어떻게 산정하고 출력할지를 고민했던 것 같다.<br/>

그 방법은 [이 블로그의 글](https://star7sss.tistory.com/356)를 참고하였다.<br/>

우선 노드에다가 부모를 저장해주는 변수를 선언하고,<br/>

최단 경로를 갱신해줄 때 마다 해당 경로의 부모를 저장하는 것이다.<br/>

```
if (Stations[Next].acculTime > Cost + nCost)
{
    Stations[Next].acculTime = Cost + nCost;
    pq.push(make_pair(-Stations[Next].acculTime, Next));
    Stations[Next].Parent = Cur;
}
```

오호라! 그리고 나서 재귀함수로 역추적 하는 방식으로 경로를 출력하면 되는 것이다.<br/>

그러나 이 문제에서는 시작점의 다음 점 만을 출력하는 것을 요구하였으므로,<br/>

아래 코드와 같이 부모의 부모가 0일 경우에만 출력하고 리턴하도록 구현하였다.<br/>

```
void TracePath(int Child)
{
    if (Stations[Child].Parent == 0)
        return;
    else if (Stations[Stations[Child].Parent].Parent == 0)
    {
        cout << Child << ' ';
        return;
    }
    TracePath(Stations[Child].Parent);
}
```

이렇게 해보니 예제코드도 결과가 원하는 대로 출력되었다.<br/>