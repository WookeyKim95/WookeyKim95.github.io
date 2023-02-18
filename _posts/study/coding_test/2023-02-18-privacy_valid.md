---
layout: post
title:  "[프로그래머스] 개인정보 수집 유효기간 C++"
subtitle:   
date: 2023-02-18 13:11:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 개인정보 수집 유효기간 C++<br/>
<br/>

2023년 카카오 블라인드 리쿠르팅 기출문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>
#include <vector>
#include <map>
#include <iostream>

using namespace std;

map<string, int> split_day(string s)
{
    map<string, int> days;
    
    int len = s.length();
    string tmp;
    
    for (int i = 0; i < len; ++i)
    {
        if (i == 4)
        {
            days.insert({"year", stoi(tmp)});
            tmp = "";
            continue;
        }
        if (i == 7)
        {
            days.insert({"month", stoi(tmp)});
            tmp = "";
            continue;
        }
        tmp += s[i];
        if (i == len-1)
        {
            days.insert({"day", stoi(tmp)});
        }
    }
    return days;
}

vector<int> solution(string today, vector<string> terms, vector<string> privacies) {
    vector<int> answer;
    
    // 오늘의 날짜 조사
    map<string, int> todayMap = split_day(today);
    map<char, int> termsMap;
    
    // 유효기간 조사 및 맵에 저장
    int terms_size = terms.size();
    for (int i = 0; i < terms_size; ++i)
    {
        // 1개월~9개월
        if (terms[i].length() == 3)
        {
            // 아스키코드를 정수로 변환
            termsMap.insert({terms[i][0], terms[i][2] - '0'});
        }
        
        // 10개월 ~ 99개월
        else if (terms[i].length() == 4)
        {
            // 아스키코드를 정수로 변환
            int tmp = (terms[i][2] - '0') * 10 + terms[i][3] - '0';
            termsMap.insert({terms[i][0], tmp});
        }
        
        // 100개월
        else if (terms[i].length() == 5)
            termsMap.insert({terms[i][0], 100});
    }
    
    // privacies에 들어온 날짜를 map에다 정리
    int priLen = privacies.size();
    for (int i = 0; i < priLen; ++i)
    {
        // 날짜를 분리해서 map에 저장
        string tmp = privacies[i].substr(0, 10);
        map<string, int> curPrivacy = split_day(tmp);
        
        // 현재 privacy의 약관 추출
        char curTerm = privacies[i][11];
        
        // 경과 날짜를 일 단위로 계산
        int termToday = ((todayMap.find("year")->second - curPrivacy.find("year")->second) * 12 * 28) +
                    ((todayMap.find("month")->second - curPrivacy.find("month")->second) * 28) +
                    (todayMap.find("day")->second - curPrivacy.find("day")->second);
        
        
        // 현재 약관의 유효기간을 일 단위로 변환
        int validDays = termsMap.find(curTerm)->second * 28;
        
        // 유효 기간이 지났을 경우 (경과 날짜가 보관일수보다 클 경우)
        if (termToday >= validDays)
            answer.push_back(i+1);
    }
    
    return answer;
}
```

실전 코딩테스트때는 파이썬으로 풀었지만 이번엔 C++로 풀어보았다.<br/>

분리(split) 작업을 구현하는데 꽤 애를 먹었다.<br/>

특히, 일정한 규칙이 있어서 다행이지 string에 있는 정수를 빼내오는데 애를 먹었다.<br/>

파이썬에서는 split함수를 쓰면 문자열 내 정수를 자동으로 분리할 수 있었는데 C++에서는 직접 구현해야했던 것이 힘들었다.<br/>

그리고 년 월 일 을 모두 일 단위로 계산해서 유효기간이 지났는지 안지났는지 여부를 체크하였다.
<br/>
<br/>

## 오답제출코드<br/>
<br/>

```
#include <string>
#include <vector>
#include <map>
#include <iostream>

using namespace std;

map<string, int> split_day(string s)
{
    map<string, int> days;
    
    int len = s.length();
    string tmp;
    
    for (int i = 0; i < len; ++i)
    {
        if (i == 4)
        {
            days.insert({"year", stoi(tmp)});
            tmp = "";
            continue;
        }
        if (i == 7)
        {
            days.insert({"month", stoi(tmp)});
            tmp = "";
            continue;
        }
        tmp += s[i];
        if (i == len-1)
        {
            days.insert({"day", stoi(tmp)});
        }
    }
    return days;
}

vector<int> solution(string today, vector<string> terms, vector<string> privacies) {
    vector<int> answer;
    
    // 오늘의 날짜 조사
    map<string, int> todayMap = split_day(today);
    map<char, int> termsMap;
    
    // 유효기간 조사 및 맵에 저장
    int terms_size = terms.size();
    for (int i = 0; i < terms_size; ++i)
    {
        // 1개월~9개월
        if (terms[i].length() == 3)
        {
            // 아스키코드를 정수로 변환
            termsMap.insert({terms[i][0], terms[i][2] - '0'});
        }
        
        // 10개월 ~ 12개월
        else if (terms[i].length() == 4)
        {
            // 아스키코드를 정수로 변환
            int tmp = 10 + terms[i][3] - '0';
            termsMap.insert({terms[i][0], tmp});
        }
    }
    
    // privacies에 들어온 날짜를 map에다 정리
    int priLen = privacies.size();
    for (int i = 0; i < priLen; ++i)
    {
        // 날짜를 분리해서 map에 저장
        string tmp = privacies[i].substr(0, 10);
        map<string, int> curPrivacy = split_day(tmp);
        
        // 현재 privacy의 약관 추출
        char curTerm = privacies[i][11];
        
        // iterTerm에 현재 privacy 추출함.
        auto iterTerm = termsMap.find(curTerm);
        
        // 경과 날짜를 일 단위로 계산
        int termToday = ((todayMap.find("year")->second - curPrivacy.find("year")->second) * 12 * 28) +
                    ((todayMap.find("month")->second - curPrivacy.find("month")->second) * 28) +
                    (todayMap.find("day")->second - curPrivacy.find("day")->second);
        
        
        // 현재 약관의 유효기간을 일 단위로 변환
        int validDays = iterTerm->second * 28;
        
        // 유효 기간이 지났을 경우 (날짜가 보관일수보다 클 경우)
        if (termToday >= validDays)
            answer.push_back(i+1);
    }
    
    return answer;
}
```

유효기간의 최대값이 12개월까지라고 생각했었다.<br/>

주의사항 유효기간의 최대값이 100개월 이었다.<br/>