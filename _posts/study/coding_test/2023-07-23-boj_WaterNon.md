---
layout: post
title:  "[백준] 1368_물대기 C++"
subtitle:   
date: 2023-07-23 07:56:37 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1368_물대기 C++<br/>
<br/>

크루스칼 알고리즘을 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, pair<int, int>> piii;

int N;
vector<int> parent;
vector<piii> edges;

bool cmp(piii a, piii b)
{
    return a.first < b.first;
}

int get_parent(int a)
{
    if (parent[a] == a)
        return a;
    
    return parent[a] = get_parent(parent[a]);
}
 
 
void union_parents(int a, int b)
{
    a = get_parent(a);
    b = get_parent(b);
    
    if (a > b)
        parent[a] = b;
    else
        parent[b] = a;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    parent.push_back(0);
    for (int i = 1; i <= N; ++i)
    {
        int _input;
        cin >> _input;
        parent.push_back(i);
        edges.push_back({ _input, {0, i} });
    }

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
        {
            int _input;
            cin >> _input;
            edges.push_back({ _input, {i, j} });
        }
    }

    sort(edges.begin(), edges.end(), cmp);

    int sum = 0;
    for (size_t i = 0; i < edges.size(); ++i)
    {
        int node1 = edges[i].second.first;
        int node2 = edges[i].second.second;
        int cost = edges[i].first;
        
        if(get_parent(node1) != get_parent(node2))
        {
            union_parents(node1, node2);
            sum += cost;
        }
    }

    cout << sum;

    return 0;
}
```

아직은 크루스칼 알고리즘 구현이 힘들어서 [참고링크](https://conkjh032.tistory.com/386)를 참고해서 풀었다.<br/>

되도록이면 나만의 코드로 구현을 하고자 했는데 그러기는 힘들었던 것 같다.<br/>

결국 edge벡터를 따로 만들어서 구현하였다.<br/>

우물을 파는 cost와 연결하는 cost를 분리하려고 했지만 그것까지는 힘들었던 것 같다.<br/>

이 점이 아쉬웠다.<br/>

```
struct Non
{
    int parent;
    vector<pair<int, int>> link;
    int Umul;
};
```

처음에 이렇게 구현했었는데 결국 이 struct는 폐기..<br/>

인덱스 부분만 주의해서 입력을 받은 다음에 유니온 파인드를 통한 크루스칼 알고리즘을 구현,<br/>

최소 스패닝 트리를 이용해서 최소 값을 출력하도록 하였다.<br/>