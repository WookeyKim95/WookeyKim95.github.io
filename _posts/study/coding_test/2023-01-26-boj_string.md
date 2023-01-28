---
layout: post
title:  "[백준] 1120번_문자열 C++"
subtitle:   
date: 2023-01-23 10:45:17 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1120번_문자열 C++<br/>
<br/>

완전탐색을 이용해서 푸는 문제<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main()
{
    string a, b;
    cin >> a >> b;

    vector<int> counts;

    int len_a = a.length();
    int len_b = b.length();

    for (int i = 0; i <= len_b - len_a; ++i)
    {
        int count = 0;
        for (int j = 0; j < len_a; ++j)
        {
            if (a[j] != b[j+i])
            {
                count++;
            }
        }
        counts.push_back(count);
    }

    int min = counts[0];
    for (int i = 1; i < counts.size(); ++i)
    {
        if (counts[i] < min)
            min = counts[i];
    }
    cout << min;
    return 0;
}
```

실버 4 문제인데 30분내로 못푸는거 보니 완전탐색에 아직 많이 약한 것 같다.<br/>

더 분발해야지<br/>

## 연구했던 오답코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <string.h>

using namespace std;

int main()
{
    string a, b;
    cin >> a >> b;

    int count = 0;
    if (a.length() == b.length())
    {
        int len = a.length();

        for (int i = 0; i < len; ++i)
        {
            if (a[i] != b[i])
                count++;
        }
    }

    else
    {
        int len = a.length();
        int start_index = 0;
        for (int i = 0; i < len; ++i)
        {
            string tmp = a.substr(start_index, len-start_index);
            
            if (strstr(&b[0], &tmp[0]) != nullptr)
                break;
            else
            {
                count++;
                start_index++;
            }
        }
    }

    cout << count;

    return 0;
}
```

strstr을 이용해서 a문자열이 b안에 포함되어있을까를 생각했었는데,<br/>

이렇게되면 첫번째 예제에서 오답이 출력되더라.<br/>

다시 한번 생각해보니 이 문제는 브루트포스, 즉 완전탐색이다.<br/>

목표로 했던 30분 이내로 풀기가 실패해서 구글링을 해보아서 파이썬코드를 통해 힌트를 얻었다.<br/>

[코드출처](https://neomindstd.github.io/%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/boj1120/)

```
a, b = input().split()

cnts = list()
for i in range(len(b) - len(a)+1):
    cnt = 0
    for j in range(len(a)):
        if(a[j] != b[j+i]):
            cnt += 1
    cnts.append(cnt)
print(min(cnts))
```

## 2차 삽질<br/>
<br/>

```
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main()
{
    string a, b;
    cin >> a >> b;

    vector<int> counts;

    int len_a = a.length();
    int len_b = b.length();

    for (int i = 0; i < len_b - len_a; ++i)
    {
        int count = 0;
        for (int j = 0; j < len_a; ++j)
        {
            if (a[j] != b[j+i])
            {
                count++;
            }
        }
        counts.push_back(count);
    }

    int min = counts[0];
    for (int i = 1; i < counts.size(); ++i)
    {
        if (counts[i] < min)
            min = counts[i];
    }
    cout << min;
    return 0;
}
```

이러면 또 hello / xello 테스트케이스에서 오류난다.<br/>

왜냐? len_a == len_b가 true이기 때문에 첫번째 for문이 돌지 않아서<br/>

counts에 값이 아무것도 안들어간다.<br/>

어디서 잘못한거지?<br/>

```
    for (int i = 0; i < len_b - len_a; ++i)
```

어머 등호를 안넣었네?<br/>

넣어주니까 해결됐다.<br/>