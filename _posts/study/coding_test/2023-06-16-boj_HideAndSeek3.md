---
layout: post
title:  "[백준] 13549_숨바꼭질 3 C++"
subtitle:   
date: 2023-06-16 06:38:42 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 13549_숨바꼭질 3 C++<br/>
<br/>

우선순위큐를 사용해서 BFS를 해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <cstring>

#define MAX 100001

using namespace std;

typedef pair<int, int> pii;

int N, K;
bool visited[MAX];

int BFS()
{
    priority_queue<pii, vector<pii>, greater<pii>> q;
    q.push({0, N});
    visited[N] = true;

    while (!q.empty())
    {
        int time = q.top().first;
        int cur = q.top().second;
        q.pop();
 
        if (cur == K)
            return time;
 
        if (cur*2 < MAX && !visited[cur*2])
        {
            q.push({time, cur*2});
            visited[cur*2] = true;
        }
 
        if (cur+1 < MAX && !visited[cur+1])
        {
            q.push({time+1, cur+1});
            visited[cur+1] = true;
        }
 
        if (cur-1 >= 0 && !visited[cur-1])
        {
            q.push({time+1 , cur-1});
            visited[cur-1] = true;
        }
    }
}

int main()
{
    cin >> N >> K;
    memset(visited, false, sizeof(visited));

    cout << BFS();

    return 0;
}
```
<br/>

BFS와 우선순위 큐를 사용해서 풀어보는 문제이다.<br/>

우선순위 큐로 최단 경로를 구하는 다익스트라 알고리즘의 전 단계 문제로도 파악이 됐다.<br/>

특히, 우선순위 큐가 time을 기준으로 자동으로 정렬해주기 때문에 최소 시간은 자동으로 갱신이 된다.<br/>