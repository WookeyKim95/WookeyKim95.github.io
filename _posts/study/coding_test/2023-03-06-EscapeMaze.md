---
layout: post
title:  "[프로그래머스] 미로 탈출 C++"
subtitle:   
date: 2023-03-06 10:03:20 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 미로 탈출 C++<br/>
<br/>

BFS를 사용하는 그래프 탐색 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>
#include <vector>
#include <queue>
#include <cstring>

using namespace std;

vector<string> maps_static;
bool visited[100][100] = {false};
int accTime[100][100] = {0};

int N, M;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

int bfs(vector<int> start, vector<int> end)
{
    queue<vector<int>> q;
    
    q.push(start);
    
    accTime[start[1]][start[0]] = 0;
    visited[start[1]][start[0]] = true;
    
    while(!q.empty())
    {
        int x = q.front()[0];
        int y = q.front()[1];
        
        q.pop();
        
        for (int i = 0; i < 4; ++i)
        {
            int new_x = x + dx[i];
            int new_y = y + dy[i];
            
            if (0 <= new_x && new_x < N && 0 <= new_y && new_y < M && !visited[new_y][new_x] && 
                maps_static[new_y][new_x] != 'X')
            {
                accTime[new_y][new_x] = accTime[y][x] + 1;
                
                q.push({new_x, new_y});
                visited[new_y][new_x] = true;
            }
            
            if (new_x == end[0] && new_y == end[1])
                return accTime[new_y][new_x];
        }
    }
    
    return -1;
}
    
int solution(vector<string> maps) {
    maps_static = maps;
    
    // 가로, 세로길이 저장
    N = maps[0].size();
    M = maps.size();
    
    vector<int> StartPoint, LeverPoint, EndPoint;
    
    for (int i = 0; i < M; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            if (maps[i][j] == 'S')
                StartPoint = {j, i};
            else if (maps[i][j] == 'L')
                LeverPoint = {j, i};
            else if (maps[i][j] == 'E')
                EndPoint = {j, i};
        }
    }
    
    int answer = 0;
    
    // 초기화
    memset(visited, false, sizeof(visited));
    memset(accTime, 0, sizeof(accTime));
    
    int result = bfs(StartPoint, LeverPoint);
    if (result == -1)
        return -1;
    else
        answer += result;
    
    // 초기화
    memset(visited, false, sizeof(visited));
    memset(accTime, 0, sizeof(accTime));
    
    result = bfs(LeverPoint, EndPoint);
    if (result == -1)
        return -1;
    else
        answer += result;
    
    return answer;
}
```

전형적인 BFS를 통해서 최단경로를 찾는 문제였다.<br/>

시작점으로부터의 시간(거리)을 기록할 배열 accTime과, 방문여부를 기록할 visited를 준비했다.<br/>

그리고 bfs를 돌 때마다 0과 false로 memset을 통해서 초기화하고,<br/>

BFS결과값은 -1이나 출발지점으로부터 도착지점까지의 누적 시간을 반환하도록 구현하였다.<br/>

그래서, 시작지점(StartPoint), 레버지점(LeverPoint), 끝지점(EndPoint)를 기록한 뒤에<br/>

BFS를 두번 돌려서 누적 거리를 합산하도록 구현하였다.<br/>