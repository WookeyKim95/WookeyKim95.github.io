---
layout: post
title:  "[백준] 1715_카드 정렬하기 C++"
subtitle:   
date: 2023-04-24 10:12:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1715_카드 정렬하기 C++<br/>
<br/>

간단한 우선순위 큐를 사용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

int main()
{
    int N;
    cin >> N;

    priority_queue<int, vector<int>, greater<int>> pq;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        pq.push(_input);
    }

    int sum = 0;

    while (pq.size() > 1)
    {
        int a = pq.top();
        pq.pop();
        int b = pq.top();
        pq.pop();

        int ToPush = a+b;
        sum += ToPush;

        pq.push(ToPush);
    }

    cout << sum;

    return 0;
}
```

알고리즘 분류를 보니 우선순위 큐가 존재했다.<br/>

우선순위 큐가 오름차순으로 진행되도록 구현하였다.<br/>

그리고 작은 순서부터 순차적으로 합쳐지도록 구현했고,<br/>

합쳐진 값이 다시 우선순위 큐로 push 되도록 했다.<br/>

그리고 합쳐진 값을 sum에다가 누적시켰다.<br/>

이 과정을 우선순위 큐의 크기가 1개일 때 까지 반복한다.<br/>

```
while (pq.size() > 1)
    {
        int a = pq.top();
        pq.pop();
        int b = pq.top();
        pq.pop();

        int ToPush = a+b;
        sum += ToPush;

        pq.push(ToPush);
    }
```

이렇게 한 다음에 sum을 출력하면 답이 나온다.<br/>