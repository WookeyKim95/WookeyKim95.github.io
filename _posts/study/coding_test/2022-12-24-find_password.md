---
layout: post
title:  "[백준] 비밀번호 찾기 C++"
subtitle:   
date: 2022-12-24 10:01:23 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 비밀번호 찾기 C++<br/>
<br/>

C++ 라이브러리 중 map을 사용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <map>

using namespace std;

int main()
{
    int N, M;
    cin >> N >> M;
    
    map<string, string> table;
    
    for (int i = 0; i < N; ++i)
    {
        string p1, p2;
        cin >> p1 >> p2;
        
        table.insert({p1, p2});
    }
    
    for (int i = 0; i < M; ++i)
    {
        string to_find;
        cin >> to_find;
    
        cout << table.find(to_find)->second << '\n';
    }

    return 0;
}
```

<br/>

## 오답 제출코드 1(시간초과)<br/>

```
#include <iostream>
#include <vector>
#include <string>

using namespace std;

int main()
{
    int N, M;
    cin >> N >> M;
    
    vector<string> add;
    vector<string> pw;
    
    for (int i = 0; i < N; ++i)
    {
        string p1, p2;
        cin >> p1 >> p2;
        
        add.push_back(p1);
        pw.push_back(p2);
    }
    
    for (int i = 0; i < M; ++i)
    {
        string to_find;
        cin >> to_find;
        
        for (int j = 0; j < N; ++j)
        {
            if (add[j] == to_find)
                cout << pw[j] << '\n';
        }
    }
    
    return 0;
}
```

벡터 2개를 준비하고 각 벡터에 주소와 패스워드를 순차적으로 저장하는 식으로 구현하였다.<br/>

그리고 주소 벡터를 순회하며 주소 벡터에서 원하는 주소를 찾으면<br/>

패스워드 베터에서 해당하는 인덱스의 값을 반환하는 식으로 구현하였다.<br/>

하지만 벡터를 순회하는 과정에서 발생하는 시간복잡도는 O(n)이기 때문에 시간초과의 결과를 낳았다.<br/>

벡터를 말고 다른 것을 사용하기로 생각하였다.<br/>
<br/>

## 오답 제출코드 2(시간초과)<br/>
<br/>

```
#include <iostream>
#include <string>
#include <map>

using namespace std;

int main()
{
    int N, M;
    cin >> N >> M;
    
    map<string, string> table;
    
    for (int i = 0; i < N; ++i)
    {
        string p1, p2;
        cin >> p1 >> p2;
        
        table.insert({p1, p2});
    }
    
    for (int i = 0; i < M; ++i)
    {
        string to_find;
        cin >> to_find;
        
        for (auto iter = table.begin(); iter != table.end(); iter++)
        {
            if (iter->first == to_find)
                cout << iter->second << '\n';
        }
    }

    return 0;
}
```

같은 로직으로 vector가 아니라 map을 사용하기로 하였다.<br/>

하지만 이 역시 시간초과라는 결과를 낳았다.<br/>

아하! 순회하는 행위 자체가 문제인듯하다.<br/>

생각해보면 key값을 이용해서 찾아야 시간복잡도가 줄어들테니까.<br/>
<br/>

## 정답코드로의 변경<br/>

```
        for (auto iter = table.begin(); iter != table.end(); iter++)
        {
            if (iter->first == to_find)
                cout << iter->second << '\n';
        }
```
이 코드를

```
        cout << table.find(to_find)->second << '\n';
```
이 코드로 변경하였다.<br/>

[참고한 주소](https://yummy0102.tistory.com/191)에 따르면 map 라이브러리에서 값을 저장할 때에는 레드 블랙트리를 사용한다고 한다.<br/>

오호라! 트리 구조로 저장되어있기 때문에 for문을 사용해서 검색하는 리스트에 비해서 검색속도가 빠를 수밖에 없다.

엔진을 구성할 때 맵을 사용할 일이 은근 많은데, find를 사용해서 검색할 수 있도록 습관을 들여놔야겠다.<br/>