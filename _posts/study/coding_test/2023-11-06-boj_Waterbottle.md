---
layout: post
title:  "[백준] 2251_물통 C++"
subtitle:   
date: 2023-11-06 07:05:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2251_물통 C++<br/>
<br/>

DFS를 활용했지만 종료 조건을 설정하기 은근 어려웠던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <set>
#include <cstring>

using namespace std;

set<pair<int, pair<int, int>>> visited;
set<int> Able;
int total;
int A, B, C;

void DFS(int CurA, int CurB, int CurC)
{
    if (visited.find({CurA, {CurB, CurC}}) != visited.end())
        return;

    visited.insert({CurA, {CurB, CurC}});

    if (CurA == 0)
        Able.insert(CurC);
    
    // C에서 A로 넣는 경우
    if (CurA + CurC <= A)
        DFS(CurA + CurC, CurB, 0);
    if (CurA + CurC > A)
        DFS(A, CurB, CurC - (A - CurA));

    // A에서 C로 넣는 경우
    if (CurA + CurC <= C)
        DFS(0, CurB, CurA + CurC);
    if (CurA + CurC > C)
        DFS(CurA - (C - CurC), CurB, C);

    // A에서 B로 넣는 경우
    if (CurA + CurB <= B)
        DFS(0, CurB + CurA, CurC);
    if (CurA + CurB > B)
        DFS(CurA - (B - CurB), B, CurC);

    // B에서 A로 넣는 경우
    if (CurA + CurB <= A)
        DFS(CurB + CurA, 0, CurC);
    if (CurA + CurB > A)
        DFS(A, CurB - (A - CurA), CurC);
    
    // B에서 C로 넣는 경우
    if (CurC + CurB <= C)
        DFS(CurA, 0, CurC + CurB);
    if (CurC + CurB > C)
        DFS(CurA, CurB - (C - CurC), C);

    // C에서 B로 넣는 경우
    if (CurC + CurB <= B)
        DFS(CurA, CurC + CurB, 0);
    if (CurC+ CurB > B)
        DFS(CurA, B, CurC - (B - CurB));
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> A >> B >> C;

    DFS(0, 0, C);

    for (auto iter = Able.begin(); iter != Able.end(); ++iter)
        cout << *iter << ' ';
        
    return 0;
}
```

어떻게 보면 6진트리로 이루어지는 구조같은데<br/>

현재 용량이 각각 CurA, CurB, CurC라고하면<br/>

각각의 경우의 수를 모두 따져서 깊이 우선탐색을 하도록 구현하였다.<br/>

그리고 (CurA, CurB, CurC) 순서쌍을 방문한 적이 있냐를 판단하기 위해서 set을 사용하였다.<br/>

방문한 적이 있다면 return 하도록 구현하였다.<br/>

그리고 모든 작업이 완료 되었을 때 Able에 들어있는 값을 출력하도록 했다.<br/>

set은 자동으로 오름차순으로 정렬해주니까 그대로 for문을 돌려서 출력하면 된다.<br/>


## 오답제출코드<br/>

```
#include <iostream>
#include <set>
#include <cstring>

using namespace std;

bool visitedC[201];

set<int> Able;
int total;
int A, B, C;

void DFS(int CurA, int CurB, int CurC)
{
    if (visitedC[CurC])
        return;
    
    visitedC[CurC] = true;
    Able.insert(CurC);
    
    // C에서 A로 넣는 경우
    if (CurA + CurC <= A)
        DFS(CurA + CurC, CurB, 0);
    if (CurA + CurC > A)
        DFS(A, CurB, CurC - (A - CurA));

    // A에서 C로 넣는 경우
    if (CurA + CurC <= C)
        DFS(0, CurB, CurA + CurC);
    if (CurA + CurC > C)
        DFS(CurA - (C - CurC), CurB, C);

    // A에서 B로 넣는 경우
    if (CurA + CurB <= B)
        DFS(0, CurB + CurA, CurC);
    if (CurA + CurB > B)
        DFS(CurA - (B - CurB), B, CurC);

    // B에서 A로 넣는 경우
    if (CurA + CurB <= A)
        DFS(CurB + CurA, 0, CurC);
    if (CurA + CurB > A)
        DFS(A, CurB - (A - CurA), CurC);
    
    // B에서 C로 넣는 경우
    if (CurC + CurB <= C)
        DFS(CurA, 0, CurC + CurB);
    if (CurC + CurB > C)
        DFS(CurA, CurB - (C - CurC), C);

    // C에서 B로 넣는 경우
    if (CurC + CurB <= B)
        DFS(CurA, CurC + CurB, 0);
    if (CurC+ CurB > B)
        DFS(CurA, B, CurC - (B - CurB));
    
    visitedC[CurC] = false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> A >> B >> C;

    total = C;

    memset(visitedC, false, sizeof(visitedC));

    DFS(0, 0, 10);

    for (auto iter = Able.begin(); iter != Able.end(); ++iter)
    {
        if (*iter == 0)
            continue;
        cout << *iter << ' ';
    }
        
    return 0;
}
```

우선 틀린 이유 하나는 문제를 제대로 안읽었다.<br/>

CurA가 0일 때에 CurC를 Able에 넣는 작업을 했어야 했다.<br/>

그리고 종료조건도 잘못되었던게 CurC만 true로 처리해버릴 것이 아니라<br/>

(CurA, CurB, CurC) 쌍을 방문한 적이 있냐를 따져야 했다.<br/>

그리고 제일 어이없는 실수.

```
DFS(0, 0, 10);
```

10 대신에 C를 넣어야지..