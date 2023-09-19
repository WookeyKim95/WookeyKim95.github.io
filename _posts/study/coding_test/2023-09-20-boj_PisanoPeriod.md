---
layout: post
title:  "[백준] 9471_피사노 주기 C++"
subtitle:   
date: 2023-09-20 07:06:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 9471_피사노 주기 C++<br/>
<br/>

피사노 주기를 알아보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int main()
{
    int T;
    cin >> T;

    for (int tc = 1; tc <= T; ++tc)
    {
        int N, M;
        cin >> N >> M;

        int Cnt = 0;
        int A = 1, B = 1;

        while (true)
        {
            int tmp = (A + B) % M;
            A = B;
            B = tmp;
            Cnt++;

            if (A == 1 && B == 1)
                break;
        }

        cout << tc << ' ' << Cnt << '\n';
    }

    return 0;
}
```

이번 문제는 피사노 주기에 대해서 알아보는 문제이다.<br/>

사실 이 문제를 풀기에 앞서서 [피보나치 수 3](https://www.acmicpc.net/problem/2749) 문제에 대해 살펴보았는데,<br/>

이 문제를 풀기 전에 먼저 피사노 주기에 대해서 알아야 한다고 해서 풀어보았던 문제이다.<br/>

피사노 주기란, **피보나치 수열을 M으로 나눈 나머지가 주기를 이룬다**는 것이다.<br/>

[피보나치 수 3](https://www.acmicpc.net/problem/2749) 문제를 풀 때의 문제가 뭐였냐면<br/>

보통 피보나치 수열을 풀 때에는 배열을 선언하고 DP를 이용해서 풀어야 하는데<br/>

N의 최대값이 무려 100경이다.<br/>

저 수라면 long long으로도 커버가 힘들 것이고, (될지도 모른다.)<br/>

100경바이트 * 4 (혹은 8) 메모리를 할당했다가는 컴퓨터 터진다.<br/>

그래서 나온 것이 피사노 주기이다.<br/>

피사노 주기를 이용해서 피보나치 수 3 문제를 푸는 과정은 나중에 포스팅 하도록 하겠다.<br/>
