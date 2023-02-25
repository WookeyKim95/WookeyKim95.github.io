---
layout: post
title:  "[백준] 1158번_요세푸스 문제 C++"
subtitle:   
date: 2023-02-25 11:00:59 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1158번_요세푸스 문제 C++<br/>
<br/>

원형큐를 구현하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

int main()
{
    int N, K;
    cin >> N >> K;

    queue<int> q;
    vector<int> result;

    for (int i = 1; i <= N; ++i)
        q.push(i);
    
    int count = 1;
    while (!q.empty())
    {
        if (count == K)
        {
            result.push_back(q.front());
            q.pop();
            count = 1;
        }
        else
        {
            q.push(q.front());
            q.pop();
            count++;
        }
    }

    cout << "<";
    int len = result.size();
    for (int i = 0; i < len; ++i)
    {
        if (i != len-1)
            cout << result[i] << ", ";
        else
            cout << result[i] << ">";
    }

    return 0;
}
```
<br/>

사실 vector는 출력을 하기 위해서 어쩔수 없이 추가를 해준 것이고,<br/>

이 문제의 메인 디쉬는 원형큐 구현부이다.<br/>

카운터가 K랑 일치하지 않으면 맨 앞의 원소를 맨 뒤로 보내는 형식으로 계속 순환시키는 것이다.<br/>