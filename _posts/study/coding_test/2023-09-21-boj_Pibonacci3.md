---
layout: post
title:  "[백준] 2749_피보나치 수 3 C++"
subtitle:   
date: 2023-09-21 07:22:06 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2749_피보나치 수 3 C++<br/>
<br/>

피사노 주기를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

#define MOD 1000000
#define PISANO 1500000

using namespace std;

long long arr[PISANO + 1];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    long long N;
    cin >> N;

    arr[0] = 0;
    arr[1] = 1;

    for (int i = 2; i <= PISANO; ++i)
        arr[i] = (arr[i-1] + arr[i-2]) % MOD;

    cout << arr[N % PISANO];

    return 0;
}
```

이번 문제는 피사노 주기를 활용하는 문제이다.<br/>

보통 피보나치 수열 같으면 배열 선언하고, for문 하나 돌려서 다이나믹 프로그래밍을 사용하겠지만,<br/>

이번 문제는 함부로 그럴 수 없다.<br/>

왜냐하면 최대 N 값이 100경이다.<br/>

즉, 100경 * 4 (또는 8) 바이트의 메모리를 선언해주어야 하는데 이러면 컴퓨터 터진다.<br/>

그리고 1초당 1억번의 연산을 한다고 가정하면 10000000000초가 걸린다. 이게 몇년이야?<br/>

따라서 우리는 다른 방법을 찾아야하는데 피사노 주기를 활용할 수 있다.<br/>

이 문제를 풀기에 앞서서 [피사노 주기](https://www.acmicpc.net/problem/9471) 문제를 알아야한다.<br/>

피사노 주기란, 피보나치 수열을 어떤 수 M으로 나누어서 나오는 순열은 주기를 이룬다는 것이다.<br/>

즉, 아무리 큰 수라고 하더라도 1000000으로 나눈 수도 어떤 수 M을 주기로 일정한 주기를 이룬다는 것이다.<br/>

피사노 주기를 통해서 구한 결과, 1000000으로 나눈 나머지들이 이루는 주기는 1500000이다.<br/>

즉, arr[1500001] = arr[1]과 같은 값을 가진다는 것이다.<br/>

따라서 arr[N % 1500000]의 값을 출력하면 된다.<br/>