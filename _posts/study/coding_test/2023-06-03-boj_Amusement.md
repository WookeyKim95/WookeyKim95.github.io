---
layout: post
title:  "[백준] 1561_놀이 공원 C++"
subtitle:   
date: 2023-06-03 04:52:26 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1561_놀이 공원 C++<br/>
<br/>

이분 탐색을 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

#define MAX 60000000000

using namespace std;

int N, M;
int Time[10001];

int main()
{
    cin >> N >> M;

    for (int i = 1; i <= M; ++i)
        cin >> Time[i];

    if (N <= M)
    {
        cout << N;
        return 0;
    }

    long long left = 0, right = MAX, TotalTime = 0;

    while (left <= right)
    {
        long long mid = (left + right) / 2;
        long long Ride = M;
        
        for (int i = 1; i <= M; ++i)
            Ride += mid / Time[i];
        
        if (Ride >= N)
        {
            TotalTime = mid;
            right = mid - 1;
        }
        else
            left = mid + 1;
    }

    long long Ride = M;

    for (int i = 1; i <= M; ++i)
        Ride += ((TotalTime - 1) / Time[i]);

    for (int i = 1; i <= M; ++i)
    {
        if (TotalTime % Time[i] == 0)
            Ride++;

        if (Ride == N)
        {
            cout << i;
            break;
        }
    }

    return 0;
}
```
<br/>

[풀이 참고 링크](https://melonicedlatte.com/algorithm/2018/08/09/115053.html)<br/>

N의 최댓값은 2000000000이며, 시간의 최댓값은 30이므로 MAX를 60000000000으로 산정한다.<br/>

이분 탐색을 사용하였는데, 현재 시간을 이분탐색을 이용해서 찾고<br/>

그 시간에 몇 명을 태울 수 있는지를 계산한다.<br/>

태울 수 있는 사람이 N명 이상이 된다면 right와 TotalTime을 갱신,<br/>

그렇지 못한다면 LeftTime을 갱신한다.<br/>

이렇게 시간을 찾으면 (걸리는 시간 - 1)분 까지 태운 사람들의 수를 찾고,<br/>

그 다음에 한명씩 더해가면서 마지막 인원이 타는 놀이기구를 찾아낸다.<br/>