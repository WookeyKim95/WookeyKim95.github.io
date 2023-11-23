---
layout: post
title:  "[백준] 1833_고속철도 설계하기 C++"
subtitle:   
date: 2023-11-24 07:18:29 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1833_고속철도 설계하기 C++<br/>
<br/>

MST를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, pair<int, int>> piii;

vector<piii> Edges;
vector<int> Parent;

int FindParent(int a)
{
    if (Parent[a] == a)
        return a;
    else
        return Parent[a] = FindParent(Parent[a]);
}

void Union(int a, int b)
{
    int A = FindParent(a);
    int B = FindParent(b);

    Parent[A] = B;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    for (int i = 0; i <= N; ++i)
        Parent.push_back(i);

    int C = 0;
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            int _input;
            cin >> _input;
            if (i <= j)
                continue;
            if (_input < 0)
            {
                C -= _input;
                Union(j+1, i+1);
            }
            else
                Edges.push_back({_input, {j+1, i+1}});
        }
    }

    sort(Edges.begin(), Edges.end());

    vector<pair<int, int>> New;
    for (size_t i = 0; i < Edges.size(); ++i)
    {
        int Cost = Edges[i].first;
        int a = Edges[i].second.first;
        int b = Edges[i].second.second;

        int A = FindParent(a);
        int B = FindParent(b);

        if (A != B)
        {
            C += Cost;
            New.push_back({a, b});
            Union(A, B);
        }
    }

    cout << C << ' ' << New.size() << '\n';
    for (size_t i = 0; i < New.size(); ++i)
        cout << New[i].first << ' ' << New[i].second << '\n';

    return 0;
}
```
<br/>

요즘들어 MST에 재미가 들린건지 MST 문제가 많이 풀린다.<br/>

이 문제도 MST를 활용해볼 수 있는 문제이다.<br/>

다만, 미리 연결되어 있는 간선에 대해서 파악할 필요가 있다.<br/>

다행히, 음수로 주어진 수는 미리 연결된 간선이기 때문에<br/>

음수로 비용이 주어질 때에는 아래 기능을 실행하도록 구현하였다.<br/>

- Union을 실행한다.
- 비용에 값을 더한다.

이렇게 하고, 양수로 주어지는 경우에는 연결할 간선 목록, 즉 Edges에 담도록 했다.<br/>

그리고 새로 연결할 간선목록도 나중에 출력해야하기 때문에 추가를 해주었고,<br/>

나머지는 기존 크루스칼 알고리즘 형태를 따라가면 된다.<br/>