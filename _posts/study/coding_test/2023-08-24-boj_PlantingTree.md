---
layout: post
title:  "[백준] 1280_나무 심기 C++"
subtitle:   
date: 2023-08-24 07:01:12 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1280_나무 심기 C++<br/>
<br/>

세그먼트 트리를 응용해보고 펜윅 트리에 대해 알아보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>

#define MAX 200001
#define MOD 1000000007

using namespace std;

int arr[MAX];
vector<int> CntTree;
vector<long long> DistTree;
int N;

void UpdateCnt(int Pos)
{
    while (Pos <= MAX)
    {
        CntTree[Pos] = CntTree[Pos] + 1;
        Pos = Pos + (Pos & -Pos);
    }
}

void UpdateDist(int Pos, int Value)
{
    while (Pos <= MAX)
    {
        DistTree[Pos] = DistTree[Pos] + Value;
        Pos = Pos + (Pos & -Pos);
    }
}

int Cnt(int Pos)
{
    int Ret = 0;
    while (Pos > 0)
    {
        Ret += CntTree[Pos];
        Pos = Pos - (Pos & -Pos);
    }
    return Ret;
}

int Dist(int Pos)
{
    int Ret = 0;
    while (Pos > 0)
    {
        Ret += DistTree[Pos];
        Pos = Pos - (Pos & -Pos);
    }
    return Ret;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 1; i <= N; i++)
    {
        int a;
        cin >> a;
        a++;
        arr[i] = a;
    }

    CntTree.resize(MAX, 0);
    DistTree.resize(MAX, 0);

    UpdateCnt(arr[1]);
    UpdateDist(arr[1], arr[1]);

    long long Ans = 1;
    for (int i = 2; i <= N; ++i)
    {
        long long LeftCnt = Cnt(arr[i] - 1);
        long long RightCnt = Cnt(MAX) - Cnt(arr[i]);
        long long LeftDist = Dist(arr[i] - 1);
        long long RightDist = Dist(MAX) - Dist(arr[i]);

        long long LeftResult = (arr[i] * LeftCnt - LeftDist) % MOD;
        long long RightResult = (RightDist - arr[i] * RightCnt) % MOD;

        long long Result = (LeftResult + RightResult) % MOD;
        Ans *= Result;
        Ans %= MOD;

        UpdateCnt(arr[i]);
        UpdateDist(arr[i], arr[i]);
    }

    cout << Ans % MOD;

    return 0;
}
```

[구글링 후 참고링크](https://yabmoons.tistory.com/479)<br/>

세그먼트 트리에 대한 문제였다.<br/>

세그먼트 트리로 분류된 것을 보고 호기롭게 혼자풀기 도전!<br/>

했으나 실패하였다 ㅋㅋ<br/>

그래서 구글링을 해보았는데 이 문제는 기본적인 세그먼트트리가 아니라<br/>

펜윅 트리라고 해서 세그먼트 트리를 응용해야하는 문제였던 것이다.<br/>

펜윅 트리에 대해서 알고 싶으면 [여기](https://yabmoons.tistory.com/438)를 참고하자.<br/>

참고링크에 따르면 펜윅 트리는<br/>

누적연산을 이용해서 구간에 대한 연산결과를 빠르게 구하는 자료구조라고 한다.<br/>

펜윅 트리에서 이진수, 비트가 사용되는데 그래서 코드에 아래와 같은 코드가 있다.<br/>

```
Pos = Pos + (Pos & -Pos);
```

현재 Pos 번호에서 1이 존재하는 최하위 비트를 찾기 위한 과정이라고 한다.<br/>

이 과정을 통해서 무엇을 할 수 있는가?<br/>

이 노드가 바뀜으로써 영향을 받는 노드를 찾아서<br/>

업데이트를 해주거나 합을 구해서 받아오는 역할을 하는 것 같다.<br/>

이 부분이 제일 신기했다.<br/>

그래서 펜윅 트리를 이용해서 정답 코드를 구현했다.<br/>

사실.. 직접 구현했기 보다는 따라치기를 해보면서 공부한 것에 가깝다..<br/>

매번 같은 분의 글을 참고하는 것 같은데 항상 감사드립니다..<br/>