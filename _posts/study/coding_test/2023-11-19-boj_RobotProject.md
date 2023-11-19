---
layout: post
title:  "[백준] 3649_로봇 프로젝트 C++"
subtitle:   
date: 2023-11-19 07:28:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 3649_로봇 프로젝트 C++<br/>
<br/>

투 포인터를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

#define NANOCENTI 10000000

using namespace std;

int main()
{
    int X, N;

    while (cin >> X >> N)
    {
        X *= NANOCENTI;
        vector<int> vec;

        for (int i = 0; i < N; ++i)
        {
            int _input;
            cin >> _input;
            vec.push_back(_input);
        }

        sort(vec.begin(), vec.end());

        int left = 0, right = vec.size() - 1;
        bool flag = false;

        while (left < right)
        {
            int sum = vec[left] + vec[right];
            if (sum == X)
            {
                flag = true;
                break;
            }
            else if (sum < X)
                left++;
            else
                right--;
        }

        if (flag)
            cout << "yes " << vec[left] << ' ' << vec[right] << '\n';
        else
            cout << "danger" << '\n';
    }

    return 0;
}
```
<br/>
<br/>


문제에서도 탐색시간 제한이 5초라서 투 포인터를 탐색할 수 있겠다 생각했다.<br/>

다만 테스트케이스가 몇개인지 주어지지 않아서 다소 혼란이 있기는 했다.<br/>

어쩐지 3884ms가 걸리더라.. 테스트케이스가 많았나보다.<br/>

근데 사실 구현 방법은 간단하다.<br/>

기본적인 투 포인터의 템플릿을 구성한 후<br/>

그냥 투 포인터가 가리키는 두 값의 합이 X가 되면 끝이다.<br/>

|l1 - l2|의 값이 최대인 것을 고르라고 했으니<br/>

정렬 후에 제일 첫번째로 나오는 것을 골라주면 끝이다.<br/>