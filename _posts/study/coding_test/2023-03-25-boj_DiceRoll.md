---
layout: post
title:  "[백준] 14499번_주사위 굴리기 C++"
subtitle:   
date: 2023-03-25 10:27:41 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 14499번_주사위 굴리기 C++<br/>
<br/>

문제의 내용을 그대로 구현하는 문제.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int map[20][20];

int N, M, x, y, K;

struct dice
{
    int top = 0, bottom = 0, left = 0, right = 0, up = 0, down = 0;
    int CurrentX = 0, CurrentY = 0;
};

dice d;

void move(int order)
{
    if (order == 1)
    {
        if (d.CurrentX == M-1)
            return;
        
        d.CurrentX += 1;

        int temp = d.top;
        d.top = d.left;
        d.left = d.bottom;
        d.bottom = d.right;
        d.right = temp;
    }

    else if (order == 2)
    {
        if (d.CurrentX == 0)
            return;
        
        d.CurrentX -= 1;

        int temp = d.top;
        d.top = d.right;
        d.right = d.bottom;
        d.bottom = d.left;
        d.left = temp;
    }

    else if (order == 3)
    {
        if (d.CurrentY == 0)
            return;
        
        d.CurrentY -= 1;

        int temp = d.top;
        d.top = d.down;
        d.down = d.bottom;
        d.bottom = d.up;
        d.up = temp;
    }

    else if (order == 4)
    {
        if (d.CurrentY == N-1)
            return;
        
        d.CurrentY += 1;

        int temp = d.top;
        d.top = d.up;
        d.up = d.bottom;
        d.bottom = d.down;
        d.down = temp;
    }

    if (map[d.CurrentY][d.CurrentX] == 0)
        map[d.CurrentY][d.CurrentX] = d.bottom;
    
    else
    {
        d.bottom = map[d.CurrentY][d.CurrentX];
        map[d.CurrentY][d.CurrentX] = 0;
    }

    cout << d.top << '\n';

    return;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M >> y >> x >> K;

    d.CurrentX = x;
    d.CurrentY = y;

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {
            cin >> map[i][j];
        }
    }

    for (int i = 0; i < K; ++i)
    {
        int _input;
        cin >> _input;

        move(_input);   
    }

    return 0;
}
```
<br/>

우선, struct를 통해서 주사위의 모양을 구현하였다.<br/>

구조체에 들어가는 자료는 각 면의 수와 현재 좌표이다.<br/>

```
struct dice
{
    int top = 0, bottom = 0, left = 0, right = 0, up = 0, down = 0;
    int CurrentX = 0, CurrentY = 0;
};
```

그리고 주사위를 굴리는 과정을 move함수로 구현하였다.<br/>

이 때, 좌표 값이 범위를 넘어가려고 하면 굴리지 말고, 출력을 하지 말아야 하므로<br/>

아래와 같은 경우처럼 return을 시켜서 굴러가지 않도록 하였다.<br/>

```
// order = 1 동쪽으로 굴릴 때 
    if (order == 1)

        // X좌표가 M-1일 때 가장 오른쪽에 있으므로
        // 함수를 바로 return 시킨다.
        if (d.CurrentX == M-1)
            return;
```

서쪽, 남쪽, 북쪽도 같은 원리로 진행하였다.<br/>

그리고 문제 조건에서 이동 완료했을 때 칸에 적혀있는 수가 0이면 바닥면의 수를 복사하고,<br/>

그렇지 않다면 바닥면으로 칸의 수를 복사하고 칸의 수는 0이 된다.<br/>

이를 아래와 같이 구현하였다.<br/>

```
    if (map[d.CurrentY][d.CurrentX] == 0)
        map[d.CurrentY][d.CurrentX] = d.bottom;
    
    else
    {
        d.bottom = map[d.CurrentY][d.CurrentX];
        map[d.CurrentY][d.CurrentX] = 0;
    }
```
