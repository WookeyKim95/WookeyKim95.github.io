---
layout: post
title:  "[백준] 11505_구간 곱 구하기 C++"
subtitle:   
date: 2023-08-23 07:01:03 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11505_구간 곱 구하기 C++<br/>
<br/>

세그먼트 트리의 기초 파트를 살짝 응용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

#define MOD 1000000007

int N, M, K;
vector<long long> arr;
vector<long long> SegTree;

long long init(int Node, int Start, int End)
{
    if (Start == End)
        return SegTree[Node] = arr[Start];

    int Mid = (Start + End) / 2;

    return SegTree[Node] = (init(Node * 2, Start, Mid) * init(Node * 2 + 1, Mid + 1, End)) % MOD;
}

long long GetMul(int Node, int Start, int End, int Left, int Right)
{
    if (Left > End || Right < Start)
        return 1;

    if (Left <= Start && End <= Right)
        return SegTree[Node];

    int Mid = (Start + End) / 2;

    return (GetMul(Node*2, Start, Mid, Left, Right) * GetMul(Node*2+1, Mid+1, End, Left, Right)) % MOD;
}

long long update(int Node, int Start, int End, int Index, long long Diff)
{
    if (Index < Start || Index > End)
        return SegTree[Node];

    if (Start == End)
        return SegTree[Node] = Diff;

    else
    {
        int Mid = (Start + End) / 2;
        return SegTree[Node] = (update(Node*2, Start, Mid, Index, Diff) * update(Node*2+1, Mid+1, End, Index, Diff)) % MOD;
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M >> K;
    
    arr.resize(N+1);
    
    for (int i = 0; i < N; ++i)
        cin >> arr[i];

    int Tree_Height = (int)ceil(log2(N));
    int Tree_size = 1 << (Tree_Height + 1);
    SegTree.resize(Tree_size);

    init(1, 0, N-1);

    for (int i = 0; i < M+K; ++i)
    {
        long long a, b, c;
        cin >> a >> b >> c;

        b--;

        if (a == 2)
        {
            c--;
            cout << GetMul(1, 0, N-1, b, c) << '\n';
        }
            
        else if (a == 1)
        {
            arr[b] = c;
            update(1, 0, N-1, b, c);
        }
    }

    return 0;
}
```

2048번 문제가 구간 합을 구하는 문제였다면<br/>

이번에는 구간 곱을 구하는 문제이다.<br/>

구간의 연산 결과를 구하는 문제이니 역시 세그먼트 트리를 사용해야하는 문제였다.<br/>

2048번 문제를 풀었다면 비교적 간단할 것으로 보인다.<br/>

구간 합을 구하는 과정에서 조금 고쳐주면 된다.<br/>

내가 겪었던 문제는 업데이트를 하는 과정에서 마음이 약간 걸렸다.<br>

구간 합을 구할 때에는 업데이트를 할 때 원래 있던 수와의 차이를 그대로 넣어서 구해주면 되는데<br/>

구간 곱을 구할 때에는 어떻게 해주면 되는 지 고민을 했다.<br/>

근데 의외로 간단했다.<br/>

수를 그대로 넣어준 다음에 return해서 다시 올라가는 과정에서 값을 갱신해주면 되는 것이었다.<br/>

마치 init을 다시 해주는 과정과 같았다.<br/>

```
long long update(int Node, int Start, int End, int Index, long long Diff)
{
    if (Index < Start || Index > End)
        return SegTree[Node];

    if (Start == End)
        return SegTree[Node] = Diff;

    else
    {
        int Mid = (Start + End) / 2;
        return SegTree[Node] = (update(Node*2, Start, Mid, Index, Diff) * update(Node*2+1, Mid+1, End, Index, Diff)) % MOD;
    }
}
```

그래서 겁만 먹지 않는다면 구간 합 구하기 문제를 풀었다면 풀 수 있는 문제였던 걸로 보인다.<br/>
