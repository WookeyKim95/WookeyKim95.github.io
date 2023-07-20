---
layout: post
title:  "[백준] 1720_타일 코드 C++"
subtitle:   
date: 2023-07-21 07:26:38 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1380_귀걸이 C++<br/>
<br/>

점화식을 찾아야 했던 DP문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);


    int DP[31];
    int N;
    cin >> N;
    
    DP[0] = 1;
    DP[1] = 1;

    for (int i = 2; i <= 30; ++i)
        DP[i] = DP[i-1] + DP[i-2]*2;

    if (N % 2 == 1)
        cout << (DP[N] + DP[(N-1)/2])/2 << '\n';
    else
        cout << (DP[N] + DP[N/2] + DP[N/2 - 1]*2)/2 << '\n';
    
    return 0;
}
```

본래 2xN타일링 문제를 응용한 DP문제이다.<br/>

그렇다는 것은 점화식을 찾으면 된다는 의미이니 점화식 찾기에 호기롭게 도전하였다.<br/>

우선, N=30까지 있으니까 자료형을 int까지만 잡아도 충분할 것이라 생각이 든다.<br/>

2xN 타일링 문제에 따르면 점화식은 아래와 같았으니 for문을 통해서 DP배열을 채워준다.<br/>

```
for (int i = 2; i <= 30; ++i)
    DP[i] = DP[i-1] + DP[i-2]*2;
```

그리고 점화식을 찾고자 했지만 20분을 헤매도 안되더라.. (ㅠㅠ)<br/>

[참고링크](https://stillchobo.tistory.com/106)에 따라서 경우의 수를 구했는데<br/>

자체적으로 좌우 대칭인 경우만을 구하면 암호의 경우의 수가 나온다고 한다.<br/>

홀수인 경우와 짝수인 경우로 나뉘었다. (아하!)<br/>

홀수인 경우는 중간에 1x2가 들어가야 한다.<br/>

그래서 아래와 같은 식이 나온다.<br/>

```
(DP[N] + DP[(N-1)/2])/2 // N가지 경우 + 좌우 대칭인 경우
```

짝수인 경우는 중간에 2x2와 1x2 2개가 들어가야 한다.<br/>

그래서 아래와 같은 식이 나온다.<br/>

```
(DP[N] + DP[N/2] + DP[N/2 - 1]*2)/2
```

이를 출력해주면 끝.