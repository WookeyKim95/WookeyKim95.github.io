---
layout: post
title:  "[백준] 9465_스티커 C++"
subtitle:   
date: 2023-06-23 08:11:04 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 9465_스티커 C++<br/>
<br/>

DP를 사용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int T, N;
int Sticker[3][100001];
int DP[3][100001];

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int main()
{
    cin >> T;

    for (int i = 0; i < T; ++i)
    {
        cin >> N;

        for (int j = 1; j <= 2; ++j)
        {
            for (int k = 1; k <= N; ++k)
                cin >> Sticker[j][k];
        }

        DP[1][0] = 0;
        DP[2][0] = 0;

        DP[1][1] = Sticker[1][1];
        DP[2][1] = Sticker[2][1];

		for (int j = 2; j <= N; ++j)
        {
			DP[1][j] = m_max(DP[2][j-1], DP[2][j-2]) + Sticker[1][j];
			DP[2][j] = m_max(DP[1][j-1], DP[1][j-2]) + Sticker[2][j];
		}
		
        cout << m_max(DP[1][N], DP[2][N]);
    }

    

    return 0;
}
```
<br/>

왼쪽에서부터 떼어내야 최대한 많은 스티커를 떼어 점수를 크게 얻을 수 있다고 생각했다.<br/>

이 부분에서 다이나믹 프로그래밍이라고 생각하였다.<br/>

처음에는 각 스티커를 사용할 수 있는가 여부를 나타내는 변수도 넣으려고 했다.<br/>

하지만 그럴 필요가 없었던게 오른쪽으로 나아가면서 어떤 스티커의 상하좌우를 사용할 수 없게 되면<br/>

다음으로 뗄 수 있는 스티커는 2칸 오른쪽에 있는 스티커 혹은 자신의 오른쪽 대각선 방향으로 있는 스티커였다.<br/>

이 부분은 [참고링크](https://m.blog.naver.com/occidere/220786307316)를 통해서 알 수 있었다.<br/>

즉, 어떤 스티커를 떼어야 더 많은 점수를 얻을 수 있을까를 판별하는 데에서 다이나믹 프로그래밍이 필요한 것이다.<br/>

이번 다이나믹 프로그래밍은<br/>

1칸 떨어지고 대각선으로 마주보는 DP값 vs 2칸 떨어지고 대각선으로 마주보는 DP값<br/>

의 구도로 설계를 해주어야 했다.<br/>

```
for (int j = 2; j <= N; ++j)
{
    DP[1][j] = m_max(DP[2][j-1], DP[2][j-2]) + Sticker[1][j];
    DP[2][j] = m_max(DP[1][j-1], DP[1][j-2]) + Sticker[2][j];
}
```

이렇게 하면 완성.