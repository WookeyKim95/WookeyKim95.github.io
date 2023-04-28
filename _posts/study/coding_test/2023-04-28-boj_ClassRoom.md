---
layout: post
title:  "[백준] 1374_강의실 C++"
subtitle:   
date: 2023-04-28 09:23:18 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1374_강의실 C++<br/>
<br/>

우선순위 큐를 활용하는 그리디 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

typedef pair<int, pair<int, int>> ppInt;

bool cmp(ppInt a, ppInt b)
{
    int aStart = a.second.first;
    int bStart = b.second.first;

    int aEnd = a.second.second;
    int bEnd = b.second.second;

    if (aStart < bStart)
        return true;
    else if (aStart > bStart)
        return false;
    else
        return aEnd < bEnd;
}

struct cmp_struct
{
    bool operator()(ppInt a, ppInt b)
    {
        int aStart = a.second.first;
        int bStart = b.second.first;

        int aEnd = a.second.second;
        int bEnd = b.second.second;

        if (aEnd > bEnd)
            return true;
        else if (aEnd < bEnd)
            return false;
        else
            return aStart < bStart;
    }
};

int main()
{
    int N, ans = 1;
    cin >> N;

    vector<ppInt> Class;
    priority_queue<ppInt, vector<ppInt>, cmp_struct> pq;

    for (int i = 0; i < N; ++i)
    {
        int num, start, end;
        cin >> num >> start >> end;

        Class.push_back({num, {start, end}});
    }

    if (N == 1)
    {
        cout << ans;
        return 0;
    }

    sort(Class.begin(), Class.end(), cmp);

    pq.push(Class[0]);

    for (int i = 1; i < N; ++i)
    {
        if (!pq.empty())
        {
            ppInt CurClass = pq.top();

            if (CurClass.second.second <= Class[i].second.first)
            {
                pq.pop();
                pq.push(Class[i]);
            }
            else
            {
                pq.push(Class[i]);
                ans++;
            }
        }

        else
            pq.push(Class[i]);
    }   

    cout << ans;

    return 0;
}
```

그리디를 활용하는 것이라 정렬을 해야하는 것 까지는 생각해냈는데,<br/>

현재 강의를 진행 중인데 다른 강의에 대한 정보를 어떻게 불러와야할지 고민을 많이 하였다.<br/>

결국, 구글링을 해봤는데 세상에, 우선순위 큐를 사용해야 했던 것이었다!<br/>

리스트나 벡터를 쓰려고 했는데 우선순위 큐를 사용해야 했던 것!<br/>

아하 생각해보니 내가 처음에 구상하려고 했던 코드가<br/>

```
    sort(Class.begin(), Class.end(), cmp);

    while(!Class.empty())
    {
        int CurClass = Class.front().first;
        int CurStart = Class.front().second.first;
        int CurEnd = Class.front().second.second;
        Class.pop_front();

    }
```
이런 모습으로 되어있었는데 이러면 list를 쓸 바엔 우선순위 큐를 쓰는게 나았을 것이다.<br/>

강의번호, 시작 시간, 끝 시간 으로 쌍을 구성해주어 벡터에 넣고 정렬한다.<br/>

이 때, 벡터는 시작 시간을 기준으로 오름차순으로 정렬시킨다.<br/>

그리고 우선순위 큐는 끝 시간을 기준으로 정렬하도록 구성하여준다.<br/>

우선순위 큐의 맨 앞에 있는 강의가 현재 진행 중인 강의이고, 끝이 나면 강의를 우선순위 큐에서 제거,<br/>

다음 강의를 우선순위 큐에 넣는다.<br/>

이 때, 강의가 끝났는지 기준은 끝 시간 <= 다음 강의 시작시간 여부로 판단할 수 있다.<br/>

이렇게 순차적으로 비교를 하고 우선순위 큐를 사용한다.<br/>
