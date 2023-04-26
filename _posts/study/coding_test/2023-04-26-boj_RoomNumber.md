---
layout: post
title:  "[백준] 1082_방 번호 C++"
subtitle:   
date: 2023-04-26 08:56:32 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1082_방 번호 C++<br/>
<br/>

DP와 그리디가 합쳐진 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <algorithm>
#include <string>

#define MAX 101

using namespace std;

int N, M;
int arr[10];
string ans;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
        cin >> arr[i];
    
    cin >> M;

    int first = MAX, second = MAX;
    int FirstIdx, SecondIdx;

    for (int i = 0; i < N; ++i)
    {
        if (arr[i] < first)
        {
            first = arr[i];
            FirstIdx = i;
        }
    }
    
    for (int i = 1; i < N; ++i)
    {
        if (arr[i] < second)
        {
            second = arr[i];
            SecondIdx = i;
        }
    }
    
    if (M < second)
    {
        cout << "0";
        return 0;
    }

    M -= second;
    ans.push_back('0' + SecondIdx);

    while (M >= first)
    {
        ans.push_back('0' + FirstIdx);
        M -= first;
    }
    
    int idx;
    
    for (size_t i = 0; i < ans.length(); ++i)
    {
        // 뒤에서 부터 살 수 있는지 판단하기
        for (int j = N - 1; j >= 0; --j)
        {
            idx = ans[i] - '0';
                        
            if (M >= arr[j] - arr[idx])
            { 
                M -= (arr[j] - arr[idx]);
                ans[i] = '0' + j;
                break;
            }            
        }
    }

    cout << ans;

    return 0;
}
```

문제를 읽어보면 그리디라는 것을 파악할 수 있지만,<br/>

DP라는 것을 깨다는 데에는 어려웠던 것 같다.<br/>

구글링을 해보니 다양한 방법의 풀이가 있었는데 그 중 나는 [이 분](https://sdy-study.tistory.com/226)의 풀이를 참고해보기로 했다.<br/>

원리는 이렇다.<br/>

주어진 숫자에서 첫번째 인덱스에서 부터 마지막번째 인덱스까지 가장 작은 수를 first<br/>

두번째 인덱스에서 부터 마지막번째 인덱스까지 가장 작은 수를 second라고 한다.<br/>

0만 여러개 나오는 경우를 방지하고, 첫번째 숫자가 0이 나오는 경우를 방지하기 위해 두 개의 인덱스를 잡아준다.<br/>

그리고 가능한 최저 비용을 산출하기 위한 이유도 있다.<br/>

그래서 가장 낮은 비용 중에서 가장 큰 값을 인덱스로 잡고,<br/>

가격 내에서 가장 많은 자릿수를 만들 수 있을 때까지 자릿수를 늘려나간다.<br/>

그리고 맨 앞에서부터 가격이 허락한다면 더 큰수로 바꾸는 것이다.<br/>

이렇게 하면 가장 큰 수를 나오게 만들 수 있다.<br/>