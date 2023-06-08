---
layout: post
title:  "[백준] 1967_트리의 지름 C++"
subtitle:   
date: 2023-06-08 09:45:33 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1967_트리의 지름 C++<br/>
<br/>

DFS를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

typedef pair<int, int> pii;

struct Node
{
    int num;
    vector<pii> link;
    bool visited = false;

    Node(int _num) : num(_num) {};
};

int N, ans = 0;
int maxDist = 0;
int maxNode;
vector<Node> Tree;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void dfs(Node cur, int val)
{
    bool IsLeaf = true;

    if (maxDist < val)
    {
        maxDist = val;
        maxNode = cur.num;
    }

    for (size_t i = 0; i < cur.link.size(); ++i)
    {
        int nextNode = cur.link[i].first;
        int nextDist = cur.link[i].second;

        if (!Tree[nextNode].visited)
        {
            Tree[nextNode].visited = true;
            dfs(Tree[nextNode], val + nextDist);
            Tree[nextNode].visited = false;
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

    for (int i = 0; i <= N; ++i)
    {
        Node N(i);
        Tree.push_back(N);
    }

    for (int i = 0; i < N-1; ++i)
    {
        int S, E, L;
        cin >> S >> E >> L;

        Tree[S].link.push_back({E, L});
        Tree[E].link.push_back({S, L});
    }

    Tree[1].visited = true;
    dfs(Tree[1], 0);
    Tree[1].visited = false;

    for (int i = 1; i <= N; ++i)
        Tree[i].visited = false;

    Tree[maxNode].visited = true;
    dfs(Tree[maxNode], 0);

    cout << ans;

    return 0;
}
```
<br/>

백준 1167번 트리의 지름과 유사한 문제.<br/>

입력 방법만 다르고 전체적인 풀이는 같았다. 그래서 복습 차원에서 풀었다.<br/>

1167번과 똑같이 DFS를 사용해서 푸는 문제이다.<br/>

```
if (maxDist < val)
{
    maxDist = val;
    maxNode = cur.num;
}
```
지금까지 계산해온 거리가 maxDist보다 값이 크면 최대 거리를 계산할 때 maxNode를 갱신해준다.<br/>

즉, 첫번째 DFS는 maxNode를 찾기 위한 DFS이다.<br/>

그리고 두번째 DFS에서 maxNode에서 시작해서 거리를 계산하는 방법으로 구현하였다.<br/>