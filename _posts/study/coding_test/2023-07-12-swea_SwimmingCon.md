---
layout: post
title:  "[SWEA] 4193_수영대회 결승전 C++"
subtitle:   
date: 2023-07-12 07:53:23 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [SWEA] 4193_수영대회 결승전 C++<br/>
<br/>

완전탐색과 BFS가 합쳐져있었던 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <queue>

#define INF 99999999

using namespace std;

int N;
vector<vector<int>> map;
int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
int record[15][15];

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int BFS(int StartY, int StartX, int EndY, int EndX)
{
    queue<pair<int, pair<int, int>>> q;
    q.push({0, {StartY, StartX}});
    record[StartY][StartX] = 0;
    int Ret = INF;

    while (!q.empty())
    {
        int CurSec = q.front().first;
        int CurY = q.front().second.first;
        int CurX = q.front().second.second;
        q.pop();

        if (CurX == EndX && CurY == EndY)
        {
            Ret = m_min(Ret, CurSec);
            continue;
        }

        for (int i = 0; i < 4; ++i)
        {
            int NextX = CurX + dx[i];
            int NextY = CurY + dy[i];

            if (0 <= NextX && NextX < N && 0 <= NextY && NextY < N && record[NextY][NextX] > CurSec + 1 && !(map[NextY][NextX] == 1))
            {
                if (map[NextY][NextX] == 0)
                {
                    record[NextY][NextX] = CurSec + 1;
                    q.push({CurSec+1, {NextY, NextX}});
                }
                
                else if (map[NextY][NextX] == 2)
                {
                    if (CurSec % 3 == 2)
                    {
                        record[NextY][NextX] = CurSec + 1;
                        q.push({CurSec+1, {NextY, NextX}});
                    }

                    else
                    {
                        int time = 1;
                        while (true)
                        {
                            if ((time + CurSec) % 3 == 2)
                                break;
                            time++;
                        }
                        if (record[NextY][NextX] > record[CurY][CurX] + time + 1)
                        {
                            record[NextY][NextX] = CurSec + time + 1;
                            q.push({CurSec+1+time, {NextY, NextX}});
                        }
                    }
                }
            }
        }
    }

    if (record[EndY][EndX] == INF)
        return -1;
    else
        return Ret;
}

int main(int argc, char** argv)
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

	int test_case;
	int T;
	cin >> T;

	for(test_case = 1; test_case <= T; ++test_case)
	{
        cin >> N;
        map.clear();
        
        for (int i = 0; i < 15; ++i)
        {
            for (int j = 0; j < 15; ++j)
                record[i][j] = INF;
        }

        for (int i = 0; i < N; ++i)
        {
            vector<int> row;
            for (int j = 0; j < N; ++j)
            {
                int _input;
                cin >> _input;

                row.push_back(_input);
            }
            map.push_back(row);
        }

        int StartX, StartY, EndX, EndY;
        cin >> StartY >> StartX;
        cin >> EndY >> EndX;

        cout << '#' << test_case << ' ' << BFS(StartY, StartX, EndY, EndX) << '\n';

	}
	return 0;
}
```

2초마다 소용돌이가 없어졌다가 다시 생기는 부분을 구현하기가 어려웠던 것 같다.<br/>

그런데 살펴보면,

- 0초, 1초 : 소용돌이가 있음
- 2초 : 소용돌이가 없음
- 3초, 4초 : 소용돌이가 있음
- 5초 : 소용돌이가 없음
...

이렇게 된다. 이것을 규칙으로 찾아보면

A % 3 == 2 일 때 소용돌이가 없어져서 지나갈 수 있는 것을 알 수 있다.<br/>

그리고 A % 3 != 2라면 기다렸다가 지나가도록 코드를 구현해준다.<br/>

이 부분을 구현한 것이 아래의 코드이다.<br/>

```
else if (map[NextY][NextX] == 2)
{
    if (CurSec % 3 == 2)
    {
        record[NextY][NextX] = CurSec + 1;
        q.push({CurSec+1, {NextY, NextX}});
    }

    else
    {
        int time = 1;
        while (true)
        {
            if ((time + CurSec) % 3 == 2)
                break;
            time++;
        }
        if (record[NextY][NextX] > record[CurY][CurX] + time + 1)
        {
            record[NextY][NextX] = CurSec + time + 1;
            q.push({CurSec+1+time, {NextY, NextX}});
        }
    }
}
```
이 부분을 구현하는 것이 제일 어려웠고 오래걸렸던 것 같다.<br/>

그리고 내가 오답을 제출할 때 위의 코드에서<br/>

CurSec 대신에 record[CurY][CurX]를 써버리는 실수를 했다.<br/>

그래서 이 부분에서 오답이 많이 나왔다.<br/>

이를 고쳐주니까 바로 정답처리되었다.<br/>

앞으로 BFS풀 때에는 이 부분에 주의해야지.<br/>