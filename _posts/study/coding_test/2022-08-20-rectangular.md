---
layout: post
title:  "[프로그래머스] 최소 직사각형 C++"
subtitle:   
date: 2022-08-20 16:03:14 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 최소 직사각형 C++<br/>
<br/>

C++의 STL 중에서 vector의 활용을 연습하기 위해서 풀어보았음.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

int solution(vector<vector<int>> sizes) {
    
    for (int i = 0; i < sizes.size(); i++)
    {
        if (sizes[i][0] < sizes[i][1])
        {
            int temp = sizes[i][0];
            sizes[i][0] = sizes[i][1];
            sizes[i][1] = temp;
        }

    }
    
    vector<int> line1;
    vector<int> line2;
    
    for (int i = 0; i < sizes.size() ; i++)
    {
        line1.push_back(sizes[i][0]);
        line2.push_back(sizes[i][1]);
    }
    
    int max1 = *max_element(line1.begin(), line1.end());
    int max2 = *max_element(line2.begin(), line2.end());
        
    int answer = max1 * max2;
    return answer;
}
```

<br/>

## 소스코드 설명<br/>
<br/>

- line1에는 가로의 길이를, line2에는 세로의 길이를 보관하는 형태로 코드를 지정함.

- 명함의 길이를 뒤집어야 할 경우를 생각하였음. 이 때, 명함을 뒤집어보는 경우는 세로의 길이가 가로의 길이보다 길 때임.

- 이때, sizes[i][0]과 sizes[i][1]을 바꿔주어 가로와 세로의 길이를 뒤집음.

- 명함을 알맞게 뒤집은 후에 line1과 line2에 가로, 세로 길이를 넣고, 최대값을 구함.

- 구한 최대값 2개를 곱해서 반환함.