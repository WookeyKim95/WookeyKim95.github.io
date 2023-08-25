---
layout: post
title:  "[백준] 1948_임계경로 C++"
subtitle:   
date: 2023-08-26 06:47:50 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1948_임계경로 C++<br/>
<br/>

트리 탐색과 위상정렬에 대해서 알아보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>


using namespace std;

int N, M, Start, End;

struct City
{
    vector<pair<int, int>> link;
    vector<pair<int, int>> R_link;
    bool visited = false;
    int Entry = 0;
    int Dist = 0;
};

vector<City> Cities;
int AnsRoute = 0;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void BFS(int s)
{
    queue<pair<int, int>> q;
    q.push({s, 0});

    while(!q.empty())
    {
        int CurNode = q.front().first;
        int CurDist = q.front().second;
        q.pop();

        for (size_t i = 0; i < Cities[CurNode].link.size(); ++i)
        {
            int Next = Cities[CurNode].link[i].first;
            int NextCost = Cities[CurNode].link[i].second;

            Cities[Next].Dist = m_max(Cities[Next].Dist, CurDist + NextCost);
            Cities[Next].Entry--;

            if (Cities[Next].Entry == 0)
                q.push({Next, Cities[Next].Dist});
        }
    }
}

void BFS2(int e)
{
    queue<int> q;
    q.push(e);
    Cities[e].visited = true;

    while(!q.empty())
    {
        int Cur = q.front();
        q.pop();

        for (size_t i = 0; i < Cities[Cur].R_link.size(); ++i)
        {
            int Prev = Cities[Cur].R_link[i].first;
            int PrevDist = Cities[Cur].R_link[i].second;

            if (Cities[Cur].Dist - PrevDist == Cities[Prev].Dist)
            {
                AnsRoute++;
                if (!Cities[Prev].visited)
                {
                    Cities[Prev].visited = true;
                    q.push(Prev);
                }
            }
        }
    }
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
        City c;
        Cities.push_back(c);
    }

    for (int i = 0; i < M; ++i)
    {
        int s, e, c;
        cin >> s >> e >> c;

        Cities[s].link.push_back({e, c});
        Cities[e].R_link.push_back({s, c});
        Cities[e].Entry++;
    }

    cin >> Start >> End;

    BFS(Start);
    BFS2(End);

    cout << Cities[End].Dist << '\n';
    cout << AnsRoute;

    return 0;
}
```

일단은 트리 상에서 사이클도 없으니 그래프 탐색이라고 느꼈다.<br/>

문제는 이 그래프를 어떻게 탐색해서 어떻게 최장 경로를 도출하는가<br/>

이게 문제였다.<br/>

문제에서 요구하는 사항에 따르면 가장 시간이 오래걸리는 사람이 지나는 도로 수를 구하는 것이니<br/>

결국 최장 경로를 구하는 것이라고 생각을 했다.<br/>

다익스트라가 최단 경로를 구하는 것인데 흠..<br/>

다익스트라로 최장 경로를 구할 수는 없을까? 생각했다.<br/>

사실 가장 가능성이 높은 것은 DFS라고 생각이 들었고..<br/>

왜냐하면 다양한 경로를 탐색을 할 수 있고, 이 트리도 그런 것을 요구하는 것 같았다.<br/>

<br/>

## 초반 코드<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N, M, Start, End;

struct City
{
    vector<pair<int, int>> link;
    bool visited = false;
};

vector<City> Cities;
int AnsTime = 0, AnsRoute = 0;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void DFS(int CurNode, int CurCount, int CurTime)
{
    if (CurNode == End)
    {
        if (AnsTime <= CurTime)
        {
            if (AnsTime == CurTime)
                AnsRoute = m_max(AnsRoute, CurCount);
                
            AnsTime = CurTime;
        }

        return;
    }

    for (size_t i = 0; i < Cities[CurNode].link.size(); ++i)
    {
        int Next = Cities[CurNode].link[i].first;
        int NextCost = Cities[CurNode].link[i].second;

        if (!Cities[Next].visited)
        {
            Cities[Next].visited = true;
            DFS(Next, CurCount+1, CurTime + NextCost);
            Cities[Next].visited = false;
        }
    }
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
        City c;
        Cities.push_back(c);
    }

    for (int i = 0; i < M; ++i)
    {
        int s, e, c;
        cin >> s >> e >> c;

        Cities[s].link.push_back({e, c});
    }

    cin >> Start >> End;

    DFS(Start, 0, 0);

    cout << AnsTime << '\n';
    cout << AnsRoute;

    return 0;
}
```

우선 이 코드로 아래의 테스트 케이스를 돌리면 오답이 나온다.<br/>

```
7
9
1 2 4
1 3 2
1 4 3
2 6 3
2 7 5
3 5 1
4 6 4
5 6 2
6 7 5
1 7
```

```
// 원하는 값
12
5

// 실제 출력 값
12
3
```

가장 늦게 도착했을 때에 달려온 도로의 수를 구하는 문제라고 생각했다.<br/>

그래서 아래와 같이 DFS에서 return 조건을 걸어주었다.<br/>

```
if (CurNode == End)
{
    if (AnsTime <= CurTime)
    {
        if (AnsTime == CurTime)
            AnsRoute = m_max(AnsRoute, CurCount);
            
        AnsTime = CurTime;
    }

    return;
}
```

하지만 플레 5 문제인 만큼 이렇게 단순하진 않을 것이라 생각했고,<br/>

실제로 오답이 출력되는 것을 봐서 다른 방법이 뭘까 생각하면서 알고리즘 분류를 펼쳐보았다.<br/>

위상정렬이 떡하니 적혀있었다.<br/>

아직 위상정렬은 제대로 혼자 다룰 줄 몰라서 구글링을 해보기로 했다.<br/>

<br/>

## 정답코드로의 변환<br/>
<Br/>

이번에도 [얍문님의 블로그](https://yabmoons.tistory.com/443)를 참고하였다.<br/>

아하 이런.. 무작정 맨 마지막에서만 조건을 따질게 아니라<br/>

각 노드를 방문하면서 조건을 따져야했던 것 같다.<br/>

그리고 위상정렬을 구현하기 위해서<br/>

앞서서 얼마나 도시를 많은 도시를 방문해야하는지 저장할 필요가 있었다.<br/>

그리고 DFS 보다는 BFS를 사용하면서 경로를 파악하고,<br/>

특히 가장 오래 걸리는 경로를 파악하기 위해서 도착점 -> 시작점으로<br/>

역으로 경로를 파악할 필요가 있었다.<br/>

이를 추가적으로 구현해야했다.<br/>

그래서 완성한 코드가 위에 있는 정답제출코드이다.<br/>