---
layout: post
title:  "[프로그래머스] 모음사전 C++"
subtitle:   
date: 2023-01-10 14:34:53 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 모음사전 C++<br/>
<br/>

완전 탐색 문제
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>
#include <vector>
#include <iostream>

using namespace std;

int counter = 0;
string arr[5] = {"A", "E", "I", "O", "U"};
bool flag = true;

void backtrack(string s, string word)
{
    if (s.length() == 6)
        return;
    
    if (flag)
        counter++;
    
    if (s == word)
    {
        flag = false;
        return;
    }
    
    for (int i = 0; i < 5; ++i)
    {
        s += arr[i];
        backtrack(s, word);
        s.pop_back();
    }
}

int solution(string word) {
    for (int i = 0; i < 5; ++i)
    {
        backtrack(arr[i], word);
    }
    int answer = counter;
    return answer;
}
```

백트래킹을 사용하여 문제를 풀었다.<br/>

단, 기존에 사용하던 백트래킹과의 차이는 중복된 단어가 들어가야 했기에 visited를 사용하지 않았다는 점이다.<br/>