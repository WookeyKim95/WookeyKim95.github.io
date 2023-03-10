---
layout: post
title:  "[백준] 1987번_알파벳 C++"
subtitle:   
date: 2023-03-10 09:31:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1987번_알파벳 C++<br/>
<br/>

DFS를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <string>
#include <cstring>

using namespace std;

int R, C;
vector<string> map;
vector<char> VisitedAlphabet;
bool visited[20][20] = {false};
int dist[20][20] = {1};

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

// 방문 목록 내에 알파벳이 이미 있는지 확인해주는 함수
bool DoHave(char c)
{
    int len = VisitedAlphabet.size();
    for (int i = 0; i < len; ++i)
    {
        if (VisitedAlphabet[i] == c)
            return true;
    }

    return false;
}

// 삽입 인자값 : x좌표, y좌표
void dfs(int x, int y, int DistCount)
{
    // 방문한 노드의 알파벳 삽입
    VisitedAlphabet.push_back(map[y][x]);

    // 상하좌우 순차적으로 방문
    for (int i = 0; i < 4; ++i)
    {
        int new_x = x + dx[i];
        int new_y = y + dy[i];

        if (0 <= new_x && new_x < C && 0 <= new_y && new_y < R && !visited[new_y][new_x])
        {
            visited[new_y][new_x] = true;

            // 알파벳을 가지고 있지 않다면?
            if (!DoHave(map[new_y][new_x]))
            {
                // 알파벳을 가지고 있지 않았다면 다음알파벳을 저장, 그리고 거리 기록
                // 단, 이미 기록되어 있는 자리라면 더 큰 값을 기록해야함.
                if (dist[new_y][new_x] < DistCount + 1)
                    dist[new_y][new_x] = DistCount + 1;
                
                dfs(new_x, new_y, DistCount + 1);
                
                // 다음 노드 방문 전에 알파벳을 뺌.
                VisitedAlphabet.pop_back();
            }
            visited[new_y][new_x] = false;
        }
    }
}

int main()
{
    cin >> R >> C;

    for (int i = 0; i < R; ++i)
    {
        string s;
        cin >> s;
        map.push_back(s);
    }

    // 배열 초기화
    memset(visited, false, sizeof(visited));

    // memset으로는 초기화가 안되어 일일이 초기화
    for(int i = 0; i < R; i++)
    {
        for(int j = 0; j < C; j++)
            dist[i][j] = 1;
    }

    int ans = 0;

    dfs(0, 0, 1);

    // 최댓값 추출
    for (int i = 0; i < R; ++i)
    {
        for (int j = 0; j < C; ++j)
        {
            if (dist[i][j] > ans)
                ans = dist[i][j];
        }
    }

    cout << ans;

    return 0;
}
```
<br/>

방문한 알파벳을 저장하는 vector을 추가해주었고,<br/>

방문할 때 마다 알파벳을 vector에 추가하고 다음 dfs 함수를 호출하도록 구현하였다.<br/>

stack을 써서 DFS를 구현하고 싶었으나, 그렇게 하면 방문을 마치고 다른 경로를 탐색할 때<br/>

vector의 맨 마지막에 있는 알파벳은 pop하고 방문해주어야 하는데<br/>

stack으로는 구현하기가 힘들었다. 그래서 재귀호출을 이용해서 구현하였다.<br/>

그리고 재귀호출을 하면서 거리를 기록하는 배열에다가 지금까지 움직인 거리를 기록하였고,<br/>

마지막에 제일 큰 값을 추출해서 정답으로 출력하도록 하였다.<br/>