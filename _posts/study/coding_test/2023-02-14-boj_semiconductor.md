---
layout: post
title:  "[백준] 2352번_반도체 설계 C++"
subtitle:   
date: 2023-02-14 10:30:28 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2352번_반도체 설계 C++<br/>
<br/>

LIS, 이분탐색을 이용해서 푸는 문제<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int bisect(vector<int> arr, int val)
{
    int s = 0, e = arr.size() - 1;
    
    while(s <= e)
    {
        int mid = (s + e) / 2;
        if (arr[mid] > val)
            e = mid-1;
        else
            s = mid+1;
    }
    return s;
}

int main()
{
    int n;
    cin >> n;

    vector<int> dest;

    for (int i = 0; i < n; ++i)
    {
        int _input;
        cin >> _input;
        dest.push_back(_input);
    }

    vector<int> link;
    
    for (int i = 0; i < n; ++i)
    {
        int link_len = link.size();
        if (link_len == 0 || link[link_len - 1] < dest[i])
            link.push_back(dest[i]);
        else
            link[bisect(link, dest[i])] = dest[i];
    }

    cout << link.size();
        
    return 0;
}
```

[참고 링크](https://suri78.tistory.com/204)<br/>

여태 쉬운 문제만 풀다가 좀 더 어려운 문제를 풀어보고 싶어서 다른 사람의 풀이를 참고하면서 풀어보기로 했다.<br/>

이 문제에서 오래 고민했던 점은 이분탐색 자체를 구현하기 보다는 왜 이 문제가 이분탐색을 써야하는지 고민하는데 더 오래걸렸던 것 같다.<br/>

사실 이 문제의 핵심은 LIS(가장 긴 증가하는 부분수열)이었는데 이를 위해선 탐색을 해야하는 것 같다.<br/>

그리고 더 빠른 탐색을 위해서 완전탐색보다는 이분탐색을 사용해야했던 것 같다.<br/>