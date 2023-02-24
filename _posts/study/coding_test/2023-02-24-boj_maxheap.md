---
layout: post
title:  "[백준] 11279번_최대힙 C++"
subtitle:   
date: 2023-02-24 14:26:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11279번_최대힙 C++<br/>
<br/>

우선순위큐를 이용해서 최대힙을 구현하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>

using namespace std;

int main()
{
    cin.sync_with_stdio(0);
    cin.tie(0);
    
    priority_queue<int> pq;

    int N;
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int tmp;
        cin >> tmp;
        
        if (tmp == 0)
        {
            if (pq.empty())
                cout << 0 << '\n';
            else
            {
                cout << pq.top() << '\n';
                pq.pop();
            }
        }

        else
        {
            pq.push(tmp);
        }
    }

    return 0;
}
```
<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <queue>

using namespace std;

int main()
{
    priority_queue<int> pq;

    int N;
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int tmp;
        cin >> tmp;
        
        if (tmp == 0)
        {
            if (pq.size() == 0)
                cout << 0 << '\n';
            else
            {
                cout << pq.top() << '\n';
                pq.pop();
            }
        }

        else
        {
            pq.push(tmp);
        }
    }
    return 0;
}
```

구글링을 해보니, 우선순위큐를 활용하면 기본적으로 최대힙이 구현된다는 것을 알았다.<br/>

그래서 우선순위큐를 이용해서 구현을 하였다.<br/>

출력 자체는 맞는 것 같은데 시간초과가 떴다.<br/>
<br/>

## sync_with_stdio(0)?? cin.tie(0)??<br/>
<br/>

```
    cin.sync_with_stdio(0);
    cin.tie(0);
```
<br/>

맨 처음에 위의 코드를 추가해주니 시간초과가 해제되었다. 무슨 역할인거지??<br/>

[참고링크](https://jaimemin.tistory.com/1521)에 따르면, 기본적인 상태에선 c언어의 stdio와 c++언어의 iostream 간의 동기화가 발생하는 과정에서 딜레이가 발생한다고 한다.<br/>

위의 cin.sync_with_stdio(0)구문은 동기화를 비활성화 시켜서 C언어의 printf와 C++언어의 cout를 같이 사용할 수 없게 되는 대신에 독립적인 버퍼를 활성화 시켜서 속도를 빠르게 한다고 한다.<br/>

그리고 cin.tie(0)는 cout와 cin의 묶음을 풀어 속도를 높여준다고 한다.<br/>
이번 문제처럼 입력과 출력을 반복할 때 사용해야 한다고 한다.<br/>

평상시에는 사용하지는 않겠으나, 알고리즘 문제를 풀 때에는 요긴하게 잘 사용해야 할 듯 하다.<br/>