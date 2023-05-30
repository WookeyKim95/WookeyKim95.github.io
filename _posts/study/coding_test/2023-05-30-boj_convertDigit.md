---
layout: post
title:  "[백준] 1112_진법 변환 C++"
subtitle:   
date: 2023-05-30 08:11:12 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1112_진법 변환 C++<br/>
<br/>

진법변환을 구현하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int x, b;
vector<int> v;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> x >> b;

    bool flag = (b < 0 || (b >= 0 && x >= 0));

    if (!flag)
        x *= -1;
    
    while (x)
    {
        int q = x / b;
        int r = x % b;
        if (r < 0)
        {
            q += 1;
            r -= b;
        }
        v.push_back(r);
        x = q;
    }

    if (v.size() > 0)
    {
        if (!flag)
            cout << '-';
        
        for (int i = v.size() - 1; i >= 0; --i)
            cout << v[i];
    }
    else
        cout << 0;
    

    return 0;
}
```
<br/>

오랜만에 보는 수학 빡구현 문제였다.<br/>

헷갈리거나 모르는 부분은 [이분의 글](https://latte-is-horse.tistory.com/361)을 보면서 풀었다.<br/>

우선, 마지막에 부호를 붙일지 말지를 결정하기 위해서 flag를 설정해주었고,<br/>

그리고 진수와 본래의 수가 음수로 주어진다고 해도 원활하게 진법변환을 하기 위해<br/>

조건을 만족한다면 양수라는 flag = true로 설정되도록 구현하였다.<br/>

```
bool flag = (b < 0 || (b >= 0 && x >= 0));
```

그리고 나누어서 나오는 나머지들을 차례로 벡터에 저장하고,<br/>

저장된 벡터를 역순으로 출력하여 진법 변환이 완료되도록 하였다.<br/>