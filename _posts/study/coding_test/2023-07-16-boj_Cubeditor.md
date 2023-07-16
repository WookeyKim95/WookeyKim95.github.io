---
layout: post
title:  "[백준] 1701_Cubeditor C++"
subtitle:   
date: 2023-07-16 07:37:32 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1701_Cubeditor C++<br/>
<br/>

KMP를 활용해야하는 문자열 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

string substring(int a, string s)
{
    int len = s.length();
    string ret = "";
    for (int i = a; i < len; ++i)
        ret += s[i];
    
    return ret;
}

int GetMax(string a)
{
    int N = a.length(), ret = 0, j = 0;
    vector<int> Fail(N);

    for (int i = 1; i < N; ++i)
    {
        while (j > 0 && a[i] != a[j])
            j = Fail[j-1];
        
        if (a[i] == a[j])
            ret = m_max(ret, Fail[i] = ++j);
            
    }
    return ret;
}   

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    string s;
    cin >> s;

    int Max = 0;
    string PutString;

    for (size_t i = 0; i < s.length(); ++i)
    {
        PutString = substring(i, s);
        Max = m_max(Max, GetMax(PutString));
    }

    cout << Max;

    return 0;
}
```

아쉽게도 창의력이 부족해서 [참고링크](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=occidere&logNo=221184825080)를 참고하여 풀었다.<br/>

<br/>

이해한 바를 작성해보자.<br/>

필자는 KMP가 무엇인지 솔직히 몰랐다.<br/>

조사를 해보니, 문자열 검색 알고리즘의 일종으로,<br/>

i와 j를 한칸씩 전진시키면서 인덱스 i와 인덱스 j에 있는 문자를 비교한다.<br/>

- 매치 되었을 경우 : 한칸씩 전진한 뒤, j 위치에 i를 저장한다.
- 매치되지 않았을 경우 : i는 상태전이함수에 있는 값으로 전환시킨다. 그리고 이전 과정으로 돌아간다.

<br/>

이 과정을 j가 문자열 길이 N보다 커질 떄 까지 반복시킨다.<br/>

이 KMP 알고리즘에서 Fail을 했을 경우를 응용해서 푸는 것이었다.<br/>



사실 완벽하게 이해를 하지는 못한 것 같지만<br/>

그래도 문자열을 어떻게 검색하는지 검색 알고리즘이 궁금했는데<br/>

KMP를 처음 접해서 흥미를 가질 수 있었던 것 같다.<br/>

문자열 관련해서는 조금 더 공부를 진행해야겠다.<br/>