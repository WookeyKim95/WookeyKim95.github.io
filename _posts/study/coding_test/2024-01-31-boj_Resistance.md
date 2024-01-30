---
layout: post
title:  "[백준] 1076_저항 C++"
subtitle:   
date: 2024-01-31 06:34:32 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1076_저항 C++<br/>
<br/>

enum과 swtich문을 활용해보려다 해시함수를 공부하게 된 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <cmath>

using namespace std;

constexpr unsigned int HashCode(char* str)
{
    return str[0] ? static_cast<unsigned int>(str[0]) + 0xEDB8832Full * HashCode(str + 1) : 8603;
}

int retColor(char *color)
{
    switch (HashCode(color))
    {
        case HashCode("black"):
            return 0;
            break;
        case HashCode("brown"):
            return 1;
            break;
        case HashCode("red"):
            return 2;
            break;
        case HashCode("orange"):
            return 3;
            break;
        case HashCode("yellow"):
            return 4;
            break;
        case HashCode("green"):
            return 5;
            break;
        case HashCode("blue"):
            return 6;
            break;
        case HashCode("violet"):
            return 7;
            break;
        case HashCode("grey"):
            return 8;
            break;
        case HashCode("white"):
            return 9;
            break;
        default:
            return -1;
            break;
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    char a[10], b[10], c[10];
    
    cin >> a;
    cin >> b;
    cin >> c;
    
    long long ans = 0;
    ans += retColor(a) * 10;
    ans += retColor(b);
    ans *= (long long)pow(10, retColor(c));
    
    cout << ans;
    
    return 0;
}
```

<br/>

브론즈 문제로 쉬운 문제이다.<br/>

사실 if문을 이용하면 쉬운 문제이지만,<br/>

실전에서는 if문을 쓰기보다 이런 경우는 enum-switch 구조를 많이 사용한다고 해서<br/>

이를 시도해보기로 하였다.<br/>

제일 고민했던 부분이<br/>

입력에서는 string을 입력받는데 이를 어떻게 Colors로 캐스팅을 할까를 고민했다.<br/>

왜냐하면, swtich 문에서는

```
switch (color)
{
    case 1:
    ...
}
```

여기서 color는 int 형식이어야 하지, string 으로 받을 수 없기 때문이다.<br/>

해답은 해시함수에 있었다.<br/>

[참고링크](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=devmachine&logNo=220952781191)에 따르면

해시함수를 이용해서 문자열을 상수화 시켜서 문제를 풀 수 있었다.<br/>

다만, constexpr라는 처음보는 것 때문에 컴파일 에러가 10번 넘게 발생했다.<br/>

해시함수를 다룰 때 필요한 것같은데.. 이것에 대해 좀 더 공부해봐야겠다.<br/>

브론즈 문제였지만 굉장히 강력하게 기억에 남을 문제였던 것 같다.<br/>