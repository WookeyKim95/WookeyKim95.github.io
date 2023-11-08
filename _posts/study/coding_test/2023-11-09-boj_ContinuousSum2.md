---
layout: post
title:  "[백준] 13398_연속합 2 C++"
subtitle:   
date: 2023-11-09 07:20:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 13398_연속합 2 C++<br/>
<br/>

연속합을 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

#define MAX 100001

using namespace std;

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

    int Arr[MAX];
    int LDP[MAX];
    int RDP[MAX];

    int N, Ans;
    cin >> N;   

    for (int i = 1; i <= N; ++i)
        cin >> Arr[i];
    
    if (N == 1)
    {
        cout << Arr[1];
        return 0;
    }

    LDP[1] = Arr[1];
    Ans = LDP[1];

    for (int i = 2; i <= N; ++i)
    {
        LDP[i] = m_max(Arr[i], LDP[i-1] + Arr[i]);
        Ans = m_max(Ans, LDP[i]);
    }

    RDP[N] = Arr[N];

    for (int i = N-1; i >= 1; --i)
        RDP[i]= m_max(Arr[i], RDP[i+1] + Arr[i]);

    for (int i = 2; i <= N-1; ++i)
        Ans = m_max(Ans, LDP[i-1] + RDP[i+1]);

    cout << Ans;

    return 0;
}
```

연속합 1 문제에 따르면 음수일 경우에는 그냥 생략하면 되는 것이었기 때문에<br/>

맨 처음에는 일일이 음수 하나만을 빼고 for문을 돌려볼까 생각했었다.<br/>

근데 그러면 시간복잡도는 O(N)이요, N의 최대값은 100000이니<br/>

당연히 시간초과가 날 수 밖에 없는 구조라서 바로 포기했다.<br/>

아쉽게도 아이디어가 떠오르지 않아서 [이분의 글](https://yabmoons.tistory.com/617)을 참고하였다.<br/>

아! 이분의 아이디어는 이거다.<br/>

음수, 즉 빼볼 수의 양 옆의 연속합을 더하면 그 경우가 바로 뺐을 경우의 연속합이지 않는가?<br/>

```
1 2 3 -4 5 6 7
```

이 있다고 하면 -4를 빼면 (1 + 2 + 3) + (5 + 6 + 7) 이 연속합이 되는 것이지.<br/>

그래서 왼쪽에서 부터 계산한 LDP와 오른쪽에서부터 계산한 RDP를 따로 마련해주고<br/>

제거하지 않았을 경우 Ans와, 제거했을 경우의 Ans 중 어느 것이 더 큰지를 비교하는 작업을 했다.<br/>

```
for (int i = 2; i <= N-1; ++i)
    Ans = m_max(Ans, LDP[i-1] + RDP[i+1]);
```

이러면 정답출력.