---
layout: post
title:  "[백준] 피보나치수 2 C++"
subtitle:   
date: 2022-08-26 13:53:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 피보나치수 2 C++<br/>
<br/>

C++의 정수자료형 사용을 주의해야 할 것.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int main()
{
    long long a1, a2, a3;
    unsigned int n;
    
    cin >> n;
    a1 = 0;
    a2 = 1;

    if (n == 0)
    {
        cout << 0;
        return 0;
    }
    
    else if (n == 1)
    {
        cout << 1;
        return 0;
    }

    
    for (int i = 2; i <= n; i++)
    {
        a3 = a1 + a2;
        a1 = a2;
        a2 = a3;
    }
    cout << a3;
    
    system("pause");

    return 0;
}
```

<br/>

## 시행착오 과정<br/>
<br/>

### 이전 제출 코드 (오답)<br/>

<br/>

```
#include <iostream>

using namespace std;

int main()
{
    int a1, a2, a3;
    int n;
    
    cin >> n;
    a1 = 0;
    a2 = 1;

    if (n == 0)
    {
        cout << 0;
        return 0;
    }
    
    else if (n == 1)
    {
        cout << 1;
        return 0;
    }

    
    for (int i = 2; i <= n; i++)
    {
        a3 = a1 + a2;
        a1 = a2;
        a2 = a3;
    }
    cout << a3;
    
    system("pause");

    return 0;
}
```

<br/>

일부 테스트 케이스를 돌렸을 땐(n = 5, n = 10 등) 정상적으로 출력되는 것이라고 생각했었다..<br/>

그래서 맞왜틀..?을 시전하면서 10분 정도를 고민했던 것 같다.<br/>

이때, 문제를 다시한번 읽어보니 n은 90이하의 자연수라고 했다.<br/>

그래서 n에다가 90을 넣어보았더니<br/>

```
-1581614984
```

음수 값이 나오는 것이다.<br/>

아차차! int의 표현 범위는 -2147483648 ~ 2147483647 까지 였다.<br/>
(-2^31 ~ 2^31 - 1)<br/>

그래서 자료형을 int에서 long long 으로 바꾸었다. 넉넉하게!<br/>

그랬더니 정답으로 체크되었다.<br/>

파이썬을 했다가 C++을 새로 공부하니 이런 실수가 발생했던 것 같다.<br/>

이번 실수를 기억해두기 위해서 이 글을 써보았다.<br/>