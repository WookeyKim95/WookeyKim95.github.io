---
layout: post
title:  "[프로그래머스] 키패드 누르기 Python"
subtitle:   
date: 2022-04-30 09:52:02 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 키패드 누르기 Python<br/>

## 정답코드<br/>
<br/>

초기에 작성한 것이라 중복코드도 있다. 수정한 것은 맨 아래에.<br/>

```
def solution(numbers, hand):
    
    key_pad =[
        [1,4,7],
        [2,5,8,0],
        [3,6,9],
             ]
    
    answer = ''
    
    l_finger = [1,3] # 엄지손가락들의 처음위치
    r_finger = [1,3]
    
    # 1번 인덱스
    # 엄지 손가락이 몇 행에 위치하여 있는가?
    
    # 0번 인덱스
    # 0 : 각 엄지가 2, 5, 8, 0에 위치할 때
    # 1 : 위치가 3, 6, 9 에 위치할 때
    
    for i in numbers:
        
        if i in key_pad[2]: # 오른손으로 누를 때
            answer += 'R'
            r_finger = [1, key_pad[2].index(i)] # 손가락 위치 재지정
            
        elif i in key_pad[0]: # 왼손으로 누를 때
            answer += 'L'
            l_finger = [1, key_pad[0].index(i)] # 손가락 위치 재지정
            
        elif i in key_pad[1]: # 가운데 줄을 눌러야 할 경우
            
            # 오른손 엄지가 더 멀리 있을 때
            if abs(r_finger[1] - key_pad[1].index(i)) + r_finger[0] > abs(l_finger[1] - key_pad[1].index(i)) + l_finger[0]:
                answer += 'L'
                l_finger = [0, key_pad[1].index(i)] # 손가락 위치 재지정
            
            # 왼손 엄지가 더 멀리 있을 때
            elif abs(r_finger[1] - key_pad[1].index(i)) + r_finger[0] < abs(l_finger[1] - key_pad[1].index(i)) + l_finger[0]:
                answer += 'R'
                r_finger = [0, key_pad[1].index(i)] # 손가락 위치 재지정
            
            # 거리가 같은 경우
            elif abs(r_finger[1] - key_pad[1].index(i)) + r_finger[0] == abs(l_finger[1] - key_pad[1].index(i)) + l_finger[0]:
                if hand == 'left':
                    answer += 'L'
                    l_finger = [0, key_pad[1].index(i)] # 손가락 위치 재지정
                elif hand == 'right':
                    answer += 'R'
                    r_finger = [0, key_pad[1].index(i)] # 손가락 위치 재지정

    return answer
```

## 소스코드 설명<br/>
<br/>

l_finger과 r_finger을 통해서 엄지손가락의 위치를 지정해주었다.<br/>

여기서, 0번 인덱스는 가운데에 위치했는지 아닌지를 지정해주는 변수이다.<br/>

생각해보자!<br/>
만약에 1, 4, 7에 왼손 엄지손가락이 위치했다면 2, 5, 8, 0에 있는 버튼으로부터 멀어질 수 밖에 없다.
그래서 리스트의 첫 번째 변수를 1로 지정해주었다.<br/>

반대로, 엄지손가락이 2, 5, 8, 0에 위치했다면 거리는 자동으로 가까워지게 되므로<br/>
첫 번째 변수는 0으로 변한다.

```
            # 거리가 같은 경우
            elif abs(r_finger[1] - key_pad[1].index(i)) + r_finger[0] == abs(l_finger[1] - key_pad[1].index(i)) + l_finger[0]:
                if hand == 'left':
                    answer += 'L'
                    l_finger = [0, key_pad[1].index(i)]
                elif hand == 'right':
                    answer += 'R'
                    r_finger = [0, key_pad[1].index(i)]
```

위의 코드가 그 기능을 한다.

그리고, l_finger과 r_finger의 1번 인덱스는 위에서 몇 번째 행에 있는지 저장하는 역할을 한다.<br/>

예를 들어, 1은 왼손의 첫행, 3은 오른손의 첫행에 있고,<br/>
4, 6은 각각 2행, 7, 9는 각각 3행에 있다.<br/>

<br/>
<br/>

```
if abs(r_finger[1] - key_pad[1].index(i)) + r_finger[0] > abs(l_finger[1] - key_pad[1].index(i)) + l_finger[0]
```
위의 코드는 현재 손가락의 위치와 눌러야 할 키패드간의 거리를 계산한다.

키패드 배열을 생각하면

```
[1, 2, 3]
[4, 5, 6]
[7, 8, 9]
[*, 0, #]
```
이렇게 위치하므로 

```
    key_pad =[
        [1,4,7],
        [2,5,8,0],
        [3,6,9],
             ]
```

위와 같이 키패드 리스트를 지정해주고, *과 #표시는 3행에 위치해있으므로

```
    l_finger = [1,3] # 엄지손가락들의 처음위치
    r_finger = [1,3]
```
처음 위치를 위와 같이 지정했다.

그렇게 한 뒤, 거리를 비교하거나 버튼의 위치에 따라 손가락들의 위치를 재지정해주면서<br/>
오른손으로 입력할지, 왼손으로 입력할지 결정하였다.<br/>
<br/>

## 제출 결과<Br/>
<br/>

![결과](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/coding_test/2022-04-30-numberpad_1.jpg?raw=true)

이왜진이라는게 이런걸까..?<br/>

놀랬다.. 진짜될 줄 몰랐다..<br/>
<br/>

## 코드 수정 (중복 코드 정리)<br/>
<br/>

복잡하고 긴 중복코드는 없애줘야 할 것!<br/>

```
def solution(numbers, hand):
    
    key_pad =[
        [1,4,7],
        [2,5,8,0],
        [3,6,9],
             ]
    
    answer = ''
    
    l_finger = [1,3] # 엄지손가락들의 처음위치
    r_finger = [1,3]
    
    # 1번 인덱스
    # 엄지 손가락이 몇 행에 위치하여 있는가?
    
    # 0번 인덱스
    # 0 : 각 엄지가 2, 5, 8, 0에 위치할 때
    # 1 : 위치가 3, 6, 9 에 위치할 때
    
    for i in numbers:

        if i in key_pad[2]: # 오른손으로 누를 때
            answer += 'R'
            r_finger = [1, key_pad[2].index(i)] # 손가락 위치 재지정
            
        elif i in key_pad[0]: # 왼손으로 누를 때
            answer += 'L'
            l_finger = [1, key_pad[0].index(i)] # 손가락 위치 재지정
            
        elif i in key_pad[1]: # 가운데 줄을 눌러야 할 경우

            r_distance = r_finger[1] - key_pad[1].index(i)) + r_finger[0]
            l_distance = abs(l_finger[1] - key_pad[1].index(i)) + l_finger[0]

            # 오른손 엄지가 더 멀리 있을 때
            if r_distance > l_distance:
                answer += 'L'
                l_finger = [0, key_pad[1].index(i)] # 손가락 위치 재지정
            
            # 왼손 엄지가 더 멀리 있을 때
            elif r_distance < l_distance:
                answer += 'R'
                r_finger = [0, key_pad[1].index(i)] # 손가락 위치 재지정
            
            # 거리가 같은 경우
            elif r_distance == l_distance:
                if hand == 'left':
                    answer += 'L'
                    l_finger = [0, key_pad[1].index(i)] # 손가락 위치 재지정
                elif hand == 'right':
                    answer += 'R'
                    r_finger = [0, key_pad[1].index(i)] # 손가락 위치 재지정

    return answer
```