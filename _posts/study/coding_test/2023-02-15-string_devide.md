---
layout: post
title:  "[프로그래머스] 문자열 나누기 C++"
subtitle:   
date: 2023-02-15 10:33:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 문자열 나누기 C++<br/>
<br/>

전형적인 문자열 문제이지만 간단한 테스트케이스 하나가 통과가 안됐던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>
#include <vector>

using namespace std;

int solution(string s) {
    int answer = 0;
    
    int len = s.length();

    // 문자열의 길이가 1일 경우 1 리턴
    if (len == 1)
        return 1;
    
    int counter_same = 1, counter_diff = 0;
    
    char x = s[0];
    
    for (int i = 1; i < len; ++i)
    {
        if (x == s[i])
            counter_same++;
        else
            counter_diff++;
        
        // 두 횟수가 같아지거나 마지막 글자일 경우 분리(answer + 1)
        if (counter_diff == counter_same || i == len-1)
        {
            // 문자열 분리횟수 증가
            answer++;
            
            // 초기화
            counter_diff = 0;
            counter_same = 0;
            
            // x를 새로 지정
            if (i != len-1)
                x = s[i+1];
        }
    }
    
    return answer;
}
```

## 오답제출코드<br/>
<br/>

```
#include <string>
#include <vector>

using namespace std;

int solution(string s) {
    int answer = 0;
    
    int len = s.length();

    int counter_same = 1, counter_diff = 0;
    
    char x = s[0];
    
    for (int i = 1; i < len; ++i)
    {
        if (x == s[i])
            counter_same++;
        else
            counter_diff++;
        
        // 두 횟수가 같아지거나 마지막 글자일 경우 분리(answer + 1)
        if (counter_diff == counter_same || i == len-1)
        {
            // 문자열 분리횟수 증가 및 x를 새로 지정
            answer++;
            
            counter_diff = 0;
            counter_same = 0;
            
            if (i != len-1)
                x = s[i+1];
        }
    }
    
    return answer;
}
```

이 오답 코드로 제출을 하니 42개의 테스트케이스 중에서 31번 테스트케이스 하나만 실패로 떴다.<br/>

왜지? 생각을 하다가 경계값 분석을 위해서 s = "a"를 넣어보았다.<br/>

이런! 기댓값은 1인데 반환값이 0이다!<br/>

문자열이 하나이기 때문에 1이 반환되어야 하는데 나누는 과정을 진행하지 않아서 0이 반환되었던 것이다.<br/>

문자열의 길이가 1인 경우가 테스트 케이스 31번 이었나보다.<br/>

그래서 문자열의 길이가 1인 경우를 예외처리 하니 정답처리되었다!<br/>
