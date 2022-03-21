---
layout: post
title:  "[코딩 도전] 일본의 연호 계산"
subtitle:   
date: 2022-03-20 09:03:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

## 일본은 공식적으로 연호를 사용한다.<br/>
<br/>

### 코딩 도전해보기<br/>

일본은 날짜를 계산할 때 연호를 사용한다.<br/>
이는 민간뿐만 아니라 공문서에도 적용된다.<br/>

그래서<br/>

```
양력 1990년 : 헤이세이 2년, H2
양력 2019년 : 레이와 원년, R1
```

과 같이 표기된다.

또한, 개막, 종막일을 기점으로 연호가 구분된다.<br/>

예시를 들어보자.<br/>

레이와 시대는 2019년 5월 1일에 개막했다.<br/>
그래서 2019년 4월 30일까지는 헤이세이 시대인 것이다.<br/>


따라서, 같은 2019년 이라고 하더라도, <br/>
```
2019년 4월 29일 : 헤이세이 31년 4월 29일
2019년 6월 2일 : 레이와 원년 6월 2일
```

위와 같이 표기된다.

그래서 파이썬을 통해 양력을 입력하면 일본 내에서 어떤 연호를 사용하고,<br/>
몇 년인지 구하는 프로그램 작성에 도전해보고자 한다.

```
입력 데이터
년도 : 1900 ~ 2022
월 : 1 ~ 12
일 : 1 ~ 30


참고 데이터

1900년 : 메이지 33년
메이지 시대 종막일 : 1912년 7월 29일

다이쇼 시대 개막일 : 1912년 7월 30일
다이쇼 시대 종막일 : 1926년 12월 24일

쇼와 시대 개막일 : 1926년 12월 25일
쇼와 시대 종막일 : 1989년 1월 7일

헤이세이 시대 개막일 : 1989년 1월 8일
헤이세이 시대 종막일 : 2019년 4월 30일

레이와 시대 개막일 : 2019년 5월 1일

출력 데이터 : 무슨시대이며, 몇년인가? (1년일 경우 원년을 출력한다.)

ex) 프로그램 입 출력 예시

1989 1 10 -> 헤이세이 원년
2018 4 30 -> 헤이세이 30년
```
<br/>

그리고 내가 구현한 파이썬 코드는 아래와 같다.

```
# 이니셜을 이름으로 고치는 함수
def print_year(A, num):
    if A == 'M':
        B = '메이지'
    elif A == 'D':
        B = '다이쇼'
    elif A == 'S':
        B = '쇼와'
    elif A == 'H':
        B = '헤이세이'
    elif A == 'R':
        B = '레이와'
    
    if num == 1:
        C = '원'
    else:
        C = str(num)
    
    print(B, C+'년')
    
    return

from datetime import datetime

input_Date = list(map(int, input().split()))

now = datetime.now()

now_YMD = [now.year, now.month, now.day, now.year - 2019 + 1]

data_to_compare = [input_Date[0], input_Date[1], input_Date[2]]

# 데이터 베이스 : [개막년, 개막월, 개막일, 년도], [종막년, 종막월, 종막일, 종막시 년도]
japan_years = { 
                'M':[[1900,1,1,33],[1912,7,29,45]],
                'D':[[1912,7,30,1],[1926,12,24,15]],
                'S':[[1926,12,25,1],[1989,1,7,64]],
                'H':[[1989,1,8,1],[2019,4,30,31]],
                'R':[[2019,5,1,1],now_YMD]
               }

japan_year_names = ['M', 'D', 'S', 'H', 'R']

# 년도 비교

for i in japan_year_names:
    
    # 년도가 경계 값에 포함되지 않을 경우
    if japan_years[i][0][0] <  data_to_compare[0] < japan_years[i][1][0]:
        print_year(i, data_to_compare[0] - japan_years[i][0][0] + 1)
    
    # 입력 값의 년도가 현재 년도와 같다면 레이와 시대이다.
    elif now_YMD[0] == data_to_compare[0]:
        print_year('R', now_YMD[-1])
        break
    
    # 년도가 경계 값에 포함될 경우 원년인지 아닌지 봐야한다. 월을 비교한다.
    elif japan_years[i][0][0] == data_to_compare[0]:

        # 경계 값 중 1900년이면 메이지 33년이다. 바로 출력하고 끝내면 된다.
        if data_to_compare[0] == 1900:
            print('메이지 33년')
            break
        
        # 월이 경계 값보다 클 경우 원년이다. 그대로 키 값을 반환하면 된다.
        elif data_to_compare[1] > japan_years[i][0][1]:
            print_year(i, 1)
            break
        
        # 월이 경계 값에 포함될 경우 일을 비교한다.
        elif japan_years[i][0][1] == data_to_compare[1]:
            
            # 일이 경계 값 보다 크면 원년이다.
            if data_to_compare[2] >= japan_years[i][0][2]:
                print_year(i, 1)
                break
            
            # 그렇지 않으면 이전 시대를 출력한다.
            else:
                print_year(japan_year_names[japan_year_names.index(i)-1],
                      japan_years[japan_year_names[japan_year_names.index(i)-1]][1][3])
                break
        # 월이 경계값보다 작으면 이전 시대를 출력한다.
        else:
            print_year(japan_year_names[japan_year_names.index(i)-1],
                  japan_years[japan_year_names[japan_year_names.index(i)-1]][1][3])
            break
```

으으.. 뭔가 난해한 것 같다.<br/>

for문과 if문, list와 dictionary를 이용해서 충분히 구현할 수 있다고 생각하였고,

테스트 결과는 아래와 같다.

```
2022 3 15
레이와 4년

2019 4 17
헤이세이 31년

2019 5 2
레이와 원년

1900 3 1
메이지 33년

1919 2 7
다이쇼 8년

1968 7 23
쇼와 43년

1926 12 24
다이쇼 15년
```
위는 직접 숫자를 입력해본 것이고,

```
import random

input_year = random.randint(1900,2023)
input_month = random.randint(1, 13)
input_day = random.randint(1, 31)

data_to_compare = [input_year, input_month, input_day]
print(data_to_compare)
```

위와 같이 난수를 도입해서 돌려보기도 했다.<br/>

음.. 일단 30일, 31일과 윤년은 구분하진 않았다.<br/>
나중에 시간 나면 업데이트 해야지.<br/>

난수를 통해 돌려본 테스트케이스는 아래와 같다.
```
[1996, 11, 11]
헤이세이 8년

[1973, 2, 10]
쇼와 48년

[1951, 13, 3]
쇼와 26년

[1904, 9, 5]
메이지 5년

[1921, 12, 8]
다이쇼 10년

[1953, 12, 10]
쇼와 28년

[1924, 5, 19]
다이쇼 13년

[2017, 3, 28]
헤이세이 29년

[1908, 5, 31]
메이지 9년

[1929, 7, 3]
쇼와 4년
```
오류 없이 잘 구동된 것 같다.<br/>


### 소감<br/>

뭔가 아쉽다.<br/>

내 머리로는 저렇게 짜는 것이 한계인 것 같다.<br/>
조금 더 머리가 좋았다면 저 코드를 더욱 간단하게 줄일 수 있을 것 같았고, 윤년과 30일, 31일 등의 구분을 구현하지 못한 점이 아쉽게 남는다.<br/>

나중에 코드 리뷰를 받을 기회가 있다면 받은 뒤 고쳐봐야지.