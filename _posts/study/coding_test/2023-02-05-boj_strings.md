---
layout: post
title:  "[백준] 11478번_서로 다른 부분 문자열의 개수 C++"
subtitle:   
date: 2023-02-05 07:49:01 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11478번_서로 다른 부분 문자열의 개수 C++<br/>
<br/>

Set을 이용해서 푸는 문제<br/>

## 정답 제출코드<br/>
<br/>

```
#include <iostream>
#include <set>

using namespace std;
 
int main() {
    string s;
    cin >> s;
 
    set<string> strs;
 
    string tmp = "";
    for (int i = 0; i < s.size(); i++) {
        for (int j = i; j < s.size(); j++) {
            tmp += s[j];
            strs.insert(tmp);
        }
        tmp = "";
    }
 
    cout << strs.size();
}
```

<br/>

## 오답 제출코드(시간 초과)<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

vector<string> strs;
string s;
int count;

bool isTmp(string _in)
{
    int len = strs.size();
    for (int i = 0; i < len; ++i)
    {
        if (strs[i] == _in)
            return true;
    }
    return false;
}

int main()
{
    cin >> s;

    int len = 1;

    string save = s;

    while (len <= s.length())
    {
        s = save;
        for (int j = 0; j <= s.length() - len; ++j)
        {

            string tmp = "";
            
            for (int i = 0; i < len; ++i)
            {
                tmp += s[i];
            }

            if (!isTmp(tmp))
            {
                strs.push_back(tmp);
            }
            s.push_back(s[0]);
            s.erase(0, 1);
        }
        len++;
    }

    cout << strs.size();

    return 0;
}
```

스택, 큐와 같은 자료구조밖에 몰랐던 나는 그냥 큐처럼 문자열을 순환시키고<br/>

순환시킬 때마다 맨 앞에서 몇번째까지 문자열을 잘라서 벡터에 넣고<br/>

그 벡터의 길이를 구하는 아이디어를 사용하였다.<br/>

그런데 시간초과가 뜨니 당황스러워서 구글링을 해보니 Set이라는 자료구조가<br/>

C++에도 있더라..<br/>