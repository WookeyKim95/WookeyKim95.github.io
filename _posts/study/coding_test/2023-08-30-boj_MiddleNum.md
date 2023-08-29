---
layout: post
title:  "[백준] 1655_가운데를 말해요 C++"
subtitle:   
date: 2023-08-30 06:38:28 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1655_가운데를 말해요 C++<br/>
<br/>

우선순위 큐 2개를 이용해 실시간 중간값 알고리즘을 구현하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    priority_queue<int> Maxheap;
    priority_queue<int, vector<int>, greater<int>> Minheap;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        if (Maxheap.size() == Minheap.size())
            Maxheap.push(_input);
        
        else
            Minheap.push(_input);
        
        if (!Minheap.empty() && !Maxheap.empty() && Maxheap.top() > Minheap.top())
        {
            int MaxVal, MinVal;
            MaxVal = Maxheap.top();
            MinVal = Minheap.top();
            Maxheap.pop();
            Minheap.pop();
            Maxheap.push(MinVal);
            Minheap.push(MaxVal);
        }

        cout << Maxheap.top() << '\n';
    }

    return 0;
}
```

이전에 수업시간 때 유사한 문제를 풀었던 기억이 났다.<br/>

우선순위 큐 2개를 이용해서 실시간으로 중간값을 찾아내는 알고리즘을 짤 수 있다는 것을<br/>

구글에서 보았다.<br/>

이번 문제도 같은 로직으로 푸는 문제였다.<br/>

실시간 중간값 알고리즘을 찾는 알고리즘은 [이분의 글](https://o-tantk.github.io/posts/finding-median/)을

참조하면 될 것 같다.<br/>

Maxheap은 작은 값들을 내림차순으로 정렬해서 top자리엔 작은 값중 가장 큰 값이 위치하고,<br/>

Minheap은 큰 값들을 오름차순으로 정렬해서 top자리엔 큰 값중 가장 작은 값이 위치한다.<br/>

그래서 두 힙의 크기를 비교하면서 top에 있는 값을 조절하는 과정인 것이다.<br/>

여기서, 개수가 짝수일 때 작은 값을 말하라고 했으므로, Maxheap의 top을 출력 할 수 있도록<br/>

조절하는 것도 관건이었다.<br/>