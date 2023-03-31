---
layout: post
title:  "[백준] 1504번_특정한 최단 경로 C++"
subtitle:   
date: 2023-03-31 06:15:18 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1504번_특정한 최단 경로 C++<br/>
<br/>

다익스트라 알고리즘을 이용해서 푸는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

#define INF 999999999

struct Node
{
    int num;
    int acculDist;
    // 1번 : 목적지, 2번 : 거리
    vector<pair<int, int>> Link;

    Node(int _num) : num(_num), acculDist(INF){}
};

int N, E;
vector<Node> NodeList;

void DistSet()
{
    for (int i = 1; i <= N; ++i)
    {
        NodeList[i].acculDist = INF;
    }
    return;
}

int dijkstra(int start, int end)
{
    priority_queue<pair<int, int>> q;
    q.push(make_pair(0, start));
    NodeList[start].acculDist = 0;

    while(!q.empty())
    {
        int Cost = -q.top().first;
        Node Cur = NodeList[q.top().second];
        q.pop();

        if (Cur.acculDist < Cost)
            continue;

        for (size_t i = 0; i < Cur.Link.size(); ++i)
        {
            int Next = Cur.Link[i].first;
            int Dist = Cur.Link[i].second;

            if (NodeList[Next].acculDist > Cost + Dist)
            {
                NodeList[Next].acculDist = Cost + Dist;
                q.push(make_pair(-NodeList[Next].acculDist, Next));
            }
        }
    }

    if (NodeList[end].acculDist == INF)
        return -1;
    else
        return NodeList[end].acculDist;
}

int CheckPath(int C1, int C2)
{
    int ans = 0;

    DistSet();

    int first = dijkstra(1, C1);
    if (first == -1)
        return -1;
    else
        ans += first;

    DistSet();

    int second = dijkstra(C1, C2);
    if (second == -1)
        return -1;
    else
        ans += second;


    DistSet();

    int third = dijkstra(C2, N);
    if (third == -1)
        return -1;
    else
        ans += third;

    return ans;
}

int main()
{
    cin >> N >> E;

    Node padding(0);
    NodeList.push_back(padding);

    for (int i = 1; i <= N; ++i)
    {
        Node n(i);
        NodeList.push_back(n);
    }

    for (int i = 0; i < E; ++i)
    {
        int point1, point2, dist;
        cin >> point1 >> point2 >> dist;

        NodeList[point1].Link.push_back(make_pair(point2, dist));
        NodeList[point2].Link.push_back(make_pair(point1, dist));
    }

    int CheckPoint1, CheckPoint2;
    cin >> CheckPoint1 >> CheckPoint2;

    int ans1 = CheckPath(CheckPoint1, CheckPoint2);
    int ans2 = CheckPath(CheckPoint2, CheckPoint1);

    if (ans1 != -1 && ans2 != -1)
    {
        if (ans1 <= ans2)
            cout << ans1;
        else
            cout << ans2;
    }
    else if (ans1 == -1)
        cout << ans2;
    else if (ans2 == -1)
        cout << ans1;

    return 0;
}
```

시작점부터 끝점을 찾도록 설정하였고,<br/>

다익스트라 알고리즘을 한번 경로를 찾을 시 총 3번 실행하도록 구현하였다.<br/>

- 시작점 - 체크포인트 1
- 체크포인트 1 - 체크포인트 2
- 체크포인트 2 - 도착점

각 경로마다 최소값을 더한 값이 결국에는 전체의 최소값이 될 것이다.<br/>

다익스트라 알고리즘은 우선순위 큐를 이용해서 구현하였다.<br/>

만일 경로를 찾는데 실패했으면 -1을 출력하고 끝내도록 구현하였다.<br/>

<br/>

그리고 주의할 점은<br/>

꼭 지나야 하는 지점의 번호가 오름차순이나 내림차순으로 정해지지 않고 주어진다는 것이다.<br/>

예를 들어, 꼭 지나야 하는 정점의 번호가 2, 3 으로 주어질 수도 있고 3, 2로 주어질 수도 있다.<br/>

그래서 위와 같이 체크포인트1을 먼저지나고 체크포인트2를 지날지,<br/>

아니면 체크포인트2를 먼저지나고 그다음에 체크포인트1을 지날지를 둘다 조사를 해야한다.<br/>

그리고 둘 중에 작은 값이 출력되도록 구현하였다.<br/>