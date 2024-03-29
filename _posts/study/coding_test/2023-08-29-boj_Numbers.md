---
layout: post
title:  "[백준] 1849_순열 C++"
subtitle:   
date: 2023-08-29 07:23:27 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1849_순열 C++<br/>
<br/>

세그먼트 트리를 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

int N;
vector<int> arr;
vector<int> SegTree;

void Init(int Node, int Start, int End)
{
    if (Start == End)
    {
        SegTree[Node] = 1;
        return;
    }
    int Mid = (Start + End) / 2;
    Init(Node * 2, Start, Mid);
    Init(Node * 2 + 1, Mid+1, End);
    SegTree[Node] = SegTree[Node * 2] + SegTree[Node * 2 + 1];
}

int FindIndex(int Node, int Start, int End, int Count)
{
    if (Start == End)
        return Start;
    
    int Mid = (Start + End) / 2;

    if (Count < SegTree[Node * 2])
        return FindIndex(Node * 2, Start, Mid, Count);
    else
        return FindIndex(Node * 2 + 1, Mid+1, End, Count - SegTree[Node*2]);
}

void UpdateTree(int Node, int Start, int End, int Index)
{
    if (Index < Start || Index > End)
        return;
    
    if (Start == End)
    {
        SegTree[Node] = 0;
        return;
    }

    int Mid = (Start + End) / 2;
    UpdateTree(Node * 2, Start, Mid, Index);
    UpdateTree(Node * 2 + 1, Mid+1, End, Index);
    SegTree[Node]--;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    int Tree_Height = (int)ceil(log2(N));
    int Tree_Size = (1 << (Tree_Height + 1));
    SegTree.resize(Tree_Size);
    arr.resize(N+1, 0);

    Init(1, 1, N);

    for (int i = 1; i <= N; ++i)
    {
        int Count, Index;
        cin >> Count;
        Index = FindIndex(1, 1, N, Count);
        arr[Index] = i;
        UpdateTree(1, 1, N, Index);
    }

    for (int i = 1; i <= N; ++i)
        cout << arr[i] << '\n';

    return 0;
}
```

우선 전체적인 코드는 [이분의 글](https://restudycafe.tistory.com/384)을 참고하였다.<br/>

시간이 0.5초밖에 주어지지 않길래 DP일까? 세그먼트 트리일까? 생각했는데<br/>

오호라! 세그먼트 트리였다!<br/>

이번에는 특이하게 어떤 수 보다 큰 수의 개수가 입력으로 주어지고<br/>

순열을 복원하는 문제였다.<br/>

이 문제의 핵심은 세그먼트 트리의 역할인데<br/>

참고 링크에 올려놓은 분의 글과 코드를 분석해보니<br/>

1부터 N까지 차례대로 arr을 채워주는데 Count 개수에 따라서<br/>

Index를 찾아 몇번째 칸에 저장을 해주어야 하는지 찾는 로직인 것이다.<br/>

그리고 값을 채웠으면 리프노드의 값을 0으로 만들어서<br/>

세그먼트 트리를 갱신해주어 다음 값이 몇번째 인덱스인지를 찾을 때 올바르게 찾도록 한다.<br/>

즉, 세그먼트 트리의 역할은 구간의 합을 저장하는 것이 아닌 무슨 수가 몇번 인덱스로 가야하는지<br/>

찾는 네비게이션 역할을 하는 것이다 이번엔.<br/>

세그먼트 트리를 처음 접했을 때에는 단순히 구간의 합을 저장하는 용도로 생각했는데<br/>

이제는 응용할 때에는 "순열의 특성"을 저장하는 용도라고 생각해야겠다.<br/>