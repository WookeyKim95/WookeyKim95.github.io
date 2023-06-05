---
layout: post
title:  "[백준] 1445_일요일 아침의 데이트 C++"
subtitle:   
date: 2023-06-05 08:46:04 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1445_일요일 아침의 데이트 C++<br/>
<br/>

우선순위 큐, 다익스트라를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <cstring>

using namespace std;
typedef pair<int, int> pii;

int N, M, SX, SY, FX, FY;

char map[51][51];
int cost[51][51];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

pii dijkstra(int a, int b)
{
    priority_queue<pair<pii, pii>, vector<pair<pii, pii>>, greater<pair<pii, pii>>> pq;
    pq.push({ {0, 0}, {a, b} });
    memset(cost, -1, sizeof(cost));

    cost[a][b] = 0;
    int ret1 = 0, ret2 = 0;

    while (!pq.empty())
    {
        pair<pii, pii> tp = pq.top();
        int Gab = tp.first.first;
        int NearGab = tp.first.second;
        int CurX = tp.second.first;
        int CurY = tp.second.second;
        pq.pop();

        bool to_break = false;

        for (int i = 0; i < 4; ++i)
        {
            int NGab = Gab;
            int NNearGab = NearGab;
            int NewX = CurX + dx[i];
            int NewY = CurY + dy[i];

            if (NewX == FX && NewY == FY)
            {
                ret1 = Gab;
                ret2 = NearGab;
                to_break = true;
                break;
            }

            if (cost[NewY][NewX] != -1)
                continue;
            if (!(1 <= NewX && NewX <= M && 1 <= NewY && NewY <= N))
                continue;
            
            if (map[NewY][NewX] == 'g')
                pq.push({ {NGab+1, NearGab}, {NewX, NewY} });
            
            else if (map[NewY][NewX] == '.')
            {
                bool flag = false;
                for (int j = 0; j < 4; ++j)
                {
                    if (map[NewY + dy[j]][NewX + dx[j]] == 'g')
                        flag = true;
                }

                if (flag)
                    pq.push({ {NGab, NNearGab+1}, {NewX, NewY} });
                else
                    pq.push({ {NGab, NNearGab}, {NewX, NewY} });
            }

            cost[NewY][NewX] = cost[CurY][CurX] + 1;
        }

        if (to_break)
            break;
    }
    
    return {ret1, ret2};
}

int main()
{
    cin >> N >> M;

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= M; ++j)
        {
            cin >> map[i][j];

            if (map[i][j] == 'S')
            {
                SX = j;
                SY = i;
            }
            else if (map[i][j] == 'F')
            {
                FX = j;
                FY = i;
            }
        }
    }

    pii ans = dijkstra(SX, SY);

    cout << ans.first << ' ' << ans.second;

    return 0;
}
```
<br/>

[풀이참고링크](https://100100e.tistory.com/302)<br/>


우선순위 큐에 들어가는 쌍을 아래와 같이 구현해두었다.<br/>

```
{ {쓰레기를 지나가는 경우, 쓰레기 옆을 지나가는 경우}, {X좌표, Y좌표} }
```

<br/>

그리고 pq를 생성할 때 아래와 같이 만들어주면 자동으로 Gab의 개수에 따라,<br/>

Gab 개수가 같다면 NearGab 개수에 따라서 오름차순 정렬을 해준다.<br/>

```
using namespace std;
typedef pair<int, int> pii;

...

priority_queue<pair<pii, pii>, vector<pair<pii, pii>>, greater<pair<pii, pii>>> pq;
```

그리고 끝 점에 도달하면 while문을 빠져나오도록 조치를 다음과 같이 해두었다.<br/>

```
bool to_break = false;

...

if (NewX == FX && NewY == FY)
{
    ret1 = Gab;
    ret2 = NearGab;
    to_break = true;
    break;
}

...

if (to_break)
    break;
```


아래 코드처럼 해놓으면 g를 지나가게 되면 쓰레기를 지나가는 경우의 수가 증가하고,<br/>

비어있다고 하더라도 g 옆을 지나가는 경우를 체크해줄 수 있다.<br/>

```
if (map[NewY][NewX] == 'g')
    pq.push({ {NGab+1, NearGab}, {NewX, NewY} });

else if (map[NewY][NewX] == '.')
{
    bool flag = false;
    for (int j = 0; j < 4; ++j)
    {
        if (map[NewY + dy[j]][NewX + dx[j]] == 'g')
            flag = true;
    }

    if (flag)
        pq.push({ {NGab, NNearGab+1}, {NewX, NewY} });
    else
        pq.push({ {NGab, NNearGab}, {NewX, NewY} });
}
```
