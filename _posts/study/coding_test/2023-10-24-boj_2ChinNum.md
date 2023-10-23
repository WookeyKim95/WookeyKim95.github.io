---
layout: post
title:  "[백준] 2193_이친수 C++"
subtitle:   
date: 2023-10-24 06:50:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2193_이친수 C++<br/>
<br/>

규칙을 찾아서 다이나믹 프로그래밍으로 푸는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    long long DP[91];

    DP[0] = 0;
    DP[1] = 1;
    DP[2] = 1;

    for (int i = 3; i <= 90; ++i)
        DP[i] = DP[i-1] + DP[i-2];

    cin >> N;
    cout << DP[N];

    return 0;
}
```

문제를 보고 바로 DP임을 직감했다.<br/>

그리고 경우의 수의 규칙을 찾아서 풀어보았다.<br/>

```
1 -> 1개
10 -> 1개
101 100 -> 2개
1010 1001 1000 -> 3개
10100 10010 10000 10001 10101 -> 5개
101000 101001 100100 100101 100000 100001 100010 101010 -> 8개
...
```

뭔가 규칙이 보이지 않는가? 그렇다. 바로 개수가 피보나치수열을 따라간다.<br/>

그래서 피보나치 수열대로 구현을 해서 풀었고, 정답이 출력되었다.<br/>

<br/>

## 오답제출코드<br/>

```
#include <iostream>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    int DP[91];

    DP[0] = 0;
    DP[1] = 1;
    DP[2] = 1;

    for (int i = 3; i <= 90; ++i)
        DP[i] = DP[i-1] + DP[i-2];

    cin >> N;
    cout << DP[N];

    return 0;
}
```

피보나치 수열은 수가 커지면 int 범위를 가볍게 넘길 수 있으니<br/>

mod 연산한 값을 넣으라고 하는게 아니면 long long을 써두자.<br/>