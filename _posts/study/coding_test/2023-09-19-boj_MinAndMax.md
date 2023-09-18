---
layout: post
title:  "[백준] 2357_최솟값과 최댓값 C++"
subtitle:   
date: 2023-09-19 07:22:18 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2357_최솟값과 최댓값 C++<br/>
<br/>

세그먼트 트리를 이용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>

#define INTMAX 2147483647

using namespace std;

int N, M;
vector<int> SegTreeMin;
vector<int> SegTreeMax;
vector<int> Arr;

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void init(int Node, int Start, int End)
{
    if (Start == End)
    {
        SegTreeMin[Node] = SegTreeMax[Node] = Arr[Start];
        return;
    }
        
    int Mid = (Start + End) / 2;

    init(2*Node, Start, Mid);
    init(2*Node+1, Mid+1, End);

    SegTreeMin[Node] = m_min(SegTreeMin[2*Node], SegTreeMin[2*Node+1]);
    SegTreeMax[Node] = m_max(SegTreeMax[2*Node], SegTreeMax[2*Node+1]);
    return;
}

pair<int, int> Find(int Node, int Start, int End, int Left, int Right)
{
    if (Start > Right || End < Left)
        return {INTMAX, 0};
    else if (Left <= Start && End <= Right)
        return {SegTreeMin[Node], SegTreeMax[Node]};
    else
    {
        int Mid = (Start + End) / 2;
        pair<int, int> L = Find(2*Node, Start, Mid, Left, Right);
        pair<int, int> R = Find(2*Node+1, Mid+1, End, Left, Right);

        return {m_min(L.first, R.first), m_max(L.second, R.second)};
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        Arr.push_back(_input);
    }

    int TreeHeight = (int)ceil(log2(N));
    int TreeSize = 1 << (TreeHeight + 1);

    SegTreeMin.resize(TreeSize);
    SegTreeMax.resize(TreeSize);

    init(1, 0, N-1);

    for (int i = 0; i < M; ++i)
    {
        int Left, Right;
        cin >> Left >> Right;
        
        pair<int, int> Result = Find(1, 0, N-1, Left-1, Right-1);
        cout << Result.first << ' ' << Result.second << '\n';
    }

    return 0;
}
```

어떻게 보면 세그먼트 트리를 살짝 응용해보는 문제이다.<br/>

보통 세그먼트 트리에다가는 구간 합, 구간 곱을 저장해놓는데,<br/>

여기서는 세그먼트 트리에다가 값을 저장해놓고<br/>

구간에서의 최댓값과 최솟값을 찾는 것으로 활용을 했다.<br/>

보통 구간 합을 구한다고 하면

```
SegTree[Node] = SegTree[Node*2] + SegTree[Node*2+1];
```
이렇게 되기 마련인데<br/>

여기서는 최댓값, 최솟값을 꺼내오는 것이니<br/>

```
int Mid = (Start + End) / 2;
pair<int, int> L = Find(2*Node, Start, Mid, Left, Right);
pair<int, int> R = Find(2*Node+1, Mid+1, End, Left, Right);

return {m_min(L.first, R.first), m_max(L.second, R.second)};
```
이렇게 변한 것이 큰 차이점이라고 볼 수 있을 것 같다.<br/>