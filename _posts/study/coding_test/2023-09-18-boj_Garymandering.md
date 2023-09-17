---
layout: post
title:  "[백준] 17471_게리맨더링 C++"
subtitle:   
date: 2023-09-18 06:54:34 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 17471_게리맨더링 C++<br/>
<br/>

백트래킹과 BFS를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>
#include <queue>
#include <cmath>

#define MAX 11
#define INF 2147483647

using namespace std;

int N;
int Population[MAX];
bool visited[MAX];
bool check[MAX];
vector<vector<int>> Nodes;

int Ans = INF;

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

bool BFS(vector<int> v, bool b)
{
    memset(visited, false, sizeof(visited));
    
    queue<int> q;
    q.push(v[0]);
    visited[v[0]] = true;

    int Cnt = 1;

    while (!q.empty())
    {
        int Cur = q.front();
        q.pop();

        for (size_t i = 0; i < Nodes[Cur].size(); ++i)
        {
            int Next = Nodes[Cur][i];
            if (check[Next] == b && !visited[Next])
            {
                visited[Next] = true;
                q.push(Next);
                Cnt++;
            }
        }
    }

    if (v.size() == Cnt)
        return true;
    else
        return false;
}

void Calculate()
{
    int A = 0;
    int B = 0;

    for (int i = 1; i <= N; ++i)
    {
        if (check[i])
            A += Population[i];
        else
            B += Population[i];
    }

    int result = abs(A - B);

    Ans = m_min(result, Ans);
}

bool Connect()
{
    vector<int> a, b;

    for (int i = 1; i <= N; ++i)
    {
        if (check[i] == true)
            a.push_back(i);
        else
            b.push_back(i);
    }

    if (!BFS(a, true))
        return false;
    if (!BFS(b, false))
        return false;
    
    return true;
}

void DFS(int idx, int Cnt)
{
    if (Cnt >= 1)
    {
        if (Connect())
            Calculate();
    }

    if (Cnt == N-1)
        return;

    for (int i = idx; i <= N; ++i)
    {
        check[i] = true;
        DFS(i+1, Cnt + 1);
        check[i] = false;
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 1; i <= N; ++i)
        cin >> Population[i];

    for (int i = 1; i <= N; ++i)
    {
        int _input;
        cin >> _input;

        for (int j = 0; j < _input; ++j)
        {
            int x;
            cin >> x;

            Nodes[i].push_back(x);
            Nodes[x].push_back(i);
        }
    }

    DFS(1, 0);

    if (Ans == INF)
        cout << -1;
    else
        cout << Ans;

    return 0;
}
```

DFS.. 정확히는 백트래킹과 BFS를 활용해보는 문제였다.<br/>

백트래킹을 이용해서 선거구를 확정짓고,<br/>

그 선거구가 이어져있는지 BFS를 활용해서 확인해보는 문제였다.<br/>

하도 BFS와 DFS를 여러번 써야하다보니까 시간복잡도가 걱정이 됐는데<br/>

N의 최대값이 10으로 작아서 그렇게 큰 걱정은 안해도 될 것 같았다.<br/>

아슬아슬하게 5천만회 (1초당 약 1억번이라고 가정하면)안의 연산에 들어갈 것 같긴하다.<br/>

- check의 역할 : 두 선거구 중 어느 선거구에 속하는지?
- visited의 역할 : BFS를 하는 과정에서 방문한 지점인지 체크
- Nodes의 역할 : 서로 이어져있는 노드 기록

변수의 역할을 대략적으로 이렇게 설계하고<br/>

DFS를 통해 백트래킹을, 그리고 탐색하는 과정으로 BFS를 구현했다.<br/>