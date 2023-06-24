---
layout: post
title:  "[백준] 4344_평균은 넘겠지 Python"
subtitle:   
date: 2023-06-25 08:27:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 4344_평균은 넘겠지 Python<br/>
<br/>

파이썬의 반올림방식 이슈가 있었던 문제<br/>
<br/>

## 기존코드(재채점 후 틀렸습니다)<br/>
<Br/>

```
import sys

a = int(input())
for i in range(a):
    b = list(map(int, sys.stdin.readline().split()))
    aver = sum(b[1:]) / b[0]
    count = 0
    for j in b[1:]:
        if j > aver:
            count += 1
    print('%.3f%%' %(count * 100 / b[0]))
```

보통 재채점 하면 재채점 했다고 알림이 오는데 음.. 뭐지 했다.<br/>

언제부턴가 틀렸습니다로 표기되어 있길래 놀랬다.<br/>

그런데 틀릴 이유가 없는데 왜 틀린거지? 생각을 했는데<br/>

파이썬의 round_half_even 방식의 반올림이 문제가 되었던 것이다.<br/>
<br/>

## 파이썬의 반올림 방식<br/>
<Br/>

우리가 흔히 알고있는 반올림 대상 값이 5일 때에는 올려주는 방식이 아닌,<br/>

반올림 대상 값이 5일때

- 앞자리가 홀수라면 올린다.
- 앞자리가 짝수라면 내린다.

이런 방식을 채택한다.<br/>

즉, 아래 코드에서 소수점 첫번째 자리에서 반올림한다고 할때

```
round(3.5) // 출력값 : 4
round(4.5) // 출력값 : 4
round(5.5) // 출력값 : 6
round(6.5) // 출력값 : 6
```

과 같이 출력된다.<br/>
<br/>

## 재제출 정답코드<br/>

```
import math

a = int(input())

for i in range(a):
    b = list(map(int, input().split()))
    aver = sum(b[1:]) / b[0]
    count = 0
    
    for j in b[1:]:
        if j > aver:
            count += 1
    
    result = count/b[0] * 100
    ForTrunc4 = math.floor(result * 10000)
    
    if ForTrunc4 % 10 >= 5:
        ForTrunc4 = ForTrunc4 + 10
    
    ToPrint = math.floor(ForTrunc4 / 10) / 1000
    print('%.3f' %(ToPrint) + '%')
    
    
```

우선, 소숫점 넷째자리 까지 남겨두기 위해서 10000을 곱한 뒤 floor함수를 사용하였다.<br/>

그리고 10000을 곱하면 기존의 소숫점 넷째자리 숫자가 1의 자리로 갈 것이다.<br/>

이 때 그 숫자가 5이상이라면 올리고, 그렇지 않다면 내리도록 하기 위해서<br/>

아래의 코드를 구현하였다.<br/>

```
if ForTrunc4 % 10 >= 5:
    ForTrunc4 = ForTrunc4 + 10

ToPrint = math.floor(ForTrunc4 / 10) / 1000
```


그리고 소숫점 셋째자리까지 프린트하면 끝!
<br/>

### 주저리 주저리<br/>
<br/>

뭐하러 데이터를 추가했나 싶다..<br/>

아니면 지문이라도 수정하던가.. 투덜투덜..<br/>