---
layout: post
title:  "[백준] 1379_강의실 2 C++"
subtitle:   
date: 2023-07-15 08:32:08 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1379_강의실 2 C++<br/>
<br/>

우선순위 큐를 이용해보는 그리디 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, pair<int, int>> piii;

struct cmp_end
{
    bool operator() (piii a, piii b)
    {
        return a.second.second > b.second.second;
    }
};

bool cmp_start(piii a, piii b)
{
    return a.second.first < b.second.first;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    vector<piii> Class;

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int num, start, end;
        cin >> num >> start >> end;
        Class.push_back( {num, {start, end} });
    }

    sort(Class.begin(), Class.end(), cmp_start);

    priority_queue<piii, vector<piii>, cmp_end> pq;
    vector<int> room(N, 0);
    int ans = 0;

    for (int i = 0; i < N; ++i)
    {
        if (!pq.empty() && pq.top().second.second <= Class[i].second.first)
        {
            room[Class[i].first-1] = room[pq.top().first-1];
            pq.pop();
        }

        else
        {
            ans++;
            room[Class[i].first-1] = ans; 
        }
        pq.push(Class[i]);
    }   

    cout << ans << '\n';

    for (int i = 0; i < N; ++i)
        cout << room[i] << '\n';

    return 0;
}
```

[참고링크](https://whyou-story.tistory.com/60)를 참고하여 풀었다.<br/>

<br/>

벡터에 강의를 넣어준 다음에 강의 시작시간 순으로 정렬하고,<br/>

```
bool cmp_start(piii a, piii b)
{
    return a.second.first < b.second.first;
}

...

for (int i = 0; i < N; ++i)
{
    int num, start, end;
    cin >> num >> start >> end;
    Class.push_back( {num, {start, end} });
}

sort(Class.begin(), Class.end(), cmp_start);
```

강의 종료시간 순서대로 우선순위 큐를 형성시켜준다.<br/>

```
struct cmp_end
{
    bool operator() (piii a, piii b)
    {
        return a.second.second > b.second.second;
    }
};

...

priority_queue<piii, vector<piii>, cmp_end> pq;
vector<int> room(N, 0);
int ans = 0;

pq.push(Class[0]);

for (int i = 0; i < N; ++i)
{
    if (!pq.empty() && pq.top().second.second <= Class[i].second.first)
    {
        room[Class[i].first-1] = room[pq.top().first-1];
        pq.pop();
    }

    else
    {
        ans++;
        room[Class[i].first-1] = ans; 
    }
    pq.push(Class[i]);
}   
```

이 문제를 풀면서 어려웠던 부분은 room을 배정해주는 것이었다.<br/>

참고링크를 참고하고 생각해보니 최대 강의실의 개수는 N을 넘지 않는다!<br/>

이 점을 이용해서 room 벡터에다가 미리 정수를 N개 할당하면 되는 문제였다.<br/>

그리고 아쉬웠던 점도 존재했다.<br/>

제출을 하면서 보니,

```
pair<int, pair<int, int>>
```
자료형을 이렇게 써서 아래 부분이 가독성이 좀 별로였다.<br/>

```
if (!pq.empty() && pq.top().second.second <= Class[i].second.first)
```

참고링크 쓴이님처럼 pair 대신에 struct를 써서 가독성을 높일 수 있겠다 생각했다.<br/>

나중에 리팩토링을 진행해봐야겠다.<br/>