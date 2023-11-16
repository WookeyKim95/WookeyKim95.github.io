---
layout: post
title:  "[백준] 2346_풍선 터뜨리기 C++"
subtitle:   
date: 2023-11-16 06:43:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2346_풍선 터뜨리기 C++<br/>
<br/>

덱을 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <deque>

using namespace std;

int main()
{
    deque<pair<int, int>> deq;

    int N;
    cin >> N;

    for (int i = 1; i <= N; ++i)
    {
        int _input;
        cin >> _input;
        deq.push_back({_input, i});
    }

    while (!deq.empty())
    {
        int Num = deq.front().first;
        int A = deq.front().second;
        cout << A << ' ';
        deq.pop_front();

        if (Num > 0)
        {
            for (int i = 0; i < Num-1; ++i)
            {
                deq.push_back(deq.front());
                deq.pop_front();
            }
        }
        else
        {
            for (int i = 0; i < -Num; ++i)
            {
                deq.push_front(deq.back());
                deq.pop_back();
            }
        }
    }

    return 0;
}
```
<br/>

큐와 스택의 기능을 모두 활용할 수 있어서 인기가 많은 자료구조 덱.<br/>

이를 활용하기 위해서 이 문제를 선택해 문제를 풀었다.<br/>

구현 원리는 간단하다.<br/>

적힌 수에 따라서 시프트를 해주는 것이다.<br/>

다만 차이점이 보일 것이다.<br/>

```
if (Num > 0)
{
	for (int i = 0; i < Num-1; ++i)
	{
		deq.push_back(deq.front());
		deq.pop_front();
	}
}
else
{
	for (int i = 0; i < -Num; ++i)
	{
		deq.push_front(deq.back());
		deq.pop_back();
	}
}
```

양수일 경우 Num-1이라고 적어준 이유는<br/>

이미 시프트를 하기 이전에 pop_front()를 한번 해주었기 때문이다.<br/>

예를 들어

```
5
3 2 1 -3 -1
```
에서 1번 풍선을 터뜨리면 다음으로는 4번째 풍선으로 가야한다.<br/>

그런데 터뜨리는 과정에서 pop_front()를 하면<br/>

```
2 1 -3 -1
```
이 남고, 여기서 pop_front()를 터뜨렸던 풍선의 번호 3만큼 하게 된다면<br/>

목표였던 -3이 아니라 -1로 가게 된다.<br/>

그래서 Num-1만큼만 pop_front()를 진행하도록 하였다.<br/>