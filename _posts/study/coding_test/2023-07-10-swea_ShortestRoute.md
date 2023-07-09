---
layout: post
title:  "[SWEA] 1247_최적경로 C++"
subtitle:   
date: 2023-07-10 07:49:28 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [SWEA] 1247_최적경로 C++<br/>
<br/>

DFS를 이용해보는 완전탐색 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <cmath>
#include <vector>

#define INF 999999999

using namespace std;

struct point
{
    int x;
    int y;
    bool visited = false;
    vector<pair<int, int>> link;
};

vector<point> Points;
int N, ans = INF;

int Distance(int a, int b)
{
    return abs(Points[a].x - Points[b].x) + abs(Points[a].y - Points[b].y);
}

void DFS(int idx, int cnt, int accul)
{
    if (accul > ans)
        return;

    if (cnt == N)
    {
        int Sum = accul;
        Sum += Distance(idx, 1);

        if (ans > Sum)
            ans = Sum;

        return;
    }

    for (size_t i = 0; i < Points[idx].link.size(); ++i)
    {
        int Next = Points[idx].link[i].first;
        int Dist = Points[idx].link[i].second;

        if (!Points[Next].visited)
        {
            Points[Next].visited = true;
            DFS(Next, cnt+1, accul+Dist);
            Points[Next].visited = false;
        }
    }
}

int main(int argc, char** argv)
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int test_case, T;
    cin >> T;

    for (test_case = 1; test_case <= T; test_case++)
    {
        cin >> N;
        ans = INF;

        Points.clear();
        for (int i = 0; i < N+2; ++i)
        {
            point p;
            int X, Y;
            cin >> X >> Y;
            
            p.x = X;
            p.y = Y;
            Points.push_back(p);
        }

        for (int i = 2; i < N+2; ++i)
        {
            int Dist = Distance(0, i);
            
            Points[0].link.push_back({i, Dist});
            Points[i].link.push_back({0, Dist});
        }

        for (int i = 2; i < N+2; ++i)
        {
            for (int j = i+1; j < N+2; ++j)
            {
                int Dist = Distance(i, j);

                Points[j].link.push_back({i, Dist});
                Points[i].link.push_back({j, Dist});
            }
        }
        
        Points[0].visited = true;
        DFS(0, 0, 0);
        
        cout << '#' << test_case << ' ' << ans << '\n';
        
    }
    return 0;
}
```

완전탐색이라고 제목에 있음에도 불구하고, 다익스트라를 이용해서 풀려고 했다가 시간을 허비했다.<br/>

뒤늦게 로직을 엎어서 DFS를 통한 완전탐색을 구현하였다.<br/>

우선, Points 배열은 아래와 같이 구성되어있다.<br/>

```
[회사좌표, 집좌표, 고객1, 고객2, 고객3, .... , 고객N]
```

그래서 1번 인덱스를 제외하고 0번과 나머지 인덱스를 모두 간선을 구한다.<br/>

그리고 나머지 인덱스 간에도 간선을 구해준다.<br/>

```
for (int i = 2; i < N+2; ++i)
{
    int Dist = Distance(0, i);
    
    Points[0].link.push_back({i, Dist});
    Points[i].link.push_back({0, Dist});
}

for (int i = 2; i < N+2; ++i)
{
    for (int j = i+1; j < N+2; ++j)
    {
        int Dist = Distance(i, j);

        Points[j].link.push_back({i, Dist});
        Points[i].link.push_back({j, Dist});
    }
}
```

DFS 함수의 주요 구성은 아래와 같다.<br/>

- DFS를 이용한 완전탐색을 진행한다.
- N번째 노드를 탐색할 차례라면 N-1번째 노드와 1번 인덱스, 즉 집과의 거리를 구한다
- 기존에 구한 값과 새로 구한 값 중 짧은 거리를 ans로 택한다.

이를 구현하면 아래와 같다.<br/>

```
int Distance(int a, int b)
{
    return abs(Points[a].x - Points[b].x) + abs(Points[a].y - Points[b].y);
}

void DFS(int idx, int cnt, int accul)
{
    if (accul > ans)
        return;

    if (cnt == N)
    {
        int Sum = accul;
        Sum += Distance(idx, 1);

        if (ans > Sum)
            ans = Sum;

        return;
    }

    for (size_t i = 0; i < Points[idx].link.size(); ++i)
    {
        int Next = Points[idx].link[i].first;
        int Dist = Points[idx].link[i].second;

        if (!Points[Next].visited)
        {
            Points[Next].visited = true;
            DFS(Next, cnt+1, accul+Dist);
            Points[Next].visited = false;
        }
    }
}
```

제목을 잘 읽고 조금더 쉽게 생각하면 됐을 것 같다.<br/>

다익스트라로 구현하려고 해도 아무리 생각해도 구해지지 않는다는 것에서 의문을 품었어야 했는데..<br/>