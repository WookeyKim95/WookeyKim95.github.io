---
layout: post
title:  "[백준] 1012번_유기농 배추 C++"
subtitle:   
date: 2023-03-02 12:00:32 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1012번_유기농 배추 C++<br/>
<br/>

bfs를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <cstring>

using namespace std;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
int field[50][50] = {0};
bool visited[50][50] = {false};

int main()
{
    int testCase;
    cin >> testCase;

    for (int i = 0; i < testCase; ++i)
    {
        // 정답 초기화
        int ans = 0;

        // 초기화
        memset(field, 0, sizeof(field));
        memset(visited, false, sizeof(visited));

        int M, N, Cabbage;
        cin >> M >> N >> Cabbage;

        // 시작점 후보가 될 큐 생성
        queue<vector<int>> q;

        for (int j = 0; j < Cabbage; ++j)
        {
            int X, Y;
            cin >> X >> Y;

            //행 - 열 이기 때문에 인덱스 순서를 Y - X 순으로 해야함.
            field[Y][X] = 1;
            q.push({X, Y});
        }

        //BFS 실시
        while(!q.empty())
        {
            // 큐로부터 시작점 추출
            int startx = q.front()[0];
            int starty = q.front()[1];
            
            q.pop();

            // 시작점이 이미 방문한 곳일 경우 아래 과정 생략.
            // 행 - 열 이기 때문에 인덱스 순서를 starty - startx 순으로 해야함.
            if (visited[starty][startx])
                continue;

            visited[starty][startx] = true;

            queue<vector<int>> area;
            area.push({startx, starty});

            while(!area.empty())
            {
                int x = area.front()[0];
                int y = area.front()[1];

                area.pop();

                for (int j = 0; j < 4; ++j)
                {
                    int newX = x + dx[j];
                    int newY = y + dy[j];

                    if (field[newY][newX] == 1 && !visited[newY][newX] &&
                        0 <= newX && newX < M && 0 <= newY && newY < N)
                    {
                        visited[newY][newX] = true;
                        area.push({newX, newY});
                    }
                }
            }
            ans++;
        }
        cout << ans << '\n';
    }


    return 0;
}
```
<br/>

bfs를 활용해서 분리된 영역의 개수를 구하는 문제였다.<br/>

다만 시작점을 어디로 정해야하는지 아이디어를 떠올리는데 힘이 들었던 것 같다.<br/>

나 같은 경우는 우선 시작점 후보를 담는 큐(위 코드 상 q)를 만들어주고,<br/>

시작점으로 추출하려고 했더니 이미 방문을 했던 지점이라면 탐색을 하지 않도록 구현하였다.<br/>

만약에 방문을 하지 않은 지점이라면 그 영역을 탐색해야한다는 의미이고, 탐색하는 과정에서<br/>

방문 여부를 true로 만들어주면 다음에 중복 방문할 일이 없어질 것이다.<br/>
<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
int field[50][50] = {0};
bool visited[50][50] = {false};

int main()
{
    int testCase;
    cin >> testCase;

    for (int i = 0; i < testCase; ++i)
    {
        // 정답 초기화
        int ans = 0;

        // 초기화
        field[50][50] = {0};
        visited[50][50] = {false};

        int N, M, Cabbage;
        cin >> N >> M >> Cabbage;

        // 시작점 후보가 될 큐 생성
        queue<vector<int>> q;

        for (int j = 0; j < Cabbage; ++j)
        {
            int X, Y;
            cin >> X >> Y;
            //행 - 열 이기 때문에 인덱스 순서를 Y - X 순으로 해야함.
            field[Y][X] = 1;
            q.push({X, Y});
        }

        //BFS 실시
        while(!q.empty())
        {
            // 큐로부터 시작점 추출
            int startx = q.front()[0];
            int starty = q.front()[1];
            
            q.pop();

            // 시작점이 이미 방문한 곳일 경우 아래 과정 생략.
            // 행 - 열 이기 때문에 인덱스 순서를 starty - startx 순으로 해야함.
            if (visited[starty][startx])
                continue;

            queue<vector<int>> area;
            area.push({startx, starty});

            while(!area.empty())
            {
                int x = area.front()[0];
                int y = area.front()[1];

                area.pop();

                for (int j = 0; j < 4; ++j)
                {
                    int newX = x + dx[j];
                    int newY = y + dy[j];

                    if (field[newY][newX] == 1 && !visited[newY][newX] &&
                        0 <= newX && newX < N && 0 <= newY && newY < M)
                    {
                        visited[newY][newX] = true;
                        area.push({newX, newY});
                    }
                }
            }
            ans++;
        }
        cout << ans << '\n';
    }


    return 0;
}
```

우선, 내가 N과 M의 입력순서를 잘못받았다. 그리고..<br/>
<br/>

### 정답코드로의 변환<br/>
<br/>

```
// 이전 코드

// 초기화
field[50][50] = {0};
visited[50][50] = {false};


// 정답 코드

// 초기화
memset(field, 0, sizeof(field));
memset(visited, false, sizeof(visited));
```

메모리 값을 초기화할 때 memset을 이용해주니 정답처리 되었다.<br/>

포인터 값을 초기화할 때 단순히 초기화를 이전 코드 처럼 하는 것이 아니라,<br/>

memset을 사용해야한다는 것을 잊어버렸다.<br/>

앞으로 주의해야겠다.<br/>

memset함수는 cstring 헤더에 있다.<br/>