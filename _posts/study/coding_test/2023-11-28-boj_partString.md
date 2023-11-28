---
layout: post
title:  "[백준] 6550_부분 문자열 C++"
subtitle:   
date: 2023-11-28 07:15:08 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 6550_부분 문자열 C++<br/>
<br/>

문자열 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>
#include <iostream>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    string s, t;


    while (cin >> s >> t)
    {
        int idx = 0;
        bool flag = false;
        for (size_t i = 0; i < t.length(); ++i)
        {
            if (s[idx] == t[i])
            {
                idx++;
                if (idx == s.length())
                {
                    cout << "Yes\n";
                    flag = true;
                    break;
                }
            }
        }
        if (!flag)
            cout << "No\n";
    }

    

    return 0;
}
```

쉬어가는 느낌으로 풀어본 문자열 문제.<br/>

원리는 간단하다.<br/>

idx = 0부터 시작해서 부분문자열 한 글자 한 글자가 순서대로 모두 문자열 안에 있는지<br/>

살펴봐주면 된다. 이렇게 하면 시간복잡도도 O(N)이니까 괜찮을 것이라 생각한다.<br/>

모두 들어있다면 idx 값이 부분 문자열의 길이가 될 것이고 이때 Yes를 출력하면 된다.<br/>

idx값이 부분 문자열 길이보다 작은데 for문이 끝나면 No를 출력하고.<br/>