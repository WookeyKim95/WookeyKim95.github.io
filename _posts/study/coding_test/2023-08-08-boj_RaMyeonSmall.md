---
layout: post
title:  "[백준] 18185_라면 사기 (Small) C++"
subtitle:   
date: 2023-08-08 07:25:33 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 18185_라면 사기 (Small) C++<br/>
<br/>

그리디 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

vector<int> Counter;
int N;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        Counter.push_back(_input);
    }

    int Price = 0;
    for (int i = 0; i < N-2; ++i)
    {
        while (Counter[i] > 0)
        {
            Counter[i]--;
            if (Counter[i+1] > 0)
            {
                Counter[i+1]--;
                if (Counter[i+2] > 0 && Counter[i+1] < Counter[i+2])
                {
                    Counter[i+2]--;
                    Price += 7;
                }
                else
                    Price += 5;
            }
            else
                Price += 3;
        }
    }

    while (Counter[N-2] > 0)
    {
        Counter[N-2]--;
        if (Counter[N-1] > 0)
        {
            Counter[N-1]--;
            Price += 5;
        }
        else
            Price += 3;
    }

    while (Counter[N-1] > 0)
    {
        Counter[N-1]--;
        Price += 3;
    }

    cout << Price;

    return 0;
}
```
다이아 문제라서 쫄고 있었다가<br>

주변 사람들이 풀어보라고해서 한번 풀어본 문제이다.<br/>

그래도 그리디 문제라서 생각보다는 할만했던 것 같다.<br/>

내 백준 인생 최초로 풀어본 다이아문제이었다.<br/>

되도록 라면을 3개씩 사도록 구현하는 것이 포인트였다.<br/>

그리고 마지막 2개의 공장만이 남았을 때를 예외처리 해주는 것이 또 다른 포인트.<br/>
<br/>

## 오답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>

using namespace std;

vector<int> Counter;
int N;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        Counter.push_back(_input);
    }

    int Price = 0;
    for (int i = 0; i < N-2; ++i)
    {
        while (Counter[i] > 0)
        {
            Counter[i]--;
            if (Counter[i+1] > 0)
            {
                Counter[i+1]--;
                if (Counter[i+2] > 0)
                {
                    Counter[i+2]--;
                    Price += 7;
                }
                else
                    Price += 5;
            }
            else
                Price += 3;
        }
    }

    while (Counter[N-2] > 0)
    {
        Counter[N-2]--;
        if (Counter[N-1] > 0)
        {
            Counter[N-1]--;
            Price += 5;
        }
        else
            Price += 3;
    }

    while (Counter[N-1] > 0)
    {
        Counter[N-1]--;
        Price += 3;
    }

    cout << Price;

    return 0;
}
```

최대한 인접한 3개를 한꺼번에 사는 것이 유리하기 때문에<br/>

낮은 것부터 비워가는 식으로 구현하였다.<br/>

그리고 다음 라면과 다다음 라면이 남아있는 지를 검사해서<br/>

가능하면 7원을 이용해서 사도록 구현하였다.<br/>

하지만 반례가 있었다.<br/>

```
4
2 3 2 1

// 정답 : 19
// 출력 값 : 20
```

2 3 2 1 -> 1 2 2 1 -> 0 1 1 1 - > 0 0 0 0 과정을 거쳐서 19원에 모두 살 수 있다.<br/>

음.. 그렇다면 어떻게 해야 좋을 까?<br/>

아! i+2번째보다 i+1번째 값이 더 크다면 i+1번째 까지만 사게 하는 것이다!<br/>

살 수 있는 조건임에도 불구하고 말이지<br/>

i+2라면을 살 때 따라서 조건문을 아래와 같이 바꿔주었다.<br/>

```
if (Counter[i+2] > 0 && Counter[i+1] < Counter[i+2])
{
    Counter[i+2]--;
    Price += 7;
}
```

이렇게 바꿔주니 19원이 나온다!