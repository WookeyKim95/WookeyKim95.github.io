---
layout: post
title:  "[프로그래머스] 숫자 문자열과 영단어 Python"
subtitle:   
date: 2022-05-01 11:10:02 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 숫자 문자열과 영단어 Python<br/>

## 정답제출코드<br/>
<br/>

```
ef solution(s):
    
    s += ' ' # 맨 마지막 문자열까지 for문에서 돌리기 위함.

    num = {'zero':'0',
           'one':'1',
           'two':'2',
           'three':'3',
           'four':'4',
           'five':'5',
           'six':'6',
           'seven':'7',
           'eight':'8',
           'nine':'9'}
    answer = ''
    slice = '' # 문자열을 임시 저장하기 위한 변수

    for i in s:


        ######### 문자열이 num 딕셔너리 안에 있는 key 모양대로 완성될 경우 ###############
        # ex) seven -> 7 등으로 변환할 수 있게 된 경우

        if slice in num.keys():
            answer += num[slice] # seven -> 7로 변환하여 추가.
            slice = '' # slice 변수 초기화

        ########## 정수값을 만났는가?? ############

        if i not in num.values():
            slice += i # 정수값이 아니거나 slice문자열이 완성되지 않았으면 계속 문자열에 문자를 추가.
            
        else: # 정수값을 만났을 경우
            answer += s[s.index(i)]
            s = s[s.index(i)+1:] # 정수를 포함, 이전의 문자열을 삭제함
            # ex) one4seveneight -> seveneight
            # se4ven과 같이 문자열 중간에 알맞지 않게 정수가 들어가는 일이 없기 때문.

    return int(answer) # 문자열에서 정수로 변환함.
```

## 소스코드 설명<br/>
<br/>

one, two 등의 단어를 1, 2 와 같은 단어로 맞춰주기 위해서 문자열을 임시 저장할 변수가 필요했다.<br/>
slice가 그 기능을 했다.<br/>
<br/>

```
        if slice in num.keys():
            answer += num[slice] # seven -> 7로 변환하여 추가.
            slice = '' # slice 변수 초기화
```

우선, 첫 번째로, slice에 저장된 단어가 딕셔너리의 키 값에 있는 경우에 해당 단어에 매치되는 정수로 변환한 후 slice를 초기화하도록 코드를 짰다.<br/>
<br/>

```
        if i not in num.values():
            slice += i # 정수값이 아니거나 slice문자열이 완성되지 않았으면 계속 문자열에 문자를 추가.
```

한편, 정수를 만나지 않는 동안에는 one, two, three, four 등의 단어를 계속해서 slice에 저장할 것이다.<br/>
<br/>

```
        else: # 정수값을 만났을 경우
            answer += s[s.index(i)]
            s = s[s.index(i)+1:] # 정수를 포함, 이전의 문자열을 삭제함
            # ex) one4seveneight -> seveneight
            # se4ven과 같이 문자열 중간에 알맞지 않게 정수가 들어가는 일이 없기 때문.
```

다른 한편, 정수를 만났을 경우에는 정수를 그대로 answer에 추가한다.<br/>
지금 코드 설명을 작성하면서 생각해보니, seven6 등의 문자열이 slice에 들어가는 경우는 없다.<br/>
<br/>
이미 seven6중 seven은 7로 변환되었기 때문이다.<br/>
그래서 이 코드가 성공하지 않았을까 생각이 든다.<br/>
<br/>

## 제출 결과<Br/>
<br/>

![결과](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/coding_test/2022-05-01_1.jpg?raw=true)

또 다시 이왜진이라는 기분을 느꼈다.<br/>
<br/>
