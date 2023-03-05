---
layout: post
title:  "[백준] 14502번_연구소 C++"
subtitle:   
date: 2023-03-05 10:50:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 14502번_연구소 C++<br/>
<br/>

bfs + 백트래킹을 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <cstring>
#include <vector>

using namespace std;

int N, M;
bool visited[8][8] = {false};
int map[8][8] = {0};
int map_copy[8][8] = {0};
int startWalls = 0;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1 ,1};

int Ans = 0;

// 바이러스의 위치 좌표를 담을 큐
queue<vector<int>> virus;

int VirusBfs()
{
    // BFS 시작점 설정
    queue<vector<int>> q_bfs = virus;

    int VirusNum = virus.size();

    // 초기화
    memset(visited, false, sizeof(visited));

    while(!q_bfs.empty())
    {
        int startx = q_bfs.front()[0];
        int starty = q_bfs.front()[1];

        q_bfs.pop();

        for (int i = 0; i < 4; ++i)
        {
            int new_x = startx + dx[i];
            int new_y = starty + dy[i];

            if (!visited[new_y][new_x] && map_copy[new_y][new_x] == 0
                && 0 <= new_x && new_x < M && 0 <= new_y && new_y < N)
            {
                // 퍼진 바이러스의 개수를 세면서 입력해주고 방문여부 true로 전환
                VirusNum++;
                q_bfs.push({new_x, new_y});
                visited[new_y][new_x] = true;
                map_copy[starty][startx] = 2;
            }
        }
    }
    
    return VirusNum;
}

void SetWalls(int SetWallCount)
{
    if (SetWallCount == 3)
    {
        // 3개의 벽을 모두 설치하면 탐색하기.

        // 안전구역의 개수 = 전체넓이 - 초기 벽의 넓이 - 바이러스의 개수 - 설치한 벽의 수(3)
        int SafetyArea = (N * M) - startWalls - VirusBfs() - 3;
        if (Ans < SafetyArea)
        {
            Ans = SafetyArea;
            // 검증용
            // cout << Ans << '\n';
        }

        // bfs를 한 뒤 초기화
        memcpy(map_copy, map, sizeof(map));
        return;
    }

    // 벽을 3개 설치하는 경우의 수를 따질것.
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            if (map[i][j] == 0)
            {
                // 벽 설치
                map[i][j] = 1;
                SetWalls(SetWallCount+1);

                // 시뮬레이션 후 벽 제거
                map[i][j] = 0;
            }            
        }
    }

}

int main()
{
    cin >> N >> M;

    // 지도 입력 받기
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            int _input;
            cin >> _input;

            map[i][j] = _input;

            // 바이러스의 위치 x, y 좌표를 삽입한다.
            if (_input == 2)
                virus.push({j, i});

            // 초기 벽의 개수를 계산해둔다.
            else if (_input == 1)
                startWalls++;
                
        }
    }

    SetWalls(0);
    cout << Ans;

    return 0;
}
```
<br/>

벽을 설치하는 경우를 백트래킹을 통해서 따지고,<br/>

바이러스가 퍼지는 모습을 BFS를 통해서 탐색을 해야하는 문제였다.<br/>

백트래킹을 통해서 벽을 설치하는 경우를 따지는 코드를 구현하는데 애를 먹었다.<br/>

특히, 백트래킹 과정에서 무한루프가 걸려서 애먹었는데,<br/>

```
SetWalls(SetWallCount++);
```
재귀호출을 하는 이 부분이 문제였다.<br/>

SetWallCount++로 해버리면 함수가 호출될 때마다 SetWallCount가 증가해버려서<br/>

엉뚱한 경우의 수가 나오거나, 무한루프에 빠지는 일이 발생한다.<br/>

실제로 SetWallCount를 출력해보니 값이 11까지 올라가는 경우가 발생했다.<br/>

이를 해결하기 위해서<br/>

```
SetWalls(SetWallCount+1);
```
로 코드를 고쳐주었다.

이렇게 하니 해결되었다.<br/>
<br/>

## SetWallCount++로 하면?<br/>
<br/>

재귀 함수를 호출할 때 마다 SetWallCount가 증가하는 것이다.<br/>

이 부분을 보자.<br/>

```
// 벽을 3개 설치하는 경우의 수를 따질것.
for (int i = 0; i < N; ++i)
{
    for (int j = 0; j < M; ++j)
    {
        if (map[i][j] == 0)
        {
            // 벽 설치
            map[i][j] = 1;
            SetWalls(SetWallCount++);

            // 시뮬레이션 후 벽 제거
            map[i][j] = 0;
        }            
    }
}
```
인자로 2을 받았다고 해보자.

그렇다면 SetWallCount = 2이다.<br/>

그리고 i와 j의 2중 for문을 돌 때마다 SetWallCount는 1씩 증가해서 재귀호출이 진행된다.<br/>

이 때, SetWalls(SetWallCount++)를 만나면 SetWallCount는 3으로 변하며, 재귀호출이 진행되지만,<br/>

**재귀호출이 끝나고 나서도 해당 함수에서는 SetWallCount는 그대로 3으로 남게 되는 것이다.**<br/>

즉, SetWallCount+1을 전달해주면 해당 함수의 SetWallCount는 2를 유지하면서 재귀 호출 함수에게는 1이 증가한 값인 3을 전달해주게 되지만,<br/>

SetWallCount++를 해주면 재귀호출을 할때 1을 증가한 값을 전달함과 동시에 재귀 호출이 끝났다고 하더라도<br/>

**해당 함수의 SetWallCount까지 1이 증가한 채로 그대로 남는 것이다.**<br/>

그래서 또 재귀호출을 진행할 때에는 3+1인 4, 5, 6... 이 전달돼서 무한루프에 빠지게 되는 것이다.<br/>

그래서 앞으로 백트래킹 문제를 풀 때에는 전달인자를 a++과 같이 작성하지 않고, a+1과 같이 작성해야 겠다고 배웠다.<br/>