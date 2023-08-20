---
layout: post
title:  "[백준] 1562_계단 수 C++"
subtitle:   
date: 2023-08-20 07:31:07 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1562_계단 수 C++<br/>
<br/>

비트마스킹과 다이나믹 프로그래밍을 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

#define mod 1000000000

int N, ans = 0;
int dp[101][10][1<<10];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    int Full = (1 << 10) - 1;

    for (int j = 1; j <= 9; ++j)
        dp[1][j][1 << j] = 1;
 
    for (int i = 2; i <= N; ++i) 
    {
        for (int j = 0; j <= 9; ++j)
        {
            for (int k = 0; k <= Full; ++k)
            {
                if (j == 0)
                    dp[i][0][k | (1 << 0)] = (dp[i][0][k | (1 << 0)] + dp[i - 1][1][k]) % mod;
                else if (j == 9)
                    dp[i][9][k | (1 << 9)] = (dp[i][9][k | (1 << 9)] + dp[i - 1][8][k]) % mod;
                else
                {
                    dp[i][j][k | (1 << j)] = (dp[i][j][k | (1 << j)] + dp[i - 1][j - 1][k]) % mod;
                    dp[i][j][k | (1 << j)] = (dp[i][j][k | (1 << j)] + dp[i - 1][j + 1][k]) % mod;
                }
            }
        }
    }
 
    for (int j = 0; j <= 9; ++j)
        ans = (ans + dp[N][j][Full]) % mod;

    cout << ans;

    return 0;
}
```

문제를 읽어보면 뭔가 DP느낌이 많이 난다.<br/>

숫자를 한 자리씩 추가해가면서, 예를 들어..

```
123 1232 1234 12323 12343..
```

조사를 해보면 좋을 것 같기는 했는데<br/>

이게 몇 번째인지를 기록하는 방법이 떠오르지가 않았다.<br/>

구글링을 해보면서 이번에는 [이분의 글](https://melonicedlatte.com/algorithm/2018/03/15/160637.html)을 보게 되었다.<br/>

이번 문제는 풀어보기 보다는 분석을 하는것에 의의를 둬보려고 한다.<br/>

우선 dp 배열의 선언 형식.<br/>

```
int dp[101][10][1 << 10]
```

일단 첫번째 칸은 높이 즉 N, 두번째 칸은 현재 자리수의 숫자, 세번째 칸은 사용한 숫자를 체크한다.<br/>

```
int Full = 1 << 10 - 1 
```
이 숫자는 10진법으로 하면 1023, 2진법으로 하면 111111111 이다.<br/>

즉 1부터 9까지 모든 수에 비트가 1로 되어있는 것이다.<br/>

그리고 dp 배열 초기화 과정.<br/>

```
for (int j = 1; j <= 9; ++j)
        dp[1][j][1 << j] = 1;
```

여기서 j = 1부터 하는 이유는 첫번째 자리 숫자가 0이 될 수 없기 때문에 0은 제외한다.<br/>

그리고 다음 단계는 탐색이다.<br/>

```
for (int i = 2; i <= N; ++i) 
{
    for (int j = 0; j <= 9; ++j)
    {
        for (int k = 0; k <= Full; ++k)
        {
            if (j == 0)
                dp[i][0][k | (1 << 0)] = (dp[i][0][k | (1 << 0)] + dp[i - 1][1][k]) % mod;
            else if (j == 9)
                dp[i][9][k | (1 << 9)] = (dp[i][9][k | (1 << 9)] + dp[i - 1][8][k]) % mod;
            else
            {
                dp[i][j][k | (1 << j)] = (dp[i][j][k | (1 << j)] + dp[i - 1][j - 1][k]) % mod;
                dp[i][j][k | (1 << j)] = (dp[i][j][k | (1 << j)] + dp[i - 1][j + 1][k]) % mod;
            }
        }
    }
}
```
for문을 돌면서 이전 단계의 높이에 있는 숫자를 기준으로 체크를 해나간다.<br/>

0과 9일 때는 각각 1과 8일 보면 되고, 그렇지 않은 경우에는 두번을 수행한다.<br/>

그리고 마지막으로 정답은 0부터 9까지 모두 등장했을 경우를 원하니<br/>

```
for (int j = 0; j <= 9; ++j)
    ans = (ans + dp[N][j][Full]) % mod;
```

첫번째 칸이 N이면서 세번째칸이 Full인 값들에 해당하는 경우를 모두 가져온다.<br/>

아직 이해하려면 더 탐구가 필요하고 꼭 다시한번 풀어봐야할 것 같은 문제이다.<br/>

나중에 DP를 다시 배우게 되는데 그 수업 때 집중해서 보아야겠다.<br/>