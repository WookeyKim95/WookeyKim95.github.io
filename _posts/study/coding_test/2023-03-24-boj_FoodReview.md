---
layout: post
title:  "[백준] 1188번_음식 평론가 C++"
subtitle:   
date: 2023-03-24 10:48:41 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1188번_음식 평론가 C++<br/>
<br/>

유클리드 호제법, 최대공약수를 응용하는 수학 문제.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>

using namespace std;

int m_gcd(int a, int b)
{
    int mod = a % b;

    while (mod > 0)
    {
        a = b;
        b = mod;
        mod = a % b;
    }

    return b;
}

int main()
{
    int N, M;
    
    cin >> N >> M;

    // 소시지의 개수 = 평론가의 수 라면 자를 필요 없음.
    if (N / M >= 1 && N % M == 0)
        cout << 0;
    
    else if (N % M != 0)
    {
        int knife = 0;
        int Gcd = m_gcd(min(N, M), max(N,M));

        N /= Gcd;
        M /= Gcd;

        knife += (M - 1);
        
        knife *= Gcd;

        cout << knife;
    }

    return 0;
}
```
<br/>

우선, 소시지의 개수 % 평론가의 수 == 0이라면 그냥 소시지를 나눠주면 되므로<br/>

자를 필요가 없어서 0을 출력한다.<br/>

그리고 문제는 소시지의 개수 % 평론가의 수 != 0일 때이다.<br/>

규칙을 발견하였다.<br/>

- N과 M의 최대 공약수로 나눈다.
- M-1 만큼 칼질을 한다.
- 누적 칼질 횟수에다가 최대 공약수를 곱한다.

<br/>

시뮬레이션을 해보자.<br/>

소시지가 4개 있고, 평론가가 8명 있다고 하면<br/>

소시지 4개를 각각 반으로 자르면 된다. 총 4번이다.<br/>

이를 구하는 과정은 아래와 같다.

- N과 M을 최대 공약수인 4로 나눈다.
- 그럼 N = 1, M = 2가 된다.
- M - 1 번, 즉 1번만 칼질을 한다.
- 4번을 반복하므로 4를 곱한다.
- 칼질 횟수 = 4를 추출 완료하였다.

또 다른 시뮬레이션을 해보자. (N = 8, M = 17)

소시지 8개를 일렬로 나열한 다음에 그대로 17등분을 하면된다.<br/>

그래서 칼질 횟수는 16이 나올 것이다.<br/>

- 최대 공약수는 1이다. N과 M은 그대로.
- M - 1 = 16으로, 칼질 횟수에 더한다.
- 칼질 횟수 = 16이 추출되었다.

<br/>

N = 8, M = 3일 때도 마찬가지다.

- 우선, 소시지 6개는 2개씩 3명에게 나눠준다. (칼질 횟수 = 0)
- 소시지 2개가 남는다.
- 두 명에게 2/3 조각을, 한 명에서 1/3조각 2개를 준다.
- 따라서 칼질 횟수는 2회를 한다.
- 이 값은 M-1 과도 일치한다.

그리고 최대 공약수를 구하는 과정은 유클리드 호제법을 이용하였다.<br/>

```
// 이때, a < b
int m_gcd(int a, int b)
{
    int mod = a % b;

    while (mod > 0)
    {
        a = b;
        b = mod;
        mod = a % b;
    }

    return b;
}
```