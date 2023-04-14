---
layout: post
title:  "[백준] 1039번_교환 C++"
subtitle:   
date: 2023-04-14 09:19:47 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1039번_교환 C++<br/>
<br/>

완전탐색인줄 알았더니 알고보니 큐를 쓰는거였던 문제.<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <string>
#include <queue>
#include <set>

using namespace std;

string N;
int K;
int num[7];
queue<string> q;

void swap(string& s, int a, int b)
{
    char temp;
    temp = s[a];
    s[a] = s[b];
    s[b] = temp;

    return;
}

int m_max(int a, string b)
{
    int B = stoi(b);

    if (a > B)
        return a;
    else
        return B;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> K;

    q.push(N);

    for (int i = 0; i < K; ++i)
    {
        set<string> s;
        size_t qsize = q.size();

        for (size_t j = 0; j < qsize; ++j)
        {
            string str = q.front();
            q.pop();

            if (s.count(str) != 0)
                continue;
            s.insert(str);

            for (size_t k = 0; k < str.length()-1; ++k)
            {
                for (size_t l = k+1; l < str.length(); ++l)
                {
                    if (!(k == 0 && str[l] == '0'))
                    {
                        swap(str, k, l);
                        q.push(str);
                        swap(str, k, l);
                    }
                }
            }
        }
    }

    int ans = -1;
    while(!q.empty())
    {
        ans = m_max(ans, q.front());
        q.pop();
    }

    cout << ans;

    return 0;
}
```

완전탐색이라고 했지만 도저히 감이 잡히지 않아서<br/>

[참고링크](https://kibbomi.tistory.com/176)를 참고해서 풀었다.<br/>

N을 swap하기 편하게 string으로 받은 뒤에 모든 경우의 수를 세트와 큐에다가 보관하는 식으로 구현하였다.<br/>

세트는 중복되는 경우를 방지하기 위해서 설치하였다.<br/>

즉, 세트 안에 있는 어떤 경우가 이미 있는 경우라면 큐에다가 넣는 과정을 생략하는 것이다.<br/>

만일 연산이 불가능하다면 ans는 -1로 그대로 남을 것이기 때문에 -1을 출력할 수 있을 것이고,<br/>

큐에 값이 들어갔다면 연산을 실시했다는 것이다.<br/>

이렇게 해서 마지막에 가장 큰 경우를 출력하도록 구현하였다.<br/>