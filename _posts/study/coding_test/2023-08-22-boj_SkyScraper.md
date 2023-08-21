---
layout: post
title:  "[백준] 1328_고층 빌딩 C++"
subtitle:   
date: 2023-08-22 07:12:31 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1328_고층 빌딩 C++<br/>
<br/>

bottom-up 다이나믹 프로그래밍을 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

#define MOD 1000000007

using namespace std;

int N, L, R;
long long DP[101][101][101];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> L >> R;

    DP[1][1][1] = 1;

    for (int i = 2; i <= N; ++i)
    {
        for (int j = 1; j <= L; ++j)
        {
            for (int k = 1; k <= R; ++k)
                DP[i][j][k] = (DP[i-1][j-1][k] + DP[i-1][j][k-1] + DP[i-1][j][k] * (i-2)) % MOD;
        }
    }

    cout << DP[N][L][R];

    return 0;
}
```

[구글링 후 참고링크](https://hyeo-noo.tistory.com/94)<br/>

다이나믹 프로그래밍 문제였는데<br/>

N L R 조건에서 가능한 경우를 구하는 것이었으니 점화식을 세워서 풀어봐야 했던 문제였다.<br/>

DP[N][L][R]이 의미하는 것은 역시 문제에 나온대로<br/>

N개의 빌딩이 있고, 왼쪽에서 보았을 때 L개, 오른쪽에서 보았을 때 R개의 빌딩이 보이면<br/>

배치 가능한 경우의 수이다.<br/>

아 N의 최대값도 작은데 일일이 탐색하고 조건을 따지면서 추가해야하나? 싶었는데<br/>

참고링크를 보니까 점화식을 세우는 것이라서 조건을 따질 필요는 없었다.<br/>

아! 물론 점화식을 세울 때에는 조건을 따져야지.<br/>

우선, 높이가 다른 빌딩들보다 작은, 혹은 높이가 1인 빌딩을 새로 놓는다고 할 때

- 가장 왼쪽에 놓으면 : 왼쪽에서 보이는 빌딩의 개수가 1 늘어난다.
- 가장 오른쪽에 놓으면 : 오른쪽에서 보이는 빌딩의 개수가 1 늘어난다.
- 중간에 놓으면 : 보이는 빌딩 개수에 변화가 없다.


이것을 점화식으로 나타낸 것이 아래의 식이다.
```
DP[i][j][k] = DP[i-1][j-1][k] + DP[i-1][j][k-1] + DP[i-1][j][k] * (N-2)
```

그리고 1000000007을 나눈 나머지를 출력하라고 했으므로<br/>

괄호를 씌우고 맨 뒤에 % MOD를 추가해준다.<br/>

이렇게 3중 for문을 돌려서 1 1 1 에서 시작해서 N L R이 될 때까지 돌려주면 된다!<br/>