---
layout: post
title:  "[백준] 1219_오민식의 고민 C++"
subtitle:   
date: 2023-06-06 10:19:59 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1219_오민식의 고민 C++<br/>
<br/>

벨만포드 알고리즘을 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

#define MAX 50
#define INF 9999999999

using namespace std;

struct City
{
    int num;
    vector<pair<int, int>> link;
    int money;
    bool visited = false;

    City(int _num) : num(_num) {};
};

int N, M, Start, End;
vector<City> Cities;
long long Cost[MAX];
queue<int> CycleNode;

bool BFS()
{
    while (!CycleNode.empty())
    {
        int Cur = CycleNode.front();
        CycleNode.pop();

        for (auto i : Cities[Cur].link)
        {
            int Next = i.first;
            if (!Cities[Next].visited)
            {
                Cities[Next].visited = true;
                CycleNode.push(Next);
            }
        }
    }

    if (Cities[End].visited)
        return true;
    else
        return false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> Start >> End >> M;

    for (int i = 0; i < N; ++i)
    {
        City C(i);
        Cities.push_back(C);
    }

    for (int i = 0; i < M; ++i)
    {
        int S, E, C;
        cin >> S >> E >> C;

        Cities[S].link.push_back({E, C});
    }

    for (int i = 0; i < N; ++i)
    {
        cin >> Cities[i].money;
        Cost[i] = INF;
    }

    Cost[Start] = -Cities[Start].money;

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            for (auto Cur : Cities[j].link)
            {
                int Next = Cur.first;
                int NextCost = Cur.second;

                if (Cost[j] != INF && Cost[Next] > Cost[j] + NextCost - Cities[Next].money)
                {
                    Cost[Next] = Cost[j] + NextCost - Cities[Next].money;
                    if (i == N)
                    {
                        Cities[j].visited = true;
                        CycleNode.push(j);
                    }
                }
            }
        }
    }

    if (Cost[End] == INF)
        cout << "gg";
    else
    {
        if (BFS())
            cout << "Gee";
        else
            cout << -Cost[End];
    }

    return 0;
}
```
<br/>

[풀이참고링크](https://velog.io/@jdja2004/%EB%B0%B1%EC%A4%80-1219%EB%B2%88-%EC%98%A4%EB%AF%BC%EC%8B%9D%EC%9D%98-%EA%B3%A0%EB%AF%BC-C)<br/>

가중치에 음수가 나올 수 있다는 것을 보고 벨만포드 알고리즘이라 생각하였다.<br/>

```
Cost[Start] = -Cities[Start].money;

...

else
    cout << -Cost[End];
```

생각해보면 우리의 목적은 "쓴 돈의 값"이 아니라 "벌은 돈의 값"을 출력하는 것이니<br/>

벌 수 있는 돈의 값에 다가 -1을 곱해서 쓴 돈으로 바꾸고,<br/>

벨만 포드 알고리즘을 통해 계산을 한다음,<br/>

쓴 돈의 값에다가 -1을 곱해서 벌은 돈의 값을 출력해야한다.<br/>

그렇기 때문에 -1을 곱해주는 것이다.<br/>

그리고 경로가 사이클을 이루게 되면 돈을 무한대로 벌 수 있다는 것을 알 수 있다.<br/>

경로가 음의 사이클을 이루는지 여부를 BFS나 DFS로 확인할 수 있는데,<br/>

나는 BFS를 활용하였다.<br/>

그래서 BFS를 통해 음의 사이클을 이룬다면 Gee를,<br/>

그렇지 않다면 그냥 보통 값을 출력하도록 하였다.<br/>