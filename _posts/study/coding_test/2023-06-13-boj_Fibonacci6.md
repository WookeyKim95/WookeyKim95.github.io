---
layout: post
title:  "[백준] 11444_피보나치 수 6 C++"
subtitle:   
date: 2023-06-13 08:50:49 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11444_피보나치 수 6 C++<br/>
<br/>

행렬의 분할정복 거듭제곱 수를 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

#define MOD 1000000007

using namespace std;

typedef long long ll;
typedef vector<vector<ll>> mat;

ll N;

mat operator * (mat& a, mat& b)
{
    mat ret = { {0, 0},
                {0, 0} };

	for (int i = 0; i < 2; i++)
    {
		for (int j = 0; j < 2; j++)
        {
			for (int k = 0; k < 2; k++)
				ret[i][j] += a[i][k] * b[k][j];
            
            ret[i][j] %= MOD;
        }
	}

	return ret;
}

int main()
{
    cin >> N;

    mat ans = { {1, 0},
                {0, 1} };

    mat a = { {1, 1},
              {1, 0} };
    
    while (N > 0)
    {
        if (N % 2 == 1)
			ans = ans * a;
		a = a * a;
		N /= 2;
    }

    cout << ans[0][1];

    return 0;
}
```
<br/>

[풀이 참고링크](https://cocoon1787.tistory.com/349)<br/>

보통 같았으면 for문을 이용해서 피보나치수를 구했을 것이다.<br/>

하지만 이번에는 N이 조, 경 단위 까지가는 엄청나게 큰 수가 들어가기 때문에<br/>

long long 을 써야하면서, for문을 쓴다고 해도 무조건 시간초과가 날 수 밖에 없다.<br/>

참고링크를 참고해보니, 피보나치의 점화식을 행렬의 곱셈을 통해서 나타낸 것으로 풀어야 한다고 나왔다.<br/>

$$
\begin{pmatrix}
  F_{n+2} \\
  F_{n+1}
 \end{pmatrix}
 =
 \begin{pmatrix}
  1 & 1 \\
  1 & 0
 \end{pmatrix}
 \begin{pmatrix}
  F_{n+1} \\
  F_{n}
 \end{pmatrix}
 $$

이렇게 생각을 해야할지 몰랐다. 아직 많이 부족하다고 느껴졌다.<br/>

분할 제곱을 이용해서 푼다면 행렬을 많이 이용해봐야 겠다.<br/>

