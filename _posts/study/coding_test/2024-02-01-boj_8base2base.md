---
layout: post
title:  "[백준] 1212_8진수 2진수 C++"
subtitle:   
date: 2024-02-01 07:00:24 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1212_8진수 2진수 C++<br/>
<br/>

잊고 있었던 메서드를 상기하게 된 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <string>

using namespace std;

int main()
{
	string eight;
	string base[8] = { "000", "001", "010", "011", "100", "101", "110", "111" };
	int temp;

	cin >> eight;

	for (size_t i = 0; i < eight.length(); i++)
    {
		temp = (int)(eight[i] - '0');
		if (i == 0)
            cout << stoi(base[temp]);
		else
            cout << base[temp];
	}
}
```

<br/>

브론즈 문제로 string의 메서드만 알고 있었으면 쉬운문제였다.<br/>

사실 이 문제를 풀 때 base를 구성하는 것은 알고 있었는데 stack을 써야하는지 고민을 많이 했다.<br/>

왜냐하면 맨 앞에서는 반드시 1로 시작해야 했기 때문에 이 숫자가 맨 앞의 숫자인지 어떻게 구분하지?<br/>

고민을 많이 했는데, 문자열을 int로 바꿔주는 stoi를 사용하면 해결될 문제였다.<br/>

오랜 시간동안 쓰질 않으니 결국 까먹어버리는 것 같다.<br/>

메서드를 상기할 겸 이런 문제들도 많이 풀어봐야겠다.<br/>