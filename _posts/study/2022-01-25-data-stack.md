---
layout: post
title:  "[자료구조] 스택(Stack)"
subtitle:   "개요"
date: 2022-01-25 19:35:28 +0900
tags: data_structure
categories: study
comments: true
related_posts:

---


## 스택이란 무엇인가?<br/>

큐와 같은 배열 자료구조의 하나이지만, LIFO(Last In, First Out)<br/>
즉, 마지막에 들어간 자료가 먼저 반환되는 자료구조이다.<br/>

### [] <br/>

이렇게 리스트를 형성한 뒤, 순서대로 3, 'funcoding', 1 을 넣어주면<br/>

### [3, 'funcoding', 1] <br/>

이 되며, 자료를 모두 반환한다면<br/>

1<br/>
'funcoding'<br/>
3<br/>

이런 순서대로 반환이 되는 자료구조이다.<br/>
<br/>

## 주로 어디에 쓰일까?<br/>

가장 흔히 알려진 사용처는 바로 인터넷이다.<br/>
정확히 말하면, 인터넷 페이지를 이동할 때 쓰인다.<br/>
지금까지 방문한 기록을 스택에 쌓아두었다가, 뒤로가기를 누르면 마지막으로 보았던 페이지를 불러오는 것, 이것도 스택의 활용이다.<br/>

또한, 간단하게 다른 사용처도 언급하면, 컴퓨터 내부의 프로세스 구조의 함수 동작 방식이라고 한다.<br/>
더 자세한 내용은 운영체제에 관련하여 배울 때 더 접할 수 있을 것 같다.<br/>

## 오늘 배운 내용은?

```
push()
pop()
```
<br/>

### push<br/>

스택에 새로운 데이터를 넣을 때 쓰는 것이다.<br/>

먼저, data_list = [1, 4, 2, 5, 6] 이라고 하자.<br/>
<br/>
여기다가 push(3)을 실행하면,<br/>
data_list = [1, 4, 2, 5, 6, 3]이 된다.<br/>

다시 push(7)을 실행하면,<br/>
data_list = [1, 4, 2, 5, 6, 3, 7]이 된다.<br/>
<br/>

### pop<br/>

자, 이제 data를 다시 꺼내보자.<br/>
지금 data_list의 상태에서 pop()을 1회 실행하면<br/>
반환 값은 7이 될 것이며,<br/>
data_list = [1, 4, 2, 5, 6, 3]이 된다.<br/>

그리고 pop()을 2회 실행해보자.<br/>
첫 번째 반환 값은 3, 두 번째 반환 값은 6이 될 것이다.<br/>
그리고 data_list = [1, 4, 2, 5]가 된다.
<br/>

### Stack과 재귀함수(Recursive)<br/>

```
def recursive(data):
    if data < 0:
        print('The end')
        return
    else:
        print(data)
        recursive(data - 1)
        print('returned : ', data)

recursive(5)

# 출력결과
5
4
3
2
1
0
The end
returned : 0
returned : 1
returned : 2
returned : 3
returned : 4
returned : 5
```

함수 안에 함수, 즉 재귀함수가 호출되면 함수의 동작 프로세스가 스택과 같이 동작한다.<br/>
```
# 동작 프로세스
recursive(-1)
recursive(0)
recursive(1)
recursive(2)
recursive(3)
recursive(4)
recursive(5)
```
즉, 위의 동작 프로세스를 보면 recursive(5) 부터 시작해서 recursive(4)...recurvise(-1) 순으로 아래에 차례로 쌓이고, 실제 동작은 위에서부터 하나씩 동작하는 것이다.<br/>
<br/>
개인적인 사담을 하자면, 실제로 코딩 테스트 문제에서 백트래킹 문제나 트리구조에서 재귀함수를 쓰는 사례를 많이 보았는데, 가장 기초가 스택에서부터 시작하는 것 같다.<br/>
<br/>

### Stack의 장단점<br/>
<br/>

- 장점
    + 구조가 단순하여 구현이 쉽다.
    + 데이터 저장, 읽기의 속도가 빠르다.

- 단점(일반적인 스택 구현 시)
    + 데이터 저장 공간의 낭비가 발생할 수 있다.<br/>
    (미리 최대 갯수 만큼 저장 공간을 확보해야 함.)
    + 데이터 최대 갯수를 미리 정해야 한다.<br/>
    (파이썬의 경우 최대 1000번 까지 재귀함수 호출 가능.)

data_list를 예를 들어 생각해보자.<br/>
데이터 저장 공간의 낭비는 예를 들어, 미리 5개의 저장 공간을 마련하면 data_list는 다음과 같아진다.<br/>
```
data_list = [Null, Null, Null, Null, Null]
```
<br/>
여기서 자료를 2개 push하면 data_list는 다시 아래와 같아진다.<br/>
<br/>

```
data_list = [data1, data2, Null, Null, Null]
```
실제로 사용하는 공간은 2개 인데, 이미 스택의 공간은 5개로 만들었으니, 나머지 Null 3개의 빈 공간은 낭비되고 있는 것이다.<br/>

### python에서 pop, push 구현하기.<br/>

파이썬에서는 리스트에 대해서 pop(), push()를 기본내장함수로 제공한다.<br/>
하지만, 직접 구현한다면 어떻게 구현할 수 있을까?<br/>

```
def push(data):
    data_list.append(data)
    return

def pop():
    data_to_pop = data_list[-1]
    del data_list[-1]
    return data_to_pop

data_list = []
```
위와 같이 구현이 가능하다.<br/>
append()함수가 리스트안에 데이터를 맨 끝자리에 추가하는 함수이니 어찌보면.. push()랑 비슷하다.<br/>
그리고 pop()은 맨 마지막 자리에 있는 것을 꺼낸다, 즉 값을 반환하면서 data_list맨 마지막 자리에 있던 data는 사라지는 것이니, data_list[-1]를 따로 저장하고, del data_list[-1]를 수행한다.<br/>

## 오늘 배운 내용에서..<br/>
DP, 백트래킹을 공부할 때 계속 재귀함수를 많이 쓸 텐데, 이 스택에 대해서 계속 상기를 해야할 것 같다.<br/>
그리고, 파이썬에서는 append를 계속 할 수 있지만, Stack의 단점에서 언급 되었듯이, Java나 C언어에서는 미리 list의 길이를 정해두어야 하는 것으로 기억하고 있다. 나중에 Java를 다시 공부할 때 더 와닿을 것 같다.<br/>