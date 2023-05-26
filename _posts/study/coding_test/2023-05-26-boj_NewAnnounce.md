---
layout: post
title:  "[백준] 1135_뉴스 전하기 C++"
subtitle:   
date: 2023-05-26 09:57:32 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1135_뉴스 전하기 C++<br/>
<br/>

트리에서 다이나믹 프로그래밍을 해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct Person
{
    int num;
    int parent;
    vector<int> child;

    Person(int _num) : num(_num) {};
};

int N;
vector<Person> People;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int SearchTree(int a)
{
    if (People[a].child.size() == 0)
        return 0;
    
    int ret = 0;
    vector<int> v;
    for (size_t i = 0; i < People[a].child.size(); ++i)
        v.push_back(SearchTree(People[a].child[i]));

    sort(v.begin(), v.end(), greater<int>());

    for(size_t i = 0; i < v.size(); ++i)
        ret = m_max(ret, v[i] + (i+1));

    return ret;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        Person p(i);
        int _input;
        cin >> _input;

        p.parent = _input;
        People.push_back(p);

        if (_input == -1)
            continue;
        
        People[_input].child.push_back(i);
    }

    cout << SearchTree(0);

    return 0;
}
```
<br/>

뭔가 대충 아래와 같은 방식으로 트리에서의 DP를 구현하는 거라곤 생각했다.<br/>

```
int SearchTree(int a)
{
    if (v[a].size() == 0)
        return 0;
    
    int ret = 0;

    ...

    return ret;
}
```

그런데 막상 코드를 작동시켜보니 원하는 대로 답이 나오지 않기도 하고<br/>

return 값을 어떻게 뽑아내야할지 감이 오지 않았다.<br/>

흠.. 알고리즘 분류를 보니 정렬이 있네?<br/>

상상도 못했다. 왜 정렬이 필요하지?<br/>

[이 글](https://everenew.tistory.com/193)에 따라 생각해보면,<br/>

부하가 가장 많은 트리를 먼저 방문해야 이득이라는 것을 알 수 있다고 한다.<br/>

아하! 부하가 끝까지 전달하는 시간을 저장하는 배열을 만들어서 그것을 정렬하는 것이고<Br/>

거기서 값을 비교하면서 ret값을 뽑아내는 방식이었다.<br/>

```
for(size_t i = 0; i < v.size(); ++i)
    ret = m_max(ret, v[i] + (i+1));
```

여기서 v[i] + (i+1)을 하는 이유는<br/>

부하가 모두 전화를 완료하는 시간 + 다른 나머지 부하에게 전화를 거는 시간을<br/>

더하는 것이라서 그런듯하다.<br/>

이미 v벡터는 내림차순으로 정렬이 완료된 상태이고,<br/>

부하가 가장 많은, 전파가 가장 오래걸리는 부하를 먼저 방문하는 상태가 되니<br/>

자동적으로 v[i] + (i+1)을 하면 비교값이 성립된다는 것을 알 수 있다.<br/>

bottom-up 방식이라는 것은 맞췄는데 이걸 생각해내지 못한것이 아쉬운 점이었다.<br/>
