---
layout: post
title:  "[백준] 1707번_이분 그래프 C++"
subtitle:   
date: 2023-04-10 09:33:07 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1707번_이분 그래프 C++<br/>
<br/>

이분 그래프에 대해서 배우는 문제.<br/>

알고보면 그래프탐색.<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>

using namespace std;

struct Vertex
{
    int num;
    vector<int> link;
    bool visited = false;
    int group = 0;

    Vertex(int _num):num(_num) {};
};

vector<Vertex> Vertexs;

bool dfs(int start, int CurGroup)
{
    Vertexs[start].visited = true;
    Vertexs[start].group = CurGroup;

    for (size_t i = 0; i < Vertexs[start].link.size(); ++i)
    {
        int Cur = Vertexs[start].link[i];
        if (!Vertexs[Cur].visited)
        {
            bool a = dfs(Cur, -CurGroup);
            if (!a)
                return false;
        }
        else if (Vertexs[start].group == Vertexs[Cur].group)
            return false;
    }

    return true;    
}

int main()
{
    int K;
    cin >> K;

    for (int i = 0; i < K; ++i)
    {
        int V, E;
        cin >> V >> E;

        Vertexs.clear();
        Vertex padding(0);
        Vertexs.push_back(padding);

        for (int j = 1; j <= V; ++j)
        {
            Vertex v(j);
            Vertexs.push_back(v);
        }

        for (int j = 0; j < E; ++j)
        {
            int a, b;
            cin >> a >> b;

            Vertexs[a].link.push_back(b);
            Vertexs[b].link.push_back(a);
        }
        
        bool result;
        for (int j = 1; j <= V; ++j)
        {
            if (!Vertexs[j].visited)
            {
                result = dfs(j, 1);
                if (!result)
                    break;
            }
        }

        if (result)
            cout << "YES" << '\n';
        else
            cout << "NO" << '\n';
    }

    return 0;
}
```

그래프 탐색으로 풀 수 있는 문제였다.<br/>

다만.. 그걸 생각해내기가 어려웠다는게 함정.<br/>

이분 그래프가 무엇인지를 먼저 이해할 필요가 있었다.<br/>

[참고링크](https://gmlwjd9405.github.io/2018/08/23/algorithm-bipartite-graph.html)를 참고하면, <br/>

이분 그래프는 아래와 같다.

- 어떤 정점을 빨강색으로 칠한다.
- 인접한 다른 정점은 파란색으로 칠한다.
- 다른 정점의 인접한 정점을 다시 빨강색으로 칠한다.
- 그래프를 모두 칠할 때 까지 과정을 반복한다.

즉, 인접한 정점끼리 서로 다른색으로 칠해서 두 가지 색으로만 모든 정점을 칠할 수 있는 그래프이다.<br/>

이를 확인하는 방법은 DFS나 BFS를 이용하면 된다. (필자는 DFS를 사용하였다.)<br/>

우선, 정점의 구성은 아래와 같이 구성하였다.<br/>

```
struct Vertex
{
    int num; // 정점의 번호
    vector<int> link; // 인접 정점 목록
    bool visited = false; // 정점 방문 여부
    int group = 0; // 어떤 색으로 칠하였는가

    Vertex(int _num):num(_num) {};
};
```

그리고 DFS 코드는 아래와 같다.<br/>

```
bool dfs(int start, int CurGroup)
{
    Vertexs[start].visited = true;
    Vertexs[start].group = CurGroup;

    for (size_t i = 0; i < Vertexs[start].link.size(); ++i)
    {
        int Cur = Vertexs[start].link[i];
        if (!Vertexs[Cur].visited)
        {
            bool a = dfs(Cur, -CurGroup);
            if (!a)
                return false;
        }
        else if (Vertexs[start].group == Vertexs[Cur].group)
            return false;
    }

    return true;    
}
```
여기서 CurGroup이 정점의 현재 색깔이고,  dfs(Cur, -CurGroup)을 통해서 서로 다른 색깔을 칠하도록 구현하였다.<br/>

만약에 현재 색깔과 인접한 다음 정점의 색깔이 같을 경우 바로 false, 즉 이분 그래프가 아님을 나타낸다.

```
    else if (Vertexs[start].group == Vertexs[Cur].group)
        return false;
```