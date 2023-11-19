---
layout: post
title:  "[백준] 15482_한글 LCS C++"
subtitle:   
date: 2023-11-12 07:28:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 15482_한글 LCS C++<br/>
<br/>

투 포인터를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>

#define MAX 3003

using namespace std;

int DP[MAX][MAX];

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

    string A, B;
    cin >> A;
    cin >> B;

    for (size_t i = 3; i <= A.length(); i += 3)
    {
        for (size_t j = 3; j <= B.length(); j += 3)
        {
            if (A.substr(i-3, 3)==B.substr(j-3, 3))
                DP[i][j] = DP[i-3][j-3] + 1;
            else
                DP[i][j] = m_max(DP[i-3][j], DP[i][j-3]);
        }
    }

    cout << DP[A.length()][B.length()];

    return 0;
}
```
<br/>


## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>

using namespace std;

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
    
    int DP[1001][1001];

    string A, B;
    cin >> A;
    cin >> B;

    DP[0][0] = 0;

    for (size_t i = 0; i < A.length(); ++i)
    {
        for (size_t j = 0; j < B.length(); ++j)
        {
            if (A[i] == B[j])
                DP[i][j] = DP[i-1][j-1] + 1;
            else
                DP[i][j] = m_max(DP[i-1][j], DP[i][j-1]);
        }
    }

    cout << DP[A.length()-1][B.length()-1];

    return 0;
}
```

솔직히 한글이라고 해도 LCS인데 다르겠어? 생각했는데<br/>

어.. 다르네.. 틀렸다네..<br/>

내가 모르고 있는 지식이 있나보다 생각이 들어서 구글링을 해보았다.<br/>

아하! utf-8로 인코딩한 한글은 3바이트씩 차지하고 있기 때문에 3칸씩 띄어서 비교를 해야하고,<br/>

비교 방식도 substr을 사용해서 3글자씩 비교를 해주어야 하는 것이다.<br/>

아무래도 추측인데 '백' 이라는 글자를 문자열로 해서 'ㅂㅐㄱ' 이렇게 되어있다보니 그런 것 같다.<br/>

3칸을 비교, 3칸씩 증가로 바꾸어주니 정답처리 되었다.<br/>