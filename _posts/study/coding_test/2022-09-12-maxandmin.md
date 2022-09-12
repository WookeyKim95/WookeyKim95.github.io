---
layout: post
title:  "[프로그래머스] 최댓값과 최솟값 C++"
subtitle:   
date: 2022-09-12 15:15:49 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 최댓값과 최솟값 C++<br/>
<br/>

C++의 STL 중에서 vector의 활용을 연습하기 위해서 풀어보았음.<br/>
그리고 to_string과 stoi 함수를 활용하였음.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>
#include <sstream>
#include <string>
#include <vector>

using namespace std;

vector<int> split(string input, char delimiter)
{
    vector<int> answer;
    stringstream ss(input);
    string temp;
    
    while (getline(ss, temp, delimiter))
    {
        answer.push_back(stoi(temp));
    }
    
    return answer;
}

string solution(string s) {
    
    vector<int> numbers = split(s, ' ');
    
    sort(numbers.begin(), numbers.end());
    
    string answer = "";
    
    answer = to_string(numbers[0]) + ' ' + to_string(numbers[numbers.size() - 1]);
    
    return answer;
}
```

<br/>

## 소스코드 설명<br/>
<br/>

- int를 string으로 바꾸는 함수 to_string과 string을 int로 바꾸는 함수 stoi를 사용함.<br/>
이 함수들을 사용하기 위해서 string과 sstream 헤더를 불러옴.

- split함수를 구현하여 string 형태를 받아서 ' '칸에 따라서 분해한 뒤 int 형태로 vector에 들어가도록 조치하엿음. 이때 stoi 함수가 사용됨.

- vector에는 int형 자료가 있으므로 sort를 이용해서 vector를 정렬함.

- 정렬한 vector에서 첫 인덱스와 마지막 인덱스에 있는 아이템을 뽑아서 to_string 함수를 이용해서 answer에 반환함.

- [참고 사이트](https://codecollector.tistory.com/999)