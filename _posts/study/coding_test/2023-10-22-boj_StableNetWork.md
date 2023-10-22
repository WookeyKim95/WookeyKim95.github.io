---
layout: post
title:  "[백준] 2406_안정적인 네트워크 C++"
subtitle:   
date: 2023-10-22 07:05:02 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2406_안정적인 네트워크 C++<br/>
<br/>

최소 스패닝 트리 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, pair<int, int>> piii;

int N, M;
int Arr[1001][1001];
int Parent[1001];
vector<piii> Edges;

bool cmp(piii a, piii b)
{
    return a.first < b.first;
}

int FindParent(int a)
{
    if (Parent[a] == a)
        return a;
    return Parent[a] = FindParent(Parent[a]);
}

bool Union(int a, int b)
{
    int Pa = FindParent(a);
    int Pb = FindParent(b);

    if (Pa == Pb)
        return false;
    else
    {
        Parent[Pa] = Pb;
        return true;
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 1; i <= N; ++i)
        Parent[i] = i;

    for (int i = 0; i < M; ++i)
    {
        int a, b;
        cin >> a >> b;

        Union(a, b);
    }

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
            cin >> Arr[i][j];
    }

    for (int i = 2; i <= N; ++i)
    {
        for (int j = i+1; j <= N; ++j)
            Edges.push_back({Arr[i][j], {i, j}});
    }

    sort(Edges.begin(), Edges.end(), cmp);

    int sum = 0;
    vector<pair<int, int>> vec;

    for (size_t i = 0; i < Edges.size(); ++i)
    {
        int N1 = Edges[i].second.first;
        int N2 = Edges[i].second.second;

        if (FindParent(N1) != FindParent(N2))
        {
            Union(N1, N2);
            vec.push_back({N1, N2});
            sum += Edges[i].first;
        }
    }

    if(vec.size() == 0)
        cout << 0 << " " << 0 << '\n';
    else
    {
        cout << sum << " " << vec.size() << '\n';

        for(size_t i = 0; i < vec.size(); ++i)
            cout << vec[i].second << " " << vec[i].first << '\n';
    }


    return 0;
}
```

오랫동안 최소 스패닝 트리 문제를 풀지 않아서 까먹을까봐 풀었다.<br/>

인접 리스트가 아니라 인접 행렬 형태로 연결이 주어지는 것이 낮설었다.<br/>

크루스칼 알고리즘이었나? 그것으로 구현했는데 순서를 잊지 말자.<br/>

- 연결 대상으로 주어진 것은 부모를 같게 만든다.
- 가능한 모든 엣지를 Edge에 수집한다.
- Edge를 비용 기준으로 오름차순으로 정렬한다.
- Edge 벡터를 돌면서 부모가 다르다면 연결하면서 Sum에 비용을 더한다

이 문제도 똑같다. 그리고 vec에 연결할 대상을 저장한다는 것만 다르다.