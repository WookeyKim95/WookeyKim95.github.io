---
layout: post
title:  "[백준] 15486_퇴사 2 C++"
subtitle:   
date: 2023-10-16 07:11:19 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 15486_퇴사 2 C++<br/>
<br/>

더 많은 범위에서 다이나믹 프로그래밍을 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>

#define First 0
#define MAX 1500010

using namespace std;

int N;
int DP[MAX] = { 0 };
int T[MAX] = { 0 };
int P[MAX] = { 0 };

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

	cin >> N;
	memset(DP, 0, sizeof(DP));

	for (int i = 1; i <= N; ++i)
		cin >> T[i] >> P[i];

	for (int i = N; i > 0; --i)
	{
		if (T[i] + i > N + 1)
            DP[i] = DP[i + 1];
			
		else
			DP[i] = m_max(P[i] + DP[i + T[i]], DP[i + 1]);
	}

	cout << DP[1];

	return 0;
}
```

기존 실버 3에 있던 [퇴사](https://www.acmicpc.net/problem/14501) 문제랑 무슨 차이일까?<br/>

바로 범위가 많이 늘어났다는 것이다.<br/>

기존의 문제는 N의 최대값이 15였다. 하지만 이번에는 1500000이며, 이에 따라서 T값도 많이 늘어났다.<br/>

근본적인 원리는 같으면서, 범위가 많이 늘어나서 대용량의 데이터를 처리해야할 경우,<br/>

기존의 퇴사 코드를 어떻게 고쳐야할지 생각을 해보자.<br/>

```
#include <iostream>
#include <cstring>

#define First 0

using namespace std;

int N;
int DP[17] = { 0 };
int T[17] = { 0 };
int P[17] = { 0 };

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

	cin >> N;
	memset(DP, 0, sizeof(DP));

	for (int i = 1; i <= N; ++i)
		cin >> T[i] >> P[i];

	for (int i = N; i > 0; --i)
	{
		if (T[i] + i > N + 1)
            DP[i] = DP[i + 1];
			
		else
			DP[i] = m_max(P[i] + DP[i + T[i]], DP[i + 1]);
	}

	cout << DP[1];

	return 0;
}
```

역시, 단순하게 17을 1500001로 고치는 작업만으로는 해결할 수 없었다.<br/>

살펴보자. N이 1500000이면, T가 모두 1이면, P가 모두 1000이면 어떤 문제가 발생하게 될까?<br/>

이때가 아마 벌어들일 수 있는 수익의 최대 값일 것이며, 그 값은 <br/>

1,500,000 * 1000 = 1,500,000,000 으로 15억이다. 일단은 int 범위 내이다.<br/>

그리도 단순하게 17을 1500001로 고치는 작업만 했을 때에는<br/>

시간초과나 메모리 초과를 예상했는데 틀렸습니다 만 출력되었다.<br/>

<br/>

그런데 황당한 일이 벌어졌다.<br/>

위에서 1500001로 고치는 작업을 했다고 했지 않았는가?<br/>

그런데 이번에는 1500010으로 고쳐서 다시 그냥 제출해보았는데 맞았다고 처리되었다.<br/>

엥???????????<br/>

```
memset(DP, 0, sizeof(DP));

...

if (T[i] + i > N + 1)
    DP[i] = DP[i + 1];
```

곰곰히 생각해보니 위 부분 때문이 아닐까 생각이 든다.<br/>

주요한 차이점이라면 1500001은 150만 + 1칸 만큼을 0으로 초기화한다.<br/>

하지만 1500010은 150만 + 10만 만큼을 0으로 초기화한다.<br/>

이 때 150만의 값이 들어왔을 때 차이를 생각해보자.<br/>

index를 세는 방법에 의해서 N이 1500000이면<br/>

DP[1500002], 즉 DP의 시작점으로부터 150만 2번째 칸 뒤를 참조한다.<br/>

그런데 1500002 지점은 1500001일때에는 0이 아니다. 초기화 범위에 들어가지 않았으니까.<br/>

하지만 1500010일 때에는 0이다. 초기화 범위 안에 있으니까.<br/>

자 그럼 1500002로 초기화 해주면?<br/>

역시 맞았습니다가 떴다.<br/>

여기서 배운 점 : 0으로 초기화 할 일이 생기면 범위는 넉넉하게 해주자.<br/>

엥? 결국 그러면 범위가 어떻던 간에 로직은 완전히 똑같았단 것 아닌가. 이런.