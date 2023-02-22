---
layout: post
title:  "[백준] 1343번_폴리오미노 C++"
subtitle:   
date: 2023-02-22 09:29:13 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1343번_폴리오미노 C++<br/>
<br/>

그리디 알고리즘을 활용하는 문제<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string s;
    cin >> s;

    int X_count = 0;
    int len = s.length();

    // X의 개수부터 먼저 확인
    for (int i = 0; i < len; ++i)
    {
        if (s[i] == 'X')
            X_count++;
    }
    
    // X의 개수가 홀수라면 모두 덮을 수 없음.
    if (X_count % 2 != 0)
    {
        cout << -1;
        return 0;
    }
    
    // 재사용을 위해 초기화
    X_count = 0;

    string output = "";
    
    for (int i = 0; i < len; ++i)
    {
        if (s[i] == 'X')
            X_count++;
        
        // X가 2개 누적되고 다음이 . 일경우 BB로 변환
        if (i < len-1 && s[i+1] == '.' && X_count == 2)
        {
            output += "BB";
            X_count = 0;
        }

        // 맨 끝에 대한 예외처리 BB 쌓아주기
        if (i == len-1 && X_count == 2)
        {
            output += "BB";
        }
        
        if (s[i] == '.')
        {
            if (X_count % 2 != 0)
            {
                cout << -1;
                return 0;
            }
            else
                output += ".";
        }
            
        // X가 4개 쌓였을 경우 AAAA로 변환
        if (X_count == 4)
        {
            output += "AAAA";
            X_count = 0;
        }
    }

    cout << output;

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

int main()
{
    string s;
    cin >> s;

    int X_count = 0;
    int len = s.length();

    // X의 개수부터 먼저 확인
    for (int i = 0; i < len; ++i)
    {
        if (s[i] == 'X')
            X_count++;
    }
    
    // X의 개수가 홀수라면 모두 덮을 수 없음.
    if (X_count % 2 != 0)
    {
        cout << -1;
        return 0;
    }
    
    // 재사용을 위해 초기화
    X_count = 0;

    string output = "";
    
    for (int i = 0; i < len; ++i)
    {
        if (s[i] == 'X')
            X_count++;
        
        // X가 2개 누적되고 다음이 . 일경우 BB로 변환
        if (i < len-1 && s[i+1] == '.' && X_count == 2)
        {
            output += "BB";
            X_count = 0;
        }

        // 맨 끝에 대한 예외처리 BB 쌓아주기
        if (i == len-1 && X_count == 2)
        {
            output += "BB";
        }
        
        if (s[i] == '.')
            output += ".";
        
        // X가 4개 쌓였을 경우 AAAA로 변환
        if (X_count == 4)
        {
            output += "AAAA";
            X_count = 0;
        }
    }

    cout << output;

    return 0;
}

```

예외 사항을 처리해주지 않았다.<br/>

X의 개수만 고려하고, ..XXX.X.. 혹은 ..X..X.. 등<br/>

X가 홀로 있을 경우를 처리해주지 않아서 오답이 발생하였다.<br/>

그래서 그에대한 예외처리를 아래와 같은 코드에서 진행하였다.<br/>

```
if (s[i] == '.')
{
    // ..X..X.. ..XX..XXX..과 같이 X가 홀수개만 쌓였는데 .이 나올 경우 모두 덮을 수 없다.
    if (X_count % 2 != 0)
    {
        cout << -1;
        return 0;
    }
    else
        output += ".";
}
```

위의 사항을 처리해주니 정답처리가 되었다.