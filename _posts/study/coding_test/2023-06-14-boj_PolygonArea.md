---
layout: post
title:  "[백준] 2166_다각형의 면적 C++"
subtitle:   
date: 2023-06-14 08:19:50 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2166_다각형의 면적 C++<br/>
<br/>

ccw를 사용해서 다각형의 넓이를 구해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

int N;
vector<pair<double, double>> vertex;

double ccw(double x1, double x2, double x3, double y1, double y2, double y3)
{
    double ret = x1 * y2 + x2 * y3 + x3 * y1;
    ret -= (y1 * x2 + y2 * x3 + y3 * x1);

    return ret / 2;
}

int main()
{
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        double input1, input2;
        cin >> input1 >> input2;

        vertex.push_back({input1, input2});
    }

    double ans = 0;
    
    for (int i = 1; i < N; ++i)
        ans += ccw(vertex[0].first,
                   vertex[i-1].first,
                   vertex[i].first,
                   vertex[0].second,
                   vertex[i-1].second,
                   vertex[i].second);
    
    cout.precision(1);
    cout << fixed;
    cout << abs(ans);

    return 0;
}
```
<br/>

[풀이 참고링크](https://ip99202.github.io/posts/%EB%B0%B1%EC%A4%80-2166-%EB%8B%A4%EA%B0%81%ED%98%95%EC%9D%98-%EB%A9%B4%EC%A0%81/)<br/>

다각형을 삼각형 여러개로 쪼갤 수 있다는 것까지는 생각했다.<br/>

하지만 풀이 참고링크에 나온 것 처럼 한 점을 기준으로 삼각형으로 쪼개는 것은 생각하지 못했다.<br/>

그리고 또 다른 문제에 직면했다.<br/>

삼각형의 넓이들을 어떻게 구할 수 있을까?<br/>

해법은 이전에도 사용했었던 CCW에 있었다.<br/>

CCW는 세 벡터를 외적을 해서 구하는 것인데,

- 결과값이 양수이면 반시계방향
- 결과값이 0 이면 직선
- 결과값이 음수이면 시계방향

이라는 결과를 내놓게 된다.

그렇다면 여기서는 외적을 통해서 삼각형의 넓이를 구하게 된다는 것인데,<br/>

생각해보니 외적의 값 자체가 두 벡터가 이루는 평행사변형의 넓이이다.<br/>

따라서 ret 값에서 반을 나눠주면 세 벡터가 이루는 삼각형의 넓이로 만들 수 있다.<br/>

```
double ccw(double x1, double x2, double x3, double y1, double y2, double y3)
{
    double ret = x1 * y2 + x2 * y3 + x3 * y1;
    ret -= (y1 * x2 + y2 * x3 + y3 * x1);

    return ret / 2;
}
```

이렇게 나오는 삼각형의 넓이들을 모두 더해주면 다각형의 넓이가 된다.<br/>