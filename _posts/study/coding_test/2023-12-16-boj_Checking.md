---
layout: post
title:  "[백준] 2981_검문 C++"
subtitle:   
date: 2023-12-16 07:38:47 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2981_검문 C++<br/>
<br/>

유클리드 호제법을 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>
 
using namespace std;
 
int arr[101];
int sol[101];
 
int Gcd(int a, int b)
{
    if(a == 0)
        return b;
    if(b == 0)
        return a;

    if(a % b == 0)
        return b;
    else
        return Gcd(b, a % b);
}
 
 
int main()
{
    int n, m, count = 0;
    cin >> n;
    
    for (int i = 0; i < n; ++i)
        cin >> arr[i];

    sort(arr, arr + n);
 
    m = arr[1] - arr[0];

    for (int i = 2; i < n; ++i)
        m = Gcd(m, arr[i] - arr[i - 1]);
 
    for (int i = 1; i * i <= m; ++i)
        if (m % i == 0)
        {
            sol[count++] = i;
            if (i != m / i)
                sol[count++] = m / i;
        }
 
    sort(sol, sol + count);

    for (int i = 0; i < count; ++i) 
        if (sol[i] != 1)
            cout << sol[i] << ' ';
    
}

```

[참고링크](https://kyunstudio.tistory.com/88)<br/>

유클리드 호제법을 이용해야한다는 것 까진 캐치했다.<br/>

하지만 최대 공약수를 찾는 것을 완전탐색 한다고 해도<br/>

범위가 10억 까지라서 시간초과가 날것은 불보듯 뻔했다.<br/>

방법이 떠오르지 않아서 참고링크를 참고했다.<br/>

참고링크에 따르면 풀이는 아래와 같다.<br/>

우선 조건에 맞춰서 M으로 나눴을 때, 나머지가 모두 동일하게 만족한 경우라고 가정을 하자.<br/>

그러면 밑의 조건을 만족하게 된다.

arr[1] % M = arr[1] - (arr[1] / M) * M<br/>
arr[2] % M = arr[2] - (arr[2] / M) * M<br/>
arr[3] % M = arr[3] - (arr[3] / M) * M<br/>

...

이 조건을 서로 빼주어보자.<br/>

그러면 아래와 같이 정리를 할 수 있게 된다.<br/>

arr[2] - arr[1] = M * (arr[2] / M - arr[1] / M)<br/>
arr[3] - arr[2] = M * (arr[3] / M - arr[2] / M)<br/>

...

arr[N] - arr[N-1] = M * (arr[N] / M - arr[N-1] / M )

이때, 규칙성을 생각해보면, arr[N] - arr[N-1]을 한 것은 'M*x' 라는 공통점이 있다는 것을 확인할 수 있다.('arr[2] / M - arr[1] / M'는 임의의 상수임으로 x로 치환)

즉, 반복문을 돌려 arr[k] - arr[k-1]들의 최대 공약수를 구한다면, M을 구해 낼 수 있게 된다.

수학적 규칙을 찾는 것은 역시 아직 어렵다 ㅠㅠ<br/>