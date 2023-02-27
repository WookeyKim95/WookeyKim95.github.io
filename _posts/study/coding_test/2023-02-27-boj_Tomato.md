---
layout: post
title:  "[백준] 7576번_토마토 C++"
subtitle:   
date: 2023-02-27 09:58:35 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 7576번_토마토 C++<br/>
<br/>

큐와 bfs를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

int N, M;
vector<vector<int>> box;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

// 안익은 토마토가 있는지 확인하는 함수
bool isRare()
{
    for (int i = 0; i < M; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            // 0이 하나라도 있다면 다 익지 않은 것.
            if (box[i][j] == 0)
                return true;
        }
    }
    return false;
}

int main()
{
    cin >> N >> M;

    // 박스에 토마토를 담음.
    for (int i = 0; i < M; ++i)
    {
        vector<int> row;
        for (int j = 0; j < N; ++j)
        {
            int _input;
            cin >> _input;

            row.push_back(_input);
        }
        box.push_back(row);
    }

    int days = 0;

    if (!isRare())
    {
        // 처음부터 모두 익었을 경우 0을 출력하고 함수를 종료한다.
        cout << 0;
        return 0;
    }

    // 날짜별로 좌표 방문을 기록할 큐
    queue<queue<vector<int>>> q;

    // 첫날, 맨 처음 토마토가 익은 곳에서 방문 개시.
    queue<vector<int>> CoordinatePerDay;

    for (int i = 0; i < M; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            if (box[i][j] == 1)
                CoordinatePerDay.push({i, j});
        }
    }

    q.push(CoordinatePerDay);

    while (!q.empty())
    {
        // 날짜별로 다음날 방문할 좌표 기록
        CoordinatePerDay = {};

        queue<vector<int>> nowday = q.front();
        q.pop();

        while(!nowday.empty())
        {
            int x = nowday.front()[1];
            int y = nowday.front()[0];

            nowday.pop();

            for (int k = 0; k < 4; ++k)
            {
                int new_x = x + dx[k];
                int new_y = y + dy[k];

                // segmentation false 방지
                if (0 <= new_x && new_x < N && 0 <= new_y && new_y < M)
                {
                    if (box[new_y][new_x] == 0)
                    {
                        // 토마토가 아직 익지 않은 경우 익은것으로 반환
                        box[new_y][new_x] = 1;  

                        // 다음날 방문할 노드 기록
                        CoordinatePerDay.push({new_y, new_x}); 
                    }
                }
            }
        }

        // 다음날도 익을 토마토가 있을 경우 큐에 넣고 하루를 추가한다.
        if (!CoordinatePerDay.empty())
        {
            q.push(CoordinatePerDay);
            days++;
        }
    }

    if (isRare())
    {
        // bfs를 모두 완료하고 나서 안익은 토마토가 있을 경우 -1을 출력한다.
        cout << -1;
    }
    else
        cout << days;

    return 0;
}
```
<br/>

int형을 담는 벡터, 그리고 그를 담는 큐, 그리고 그 큐를 또 담는 큐를 활용해서 구현하였다.<br/>

날짜별로 어떤 좌표를 방문해야하는지 기록을 할 필요가 있었기 때문에 벡터와 이중 큐를 사용하였다.<br/>

아쉬운 점은 이중큐를 사용해서 그런지 메모리를 많이 잡아먹는것 같다.<br/>

<br/>


## 오답코드 (맨 처음 아이디어)<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N, M;
vector<vector<int>> box;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

// 안익은 토마토가 있는지 확인하는 함수
bool isRare()
{
    for (int i = 0; i < M; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            // 0이 하나라도 있다면 다 익지 않은 것.
            if (box[i][j] == 0)
                return true;
        }
    }
    return false;
}

int main()
{
    cin >> N >> M;

    // 박스에 토마토를 담음.
    for (int i = 0; i < M; ++i)
    {
        vector<int> row;
        for (int j = 0; j < N; ++j)
        {
            int _input;
            cin >> _input;

            row.push_back(_input);
        }
        box.push_back(row);
    }

    int days = 0;

    while (isRare())
    {
        // 오늘 이미 익은 토마토인가 판별하기 위함.
        bool flag[1001][1001] = {false};

        for (int i = 0; i < M; ++i)
        {
            for (int j = 0; j < N; ++j)
            {
                if (box[i][j] == 1 && flag[i][j] == false)
                {
                    int x = j;
                    int y = i;
                    
                    for (int k = 0; k < 4; ++k)
                    {
                        int new_x = x + dx[k];
                        int new_y = y + dy[k];

                        // segmentation false 방지
                        if (0 <= new_x && new_x < N && 0 <= new_y && new_y < M)
                        {
                            if (box[new_y][new_x] == 0)
                            {
                                // 인접한 토마토가 오늘 익은 것으로 표기
                                flag[new_y][new_x] = true;

                                // 토마토가 아직 익지 않은 경우 익은것으로 반환
                                box[new_y][new_x] = 1;   
                            }
                        }
                    }
                }
                
            }
        }

        days++;
    }

    cout << days;

    return 0;
}
```

맨 처음에는 안익은 토마토가 있는지 확인한 뒤에 while문을 진행하는 과정을 진행하였다.<br/>

그런데 이렇게 하게 된다면 발생하는 문제점이 두 가지가 있었다.<br/>

- 안익은 토마토의 인접 지역이 -1로 막혀있는 경우 방어가 안된다.
- day가 지날 때마다 안익은 토마토가 있는지 일일이 확인하는 과정에서 시간초과가 발생한다.

알고리즘 분류를 보니 그래프 이론이라는 건 알았지만, bfs라고는 생각을 못했다.<br/>

아! 큐에다가 다음 방문할 노드를 기록해두면서 팝을 해야겠구나 라는 아이디어가 그제야 생각났었다.<br/>