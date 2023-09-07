---
layout: post
title:  "[백준] 13397_구간 나누기 2 C++"
subtitle:   
date: 2023-09-08 07:30:27 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 13397_구간 나누기 2 C++<br/>
<br/>

이분 탐색 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N, M;
int Left, Right, Mid;
vector<int> arr;

bool Calcul()
{
    int Cnt = 1;
    int Maxv = arr[0];
    int Minv = arr[0];

    for (size_t i = 1; i < arr.size(); ++i)
    {
        if (Maxv < arr[i])
            Maxv = arr[i];
        if (Minv > arr[i])
            Minv = arr[i];
        
        if (Maxv - Minv > Mid)
        {
            Cnt++;
            Maxv = arr[i];
            Minv = arr[i];
        }
    }

    return Cnt <= M;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    int Max = 0;
    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        if (_input > Max)
            Max = _input;
        arr.push_back(_input);
    }

    Left = 0;
    Right = Max;
    int ans = Max;

    while (Left <= Right)
    {
        Mid = (Left + Right) / 2;

        if (Calcul())
        {
            Right = Mid - 1;
            if (ans > Mid)
                ans = Mid;
        }
        else
            Left = Mid + 1;
    }

    cout << ans;

    return 0;
}
```

이분 탐색 문제를 푸는데 있어서 제일 중요한 것은<br/>

목표치를 Mid로 설정하고 새로운 Mid 값을 어떻게 설정할 것인지<br/>

조건을 구현하는 것이다. 이번 문제에서도 그 점이 까다로웠던 것 같다.<br/>

우선, 문제에서 최댓값이 가장 작은 경우를 구하라고 했으니<br/>

```
if (Calcul())
{
    Right = Mid - 1;
    if (ans > Mid)
        ans = Mid;
}
```

조건을 만족했을 때 Right 값을 갱신시키면서 만약에 기존 ans보다 새롭게 작은 Mid가 등장한다면<br/>

ans 값을 갱신시켜줌으로써 이를 만족시켰다.<br/>

그리고 다음으로 구간에서 점수를 계산하는 코드이다.<br/>

```
bool Calcul()
{
    int Cnt = 1;
    int Maxv = arr[0];
    int Minv = arr[0];

    for (size_t i = 1; i < arr.size(); ++i)
    {
        if (Maxv < arr[i])
            Maxv = arr[i];
        if (Minv > arr[i])
            Minv = arr[i];
        
        if (Maxv - Minv > Mid)
        {
            Cnt++;
            Maxv = arr[i];
            Minv = arr[i];
        }
    }

    return Cnt <= M;
}
```
그룹의 개수를 Cnt로 설정하고,<br/>

만약에 최대값 최소값의 차이가 Mid를 넘어서게 된다면<br/>

그룹을 하나 형성시키고, 이렇게 형성시킨 그룹이 M 이하면 조건을 만족시킨다는 것을 반환하였다.<br/>

이 때, 조건이 끝나는 순간에도 그룹이 추가가 되므로 Cnt = 1 부터 시작하도록 조치하였다.<br/>