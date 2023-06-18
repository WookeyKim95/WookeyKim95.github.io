---
layout: post
title:  "[백준] 1918_후위 표기식 C++"
subtitle:   
date: 2023-06-19 08:12:36 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1918_후위 표기식 C++<br/>
<br/>

스택 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <stack>
#include <string>

using namespace std;

int main()
{
    string s;
    cin >> s;

    stack<char> st;

    for (size_t i = 0; i < s.length(); ++i)
    {
        if (s[i] == '(')
            st.push(s[i]);
        
        else if (s[i] == '+' || s[i] == '-')
        {
            while (!st.empty() && st.top() != '(')
            {
                cout << st.top();
                st.pop();
            }
            st.push(s[i]);
        }
        else if (s[i] == '*' || s[i] == '/')
        {
            while (!st.empty() && st.top() != '(' && (st.top() == '*' || st.top() == '/'))
            {
                cout << st.top();
                st.pop();
            }
            st.push(s[i]);
        }
        else if (s[i] == ')')
        {
            while (!st.empty() && st.top() != '(')
            {
                cout << st.top();
                st.pop();
            }
            st.pop();
        }
        else if ('A' <= s[i] && s[i] <= 'Z')
            cout << s[i];
    }

    while (!st.empty())
    {
        cout << st.top();
        st.pop();
    }

    return 0;
}
```
<br/>

스택을 이용해서 식을 출력해보는 문제였다.<br/>

하지만 곱하기와 나누기는 더하기와 빼기에 비해서 우선 순위가 높고,<br/>

괄호가 나왔을 때에도 우선 순위를 부여하는 데 어려움을 겪었다.<br/>

솔직히.. 도저히 우선순위를 부여할 방법이 떠오르지 않아서<br/>

[참고링크](https://velog.io/@yeomja99/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EB%AC%B8%EC%A0%9C-%ED%92%80%EC%9D%B4%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%B0%B1%EC%A4%80-1918%EB%B2%88-%ED%9B%84%EC%9C%84-%ED%91%9C%EA%B8%B0%EC%8B%9D)를 참고해서 풀었다.<br/>

- A~Z일 경우 바로 출력
- 왼쪽 괄호라면 stack에 push
- 연산자라면 우선순위 고려

```
stack top: + or -, 현재 연산자: * or /
stack에 현재 연산자 push

stack top: + or -, 현재 연산자: + or -
stack이 빌 때까지, 현재 연산자 우선순위와 같거나 낮을 동안 출력후 현재 연산자 push

stack top: or /, 현재 연산자: or /
stack이 빌 때까지, 현재 연산자 우선순위와 같거나 낮을 동안 출력후 현재 연산자 push

stack top: * or /, 현재 연산자: + or -
stack이 빌 때까지, 현재 연산자 우선순위와 같거나 낮을 동안 출력후 현재 연산자 push
```

이를 그대로 구현하면 위의 코드와 같아진다.<br/>