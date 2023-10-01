---
layout: post
title:  "[백준] 11779_최소비용 구하기 2 C++"
subtitle:   
date: 2023-10-01 06:53:35 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11779_최소비용 구하기 2 C++<br/>
<br/>

다익스트라 알고리즘을 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>
#include <string>
#include <queue>
#include <cstring>

#define INF 99999999

using namespace std;

struct City
{
    int acculCost = INF;
    vector<pair<int, int>> link;
};

vector<City> Course;
int N, M;
vector<int> CourseSelect;
vector<int> ans;
int MaxCost = 0;
int Start, End;

int Dijkstra(int start, int end)
{
    priority_queue<pair<int, int>> pq;
    pq.push({ 0, start });
    Course[start].acculCost = 0;

    while (!pq.empty())
    {
        int CurCost = -pq.top().first;
        int CurNum = pq.top().second;
        pq.pop();

        if (CurCost > Course[CurNum].acculCost)
            continue;

        for (size_t i = 0; i < Course[CurNum].link.size(); ++i)
        {
            int Next = Course[CurNum].link[i].second;
            int NextCost = Course[CurNum].link[i].first + CurCost;

            if (NextCost < Course[Next].acculCost)
            {
                CourseSelect[Next] = CurNum;

                Course[Next].acculCost = NextCost;
                pq.push({ -NextCost, Next });
            }
        }
    }

    return Course[end].acculCost;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;
    cin >> M;

    for (int i = 0; i <= N; ++i)
    {
        City f;
        Course.push_back(f);

        int path = 0;
        CourseSelect.push_back(path);
    }

    for (int i = 0; i < M; ++i)
    {
        int a, b, c;
        cin >> a >> b >> c;

        Course[a].link.push_back({ c, b });
    }

    cin >> Start >> End;

    int Cost = Dijkstra(Start, End);

    cout << Cost << '\n';

    int idx = End;

    while (idx != Start)
    {
        ans.push_back(idx);
        idx = CourseSelect[idx];
    }
    ans.push_back(Start);

    cout << ans.size() << '\n';

    for (int i = ans.size() - 1; i >= 0; --i)
        cout << ans[i] << ' ';
    cout << '\n';

    return 0;
}
```

답이 여러개 나올 수 있는 스페셜 저지 문제이다.<br/>

기본적인 다익스트라 알고리즘에서 꼬아서, 경로까지 출력하도록 만들어야했던 문제이다.<br/>

경로를 출력하는 방법은 아래와 같다.

```
vector<int> CourseSelect;

...

int Dijkstra(int start, int end)
{
    priority_queue<pair<int, int>> pq;
    pq.push({ 0, start });
    Course[start].acculCost = 0;

    while (!pq.empty())
    {
        int CurCost = -pq.top().first;
        int CurNum = pq.top().second;
        pq.pop();

        if (CurCost > Course[CurNum].acculCost)
            continue;

        for (size_t i = 0; i < Course[CurNum].link.size(); ++i)
        {
            int Next = Course[CurNum].link[i].second;
            int NextCost = Course[CurNum].link[i].first + CurCost;

            if (NextCost < Course[Next].acculCost)
            {
                CourseSelect[Next] = CurNum;

                Course[Next].acculCost = NextCost;
                pq.push({ -NextCost, Next });
            }
        }
    }

    return Course[end].acculCost;
}

...

    int idx = End;

    while (idx != Start)
    {
        ans.push_back(idx);
        idx = CourseSelect[idx];
    }
    ans.push_back(Start);

    cout << ans.size() << '\n';

    for (int i = ans.size() - 1; i >= 0; --i)
        cout << ans[i] << ' ';
    cout << '\n';
```

위 코드에서 노드 별로 이전 방문노드가 어디였는지를 기록하는 시스템이다.<br/>

다시 말하면 최소 비용 값을 갱신할 때, Next노드의 방문 직전 노드를<br/>

현재 노드로 기록해주는 것이다.<br/>

그렇게 한 다음, 도착점 노드에서부터 역순으로 추적해서 스택에 쌓아주고,<br/>

스택을 다시 역순으로 출력하면 올바른 경로가 되는 것이다.<br/>

그렇게 구현한 것이 위의 코드이다.<br/>