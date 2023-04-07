---
layout: post
title:  "[백준] 1407번_2로 몇 번 나누어질까 C++"
subtitle:   
date: 2023-04-07 07:11:33 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1407번_2로 몇 번 나누어질까 C++<br/>
<br/>

수학 규칙을 활용하는 문제.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int A, B;

long long m_func(long long a)
{
    long long result = a;

    for(long long i = 2; i <= a; i *= 2)
        result += (a/i) * (i/2);

    return result;
}

int main()
{
    cin >> A >> B;

    cout << m_func(B) - m_func(A-1);
    return 0;
}
```

[참고링크](https://sdev.tistory.com/607)를 참고하여 문제를 풀었다.<br/>

참고링크를 통해서 알 수 있었던 사실인데, 1부터 N까지 나눌 수 있는 2의 거듭제곱 수의 합을 구하는 공식이 있었다.<br/>

그래서 N을 그 공식에 넣고 그 공식을 통해 나온 값을 F(N)이라고 할 때,

F(B) - F(A-1)을 구함으로써 답을 도출할 수 있었다.<br/>

<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int A, B;

int m_pow(int a, int b)
{
    if (b == 0)
        return 1;

    int result = 1;
    for (int i = 1; i <= b; ++i)
    {
        result *= a;
    }

    return result;
}

int main()
{
    cin >> A >> B;

    long long ans = 0;
    for (long long i = A; i <= B; ++i)
    {
        long long Counter = i;
        int PowCounter = 0;

        while (Counter % 2 == 0)
        {
            Counter /= 2;
            PowCounter++;
        }
        ans += m_pow(2, PowCounter);
    }

    cout << ans;
    return 0;
}
```

범위가 1조까지 올라가므로 long long타입을 지정하였고

2의 승수를 세줄 PowCounter을 지정해주었다.

그런데 이렇게 탐색하면 시간초과가 나지 않을까 했지만

틀렸다고 나왔다. 이유는 내가 변수선언을 int로해서..

하지만 맞았다고 해도 분명 10^15라는 범위때문에 시간초과가 났을 것이다.

하지만 해결방법이 떠오르지 않아 구글링을 하기로 하였다.