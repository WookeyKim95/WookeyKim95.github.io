---
layout: post
title:  "[백준] 14921_용액 합성하기 C++"
subtitle:   
date: 2023-12-22 07:15:54 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 14921_용액 합성하기 C++<br/>
<br/>

투 포인터 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>
#include <vector>
#include <cmath>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    vector<int> arr;

    cin >> N;
    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        arr.push_back(_input);
    } 

    int left = 0, right = N-1, min = 999999999, ans = -1;

    while (left < right)
    {
        int s = arr[left] + arr[right];
        if (abs(s) < min)
        {
            min = abs(s);
            ans = s;
        }
        
       if (s > 0)
           right--;
       else
           left++;
    }

    cout << ans;

    return 0;
}
```

이전에 풀었던 두 용액 문제랑 비슷한 느낌이 난다.<br/>

10ml 짜리 두 용액만을 섞는다고 했으니 이 상황에서 최선의 경우를 구하려면 투 포인터를 사용해야할 것이라 생각했다.<br/>

본래 같았으면 오름차순으로 정렬을 해야하지만, 고맙게도 문제에서 이미 정렬된 상태로 입력된다고 한다.<br/>

그 다음에 두 포인터가 가리키고 있는 용액값의 합의 절댓값이 작으면 갱신한 뒤, 용액의 값을 저장해둔다.<br/>

그런 뒤, 합이 음수면 left포인터를 늘리고, 합이 양수면 right포인터를 낮춰주면서<br/>

합이 0과 가까워 지도록 만들어주며 절댓값의 최솟값을 갱신시켜주면된다.<Br/>