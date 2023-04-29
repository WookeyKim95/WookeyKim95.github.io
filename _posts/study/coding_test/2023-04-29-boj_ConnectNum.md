---
layout: post
title:  "[백준] 1323_숫자 연결하기 C++"
subtitle:   
date: 2023-04-29 09:55:12 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1323_숫자 연결하기 C++<br/>
<br/>

수학적 규칙을 찾는 정수론 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <set>

using namespace std;

set<long long> visited;

int main()
{
    long long N, N_tmp, K, ans = 1;

    cin >> N >> K;
    
    long long Digit = 10;

    while (Digit <= N)
        Digit *= 10;

    N_tmp = N;

    while (true)
    {
        long long Remain = N_tmp % K;
        if (Remain == 0)
        {
            cout << ans;
            break;
        }

        N_tmp = Remain;
        N_tmp *= Digit;
        N_tmp += N;

        if (visited.find(N_tmp) != visited.end())
        {
            cout << -1;
            break;
        }

        visited.insert(N_tmp);
        ans++;
    }

    return 0;
}
```

마음 같아서는 숫자 뒤에다가 계속 이어 붙이고 나누기를 반복하고 싶지만,<br/>

그렇게 하면 오버플로우가 발생하기 때문에 다른 방법을 찾아야했다.<br/>

구글링 해본 결과 [참고링크](https://khu98.tistory.com/96)를 통해서 힌트를 얻을 수 있었다.<br/>

원래 숫자에다가 이어붙인 숫자를 나눈 결과는<br/>

나머지에다가 숫자를 이어붙인 결과랑 나눗셈을 했을 때 나머지의 값이 같다.<br/>

예를 들어, 2에다가 2를 이어붙이면 22이다.<br/>

이를 9로 나누면 4가 나온다. 여기까지는 오케이<br/>

다시 2를 이어붙이면 222이다. 이를 9로 나누면 나머지는 6이다.<br/>

이때, 이 결과는 4에다가 2를 이어붙여서 나온 값 42를 9로 나눈 나머지인 6과 같다.<br/>

다시한번 더 해보자. 2222를 9로 나누면 나머지는 8이다.<br/>

이 결과는 4의 다음 나머지인 6에다가 2를 이어붙여서 나온 값 62를 나눈 나머지인 8과 같다.<br/>

즉, 나머지에다가 숫자 K를 이어붙여서 연산을 할 수 있는 것이다!<br/>

참고링크에 있는 그림을 참고해도 될 것이다!<br/>

그렇다면 -1은 언제 출력하냐가 다음 문제이다.<br/>

여태까지 나왔던 나머지 패턴이 다시 나온다면 반복이 될 것이므로,<br/>

Set에다가 나머지 패턴을 저장해두고 이미 나온 패턴인지 조사를 한다.<br/>

이 때, 이미 나온 패턴이 다시 나온다면 그 때 -1을 출력하도록 구현하였다.<br/>