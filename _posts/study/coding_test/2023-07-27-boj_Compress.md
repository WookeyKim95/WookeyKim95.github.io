---
layout: post
title:  "[백준] 1662_압축 C++"
subtitle:   
date: 2023-07-27 07:17:33 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1662_압축 C++<br/>
<br/>

재귀, 스택을 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <string>
#include <stack>
#include <cstring>

using namespace std;

bool visited[51];

int compress(string& s, int idx)
{
    int count = 0;

    for (size_t i = idx; i < s.length(); ++i)
    {
        if (s[i] == '(' && !visited[i])
        {
            visited[i] = true;
            int num = s[i - 1] - '0';
            count--;
            count += num * compress(s, i + 1);
        }
        else if (s[i] == ')' && !visited[i])
        {
            visited[i] = true;
            return count;
        }
        else if (!visited[i])
        {
            visited[i] = true;
            count++;
        }
    }

    return count;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    string s;
    cin >> s;
    memset(visited, false, sizeof(visited));

    cout << compress(s, 0);

    return 0;
}
```

?? 문제를 처음에 이해 못해서 많이 해맸었다.<br/>

아! 괄호 앞에 있는 정수는 한 자리구나!<br/>

즉,

```
1234(5)
```
가 있다고 하면 압축을 풀면 1235555가 되는 것이다.<br/>

이를 이해하는데 시간을 좀 써먹었다.<br/>

하지만 아이디어가 안떠오른 것은 매한가지..<br/>

처음엔 스택으로 풀면서 해메다가 재귀를 이용하는 방법을 활용하기로 했다.<br/>

[이분의 블로그](https://hwan-shell.tistory.com/305)를 참고하였다.<br/>

나는 flag라는 bool변수를 생각했는데, 이분은 방문 여부를 체크하셨다.<br/>

방문여부를 체크하는 것이 덜 위험하고 깔끔할 것이라 생각했다.<br/>

다만 숫자와 문자열을 어떻게 구분할지 걱정했었는데<br/>

그냥 여는 괄호 앞에 있는 숫자만 int로 변경시키면 되는 것이었다.<br/>