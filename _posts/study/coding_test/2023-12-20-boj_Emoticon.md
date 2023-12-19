---
layout: post
title:  "[백준] 14226_이모티콘 C++"
subtitle:   
date: 2023-12-20 06:48:02 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 14226_이모티콘 C++<br/>
<br/>

BFS를 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>

#define MAX 2001

using namespace std;

int S;
bool visited[MAX][MAX];

int BFS(int s)
{
    queue<pair<int, pair<int, int>>> q;
    q.push({1, {0, 0}});
    visited[1][0] = true;

    while (!q.empty())
    {
        int Cur = q.front().first;
        int CurT = q.front().second.first;
        int CurClipBoard = q.front().second.second;
        q.pop();

        if (Cur == s)
            return CurT;

        for (int i = 0; i < 3; ++i)
        {
            int Next;
            if (i == 0)
                Next = Cur - 1;
                
            else if (i == 1 && CurClipBoard != 0)
                Next = Cur + CurClipBoard;

            if ((i == 0 || i == 1) && (1 <= Next && Next <= MAX-1) && !visited[Next][CurClipBoard])
            {
                visited[Next][CurClipBoard] = true;
                q.push({Next, {CurT + 1, CurClipBoard}});
            }
            
            else if (i == 2 && (1 <= Cur && Cur <= MAX-1) && !visited[Cur][Cur])
            {
                visited[Cur][Cur] = true;
                q.push({Cur, {CurT + 1, Cur}});
            }
        }
    }
}


int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> S;

    cout << BFS(S);

    return 0;
}
```

## 오답제출코드<br/>

```
#include <iostream>
#include <queue>

using namespace std;

int S;
bool visited[2001];

int BFS(int s)
{
    queue<pair<int, pair<int, int>>> q;
    q.push({1, {0, 0}});
    visited[1] = true;

    while (!q.empty())
    {
        int Cur = q.front().first;
        int CurT = q.front().second.first;
        int CurClipBoard = q.front().second.second;
        q.pop();

        if (Cur == s)
            return CurT;

        for (int i = 0; i < 3; ++i)
        {
            int Next;
            if (i == 0)
                Next = Cur - 1;
                
            else if (i == 1 && CurClipBoard != 0)
                Next = Cur + CurClipBoard;

            if ((i == 0 || i == 1) && !visited[Next] && (1 <= Next && Next <= 2000))
            {
                visited[Next] = true;
                q.push({Next, {CurT + 1, CurClipBoard}});
            }
            
            else if (i == 2)
                q.push({Cur, {CurT + 1, Cur}});
                
        }
    }
}


int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> S;

    cout << BFS(S);

    return 0;
}
```

클립보드의 수를 배열에 저장하지 않고 1차원 배열로 visited를 배치했을 때의 코드이다.

이 때에는 37%에서 오답으로 처리되길래 어디서 틀렸는지 모르겠어서 결국 구글링을 했다.<br/>

[참고링크](https://yabmoons.tistory.com/74)

참고링크에 따르면, visited를 [이모티콘의 수][클립보드에 저장된 이모티콘의 수] 형태로 2차원 배열로 저장을 했어야 했다.<br/>

나머지는 경우의 수에 따라서 i = 0, 1, 2로 나눠서 각각 알맞은 순서쌍을 큐에 넣도록 구현하였다.