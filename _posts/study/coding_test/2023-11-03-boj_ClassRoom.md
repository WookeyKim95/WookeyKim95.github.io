---
layout: post
title:  "[백준] 11000_강의실 배정 C++"
subtitle:   
date: 2023-11-03 07:17:26 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11000_강의실 배정 C++<br/>
<br/>

정렬과 우선순위 큐를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    vector<pair<int, int>> vec;
    priority_queue<int, vector<int>, greater<int>> pq;

    for (int i = 0; i < N; ++i)
    {
        int s, e;
        cin >> s >> e;
        vec.push_back({s, e});
    }

    sort(vec.begin(), vec.end());

    for (size_t i = 0; i < vec.size(); ++i)
    {
        pq.push(vec[i].second);
        if (pq.top() <= vec[i].first)
            pq.pop();
    }

    cout << pq.size();

    return 0;
}
```

맨 처음 생각했던 아이디어는 강의 종료시간을 담은 벡터를 또 하나 만들어서<br/>

강의 종료 시간을 순회를 하는 것이었다.<br/>

만약에 강의 종료 시간에 맞게 다음 강의가 시작되는 것이면 그냥 넘어가고<br/>

그렇지 않다면 vec에다가 push를 하는 것이다.<br/>

그런데 생각해보면 N의 최대값은 200000이기 때문에 100% 시간 초과가 뜰 것이라 생각했다.<br/>

근데 생각해보면 강의실 하나당 제일 늦은 강의의 종료 시간만 남겨주면 되는 것 아닌가?<br/>

그리고 정렬을 하면 더 쉬워질 것이고 말이지.<br/>

vec 대신에 우선순위 큐를 사용하면 되는 문제였다.<br/>

그래서 우선순위 큐를 만들어 주었고,<br/>

```
for (size_t i = 0; i < vec.size(); ++i)
{
    pq.push(vec[i].second);
    if (pq.top() <= vec[i].first)
        pq.pop();
}
```

이부분을 이용해서 강의실의 시간을 관리해준다.<br/>

그리고 우선순위 큐의 크기가 곧 필요한 강의실의 개수가 되는 것이다.<br/>