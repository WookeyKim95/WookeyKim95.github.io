---
layout: post
title:  "[백준] 1414_불우이웃돕기 C++"
subtitle:   
date: 2023-04-24 09:21:03 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1414_불우이웃돕기 C++<br/>
<br/>

최소 스패닝 트리를 사용하는 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

int N;
int total = 0;
int Parent[101] = {0,};
vector<pair<int, pair<int, int>>> Edges;

// 부모 찾기
int FindParent(int x)
{
    if (x == Parent[x])
        return x;
    else
    {
        int parent = FindParent(Parent[x]);
        Parent[x] = parent;
        return parent;
    }
}

// 같은 부모를 가진 노드 집합 합치기
void Union(int x, int y)
{
    x = FindParent(x);
    y = FindParent(y);

    if (x < y)
        Parent[y] = x;
    else
        Parent[x] = y;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
        {
            int cost;
            char _input;
            cin >> _input;

            if ('a' <= _input && _input <= 'z')
            {
                cost = _input - 'a' + 1;
                Edges.push_back({cost, {i, j}});
                total += cost;
            }

            else if ('A' <= _input && _input <= 'Z')
            {
                cost = _input - 'A' + 27;
                Edges.push_back({cost, {i, j}});
                total += cost;
            }
        }
    }

    // 부모 초기화
    for (int i = 1; i <= N; ++i)
        Parent[i] = i;

    sort(Edges.begin(), Edges.end());

    for (size_t i = 0; i < Edges.size(); ++i)
    {
        int Node1 = Edges[i].second.first;
        int Node2 = Edges[i].second.second;
        int cost = Edges[i].first;

        if (FindParent(Node1) != FindParent(Node2))
        {
            Union(Node1, Node2);
            total -= cost;
        }
    }

    int p = FindParent(1);

    for (int i = 2; i <= N; ++i)
    {
        if (FindParent(i) != p)
        {
            cout << -1;
            return 0;
        }
    }
    cout << total;

    return 0;
}
```

이 문제를 먼저 이해하는데 오래걸렸던 것 같다.<br/>

무슨소리인고 했더니 다솜이의 컴퓨터를 연결하고 있는 랜선이 있는데<br/>

이 중의 일부를 떼어서 촤소 스패닝 트리만 남겨두고 나머지를 모두 기부한다는 의미였다.<br/>

즉, (총 랜선의 길이) - (최소 스패닝 트리의 길이) 값을 구하라는 이야기였다 아하..<br/>

그래서 최소 스패닝 트리를 써야하는 문제였다.<br/>

크루스칼 알고리즘을 이용해서 최소 스패닝 트리를 구해준 다음에<br/>

마지막에 모두 연결되었는지 체크를 해주었다.<br/>

```
    int p = FindParent(1);

    for (int i = 2; i <= N; ++i)
    {
        if (FindParent(i) != p)
        {
            cout << -1;
            return 0;
        }
    }
    cout << total;
```

위의 코드를 통해서 나머지 노드가 모두 1번 노드와 같은 부모를 두고 있는지,<br/>

그렇다는 이야기는 한 집합 내에 있다는 이야기이다.<br/>

한 집합 내에 있다는 것은 모두 연결되었다는 소리.<br/>

성립한다면 total을 출력하고, 그렇지 않다면 -1을 출력하면 된다.<br/>