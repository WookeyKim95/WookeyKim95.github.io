---
layout: post
title:  "[백준] 9935_문자열 폭발 C++"
subtitle:   
date: 2023-06-10 10:11:03 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 9935_문자열 폭발 C++<br/>
<br/>

문자열을 스택처럼 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string s, bomb;
    cin >> s;
    cin >> bomb;

    int BombLen = bomb.length();

    string st = "";

    for (size_t i = 0; i < s.length(); ++i)
    {
        st += s[i];
        int StLen = st.length();

        if (BombLen <= StLen)
        {
            bool flag = true;

            for (size_t j = StLen - BombLen; j < st.length(); ++j)
            {
                if (st[j] != bomb[j - StLen + BombLen])
                {
                    flag = false;
                    break;
                }
            }

            if (flag)
                st.erase(st.end() - BombLen, st.end());
        }
    }

    if (st.length() == 0)
        cout << "FRULA";
    else
        cout << st;

    return 0;
}
```

<br/>

함수 사용을 줄여야했다..<br/>
<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <string>

using namespace std;

bool Search(string a, string b)
{
    int alen = a.length();
    int blen = b.length();
    
    for (int i = alen - blen; i < alen; ++i)
    {
        if (a[i] != b[i - alen + blen])
            return false;
    }
    return true;
}

int main()
{
    string s, bomb;
    cin >> s;
    cin >> bomb;

    string st = "";

    for (size_t i = 0; i < s.length(); ++i)
    {
        st += s[i];

        if (bomb.length() <= st.length() && Search(st, bomb))
        {
            for (size_t j = 0; j < bomb.length(); ++j)
                st.pop_back();  
        }
    }

    if (st.length() == 0)
        cout << "FRULA";
    else
        cout << st;

    return 0;
}
```
<br/>

문자열을 스택처럼 활용해서 풀었다.<br/>

우선, 문자열 전체를 받아준 다음에, 스택 역할을 할 문자열 st를 새로 생성한다.<br/>

그리고 st에다가 문자를 하나씩 차례대로 넣어주는 것이다.<br/>

이 때, Search 함수를 통해서 맨 끝의 bomb의 길이만큼을 조사한다.<br/>

이 때, st문자열 맨끝에 있는 문자열이 bomb과 같으면 그 길이만큼 pop_back을 진행한다.<br/>

```
bool Search(string a, string b)
{
    int alen = a.length();
    int blen = b.length();
    
    for (int i = alen - blen; i < alen; ++i)
    {
        if (a[i] != b[i - alen + blen])
            return false;
    }
    return true;
}

...

if (bomb.length() <= st.length() && Search(st, bomb))
{
    for (size_t j = 0; j < bomb.length(); ++j)
        st.pop_back();  
}

```

그 다음에 다시 차례대로 문자열을 넣어주는 것이다.<br/>

이렇게 문자열을 스택처럼 활용하면 원하는 결과가 출력되도록 구현할 수 있었다.<br/>

하지만 시간초과라는 결과가 떴다.<br/>

## 정답코드로의 변환<br/>
<br/>

왜 시간초과가 뜨는지 모르겠어서 참고를 했다.<br/>

[이분의 글](https://hagisilecoding.tistory.com/127)을 보니 <br/>

pop_back을 사용하는 대신에 erase함수를 사용하셨다.<br/>

확실히, erase함수를 사용하면 1번만 연산하면 되기 때문에 속도도 더 빨라진다.<br/>

하지만 여전히 시간초과가 떠서 무엇이 문제인지 더 보니,<br/>

이분은 Search함수로 따로 분리하지 않고 main함수 내에서 bomb을 검색을 하도록 하셨다.<br/>

문자열이 길면 함수 호출 횟수가 늘어나기 때문에 이 부분에서 소요시간이 길어지는 것 같았다.<br/>

그래서 나도 마찬가지로 Search함수를 제거하고 아래와 같이 옮겼더니 정답처리 되었다.<br/>

```
if (BombLen <= StLen)
{
    bool flag = true;

    for (size_t j = StLen - BombLen; j < st.length(); ++j)
    {
        if (st[j] != bomb[j - StLen + BombLen])
        {
            flag = false;
            break;
        }
    }

    if (flag)
        st.erase(st.end() - BombLen, st.end());
}
```

로직 자체는 정답이었지만 erase를 사용 하지 않은 것과 코드 배치가 문제였던 것 같다.<br/>

string에도 erase가 있었구나.. 숙지해야겠다.<br/>