---
layout: post
title:  "[백준] 17298_오큰수 C++"
subtitle:   
date: 2023-12-13 06:47:34 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 17298_오큰수 C++<br/>
<br/>

스택을 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <stack>

#define MAX 1000001

using namespace std;

int N;
stack<int> st;
int ans[MAX];
int seq[MAX];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 1; i <= N; ++i)
        cin >> seq[i];

    for (int i = N; i >= 1; --i)
	{
		while (!st.empty() && st.top() <= seq[i])
			st.pop();

		if (st.empty())
            ans[i] = -1;
		else
            ans[i] = st.top();

		st.push(seq[i]);
	}
	
	for (int i = 1; i <= N; i++)
		cout << ans[i] << ' ';

    return 0;
}
```

[참고링크](https://cocoon1787.tistory.com/347)

참고링크에 따르면 핵심 아이디어는 스택에 오름차순으로 쌓아놓는 것인 것 같다.<br/>

그래서 코드를 잘 보면 i = 0이 아니라 i = N 부터 해서 거꾸로 진행하고 있다.<br/>

그렇게 해야 오른쪽에 있는 큰 수를 구할 수 있기 때문이다.<br/>

코드를 보면 어려워 보이지 않지만 뭔가 아이디어를 생각해내는 것은 어려웠던 문제이다.<br/>

그래서 이번 문제도 구글링을 통해서 참고링크를 찾아내었다.<br/>

완벽히 이해하는 데는 좀 더 시간이 걸릴 것 같다.<br/>