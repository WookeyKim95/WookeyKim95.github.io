---
layout: post
title:  "[백준] 2866_문자열 잘라내기 C++"
subtitle:   
date: 2023-08-07 07:35:25 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2866_문자열 잘라내기 C++<br/>
<br/>

Set을 활용해보는 문자열 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <string>
#include <set>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    string arr[1001];
    string str[1001];
    int R, C;

    cin >> R >> C;

    for (int i = 0; i < R; ++i)
        cin >> arr[i];
    
    for (int i = 0; i < C; ++i)
    {
        string s = "";

        for (int j = 0; j < R; ++j)
            s += arr[j][i];
        
        str[i] = s;
    }

    int count = 0;
    for (int i = 0; i < R; ++i)
    {
        set<string> s;
        set<string>::iterator iter;
        for (int j = 0; j < C; ++j)
        {
            str[j].erase(str[j].begin());

            iter = s.find(str[j]);

            if (iter == s.end())
                s.insert(str[j]);
            else
            {
                cout << count;
                return 0;
            }
        }
        count++;
    }

    cout << R-1;

    return 0;
}
```

중복 여부를 따질만한 데는 set만한 자료구조가 없지.<br/>

입력을 받을 배열을 하나 만들어주고,<br/>

열 방향 문자열을 받을 배열 str을 하나 더 만들어 주었다.<br/>

그리고 행 하나씩 과정을 진행하였는데,<br/>

내가 했던 실수가 있다.<br/>

맨 위의 행을 지우고 나서 중복여부를 따져야하는데, 중복 여부를 따지고 나서 지우는 행위를 해서<br/>

값이 다르게 나오는 오류가 발생하였다.<br/>

이래서 문제를 잘 읽어야 한다.<br/>

중복 여부를 따질 때에는 set을 활용하였다.<br/>

```
str[j].erase(str[j].begin());

iter = s.find(str[j]);

if (iter == s.end())
    s.insert(str[j]);
else
{
    cout << count;
    return 0;
}
```

그리고 만일 행 하나가 남을 때 까지 중복이 없는 경우를 대비해서<br/>

count가 그래도 출력 될 수 있도록 맨 아래에 아래와 같은 코드를 넣어주었다.<br/>

```
cout << R-1;
```
즉, 예외처리인 것이다.<br/>

처음엔 쫄았지만 그래도 생각보단 무난하게 풀 수 있는 문제였다.<br/>