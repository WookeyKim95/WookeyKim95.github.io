---
layout: post
title:  "[백준] 1005번_ACM Craft C++"
subtitle:   
date: 2023-04-16 09:21:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1005번_ACM Craft C++<br/>
<br/>

DP, 그래프, 위상정렬을 사용하는 문제.<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

struct building
{
    int num;
    int delay;
    int AcculTime;
    int parents = 0;
    vector<int> child;
    
    building(int _num) : num(_num) {}
};

int T, N, K;
vector<building> buildings;

void Construction()
{
    queue<pair<int, int>> q;

    for (int i = 1; i <= N; ++i)
    {
        if (buildings[i].parents == 0)
            q.push(make_pair(i, buildings[i].delay));
    }

    while (!q.empty())
    {
        int CurNum = q.front().first;
        int CurAccul = q.front().second;
        q.pop();

        building CurBuild = buildings[CurNum];
        vector<int> CurBuildChild = CurBuild.child;

        for (size_t i = 0; i < CurBuildChild.size(); ++i)
        {
            int ChildIndex = CurBuildChild[i];
            int NextAccul = CurAccul + buildings[ChildIndex].delay;
            buildings[ChildIndex].parents--;

            if (buildings[ChildIndex].AcculTime <= NextAccul)
                buildings[ChildIndex].AcculTime = NextAccul;
            
            if (buildings[ChildIndex].parents == 0)
                q.push(make_pair(ChildIndex, buildings[ChildIndex].AcculTime));
        }
    }
}

int main()
{
    cin >> T;

    for (int i = 0; i < T; ++i)
    {
        buildings.clear();

        building padding(0);
        buildings.push_back(padding);

        cin >> N >> K;
        for (int j = 1; j <= N; ++j)
        {
            building b(j);

            int DelayInput;
            cin >> DelayInput;
            b.delay = DelayInput;
            b.AcculTime = DelayInput;

            buildings.push_back(b);
        }

        for (int j = 0; j < K; ++j)
        {
            int P, C;
            cin >> P >> C;

            buildings[P].child.push_back(C);
            buildings[C].parents++;
        }

        Construction();

        int W;
        cin >> W;

        cout << buildings[W].AcculTime << '\n';
    }

    return 0;
}
```

building 구조체를 만들었고 이 구조체 안에 아래의 정보를 담았다.

- 건물의 번호
- 짓는데 걸리는 시간
- 이 건물이 지어질 때 까지 누적된 시간
- 이 건물을 짓기 위해 지어져야 하는 건물의 수
- 지어지기 위해 이 건물이 필요한 건물 (자식 번호)

그리고 buildings라는 벡터에다가 건물들의 정보들을 담은 배열을 저장하였다.<br/>

```
void Construction(int start)
{
    queue<pair<int, int>> q;
    q.push(make_pair(start, buildings[start].delay));

    while (!q.empty())
    {
        int CurNum = q.front().first;
        int CurAccul = q.front().second;
        q.pop();

        building CurBuild = buildings[CurNum];
        vector<int> CurBuildChild = CurBuild.child;

        for (size_t i = 0; i < CurBuildChild.size(); ++i)
        {
            int ChildIndex = CurBuildChild[i];
            int NextAccul = CurAccul + buildings[ChildIndex].delay;

            if (buildings[ChildIndex].AcculTime < NextAccul)
            {
                buildings[ChildIndex].AcculTime = NextAccul;
                q.push(make_pair(ChildIndex, NextAccul));
            }
        }
    }
}

...

for (int j = 1; j <= N; ++j)
{
    if (buildings[j].parent.size() == 0)
        Construction(j);
}
```

우선 처음 지을 건물 선택은 부모가 없는 건물을 먼저 짓도록 구현하였다.<br/>

큐에다가는 (int, int)형 순서쌍을 저장하는데<br/>

왼쪽에다 건물의 번호, 오른쪽에다 건물을 짓는데 누적된 시간을 저장한다.<br/>

어떤 건물을 짓고 그 다음에 자식 건물을 짓는다.<br/>

이 때, 자식 건물을 짓는 데 누적된 시간이 자식 건물에 이전에 저장된 값보다 클 경우에만<br/>

값을 덮어씌운다.<br/>

그리고 어떤 건물의 부모 건물을 모두 지으면 그 다음으로 자식 건물을 짓기 시작한다.<br/>

이 과정이 위상정렬이라고 한다.<br/>

어떤 선행 과정이 완료된 다음에 다음 과정을 진행하도록 그래프를 정렬하는 것이다.<br/>

위상 정렬을 적용한 건설과정 코드는 아래와 같다.<br/>

```
void Construction()
{
    queue<pair<int, int>> q;

    for (int i = 1; i <= N; ++i)
    {
        if (buildings[i].parents == 0)
            q.push(make_pair(i, buildings[i].delay));
    }

    while (!q.empty())
    {
        int CurNum = q.front().first;
        int CurAccul = q.front().second;
        q.pop();

        building CurBuild = buildings[CurNum];
        vector<int> CurBuildChild = CurBuild.child;

        for (size_t i = 0; i < CurBuildChild.size(); ++i)
        {
            int ChildIndex = CurBuildChild[i];
            int NextAccul = CurAccul + buildings[ChildIndex].delay;
            buildings[ChildIndex].parents--;

            if (buildings[ChildIndex].AcculTime <= NextAccul)
                buildings[ChildIndex].AcculTime = NextAccul;
            
            if (buildings[ChildIndex].parents == 0)
                q.push(make_pair(ChildIndex, buildings[ChildIndex].AcculTime));
        }
    }
}
```

뭔가 BFS랑 비슷하게 생겼다.<br/>