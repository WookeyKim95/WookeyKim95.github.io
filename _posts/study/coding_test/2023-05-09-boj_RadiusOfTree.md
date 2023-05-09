---
layout: post
title:  "[백준] 1167_트리의 지름 C++"
subtitle:   
date: 2023-05-09 12:12:12 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1167_트리의 지름 C++<br/>
<br/>

DFS를 사용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

struct Vertex
{
    int num;
    vector<pair<int, int>> link;
    bool visited = false;

    Vertex(int _num) : num(_num) {};
};

int N;
vector<Vertex> Ver;
int maxDist = 0, ans = 0;
int maxNode;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void dfs(Vertex cur, int val)
{
    bool IsLeaf = true;

    if (maxDist < val)
    {
        maxDist = val;
        maxNode = cur.num;
    }

    for (size_t i = 0; i < cur.link.size(); ++i)
    {
        int nextVer = cur.link[i].first;
        int nextDist = cur.link[i].second;

        if (!Ver[nextVer].visited)
        {
            Ver[nextVer].visited = true;
            dfs(Ver[nextVer], val + nextDist);
            Ver[nextVer].visited = false;
            IsLeaf = false;
        }   
    }

    if (IsLeaf)
        ans = m_max(val, ans); 
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    Vertex padding(0);
    Ver.push_back(padding);

    for (int i = 1; i <= N; ++i)
    {
        Vertex V(i);
        Ver.push_back(V);
    }

    for (int i = 1; i <= N; ++i)
    {
        int num;
        cin >> num;

        while (true)
        {
            int VerNum, Dist;
            cin >> VerNum;

            if (VerNum == -1)
                break;
                
            cin >> Dist;

            Ver[num].link.push_back({VerNum, Dist});
        }
    }

    Ver[1].visited = true;
    dfs(Ver[1], 0);
    Ver[1].visited = false;

    for (int i = 1; i <= N; ++i)
        Ver[i].visited = false;

    Ver[maxNode].visited = true;
    dfs(Ver[maxNode], 0);

    cout << ans;

    return 0;
}
```

<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

struct Vertex
{
    int num;
    vector<pair<int, int>> link;
    bool visited = false;

    Vertex(int _num) : num(_num) {};
};

int N;
vector<Vertex> Ver;
int ans = 0;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void dfs(Vertex cur, int val)
{
    bool IsLeaf = true;

    for (size_t i = 0; i < cur.link.size(); ++i)
    {
        int nextVer = cur.link[i].first;
        int nextDist = cur.link[i].second;

        if (!Ver[nextVer].visited)
        {
            Ver[nextVer].visited = true;
            dfs(Ver[nextVer], val + nextDist);
            Ver[nextVer].visited = false;
            IsLeaf = false;
        }   
    }

    if (IsLeaf)
        ans = m_max(val, ans); 
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    Vertex padding(0);
    Ver.push_back(padding);

    for (int i = 1; i <= N; ++i)
    {
        Vertex V(i);
        Ver.push_back(V);
    }

    for (int i = 1; i <= N; ++i)
    {
        int num;
        cin >> num;

        while (true)
        {
            int VerNum, Dist;
            cin >> VerNum;

            if (VerNum == -1)
                break;

            cin >> Dist;

            Ver[num].link.push_back({VerNum, Dist});
        }
    }

    for (int i = 1; i <= N; ++i)
    {
        Ver[i].visited = true;
        dfs(Ver[i], 0);
        Ver[i].visited = false;
    }

    cout << ans;

    return 0;
}
```

DFS를 이용해서 정답코드를 구현하였다.<br/>

이 문제를 풀면서 제일 관건이었던 사항은 어떻게 리프노드인지를 판단하는 과정이었다.<br/>

이전까지는 깊이를 알 수 있었으니 제일 깊은 곳에 도달하면 처리를 하고 리턴해주면 되었다.<br/>

하지만 이번에는 깊이가 주어져있지 않았기 때문에 이 부분을 좀 고민했었다.<br/>

근데 생각해보니 답은 간단했다.<br/>

연결되어있는 지점이 모두 이미 방문한 지점이면 그 노드가 마지막이라는 것이다.<br/>

맨 처음에 깊이 노드인지 여부를 판단하는 변수를 선언하고<br/>

for문을 돌면서 연결된 지점이 모두 방문한 지점이면 true를 유지,<br/>

그렇지 않다면 false로 값을 바꾸도록 구현하였다.<br/>

```
// dfs코드

void dfs(Vertex cur, int val)
{
    bool IsLeaf = true;

    for (size_t i = 0; i < cur.link.size(); ++i)
    {
        int nextVer = cur.link[i].first;
        int nextDist = cur.link[i].second;

        if (!Ver[nextVer].visited)
        {
            Ver[nextVer].visited = true;
            dfs(Ver[nextVer], val + nextDist);
            Ver[nextVer].visited = false;
            IsLeaf = false;
        }   
    }

    if (IsLeaf)
        ans = m_max(val, ans); 
}
```

이렇게 해서 리프노드가 맞다면 ans를 갱신하도록 처리하였다.<br/>

하지만 문제점이 발생했다.<br/>

모든 지점을 시작점으로 해서 탐색을 하려고 하니 시간초과가 나는 것이다.<br/>

그렇다면 하나의 지점만을 탐색해야 한다는 의미인데<br/>

어떻게 탐색하면 하나의 지점만 탐색하고도 최장 거리를 구할 수 있을까? 생각했다.<br/>
<br/>

### 정답코드로의 변환<br/>
<br/>

아! 갱신을 해주고 dfs를 두번 하는 방법을 하면 됐다!<br/>

구글링을 통해 알 수 있었다.<br/>

임의의 정점 1에서 가장 거리가 먼 정점을 찾아두고, 그 정점에서 다시 dfs를 수행하면 지름이 나온다.<br/>

```
void dfs(Vertex cur, int val)
{
    bool IsLeaf = true;

    if (maxDist < val)
    {
        maxDist = val;
        maxNode = cur.num;
    }

    for (size_t i = 0; i < cur.link.size(); ++i)
    {
        int nextVer = cur.link[i].first;
        int nextDist = cur.link[i].second;

        if (!Ver[nextVer].visited)
        {
            Ver[nextVer].visited = true;
            dfs(Ver[nextVer], val + nextDist);
            Ver[nextVer].visited = false;
            IsLeaf = false;
        }   
    }

    if (IsLeaf)
        ans = m_max(val, ans); 
}
```

```
    Ver[1].visited = true;
    dfs(Ver[1], 0);
    Ver[1].visited = false;

    for (int i = 1; i <= N; ++i)
        Ver[i].visited = false;

    Ver[maxNode].visited = true;
    dfs(Ver[maxNode], 0);

    cout << ans;
```

DFS를 두번 실행하고, maxNode를 기록하는 과정을 추가하였다.<br/>

그렇게 해서 정답 코드를 도출할 수 있었다!