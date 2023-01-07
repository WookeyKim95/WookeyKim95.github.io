---
layout: post
title:  "[프로그래머스] 이모티콘 할인행사 C++"
subtitle:   
date: 2023-01-07 09:06:45 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 이모티콘 할인행사 C++<br/>
<br/>

완전 탐색, dfs 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>
#include <vector>
#include <iostream>

using namespace std;

vector<int> emo_price;
vector<int> discount_arr;

vector<int> user_discount;
vector<int> user_emoplus;

int discount[4] = {10, 20, 30, 40};

int emo_plus = 0;
int sale = 0;

void price_cal()
{   
    int emo_plus_temp = 0;
    int price_temp = 0;
    
    for (int i = 0; i < user_discount.size(); ++i)
    {
        // 유저별 총 구매가격
        int price_sum = 0;
        
        for (int j = 0; j < discount_arr.size(); ++j)
        {
            // j번째 이모티콘 할인율이 유저의 의향보다 높을 경우
            if (discount_arr[j] >= user_discount[i])
            {
                // i번째 유저가 j번째 이모티콘을 할인된 가격에 구매
                price_sum += emo_price[j] * (100 - discount_arr[j]) / 100;
            }
        }
        
        // 유저의 의향보다 이모티콘 가격이 더 나왔을경우 임티플 가입
        if (price_sum >= user_emoplus[i])
        {
            emo_plus_temp++;
            price_sum = 0;
        }
        
        // 그렇지 않을경우 매출액에 저장
        else
        {
            price_temp += price_sum;
        }

        // 이모티콘 플러스 유저 가입수 바꾸기
        // 그리고 그때의 매출액 저장
        if (emo_plus_temp >= emo_plus)
        {
            // 가격 초기화
            if (emo_plus_temp > emo_plus)
            {
                sale = 0;
            }
            emo_plus = emo_plus_temp;
            
            // 가격 새로 저장            
            if (price_temp >= sale)
                sale = price_temp;
        }
    }
    
}

// 할인율 적용 경우의수 따지는 것을 dfs로 적용
void dfs(int cnt, int len)
{
    if (cnt == len)
    {
        // 할인율을 모두 적용했을때 계산 실시
        price_cal();
        return;
    }
        
    for (int i = 0; i < 4; ++i)
    {
        discount_arr.push_back(discount[i]);
        dfs(cnt+1, len);
        discount_arr.pop_back();
    }
}

vector<int> solution(vector<vector<int>> users, vector<int> emoticons) {
    vector<int> answer;
    
    int users_count = users.size();
    int emo_len = emoticons.size();
    
    emo_price = emoticons;
    
    for (int i = 0; i < users_count; ++i)
    {
        user_discount.push_back(users[i][0]);
    }
    
    for (int i = 0; i < users_count; ++i)
    {
        user_emoplus.push_back(users[i][1]);
    }
    
    dfs(0, emo_len);
    
    answer.push_back(emo_plus);
    answer.push_back(sale);
    
    return answer;
}
```

이모티콘 별 할인율 적용에 dfs를 사용하였다.<br/>

그리고 가격을 새로 산정할 때 조금 애먹었던 것 같다.<br/>

지난번 실제 지원을 했을 때 실전에서는 풀지 못했던 것 같은데 여유를 가지고 생각을 해보니 dfs를 이용하여 풀 수 있었다.<br/>
