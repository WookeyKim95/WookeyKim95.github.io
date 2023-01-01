---
layout: post
title:  "[프로그래머스] 귤 고르기 C++"
subtitle:   
date: 2022-12-30 17:25:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 귤 고르기 C++<br/>
<br/>

C++ 라이브러리 중 map을 사용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>
#include <vector>
#include <map>
#include <algorithm>
#include <iostream>

using namespace std;

// value 값으로 map을 내림차순 정렬하기 위한 함수
bool cmp(const pair<int, int>& a, const pair<int, int>& b){
	return a.second > b.second;
}

int solution(int k, vector<int> tangerine) {
    int answer = 0;

    // 귤의 크기 별로 몇개가 담겨있는지 저장할 map
    map<int, int> m;
    int len = tangerine.size();
    
    // map에다가 자료 작성
    for (int i = 0; i < len; ++i)
    {
        auto iter = m.find(tangerine[i]);
        if (iter != m.end())
        {
            iter->second += 1;
        }
        else
        {
            m.insert({tangerine[i], 1});
        }
    }
    
    // map을 value 크기로 정렬하는 과정
    // map을 새로 저장할 vector 형성
    vector<pair<int, int>> v(m.begin(), m.end());
    
    // map을 cmp함수를 이용해 내림차순 정렬
    sort(v.begin(), v.end(), cmp);
    
    // 내림차순 정렬한 value 값을 count에 담음
    // k값에 도달할 경우 그때 가지수가 answer임.
    int count = 0;
    for (auto iter = v.begin(); iter != v.end(); ++iter)
    {
        if (count >= k)
            continue;
        
        cout << iter->second << '\n';
        count += iter->second;
        answer++;
    }
    
    return answer;
}
```

<br/>

## 코드 설명<br/>
<br/>

- 귤의 크기 별 개수를 저장하기 위해서 map을 사용하였다.
- 그리고 map을 내림차순 정렬하기 위해서 vector을 사용하였다.
- 내림차순 정렬을 사용하고자 한 이유는 귤이 많은 것부터 넣어야 최소 가지수로 상자를 채울 수 있다고 생각했기 때문이다.
```
vector<pair<int, int>> v.(m.begin(), m.end())
```
- 위 코드를 통해 map을 정렬하기 위한 자료구조를 새로 생성하였다.
- sort(v.begin(), v.end(), cmp) 함수를 통해 내림차순 정렬을 실시하였다.
- for문을 통해서 가장 많은 수를 가진 귤을 count 변수를 통해 상자에 담고, answer를 1씩 증가시켰다.
- count가 k 이상이 되었을 경우 answer를 더 이상 증가시키지 않는다.