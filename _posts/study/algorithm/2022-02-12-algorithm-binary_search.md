---
layout: post
title:  "[알고리즘] 이진 탐색"
subtitle:
date: 2022-02-12 11:18:35 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

# [알고리즘] 이진 탐색<br/>

## 이진 탐색이란?<br/>

찾고자하는 값(Target)을 정한 뒤, 리스트의 중간 값과 비교한다. 맞으면 True를, 틀리면 리스트를 중간 값을 기준으로 반으로 나눈 후에 위 과정을 반복한다.<br/>
만약 리스트의 길이가 1이거나 0이 되었음에도 불구하고 값을 찾지 못하면 False를 반환한다.<br/>

![이진탐색과정](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-02-12_binary_search_1.jpg?raw=true)
<br/>

## 파이썬 코드로 구현

### 기본 이진 탐색<br/>

```
# 사용자 정의 함수
def binary_search(list_input, value):
    medium = len(list_input) // 2
    
    if list_input[medium] == value:
        return True
    
    else:
        if len(list_input) <= 1:
            return False
        if binary_search(list_input[medium:], value):
            return True
        else:
            return binary_search(list_input[:medium], value)
```
사용자 정의함수로는 위와 같이 구현할 수 있으며

```
# Test Code

import random

for i in range(10):
    sample = random.sample(range(25), 15)
    target = random.randint(0, 25)
    
    print(sample)
    print(target)
    print(binary_search(sample, target))
    print()

# Test Result

[13, 4, 1, 14, 17, 21, 12, 18, 24, 20, 6, 15, 7, 2, 3]
5
False

[20, 9, 0, 23, 22, 12, 21, 16, 14, 3, 13, 10, 4, 24, 11]
25
False

[15, 0, 4, 14, 1, 21, 20, 7, 11, 12, 6, 19, 22, 2, 18]
6
True

[8, 0, 24, 11, 23, 5, 20, 9, 22, 10, 13, 18, 6, 3, 15]
18
True

[16, 3, 15, 9, 2, 18, 4, 8, 14, 0, 13, 22, 6, 10, 12]
24
False

[6, 7, 16, 20, 11, 4, 9, 1, 19, 2, 5, 13, 18, 3, 12]
14
False

[8, 12, 7, 13, 15, 9, 0, 24, 14, 23, 18, 21, 1, 19, 4]
23
True

[12, 22, 3, 20, 16, 2, 17, 1, 24, 10, 9, 4, 11, 7, 0]
12
True

[11, 19, 14, 1, 5, 2, 20, 18, 12, 23, 7, 4, 17, 8, 21]
7
True

[16, 22, 20, 14, 8, 5, 4, 6, 3, 17, 10, 18, 21, 19, 11]
9
False
```
잘 작동하는 것을 확인할 수 있었다.<br/>
<br/>

### 정렬된 리스트에 대한 이진 탐색

참고 링크: [잔재미코딩_이진탐색](https://fun-coding.org/Chapter16-binarysearch.html)

정렬된 상태라면 찾고자 하는 값이 중간값보다 높은 곳에 있을 테니 중간에 중간 값과 타겟을 비교하는 과정을 추가하면 반으로 나눈 리스트 중에서 한 쪽만 탐색하면 되기 때문에 더 빠르게 찾을 수 있을 것이다.<br/>

```
def binary_search(list_input, value):
    
    
    if len(list_input) <= 1:
        if len(list_input) == 0:
            return False
        else:
            if list_input[0] == value:
                return True
            else:
                return False
    
    else:
        medium = len(list_input) // 2
        if list_input[medium] == value:
            return True
        else:
            if value < list_input[medium]:
                return binary_search(list_input[:medium], value)
            else:
                return binary_search(list_input[medium:], value)
```
위와 같이 작성할 수 있다.

```
# Test Code

import random

for i in range(10):
    sample = random.sample(range(25), 15)
    target = random.randint(0, 25)
    
    print(sample)
    print(target, binary_search(sample, target))
    print()

# Test Result
[0, 1, 6, 8, 11, 12, 15, 16, 17, 18, 19, 21, 22, 23, 24]
14 False

[1, 2, 3, 4, 5, 6, 9, 13, 14, 15, 16, 17, 18, 20, 21]
12 False

[1, 3, 4, 5, 7, 9, 10, 11, 14, 16, 17, 20, 22, 23, 24]
23 True

[0, 2, 4, 5, 7, 9, 12, 13, 16, 18, 19, 20, 21, 22, 23]
9 True

[2, 3, 5, 6, 7, 9, 14, 15, 17, 18, 19, 21, 22, 23, 24]
1 False

[0, 1, 5, 6, 8, 9, 10, 12, 15, 16, 18, 20, 22, 23, 24]
8 True

[0, 1, 3, 4, 5, 7, 8, 9, 13, 14, 15, 17, 20, 23, 24]
12 False

[0, 1, 3, 5, 7, 8, 9, 10, 11, 12, 14, 19, 20, 21, 23]
1 True

[1, 2, 6, 8, 9, 10, 11, 12, 13, 15, 19, 20, 21, 22, 24]
23 False

[0, 1, 2, 3, 4, 6, 8, 10, 11, 13, 14, 15, 16, 21, 22]
23 False

```
10회 테스트를 한 결과 잘 작동한다.<br/>

**하지만 이 코드는 시간을 조금 더 단축시킬 수 있는 대신 정렬된 코드에서만 유효하게 동작한다는 것을 주의하자.** 

## 코딩 오류 수정

### 1. 재귀호출을 할 때에는 복사 붙여넣기를 생활화하자.<br/>
```
# 수정 전 코드

    else:
        if len(list_input) == 1:
            return False
        if binary_search(list_input[medium:]):
            return True
        else:
            return binary_search(list_input[:medium])

# 발생 오류

TypeError: binary_search() missing 1 required positional argument: 'value'
```
재귀호출시 입력값이 2개인데 1개만 넣었다. 직접 입력하는 바람에 이런 실수를 많이 하는 것 같다.<br/>
<br/>

### 2. len = 0인 리스트가 input된 경우를 항상 염두하자.<br/>


```
# 발생 버그
list : [12, 25, 42, 6, 28, 22, 33, 37, 26, 46, 15, 10, 20, 0, 38, 16, 41, 18, 7, 45]
Target : 44
Return : True

리스트에 44가 없지만 True가 반환되었음.

# 버그 발생 원인 및 수정 전 코드

    else:
        if len(list_input) == 1:
            return False

# 수정 후 코드

    else:
        if len(list_input) <= 1:
            return False
(마지막으로 input이 된 리스트가 []인 경우에도 False 반환하도록 조치)

# 같은 리스트와 타겟 값으로 재 테스트

# Test Code
print(binary_search([12, 25, 42, 6, 28, 22, 33, 37, 26, 46, 15, 10, 20, 0, 38, 16, 41, 18, 7, 45], 44))

# 결과
False

# 버그가 픽스되었음.
```


