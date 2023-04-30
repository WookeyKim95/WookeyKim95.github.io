---
layout: post
title:  "[백준] 1563_개근상 C++"
subtitle:   
date: 2023-04-30 09:27:20 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1563_개근상 C++<br/>
<br/>

수학적 규칙을 찾는 다이나믹 프로그래밍 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>

#define MOD 1000000

using namespace std;

int N;
int dp[1001][3][4];

int func(int day, int c1, int c2)
{
    if (c1 >= 2 || c2 >= 3)
        return 0;
 
    if (day == N)
        return 1;
 
    int& ref = dp[day][c1][c2];
    if (ref != -1)
        return ref;
 
    return ref = 
        (func(day + 1, c1, 0) + 
        func(day + 1, c1 + 1, 0) + 
        func(day + 1, c1, c2 + 1)) % MOD;
}
 
int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    memset(dp, -1, sizeof(dp));

    cout << func(0, 0, 0);

    return 0;
}
```

구글링을 하여 [참고링크](https://js1jj2sk3.tistory.com/44)의 풀이를 따라 코딩하였다.<br/>

1000000으로 나눈 나머지를 출력하라고 해서 점화식이 있는 문제라고 생각했지만,<br/>

그렇지 않았다.<br/>

3차원 배열을 정의해서 날짜를 계속 늘려가면서 풀이해야하는 다이나믹 프로그래밍 문제였다.<br/>

골드 문제가 되니까 조건에 맞게 배열을 정의하는 다이나믹 프로그래밍 문제가 많이 보이는 것 같다.<br/>

그리고 시간 초과가 날까봐 재귀함수를 이용하지 않는 방법을 선택했었는데, 풀이 코드에는 재귀함수를 이용하는 방법을 채택하였다.<br/>

풀이를 이해하길 배열은 다음과 같이 정의되었다.<br/>

DP[지금까지 출결한 날][지금까지 지각한 횟수][지금까지 연속으로 결석한 횟수] = DP[day][cnt1][cnt2]<br/>

그리고 함수를 리턴할 기저는

- day == N (1을 반환)
- 지각횟수가 2 이상 (0을 반환)
- 연속 결석횟수가 3 이상 (0을 반환)

일 것이다.<br/>

이를 잘 활용해서 코딩을 하면 위와 같은 코드가 나온다.<br/>

아참, 재귀함수를 이용하는 코드를 보니 &기호를 사용하는 경우가 많이 보인다.<br/>

함수를 호출하며 변수를 복사하는 것이 아니라 어떤 주소에 있는 변수를 참조하는 것이기 때문에 시간이 훨씬 덜 오래 걸린다.<br/>

앞으로는 이를 잘 활용할 수 있도록 생각해야겠다.<br/>

<br/>

## 오답제출코드<br>
<br/>

```
#include <iostream>

#define MOD 1000000

using namespace std;

int dp[1001];

int main()
{
    int N;
    cin >> N;

    dp[1] = 3; // 3
    dp[2] = 9 - 1; // 8
    dp[3] = 27 - 7 - 1; // 19

    for (int i = 4; i <= N; ++i)
        dp[i] = (3 * dp[i-1]) % MOD - (2 * dp[i-2]) % MOD + i-2;

    cout << dp[N] % MOD;

    return 0;
}
```

1000000을 나눈 나머지를 출력하라는 문구를 보고 경우의 수 사이에 관계를 잇는 점화식이 있을거라 생각했다.<br/>

그래서 우선 학기가 1일, 2일, 3일, 4일 일 경우의 경우를 모두 따져보았다.<br/>

- 1일일 경우 : A, L, O 모두 개근상을 받을 수 있으므로 3가지
- 2일일 경우 : 전체 9가지 중 LL만 개근상을 받지 못하므로 9 - 1 = 8로 8가지
- 3일일 경우 : 전체 27가지 중 AAA, LLO, LLA, LLL, LOL, LAL, OLL, ALL만 개근상을 받지 못하므로 27 - 8 = 19가지
- 4일일 경우 (43가지)<br/>
전체 81가지 중에서 지각 2회 이상으로 개근상을 받지 못하는 경우는 아래와 같다.<br/>
LLOO, LLOA, LLOL, LLAO, LLAA, LLAL, LLLO, LLLA, LLLL, LOLO, LOLA, LOLL, LALO, LALA, LALL, LOAL, LOOL, LAOL, LAAL, OLLO, OLLA, OLLL, OLAL, OLOL, OOLL, OALL, ALLO, ALLA, ALLL, ALAL, ALOL, AALL, AOLL
<br/>
<br/>
그리고 연속 3회 이상 결석으로 개근상을 받지 못하는 경우는 아래와 같다.<br/>
AAAL, AAAO, AAAA, LAAA, OAAA

3 - 8 - 19 - 43을 이어보니 아래와 같은 점화식을 발견하였다.<br/>

- An = 3 * An-1 - 2 * An-2 + n - 2

그래서 dp를 구하는 과정에 적용하였다.<br/>

하지만 결과는 틀렸습니다! 규칙을 찾는데 실패했던 모양이다.<br/>

그래서 구글링을 해보기로 했다.<br/>