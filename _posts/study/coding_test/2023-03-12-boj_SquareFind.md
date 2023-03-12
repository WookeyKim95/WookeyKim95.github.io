---
layout: post
title:  "[백준] 1025번_제곱수 찾기 C++"
subtitle:   
date: 2023-03-12 11:32:28 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1025번_제곱수 찾기 C++<br/>
<br/>

완전탐색 알고리즘을 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <vector>
#include <cmath>

using namespace std;

int N, M;
int ans = -1;
vector<string> map;

bool is_square(int a)
{
    // 정수의 제곱이 맞다면 내림값과 그렇지 않은 값이 일치해야함.

    double a1 = sqrt(a);
    double a2 = floor(sqrt(a));

    if (a1 == a2)
        return true;

    else
        return false;
}

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void check(string s)
{
    // 유효한 s인지 검사하는 파트
    if (s.empty())
        return;

    for (char c : s)
    {
        if (!isdigit(c))
            return;
    }

    // 유효한 문자열이라면 stoi를 시키고 완전제곱수 여부를 판별함.
    int Candidate = stoi(s);

    if (is_square(Candidate))
        ans = m_max(Candidate, ans);
}

// 탐색하면서 등차로 문자열을 추가하는 함수
void search(int x, int y, int x_gap, int y_gap)
{
    // 등차가 가로, 세로 둘다 0일 경우 무한루프에 빠짐.
    if (!x_gap && !y_gap)
        return;

    int r = y, c = x;
    string s = "";
    
    while (0 <= r && r < N && 0 <= c && c < M)
    {
        // 문자열 추가
        s += map[r][c];

        // 등차수열
        r += y_gap;
        c += x_gap;
        check(s);
    }
}

int main()
{
    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        map.push_back(_input);
    }

    // 탐색
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {   
            // 등차 수열을 위한 다음 2중 for문
            for (int k = -N; k < N; k++)
            {
                for (int l = -M; l < M; l++)
                {
                    search(j, i, l, k);
                }
            }
        }
    }

    cout << ans;

    return 0;
}
```
<br/>

다른 자잘한 경우는 잘 구현 됐는데, 등차수열을 이용해서 완전 탐색을 구현하는 부분이 어려웠다.<br/>

[참고링크](https://codecollector.tistory.com/1462)를 통해서 참고를 하고 마저 구현하였다.<br/>

필자는 string으로 입력받은 다음에 stoi함수를 이용해서 정수 계산을 하는 방법을 이용하였다.<br/>

```

bool is_square(int a)
{
    // 정수의 제곱이 맞다면 내림값과 그렇지 않은 값이 일치해야함.
    if (sqrt(a) == floor(sqrt(a)))
        return true;
    else
        return false;
}
```
그리고 이 부분은 sqrt함수와 floor함수를 이용했다.<br/>

예를 들어서, 196의 제곱근을 구하면 sqrt함수는 double을 반환하므로<br/>

sqrt(196) = 14.00, floor(sqrt(196)) = 14.00으로 값이 일치한다.

반면에, 200의 제곱근을 구하면

sqrt(200) = 14.00, floor(sqrt(200)) = 14.xx...가 반환되므로<br/>

값이 다르다.<br/>

쉽게 생각하면, 소수부가 0이냐를 판별하는 것이라 생각하면 된다.<br/>
<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <vector>
#include <cmath>

using namespace std;

int N, M;
int ans = -1;
vector<string> map;

bool is_square(int a)
{
    // 정수의 제곱이 맞다면 내림값과 그렇지 않은 값이 일치해야함.
    if (sqrt(a) == floor(sqrt(a)))
        return true;

    else
        return false;
}

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void check(string s)
{
    // 유효한 s인지 검사하는 파트
    if (s.empty())
        return;

    for (char c : s)
    {
        if (!isdigit(c))
            return;
    }

    // 유효한 문자열이라면 stoi를 시키고 완전제곱수 여부를 판별함.
    int Candidate = stoi(s);

    if (is_square(Candidate))
    ans = m_max(Candidate, ans);
}

int main()
{
    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        map.push_back(_input);
    }

    // 탐색
    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < M; ++j)
        {   
            // 등차 수열을 위한 다음 2중 for문
            for (int k = -N+1; k < N; k++)
            {
                for (int l = -M+1; l < M; l++)
                {
                    // k와 l이 0이면 무한루프에 빠진다.
                    if (!k && !l)
                        continue;
                    
                    int r = i, c = j;
                    string s = "";
                    
                    while (0 <= r && r < N && 0 <= c && c < M)
                    {
                        // 문자열 추가
                        s += map[r][c];

                        // 등차수열
                        r += k;
                        c += l;
                    }
                    check(s);
                }
            }
        }
    }

    cout << ans;

    return 0;
}
```

<br/>

구글링을 하면서 정답코드를 분석해보니 문제 이해를 완벽하게 하지 못했던 것 같다.<br/>

우선, 바꿔준 부분은 아래이다.<br/>

```
// 변경 전
// 탐색하면서 등차로 문자열을 추가하는 함수
void search(int x, int y, int x_gap, int y_gap)
{
    // 등차가 가로, 세로 둘다 0일 경우 무한루프에 빠짐.
    if (!x_gap && !y_gap)
        return;

    int r = y, c = x;
    string s = "";
    
    while (0 <= r && r < N && 0 <= c && c < M)
    {
        // 문자열 추가
        s += map[r][c];

        // 등차수열
        r += y_gap;
        c += x_gap;
    }
    check(s);
}

// 변경 후

// 탐색하면서 등차로 문자열을 추가하는 함수
void search(int x, int y, int x_gap, int y_gap)
{
    // 등차가 가로, 세로 둘다 0일 경우 무한루프에 빠짐.
    if (!x_gap && !y_gap)
        return;

    int r = y, c = x;
    string s = "";
    
    while (0 <= r && r < N && 0 <= c && c < M)
    {
        // 문자열 추가
        s += map[r][c];

        // 등차수열
        r += y_gap;
        c += x_gap;
        check(s);
    }
}
```

그냥 등차수열만 이루면 되지, 반드시 끝까지 탐색해야한다는 조건은 없었다.<br/>

예를 들어

```
5 5

55555
51555
55255
55515
55555
```

과 같은 상황이 있다고 할때 정답으로 출력되는 값은 121이어야 했다.<br/>

그런데 예전 오답코드로 하면 25가 출력된다.<br/>

1 2 1이 있는 자리는 (2, 2), (3, 3), (4, 4)로 행과 열의 번호가 등차수열에 포함되므로<br/>

유효한 수열로 판별이 나야하는데 예전 오답코드는 그렇지 않게 설계되었다.<br/>

문제 이해를 잘 해야했었다.<br/>