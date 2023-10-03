---
layout: post
title:  "[백준] 1931_회의실 배정 C++"
subtitle:   
date: 2023-10-02 06:53:53 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1931_회의실 배정 C++ <br/>
<br/>

정렬 방식을 설정해보는 그리디 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

typedef pair<int, int> pii;

bool cmp(pii a, pii b)
{
    if (a.second < b.second)
        return true;
    else if (a.second == b.second)
        return a.first < b.first;
    else
        return false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    vector<pii> vec;
    int N;
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int S, E;
        cin >> S >> E;
        vec.push_back({S, E});
    }

    sort(vec.begin(), vec.end(), cmp);

    int ans = 0;
    int EndTime = 0;

    for (int i = 0; i < N; ++i)
    {   
        if (EndTime <= vec[i].first)
        {
            ans++;
            EndTime = vec[i].second;
        }
    }

    cout << ans;

    return 0;
}
```

회의실 하나에 최대한 많은 회의를 할 수 있도록 만들어야하는 문제였다.<br/>

여기서 핵심은 그리디 문제이니 회의 정보를 정렬해서<br/>

하나의 회의실에 최대한 많은 회의를 할 수 있도록 만들어야한다.<br/>

자, 그럼 어떻게 정렬을 해야할까?<br/>

```
bool cmp(pii a, pii b)
{
    if (a.second < b.second)
        return true;
    else if (a.second == b.second)
        return a.first < b.first;
    else
        return false;
}
```
나는 int쌍을 받았고, 첫번째 인자에다가 회의 시작시각, 두번째 인자에다가 회의 종료시각을 넣었다.<br/>

그리고 회의가 끝나는 시각이 빠른 순서대로 정렬을 시켜주었다.<br/>

```
for (int i = 0; i < N; ++i)
{   
    if (EndTime <= vec[i].first)
    {
        ans++;
        EndTime = vec[i].second;
    }
}
```

그리고 정렬된 벡터를 순회하면서, 현재 진행하는 회의 종료시각보다 다음 회의 시작시각이 늦는다면<br/>

다음 회의를 진행시키면서 ans를 증가시킨다.<br/>

여기서 중요한 점은 왜 회의 종료시각 순서대로 정렬했는가인데<br/>

```
0 6
1 3
3 5
```
와 같이 회의가 잡혔다고 해보자.<br/>

회의 시작 시각 순서대로 정렬하면<br/>

```
0 6
1 3
3 5
```
로 정렬되고,<br/>

회의 종료 시각 순서대로 정렬하면<br/>

```
1 3
3 5
0 6
```
이 된다.<br/>

즉, 회의 종료 시각 순서대로 정렬해야 일찍 시작한다고 하더라도 회의 시간이 긴 회의를 최대한 회피할 수 있다.<br/>

빠르게 시작하면서 회의 시간이 짧은 회의를 넣어야 더 많은 회의를 진행할 수 있으니까.<br/>

그 기준이 바로 회의 종료 시각이 빠른 시간 순서대로 정렬하는 것이다.<br/>