---
layout: post
title:  "[백준] 10655_마라톤 1 C++"
subtitle:   
date: 2024-01-15 07:31:42 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 10655_마라톤 1 C++<br/>
<br/>

최적화를 신경써야 했던 브루트포스 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

int Distance(int x1, int y1, int x2, int y2)
{
    return (int)abs(x1 - x2) + (int)abs(y1 - y2);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;
    vector<pair<int, int>> Checkpoint;

    int Sum = 0;
    for (int i = 0; i < N; ++i)
    {
        int x, y;
        cin >> x >> y;
        Checkpoint.push_back({x, y});

        if (i > 0)
            Sum += Distance(Checkpoint[i-1].first, Checkpoint[i-1].second, Checkpoint[i].first, Checkpoint[i].second);
    }

    int Ans = Sum;
    for (int i = 1; i < N-1; ++i)
    {
        int PrevDist = Distance(Checkpoint[i-1].first, Checkpoint[i-1].second, Checkpoint[i].first, Checkpoint[i].second);
        int NextDist = Distance(Checkpoint[i+1].first, Checkpoint[i+1].second, Checkpoint[i].first, Checkpoint[i].second);
        int NewDist = Distance(Checkpoint[i-1].first, Checkpoint[i-1].second, Checkpoint[i+1].first, Checkpoint[i+1].second);
        
        int NewSum = Sum - PrevDist - NextDist + NewDist;

        if (Ans > NewSum)
            Ans = NewSum;
    }

    cout << Ans;

    return 0;
}
```

<br/>

사실 일반적으로 생각하는 방법은 2중 for문을 돌리는 방법일 것이다.<br/>

그래서 건너 뛸 점 하나를 정한 뒤, 만약에 달리다가 건너 뛸 점이라면 생략하도록 해서 최소 값을 구하는 방법을 생각할 수 있겠지.<br/>

하지만 그렇게 하면 시간초과가 날 것이 뻔했기 때문에 시간복잡도를 O(N^2)에서 O(N)으로 줄이는 방법을 생각했다.<br/>

- 거리의 총 합을 구한다.
- for문을 돌려서 중간 점에서 (그 점의 이전 점과의 거리), (그 점의 다음 점과의 거리), (이전 점과 다음 점의 거리)를 구한다.
- 원래 총 합에서 이전점과의 거리 그리고 다음 점과의 거리를 뺀 뒤, 이전 점과 다음 점의 거리를 구한다.

이렇게 하면 생략했을 때의 달리는 거리를 구할 수 있다.<br/>

이렇게 하면 for문을 1번만 돌리면 되기 때문에 시간 내에 원하는 답을 구할 수 있다.<br/>