---
layout: post
title:  "[백준] 10830_행렬 제곱 C++"
subtitle:   
date: 2023-06-11 10:21:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 10830_행렬 제곱 C++<br/>
<br/>

분할 정복을 이용한 거듭제곱으로 풀어보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

typedef long long ll;
typedef vector<vector<ll>> llmat;

ll N, B;

llmat matrix_mul(llmat a, llmat b)
{
    llmat ret;
    for (int i = 0; i < N; ++i)
    {
        vector<ll> row;

        for (int j = 0; j < N; ++j)
            row.push_back(0);
        
        ret.push_back(row);
    }

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            for (int k = 0; k < N; ++k)
            {
                ret[i][j] += (a[i][k] * b[k][j]);
            }
            ret[i][j] = ret[i][j] % 1000;
        }
    }

    return ret;
}

int main()
{
    cin >> N >> B;

    llmat Mat, AnsMat;

    for (int i = 0; i < N; ++i)
    {
        vector<ll> row;
        vector<ll> AnsRow;

        for (int j = 0; j < N; ++j)
        {
            int _input;
            cin >> _input;

            row.push_back(_input);

            if (i == j)
                AnsRow.push_back(1);
            else
                AnsRow.push_back(0);
        }
        Mat.push_back(row);
        AnsMat.push_back(AnsRow);
    }

    while (B > 0)
    {
        if (B % 2 == 1)
            AnsMat = matrix_mul(AnsMat, Mat);
        
        Mat = matrix_mul(Mat, Mat);
        B /= 2;
    }

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
            cout << AnsMat[i][j] << ' ';
        cout << '\n';
    }

    return 0;
}
```
<br/>

거듭 제곱을 분할제곱으로 풀어본다는 것은 처음 접해보았다.<br/>

그게 대체 무슨소리일까? 생각을 해보았는데<br/>

오호라! 이런 느낌인 것이다.<br/>

행렬 A가 있다고 하면 A의 6제곱은 AAAAAA로 나타낼 수 있다.<br/>

이때, 두개씩 묶어준다면 (AA)(AA)(AA)이렇게 묶어주는 것이 가능하다.<br/>

즉, C = AA라고 한다면 CCC 이렇게 묶어줌으로써 거듭제곱의 횟수를 줄이는 것이 가능하다.<br/>

만약 A의 5제곱 AAAAA라고 한다면 A는 미리 정답행렬에 곱해놓고 AAAA만을 남긴다.<br/>

그 다음에 묶어주는 방식으로 분할정복을 이용한 거듭제곱을 진행할 수 있다.<br/>

그 부분이 바로 이부분!

```
while (B > 0)
{
    if (B % 2 == 1)
        AnsMat = matrix_mul(AnsMat, Mat);
    
    Mat = matrix_mul(Mat, Mat);
    B /= 2;
}
```