---
layout: post
title:  "[백준] 1912_연속합 C++"
subtitle:   
date: 2023-10-20 07:05:09 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1912_연속합 C++<br/>
<br/>

투 포인터 같이 생겼으면서 다이나믹 프로그래밍을 써보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int Arr[100001];
int DP[100001];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    for (int i = 1; i <= N; ++i)
        cin >> Arr[i];
    
    int Max = Arr[1];
    DP[1] = Arr[1];
    for (int i = 2; i <= N; ++i)
    {
        if (DP[i-1] + Arr[i] >= Arr[i])
            DP[i] = DP[i-1] + Arr[i];
        else
            DP[i] = Arr[i];
        
        if (DP[i] > Max)
            Max = DP[i];
    }

    cout << Max;

    return 0;
}
```

부분합이랑 헷갈려서 그런지 투 포인터를 생각했었는데 다이나믹 프로그래밍 문제였다.<br/>

처음엔 아이디어가 안떠올라서 살짝 해맸다가<br/>

뒤늦게 아이디어 한 가지를 떠올렸다.<br/>

i 번째 까지 연속합 중에서 가장 큰 값을 저장한다고 해보자.<br/>

이 때, (i-1번째 까지 연속합 중에서 가장 큰 값) + i번째 수를 합하면 그 값이 나오지 않을까?<br/>

그렇다면 i-1번째 까지 연속합 중에서 가장 큰 값은<br/>

i-2번째 까지 연속합 중에서 가장 큰 값 + i-1번째 수겠지?<br/>

그런데 여기서 변수 한가지.<br/>

연속합을 했더니 오히려 자기 자신보다 작아지는 경우가 있다.<br/>

이렇게 될 경우에는 그냥 초기화 해버리고 DP[i]에다가 자기자신인 Arr[i]를 저장한다면<br/>

i번째 까지 연속합 중에서 가장 큰 값이 되는 것이다.<br/>

따라서 DP의 구성을 나는 이렇게 구성하였다.<br/>

```
int Max = Arr[1];
DP[1] = Arr[1];
for (int i = 2; i <= N; ++i)
{
    if (DP[i-1] + Arr[i] >= Arr[i])
        DP[i] = DP[i-1] + Arr[i];
    else
        DP[i] = Arr[i];
    
    if (DP[i] > Max)
        Max = DP[i];
}
```

이렇게 해주니 정답으로 처리되었다!