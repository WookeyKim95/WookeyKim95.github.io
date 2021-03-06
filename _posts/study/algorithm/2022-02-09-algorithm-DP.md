---
layout: post
title:  "[알고리즘] 동적 계획법(DP)"
subtitle:
date: 2022-02-09 10:32:54 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

# [알고리즘] 동적 계획법(DP)<br/>

## 동적 계획법이란?<br/>

입력 크기가 작은 부분 문제들을 해결한 뒤, 반횐된 값을 활용해서 이전 보다 큰 문제들을 해결해나가면서 최종적으로 전체적 문제를 해결하는 상향식 접근법이다.<br/>

가장 최하위 문제들의 답을 구해놓고, 이 값을 저장해서 다음 단계의 문제의 답을 구하는 형태를 취한다. 이렇게 해놓으면 이전 값을 다시 계산할 필요가 없어 계산속도가 빨라진다.<br/>
<br/>

### 피보나치수열을 동적 계획법으로 해결하기.<br/>

재귀호출 포스트에서 피보나치수열의 풀이를 소개한 적이 있었다.<br/>
하지만 피보나치수열은 동적 계획법으로도 해결이 가능하다.<br/>

- 1번째 값인 1, 2번째 값인 1를 저장해둔다.
- 1 + 1 = 2를 연산하여 3번째 값 2를 구한다.
- 1 + 2 = 3을 연산하여 4번째 값 3을 구한다.
- 2 + 3 = 5를 연산하여 5번째 값 5를 구한다.
- .... 이하 반복

이렇게 맨 처음의 두 값만을 저장하고 두 값을 더하여 다음 값을 구하며, 새로구한 값과 두번째 값을 다시 연산하여 또 다시 새로운 값을 구하는 과정을 반복하고 있다.<br/>

이 과정을 파이썬 코드로 구현하면
```
def fibonacci_dp(n):
    val_1 = 1
    val_2 = 1
    
    for i in range(n-2):
        next_val = val_1 + val_2
        val_1 = val_2
        val_2 = next_val
    
    return next_val
```

이렇게 구현할 수 있다. 그렇다면 잘 작동하는지 이전에 재귀호출에서 썼던 피보나치수열 코드랑 비교를 해보자.<br/>

```
# Test Code
def fibonacci(n):
    if n == 1:
        return 1
    elif n == 2:
        return 1
    elif n >= 3:
        return fibonacci(n-1) + fibonacci(n-2)

def fibonacci_dp(n):
    val_1 = 1
    val_2 = 1
    
    for i in range(n-2):
        next_val = val_1 + val_2
        val_1 = val_2
        val_2 = next_val
    
    return next_val

print(fibonacci_dp(11))
print(fibonacci(11))

print(fibonacci_dp(5))
print(fibonacci(5))

print(fibonacci_dp(35))
print(fibonacci(35))

print(fibonacci_dp(40))
print(fibonacci(40))

# 결과
89
89
5
5
9227465
9227465
102334155
102334155
```
두 결과가 일치하다!<br/>

여기서 신기했던 점은 동적 계획법으로 한 경우는 바로 처리가 된 반면, 재귀호출을 이용하여 피보나치 수열을 계산할 때 35번째의 값을 반환하는데는 약 4초, 40번째의 값을 반환하는데는 약 36초가 소요되었다.<br/>

부득이하게 재귀호출을 사용해야하는 경우가 아니라면 큰 값을 계산할 때에는 동적 계획법을 사용하는 것이 훨씬 좋다.<br/>

![피보나치 계산과정](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-02-09_DP_1.jpg?raw=true)

그림으로 그려보니까 실감이 난다.<br/>

재귀호출을 사용하면 트리안에 있는 노드를 모두 거쳐야하지만, 동적 계획법을 사용한다면 붉은색으로 칠해진 부분은 모두 제외된다.<br/>