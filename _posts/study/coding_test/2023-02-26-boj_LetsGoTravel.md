---
layout: post
title:  "[백준] 1976번_여행 가자 C++"
subtitle:   
date: 2023-02-26 10:41:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1976번_여행 가자 C++<br/>
<br/>

그래프를 작성하고 bfs를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

struct City
{
    vector<int> link;
};

vector<City> Cities;

bool bfs(int start, int dest)
{
    bool visited[201] = {false};
    queue<int> q;
    q.push(start);
    visited[start] = true;

    while(!q.empty())
    {
        int x = q.front();
        q.pop();
        
        if (x == dest)
        {
            // 이번에 탐색할 x가 목적지 dest인 경우 true 반환
            return true;
        }

        // 도시에 연결된 링크를 큐에 추가함.
        for (int i = 0; i < Cities[x].link.size(); ++i)
        {
            int y = Cities[x].link[i];
            if (!visited[y])
            {
                // 방문했다는 표시로 바꿔줌.
                q.push(y);
                visited[y] = true;
            }
        }
    }
    // 모두 탐색을 했지만 목적지가 연결이 안된경우 false 반환
    return false;
}

int main()
{
    int N, M;
    cin >> N;
    cin >> M;

    // 안정적 인덱싱을 위해 패딩을 하나 넣어줌.
    City Padding;
    Cities.push_back(Padding);

    // 지도 작성
    for (int i = 0; i < N; ++i)
    {
        City aCity;        
        for (int j = 1; j <= N; ++j)
        {
            int LinkInput;
            cin >> LinkInput;

            if (LinkInput == 1)
                aCity.link.push_back(j);
        }
        Cities.push_back(aCity);
    }

    // 여행 계획 입력
    vector<int> Plan;
    for (int i = 0; i < M; ++i)
    {
        int PlanInput;
        cin >> PlanInput;

        Plan.push_back(PlanInput);
    }

    for (int i = 0; i < M-1; ++i)
    {
        if (!bfs(Plan[i], Plan[i+1]))
        {
            cout << "NO";
            break;
        }

        if (bfs(Plan[i], Plan[i+1]) && i == M-2)
        {
            cout << "YES";
        }
    }

    return 0;
}
```
<br/>

필자는 그래프 탐색을 위해서 BFS를 사용하였다.<br/>

그리고 생각해보니 Cities의 자료구조를
```
vector<vector<int>>
```
로 해도 되는것으로 보인다.<br/>

하지만 앞으로 노드에 여러 데이터를 넣는 것을 습관화 하기 위해서 일부러 City라는 구조체를 사용하였다.<br/>

헷갈리지 않게 구현하기도 더 쉽고.<br/>

그래서 링크된 데이터를 넣을 때마다 새로운 City 구조체를 선언하면서 City를 담는 벡터에 넣었다.<br/>

이렇게하면 지도가 완성된다.<br/>

그리고 지도를 탐색할 때 입력인자로 시작점, 목적지를 받고 bfs를 사용하였는데,

여행계획이 1-3-2 라고 하면 1-3 구간에서 bfs를 한번, 3-2 구간에서 bfs를 한번 사용하는 구조이다.<br/>

그래서 마지막인 3-2구간에서 bfs결과가 true가 반환되면 그때 YES를 출력하도록 했고, 중간에 한번이라도 false가 반환된다면 NO를 출력하도록 구현하였다.