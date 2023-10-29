---
layout: post
title:  "[백준] 4949_균형잡힌 세상 C++"
subtitle:   
date: 2023-10-29 04:44:15 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 4949_균형잡힌 세상 C++<br/>
<br/>

쉬어가는 느낌으로 풀어보는 스택 활용 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <stack>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    string s;

    while (true)
    {
        getline(cin, s);
        stack<char> st;

		if (s[0] == '.')
            break;

		for (size_t i = 0; i < s.length() - 1; ++i)
		{
			if (s[i] == '(')
                st.push('(');
			if (s[i] == '[')
                st.push('[');
			if (s[i] == ']')
			{
				if (!st.empty() && st.top() == '[')
                    st.pop();
				else
                {
                    cout << "no\n";
                    break;
                }
			}

			if (s[i] == ')')
			{
				if (!st.empty() && st.top() == '(')
                    st.pop();
				else
                {
                    cout << "no\n";
                    break;
                }
			}

			if (st.empty() && i == s.length() - 2)
                cout << "yes\n";
			else if (!st.empty() && i == s.length() - 2)
                cout << "no\n";
		}
    }

    return 0;
}
```

단순히 괄호끼리 짝이 있는가를 체크해야 하는게 아니라,<br/>

([)]와 같이 순서가 안맞는 경우는 예외처리를 해주어야 해서 다소 힘들었다.<br/>

그리고 이번 문제는 로직설계보다는 입력을 받는 과정을 조절하는 것이 힘들었던 것 같다.<br/>

그리고 중괄호는 짝짓기 대상이 아닌 것이었다는 것에 약간 당황했다.<br/>

문제를 제대로 읽어봐야겠어.<br/>

<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <stack>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    string s;

    while (true)
    {
        getline(cin, s);

        stack<int> st1;
        stack<int> st2;
        stack<int> st3;

        if (s == ".")
            break;

        for (size_t i = 0; i < s.length(); ++i)
        {
            if (s[i] == '(')
                st1.push(1);
            else if (s[i] == '{')
                st2.push(1);
            else if (s[i] == '[')
                st3.push(1);
            
            else if (s[i] == ')' && !st1.empty())
                st1.pop();
            else if (s[i] == '}' && !st2.empty())
                st2.pop();
            else if (s[i] == ']' && !st3.empty())
                st3.pop();
        }

        if (st1.empty() && st2.empty() && st3.empty())
            cout << "yes\n";
        else
            cout << "no\n";
    }

    return 0;
}
```

(hi [hello )] 와 같은 반례를 간과하여 이런 일이 생겼다.<br/>

로직이 잘못됐음을 깨닫고 다시 설계해야했다.<br/>