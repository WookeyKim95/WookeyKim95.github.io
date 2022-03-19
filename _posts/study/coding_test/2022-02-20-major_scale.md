---
layout: post
title:  "[코딩 도전] 간단한 major scale 구현"
subtitle:   
date: 2022-02-20 16:10:27 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

### C Major Scale인 상태에서 다른 Major Scale로 변환하는 코드를 작성하시오.<br/>

우리가 '도'라고 알고있었던 소리의 공식적인 이름은 C이다.<br/>

key_board = ['C','C#','D','D#','E','F','F#','G','G#','A','A#','B']<br/>
위의 리스트가 절대적인 음정을 나타내고,<br/>

major_scale_key = ['도', '도#', '레', '레#', '미', '파', '파#', '솔', '솔#', '라', '라#', '시']<br/>

위의 리스트가 상대적인 음정을 나타내는 것이다.<br/>

즉, F Major Scale은 F라는 음을 도 자리에 놓는 음계이다.<br/>
이때 '레' 자리에는 G, 즉 우리가 평소 '솔'이라고 알고있던 음이 들어간다.<br/>


### 파이썬 코드로 구현해보기.<br/>

그러면 큐를 이용해서 major scale 별 음정을 출력할 수 있을 것이라 생각했다.

예를 들어,
```
F Major Scale을 출력하면

{'도': 'F', '도#': 'F#', '레b': 'F#', '레': 'G', '레#': 'G#', '미b': 'G#',
 '미': 'A', '파': 'A#', '파#': 'B', '솔b': 'B', '솔': 'C', '솔#': 'C#',
  '라b': 'C#', '라': 'D', '라#': 'D#', '시b': 'D#', '시': 'E'}
```
이렇게 출력할 수 있을 것이다.<br/>

```
# 절대음정 구성
key_board = ['C','C#','D','D#','E','F','F#','G','G#','A','A#','B']
# 상대음정 구성
major_scale_key = ['도', '도#', '레', '레#', '미', '파', '파#', '솔', '솔#', '라', '라#', '시']

# 기본 상태는 C Major Scale
major_scale = {'도':'C', '도#':'C#', '레b':'C#', '레':'D', '레#':'D#', '미b':'D#', '미':'E',
               '파':'F', '파#':'F#', '솔b':'F#', '솔':'G', '솔#':'G#', '라b':'G#', '라':'A',
               '라#':'A#','시b':'A#','시':'B'}

Current_major_key = key_board

key_major = input('Major Scale의 1음은 무엇입니까?')

if key_major in key_board:
    # 큐를 이용하여 음정이 맞춰질 때 까지 음계 조정.
    while key_major != Current_major_key[0]:
        move_key = Current_major_key.pop(0)
        Current_major_key.append(move_key)
    
    for i in range(len(major_scale_key)):
        major_scale[major_scale_key[i]] = Current_major_key[i]
    
    
    ## 이명 동음 처리
    major_scale['레b'] = major_scale['도#']
    major_scale['미b'] = major_scale['레#']
    major_scale['솔b'] = major_scale['파#']
    major_scale['라b'] = major_scale['솔#']
    major_scale['시b'] = major_scale['라#']
    
    print(major_scale)
else:
    print('다시 입력하세요')
    
```
기본 상태는 C Major Scale, 즉 우리가 평소 알고있던 그 상태이다.<br/>

```
# Test Case 1

Major Scale의 1음은 무엇입니까?F

# Result 1
{'도': 'F', '도#': 'F#', '레b': 'F#', '레': 'G', '레#': 'G#', '미b': 'G#',
 '미': 'A', '파': 'A#', '파#': 'B', '솔b': 'B', '솔': 'C', '솔#': 'C#',
  '라b': 'C#', '라': 'D', '라#': 'D#', '시b': 'D#', '시': 'E'}

# Test Case 2

Major Scale의 1음은 무엇입니까?

# Result 2
Major Scale의 1음은 무엇입니까?E
{'도': 'E', '도#': 'F', '레b': 'F', '레': 'F#', '레#': 'G', '미b': 'G',
 '미': 'G#', '파': 'A', '파#': 'A#', '솔b': 'A#', '솔': 'B', '솔#': 'C',
  '라b': 'C', '라': 'C#', '라#': 'D', '시b': 'D', '시': 'D#'}
```
의도한대로 잘 출력된다.<br/>

다만 여기서 해결해야할 과제는 **이명동음처리**이다.

### 이명동음이란?<br/>

피아노 건반을 보면 레#과 미b(플렛)이 같은 음정을 나타낸다.<br/>
이런 종류가 총 5개가 존재한다. 같은 음을 내지만 이름이 다른 경우를 이명동음이라고 한다.<br/>

위 코드의 문제는 내가 Gb(G플렛)의 Major Scale을 알고 싶지만, 막상 입력란에 Gb를 입력하면 다시 입력하라고 결과가 반환될 것이다.<br/>

튜플을 이용해서 해결방안을 고안하고자 한다. 시간상 이번 포스트는 여기서 마치거나, 나중에 내용을 추가하여 수정하도록 하겠다.