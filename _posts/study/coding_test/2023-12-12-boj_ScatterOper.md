---
layout: post
title:  "[백준] 1009_분산처리 C++"
subtitle:   
date: 2023-12-12 06:34:44 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1009_분산처리 C++<br/>
<br/>

수학적 규칙을 이용해보는 문제<br/>
<br/>

## 정답제출코드1<br/>
<br/>

```
#include <iostream>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    int T;
    cin >> T;
    
    for (int tc = 0; tc < T; ++tc)
    {
        int a, b;
        cin >> a >> b;
        
        int tmp = a;
        
        for (int i = 1; i < b; ++i)
            tmp = tmp * a % 10;
        
        if (tmp % 10 == 0)
            cout << 10 << '\n';
        else
            cout << tmp % 10 << '\n';
    }
    
    return 0;
}
```

그냥 1의 자리 수를 출력하면 되는 문제이다.<br/>

사실 쉽게 제출한다면 이렇게 제출하면 된다.<br/>

하지만 최악의 경우 b = 1000000일 때가 있기 때문에<br/>

더 빠르게 처리할 수 있는 방법이 없을 지 생각해보았다.<br/>

a 의 1의 자리 수가 3이라고 가정하면

3, 9, 7, 1, 3, 9, 7, 1, ... 이렇게 반복되기 때문에<br/>

b = 100이라고 할 때 그냥 4의 배수인 1을 출력하면 되지 않는가? 생각했다.<br/>

그래서 모듈러 연산과 vector을 사용해서 구현해 보았다.<br/>

<br/>

## 정답제출코드2<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

bool IsIn(int a, vector<int> b)
{
    for (size_t i = 0; i < b.size(); ++i)
    {
        if (b[i] == a)
            return true;
    }
    return false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    int T;
    cin >> T;
    
    for (int tc = 0; tc < T; ++tc)
    {
        int a, b;
        cin >> a >> b;

        vector<int> Group;
        int tmp = a;
        
        while (true)
        {
            if (IsIn(tmp, Group))
                break;
            
            Group.push_back(tmp % 10);
            tmp = tmp * a % 10;
        }
        
        b %= Group.size();

        if (b == 0)
            b = Group.size();
            

        if (Group[b-1] % 10 == 0)
            cout << 10 << '\n';
        else
            cout << Group[b-1] % 10 << '\n';
    }
    
    return 0;
}
```

실제로 이렇게 제출을 해보니 확연히 차이가 났다.

- 정답제출코드 1 (C++) : 사용 메모리 (2020KB) / 소요시간 (800ms)
- 정답제출코드 2 (C++) : 사용 메모리 (2020KB) / 소요시간 (0ms)

이렇게 b가 100만이라고 해도 100만번 돌릴 필요 없이 한자리 수만큼만 돌리게 해서<br/>

최적화를 할 수 있었다.