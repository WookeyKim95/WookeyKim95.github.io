---
layout: post
title:  "[백준] 18352_특정 거리의 도시 찾기 C++"
subtitle:   
date: 2024-01-09 07:05:28 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 18352_특정 거리의 도시 찾기 C++<br/>
<br/>

간단한 BFS 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

struct City
{
    vector<int> link;
    int MinDist = 0;
    bool visited = false;
};

int N, M, K, X;
vector<City> Cities;

void BFS(int start)
{
    queue<pair<int, int>> q;
    q.push({start, 0});
    Cities[start].visited = true;

    while (!q.empty())
    {
        int Cur = q.front().first;
        int CurDist = q.front().second;
        q.pop();
        Cities[Cur].MinDist = CurDist;

        for (size_t i = 0; i < Cities[Cur].link.size(); ++i)
        {
            int Next = Cities[Cur].link[i];
            int NextDist = CurDist + 1;

            if (!Cities[Next].visited)
            {
                Cities[Next].visited = true;
                q.push({Next, CurDist+1});
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M >> K >> X;

    for (int i = 0; i <= N; ++i)
    {
        City c;
        Cities.push_back(c);
    }

    for (int i = 0; i < M; ++i)
    {
        int s, e;
        cin >> s >> e;
        Cities[s].link.push_back(e);
    }

    BFS(X);
    bool flag = false;

    for (int i = 1; i <= N; ++i)
    {
        if (Cities[i].MinDist == K)
        {
            flag = true;
            cout << i << '\n';
        }
    }

    if (!flag)
        cout << -1;

    return 0;
}
```

<br/>

이번에는 큐에다가는 정수쌍을 저장해야 했다.<br/>

(현재 도시 번호, 현재 도시 까지 거리) 이렇게!<br/>

그렇게 저장해야 나중에 도시 정보에다가 현재 도시 까지 거리를 저장하고,<br/>

도시의 번호를 추출할 수 있다.<br/>

나머지는 기본적인 BFS 템플릿을 따라가면 된다.<br/>

필요한 도시 정보로는 아래와 같다.

- 도시까지의 거리
- 도시 방문 여부
- 도시 연결 정보

나는 이것을 struct로 저장해두었다.<br/>