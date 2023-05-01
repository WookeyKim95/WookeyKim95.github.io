---
layout: post
title:  "[백준] 1644_소수의 연속합 C++"
subtitle:   
date: 2023-05-01 11:34:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1563_개근상 C++<br/>
<br/>

투 포인터와 에라토스테네스의 체를 사용하는 수학 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>
#include <cmath>

#define MAX 4000000

using namespace std;

int N;
bool IsPrime[MAX+1];
vector<int> ContinualPrimeSum;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    memset(IsPrime, true, sizeof(IsPrime));

    cin >> N;

    for (int i = 2; i <= sqrt(N); ++i)
    {
        for (int j = i+i; j <= N; j += i)
        {
            IsPrime[j] = false;
        }
    }

    ContinualPrimeSum.push_back(0);
    int sum = 0;
    for (int i = 2; i <= N; ++i)
    {
        if (IsPrime[i])
        {
            sum += i;
            ContinualPrimeSum.push_back(sum);
        }
    }

    int ans = 0;
    size_t left = 0, right = 0;

    while (left <= right && right < ContinualPrimeSum.size())
    {
        if (ContinualPrimeSum[right] - ContinualPrimeSum[left] < N)
            right++;
        else if (ContinualPrimeSum[right] - ContinualPrimeSum[left] > N)
            left++;
        
        else
        {
            right++;
            ans++;
        }    
    }

    cout << ans;

    return 0;
}
```

구글링을 하여 [참고링크](https://cocoon1787.tistory.com/357)의 풀이를 따라 코딩하였다.<br/>

코드에 적혀있는 방법대로 에라토스테네스의 체를 사용하는 방법은 아직 익숙치 않은 것 같다.<br/>

```
    memset(IsPrime, true, sizeof(IsPrime));

    for (int i = 2; i < sqrt(N); ++i)
    {
        for (int j = i+i; j <= N; j += i)
        {
            IsPrime[j] = false;
        }
    }
```

그리고 연속된 소수 합의 모음을 벡터에다가 넣어주었고,<br/>

투 포인터를 사용해서 합의 경우의 수를 체크하도록 구현하였다.<br/>
