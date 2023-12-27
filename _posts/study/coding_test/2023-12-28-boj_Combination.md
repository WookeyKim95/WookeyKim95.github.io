---
layout: post
title:  "[백준] 2407_조합 C++"
subtitle:   
date: 2023-12-28 08:19:15 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2407_조합 C++<br/>
<br/>

사실 C++유저 입장에선 살짝 어려웠던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

string arr[105][105];

string add(string num1, string num2)
{
    string num = "";
    int sum = 0;
    int size = max(num1.size(), num2.size());

    for(int i = 0; i < size || sum; ++i)
	{
        if (num1.size() > i)
			sum += num1[i] - '0';
        if (num2.size() > i)
			sum += num2[i] - '0';

        num += sum % 10 + '0';
        sum /= 10;
    }

    return num;
}

string combi(int n,int m)
{
    if (n == m || m == 0)
		return "1";

    string &ans = arr[n][m];

    if (ans != "")
		return ans;

    ans = add(combi(n - 1, m - 1), combi(n - 1, m));

    return ans;
}

int main()
{
    int n, m;

    cin >> n >> m;

    combi(n, m);

    for(int i = arr[n][m].size()-1; i >= 0; --i)
        cout << arr[n][m][i];

    return 0;
}
```

보통 파이썬 같으면 그냥 출력하면 되는 문제이긴 했는데<br/>

문제는 C++로 풀었기에 어려웠던 문제였던 것 같다.<br/>

왜냐하면 long long 범위마저 초과를 했기 때문이다.<br/>

다행히 출력은 문자, 숫자 안가리기 때문에 그냥 문자열로 출력해도 무방했던 것 같다.<br/>

참고로 이 문제는 [이분의 글](https://rujang.tistory.com/entry/%EB%B0%B1%EC%A4%80-2407%EB%B2%88-%EC%A1%B0%ED%95%A9-CC)을 참고해서 풀었다.<br/>

재귀함수를 이용해서 구현하였다.<br/>
<br/>

