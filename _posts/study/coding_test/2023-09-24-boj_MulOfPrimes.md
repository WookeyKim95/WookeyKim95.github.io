---
layout: post
title:  "[백준] 2014_소수의 곱 C++"
subtitle:   
date: 2023-09-24 07:34:17 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2014_소수의 곱 C++<br/>
<br/>

우선순위 큐를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>

using namespace std;

int main()
{
    int N, K;
    priority_queue<int, vector<int>, greater<int>> pq;
    vector<int> List;

    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> K >> N;

    int count = 0;
    long long result = 0;
    int prevNum = 0;

    for (int i = 0; i < K; ++i)
    {
        int _input;
        cin >> _input;
        pq.push(_input);
        List.push_back(_input);
    }

    while (count != N)
    {
        while (prevNum == pq.top())
            pq.pop();
        
        for (int i = 0; i < K; ++i)
        {
            result = pq.top() * (long long)List[i];
            if (result >= 2147483648)
                break;
            pq.push(result);
        }

        prevNum = pq.top();
        pq.pop();
        count++;
    }

    cout << prevNum;

    return 0;
}
```

오답제출코드에서 정답제출코드로 넘어올 때 형변환에서 많은 애를 먹었다.<br/>

우선, 오답 원인이 int보다 2배의 메모리를 먹는 long long을<br/>

우선순위 큐에 많이 쌓아두어서이다.<br/>

그래서 메모리초과가 났고, long long 대신에 int를 쌓기로 하였다.<br/>

그런데 문제점이 있었다.<br/>

그러면 int로 어떻게 int 최대 범위가 넘어가는 것을 판별할 수 있을까?<br/>

내가 처음에 생각했던 방법은 아래와 같았다.<br/>

```
int result = 0;

...

result = pq.top() * (long long)List[i];
if (result <= 0)
    break;
pq.push(result);
```

범위가 넘어가면 int 값은 다시 -2147483648부터 시작하기 때문에<br/>

음수가 나올 것이라 생각했다.<br/>

그래서 음수일 경우에 break하도록 처리했지만 결과는 64%에서 틀렸습니다가 떴다.<br/>

지금 글을 쓰면서 반례가 생각난건데<br/>

만약에 long long으로 했을 때 값이 2147483648+2147483848의 값이 넘어간다면?<br/>

다시 양수로 돌아오게 되는 것이다. 그래서 예외처리가 되지 않아서 틀렸다고 뜨게 될 것이다.<br/>

그렇기 때문에 long long으로 형변환을 한 뒤 값을 비교해주어야 했다.<br/>

<br/>

## 오답제출코드 (메모리 초과)<br/>
<br/>

```
#include <iostream>
#include <queue>

using namespace std;

typedef long long ll;

int main()
{
    int N, K;
    priority_queue<ll, vector<ll>, greater<ll>> pq;
    vector<ll> List;

    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> K;

    int count = 0;
    ll result = 0;
    ll prevNum = 0;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        pq.push(_input);
        List.push_back(_input);
    }

    while (count != K)
    {
        while (prevNum == pq.top())
            pq.pop();
        
        for (int i = 0; i < N; ++i)
        {
            result = pq.top() * List[i];
            if (result >= 2147483648)
                break;
            pq.push(result);
        }

        prevNum = pq.top();
        pq.pop();
        count++;
    }

    cout << prevNum;

    return 0;
}
```

쉬워보이긴해도 고려해야할 요소가 많았던 문제였다.<br/>

우선, 내가 한 실수.<br/>

중복된 수를 고려해 주지 않았다.<br/>

예를 들어, 2, 5, 7이 주어졌다고 하면<br/>

2와 2를 곱한 수를 넣고, 2와 5를 곱한 수를 넣고, 2와 7을 곱한 수를 큐에 넣는다.<br/>

그런데 여기서 발생할 수 있는 문제점은<br/>

중복된 수가 나올 수 있다는 점이다.<br/>

로직에 따르면 2와 5를 곱한 수도 들어가고, 5와 2를 곱한 수도 들어가는데<br/>

10이라는 중복된 수를 발생시킬 수 있다.<br/>

이렇게 되면 count가 잘못세어질 수 있으니 아래 코드로 중복된 수를 제거해주자.<br/>

```
while (prevNum == pq.top())
    pq.pop();
```

그래서 prevNum이 필요했다.<br/>

prevNum으로 count번째 수가 얼마였는지 알고, 중복된 수를 제거할 수 있으니.<br/>

하지만 메모리초과가 떴고, 상술했듯이 long long을 int로 바꾸는 작업을 해주었다.<br/>

와중에 N이랑 K가 자리가 바뀐 것은 안비밀.<br/>