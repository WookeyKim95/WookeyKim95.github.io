---
layout: post
title:  "[백준] 1365_꼬인 전깃줄 C++"
subtitle:   
date: 2023-05-31 08:41:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1365_꼬인 전깃줄 C++<br/>
<br/>

가장 긴 부분증가 수열(LIS)를 활용 하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N;
vector<int> poll;

int BinarySearch(int num)
{
    int left = 0;
    int right = poll.size() - 1;

    while (left < right)
    {
        int mid = (left + right) / 2;
        
        if (poll[mid] >= num)
            right = mid;
        else
            left = mid + 1;
    }
    return right;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    poll.push_back(-1);

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        if (_input > poll[poll.size() - 1])
            poll.push_back(_input);
        else
            poll[BinarySearch(_input)] = _input;
    }

    cout << N - poll.size() + 1;

    return 0;
}
```
<br/>

전깃줄이 꼬이지 않기 위해서는 증가 수열을 이루어야 한다.<br/>

즉, 전깃줄이 꼬이지 않으면서 가장 많이 남아있는 경우는 가장 긴 부분증가 수열을 이룰 때이다.<br/>

그래서 이진탐색과 LIS 알고리즘을 사용하여 구현하였다.<br/>

인덱스 값을 조절하기 위해서 패딩(padding) 역할을 할 -1을 미리 넣어주었고,<br/>

입력을 받을 때마다 맨 끝의 값보다 입력 값이 크면 벡터에다가 추가를 해주고,<br/>

그렇지 않다면 이진 탐색을 사용하여 적절한 위치에 있는 값을 대체해주는 식으로 구현하였다.<br/>

이렇게 LIS의 길이를 구하고, 전봇대의 개수에서 LIS의 길이를 빼고 1을 더하는 식으로 답을 도출하였다.<br/>

1을 더하는 이유는 패딩으로 넣어준 -1까지 개수에 포함해서 빼줬으니 이를 다시 보충하기 위함이다.<br/>