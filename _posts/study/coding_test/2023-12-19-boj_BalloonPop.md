---
layout: post
title:  "[백준] 11509_풍선 맞추기 C++"
subtitle:   
date: 2023-12-19 06:02:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11509_풍선 맞추기 C++<br/>
<br/>

백트래킹을 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    vector<int> vec;
    
    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        vec.push_back(_input);
    }

    vector<int> Arrow;

    Arrow.push_back(vec[0]);

    for (size_t i = 1; i < vec.size(); ++i)
    {
        bool flag = false;
        for (size_t j = 0; j < Arrow.size(); ++j)
        {
            if(Arrow[j] - 1 == vec[i])
            {
                flag = true;
                Arrow[j]--;
                break;
            }
        }

        if(!flag)
            Arrow.push_back(vec[i]);
    }

    cout << Arrow.size();

    return 0;
}
```

풍선이 터지는 경우는 100, 99, 98, ... 3, 2, 1 등 이렇게 1씩 내려가는 수가 배열되어 있을 때이다.<br/>

맨 앞에 있는 풍선의 높이에서 화살을 발사하여 얼마나 많이 터트릴 수 있는지 측정하고,<br/>

만약에 더 이상 터트릴 풍선이 없는데 풍선이 남아있다면 다시 나머지 중 맨 앞의 풍선 높이에서 터트리면 된다.<br/>

그렇게 나타낸 코드가 위의 코드이다.<br/>

이렇게 해서 화살의 개수를 측정해서 출력해주면 된다.<br/>