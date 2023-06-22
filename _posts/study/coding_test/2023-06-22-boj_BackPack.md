---
layout: post
title:  "[백준] 12865_평범한 배낭 C++"
subtitle:   
date: 2023-06-22 08:04:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 12865_평범한 배낭 C++<br/>
<br/>

정렬, 그리디인줄 알았더니 DP로 푸는 것이었던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int DP[101][100001];
int W[101];
int V[101];

int N, K;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> K;

    for (int i = 1; i <= N; ++i)
        cin >> W[i] >> V[i];
    
    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= K; ++j)
        {
             if (j-W[i] >= 0)
                DP[i][j] = m_max(DP[i-1][j], DP[i-1][j-W[i]] + V[i]);
			 
             else
                DP[i][j] = DP[i-1][j];
        }
    }

    cout << DP[N][K];

    return 0;
}
```
<br/>

배낭 문제에 대한 기초적인 문제였다.<br/>

처음 접했을 때에는 정렬을 하고 그리디를 통해서 풀어야하는 문제로 생각했다.<br/>

하지만 설계가 잘 되지도 않았고, 입력 수가 10만으로 큰 것으로 보아<br/>

시간이 오래걸리는 그리디는 아니라고 생각했었다.<br/>

궁금해서 검색을 통해 풀었고, [참고링크](https://cocoon1787.tistory.com/206)를 찾았다.<br/>

배낭 문제를 풀어본 적이 없어서 내가 이것을 DP라고 생각하지 못했던 것 같다.<br/>

뭔가 가장 긴 부분수열 구하기 같은 문제랑 비슷한 느낌이 난다.<br/>

기존에 있던 값과 다른 물건을 넣었을 때 값 중 어느것이 더 크냐를 비교하게 되는 원리이다.<br/>