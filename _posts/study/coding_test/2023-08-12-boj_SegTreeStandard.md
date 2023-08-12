---
layout: post
title:  "[백준] 2042_구간 합 구하기 C++"
subtitle:   
date: 2023-08-12 07:25:46 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2042_구간 합 구하기 C++<br/>
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

int N, M, K;
vector<long long> arr;
vector<long long> SegTree;

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

    cin >> N >> M >> K;
    arr.resize(N);

    for (int i = 0; i < N; ++i)
        cin >> arr[i];
    
    int Tree_Height = (int)ceil(log2(N));
    int Tree_Size = (1 << (Tree_Height + 1));
    SegTree.resize(Tree_Size);

    init(1, 0, N-1);

    for (int i = 0; i < M+K; ++i)
    {
        long long a, b, c;
        cin >> a >> b >> c;

        b--;

        if (a == 2)
        {
            c--;
            cout << GetSum(1, 0, N-1, b, c) << '\n';
        }
            
        else if (a == 1)
        {
            long long diff = c-arr[b];
            arr[b] = c;
            update(1, 0, N-1, b, diff);
        }
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

이 문제에서는 10000번의 연산, 그리고 10000번의 데이터 변경이 일어날 수 있다.<br/>

따라서 위의 코드로 풀었다가는 시간초과의 무수한 악수요청이 들어오게 된다.<br/>

메모리는 많이 잡아먹겠지만 구간의 합을 저장해서 계산속도를 빠르게 할 수 있는 자료구조가<br/>

세그먼트 트리이다.<br/>

이 문제는 그런 자료구조를 배워볼 수 있는 문제이다.<br/>

```
int Tree_Height = (int)ceil(log2(N));
int Tree_Size = (1 << (Tree_Height + 1));
SegTree.resize(Tree_Size);
```

우선, 트리의 높이는 (int)ceil(log2(N)) 이며,<br/>

그 높이만큼 2의 제곱을 해준 값이 트리의 크기이다.<br/>

```
long long init(int Node, int Start, int End)
{
    if (Start == End)
        return SegTree[Node] = arr[Start];

    int Mid = (Start + End) / 2;

    return SegTree[Node] = init(Node * 2, Start, Mid) + init(Node * 2 + 1, Mid + 1, End);
}
```
노드를 방문하면서 아래로 내려가다가 리프노드에 도달하면<br/>

값을 저장하고 구간의 합을 기록하면서 다시 올라오는 구조이다.<br/>

<br/>

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

그리고 구간의 합을 구할 때에는 해당 노드를 찾아가서 값을 가지고 합치면서 올라오는 구조이다.<br/>

<br/>

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

업데이트는 수를 변경함과 동시에 구간의 합을 변경해줘야한다.<br/>
<br/>

## 오답제출코드<br/>
<br/>

```
init(1, 0, N-1);

for (int i = 0; i < M+K; ++i)
{
    int a, b, c;
    cin >> a >> b >> c;

    b--;

    if (a == 2)
    {
        c--;
        cout << GetSum(1, 0, N-1, b, c) << '\n';
    }
        
    else if (a == 1)
        update(1, 0, N-1, b, c-arr[b]);
}
```

맞왜틀의 수렁에 잠시 빠졌다가 아차! 했었다.<br/>

어디가 틀렸었을까?<br/>

```
int a, b, c;
```

따란! 이부분이다.<br/>

c값은 인덱스 뿐만 아니라 바꿀 값도 넣어줘야하기 때문에<br/>

c는 반드시 long long으로 선언해줘야한다.<br/>

이 점을 실수하지 말자!<br/>