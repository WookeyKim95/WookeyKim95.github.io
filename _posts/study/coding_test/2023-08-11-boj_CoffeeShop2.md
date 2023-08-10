---
layout: post
title:  "[백준] 1275_커피숍2 C++"
subtitle:   
date: 2023-08-11 07:33:52 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1275_커피숍2 C++<br/>
<br/>

세그먼트 트리에 대해서 배워보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

long long arr[100001];
vector<long long> SegTree;
int N, Q;

void m_swap(long long& x, long long& y)
{
    long long temp = x;
    x = y;
    y = temp;
    return;
}

long long init(int Node, int Start, int End)
{
    if (Start == End)
        return SegTree[Node] = arr[Start];

    int Mid = (Start + End) / 2;

    return SegTree[Node] = init(Node * 2, Start, Mid) + init(Node * 2 + 1, Mid + 1, End);
}

long long GetSum(int Node, int Start, int End, int Left, int Right)
{
    if (Left > End || Right < Start)
        return 0;

    if (Left <= Start && End <= Right)
        return SegTree[Node];

    int Mid = (Start + End) / 2;

    return GetSum(Node*2, Start, Mid, Left, Right) + GetSum(Node*2+1, Mid+1, End, Left, Right);
}

void update(int Node, int Start, int End, int Index, long long Diff)
{
    if (Index < Start || Index > End)
        return;
    SegTree[Node] = SegTree[Node] + Diff;

    if (Start != End)
    {
        int Mid = (Start + End) / 2;
        update(Node*2, Start, Mid, Index, Diff);
        update(Node*2+1, Mid+1, End, Index, Diff);
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> Q;

    for (int i = 0; i < N; ++i)
        cin >> arr[i];

    int Tree_Height = (int)ceil(log2(N));
    int Tree_Size = (1 << (Tree_Height + 1));
    SegTree.resize(Tree_Size);

    init(1, 0, N-1);

    for (int i = 0; i < Q; ++i)
    {
        long long x, y, a, b;
        cin >> x >> y >> a >> b;

        x--; y--; a--;

        if (x > y)
            m_swap(x, y);

        cout << GetSum(1, 0, N-1, x, y) << '\n';
        
        long long Diff = b - arr[a];
        arr[a] = b;
        update(1, 0, N - 1, a, Diff);
    }

    return 0;
}
```

x번째 수 부터 y번째 수까지 합을 구하는 거라면 단순하게 풀면 아래와 같은 코드로 구하면 되긴한다.<br/>

```
vector<int> vec;
int sum = 0;
for (int i = x; i <= y; ++i)
    sum += vec[i];
```

하지만 데이터의 개수가 적을 때에는 유효하지만<br/>

이 문제에서는 10만 * 10만의 데이터가 최대로 주어지기 때문에<br/>

최대 100억번의 연산을 진행해야한다.<br/>

그렇게 된다면? 시간초과로부터의 무수한 악수요청이 들어올것이다.<br/>

이 문제는 세그먼트 트리로 푸는 문제였다.<br/>

나는 아직 세그먼트 트리의 구현에 대해서 솔직히 잘 모르기 때문에<br/>

구글링을 해서 풀어보았다.<br/>

[도움을 받은 참고링크](https://eun-jeong.tistory.com/18)에 세그먼트 트리에 대한 설명이 있었다.<br/>

세그먼트 트리는 구간의 합을 이진트리에다가 저장하는 자료구조이다.<br/>

그래서 세그먼트 트리에 필요한 크기는 아래와 같다.<br/>

```
int Tree_Height = (int)ceil(log2(N));
int Tree_Size = (1 << (Tree_Height + 1));
SegTree.resize(Tree_Size);
```
또한, 벡터의 resize 함수를 이용해서 트리사이즈를 미리 확장시켜놓으면 된다.<br/>
<br/>

### Init(트리 만들기)<br/>

```
long long init(int Node, int Start, int End)
{
    if (Start == End)
        return SegTree[Node] = arr[Start];

    int Mid = (Start + End) / 2;

    return SegTree[Node] = init(Node * 2, Start, Mid) + init(Node * 2 + 1, Mid + 1, End);
}

```

이진 트리를 확장해가면서 리프노드에 도달하면 수를 기록하고<br/>

그 수들의 합을 부모노드(리프노드 입장에선 부모노드)에 기록하면서 올라온다.<br/>

<br/>

### 합 구하기<br/>

```
long long GetSum(int Node, int Start, int End, int Left, int Right)
{
    if (Left > End || Right < Start)
        return 0;

    if (Left <= Start && End <= Right)
        return SegTree[Node];

    int Mid = (Start + End) / 2;

    return GetSum(Node*2, Start, Mid, Left, Right) + GetSum(Node*2+1, Mid+1, End, Left, Right);
}
```

만약에 범위를 벗어났으면 0을 리턴시키고<br/>

목표로 했던 범위 안에 들어가게 되면 합을 리턴하도록 시킨다.<br/>

<br/>

### 노드 갱신하기<br/>

```
void update(int Node, int Start, int End, int Index, long long Diff)
{
    if (Index < Start || Index > End)
        return;
    SegTree[Node] = SegTree[Node] + Diff;

    if (Start != End)
    {
        int Mid = (Start + End) / 2;
        update(Node*2, Start, Mid, Index, Diff);
        update(Node*2+1, Mid+1, End, Index, Diff);
    }
}
```

만약에 초기배열의 값이 바뀌었다면 세그먼트 트리도 바꿔줘야한다.<br/>

Diff값은 바뀐값과 초기에 있던 값의 차이이다.<br/>

이를 세그먼트 트리에 반영하는 과정이라고 보면 되겠다.<br/>