---
layout: post
title:  "[자료구조] 링크드리스트(Linked_List) 3"
subtitle:   "개요"
date: 2022-01-26 18:53:49 +0900
categories: study
tags: data_structure
comments: true
related_posts:

---

### 값을 삭제할 때 삭제할 데이터가 없을 때에는?<br/>
<br/>
2편에서 링크드리스트의 데이터를 삭제하는 과정을 포스팅했는데, 두 가지 오류가 있었다.<br/>
<br/>

**1. 코드상에 누락한 부분이 있다.**<br/>
**2. 삭제할 데이터가 링크드리스트 내에 존재하지 않는다면?**<br/>


### 누락한 부분 수정<br/>

```
def delete(data):
    global head
    
    if head.data == data: # 삭제하려는 데이터가 head라면?
        node = head
        head = head.next # 새로운 head 지정
        del node # 원래 head node 삭제
        return # 아래쪽의 코드가 실행되지 않도록 함수 종료.
    
    # head가 아니라면 이쪽으로 코드가 넘어온다.

    ## 누락부분 ##
    node = head
    ##############

    while (node.next).data != data:
        node = node.next
    
    temp = (node.next).next
    
    del node.next

    node.next = temp
```
이 코드에서 누락부분을 제외한 채로 링크드리스트 안에 없는 값을 삭제하라는 코드를 실행해보았다.
```
delete(1)
```
그리고 결과는..
```
while (node.next).data != data:

UnboundLocalError: local variable 'node' referenced before assignment
```

오류가 발생한 부분은 데이터를 search하는 파트였는데, search 과정의 시작노드를 정해주지 않아서 참조할 변수를 찾을 수 없어 발생하는 오류였다.<br/>

누락된 코드 `node = head`를 넣어주니 다시 잘 작동한다.

### 삭제할 데이터가 링크드리스트 내에 존재하지 않는 경우.<br/>

첫 번째로 누락한 부분은 수정했지만, 링크드리스트 내에 없는 데이터를 삭제하려고 할 경우
```
while (node.next).data != data:

AttributeError: 'NoneType' object has no attribute 'data'
```
NoneType 오류가 발생한다.<br/>
음, 없는 데이터를 삭제하려고 하니 오류가 당연히 발생할 수 밖에.<br/>
자, 여기서 발생한 에러가 AttributeError라는 점에 주목했다.<br/>
<br/>
삭제하고자 하는 데이터가 링크드리스트에 없다면 컴퓨터는 링크드리스트 끝까지 search를 진행할 것이다.<br/>

**하지만 끝까지 갔을 때 `node = node.next`를 실행하면 node.data는 NoneType이 되며,**<br/>
**`while (node.next).data != data:` 이 부분에서 오류가 난다.**<br/>
node에 저장되는 data의 특성은 str, int 혹은 float 등 일테니.<br/>

#### 나의 해결방법.<br/>

```
    try:
        while (node.next).data != data:
            node = node.next
        
    except AttributeError:
        print('찾는 코드가 없습니다.')
        return
```
try, except 구문을 사용하였다.<br/>
데이터가 없어서 발생할 수 있는 오류의 종류는 node.data가 NoneType가 되어 발생하는 AttributeError 종류라고 판단하였다. 따라서, 오류가 발생하지 않을 때에는 링크드리스드를 서치하고<br/>
node.data = NoneType이 될 때에는 데이터가 없다고 판단하여 AttributeError가 발생, delete()함수를 종료하는 코드로 작성하였다.<br/>

아! 그리고 insert() 함수에서도 링크드리스트 내에 데이터가 없으면 같은 오류가 발생할 것이다.<br/>
그 부분에서도 try, except구문을 적용한다.<br/>
이 시점에서 작성된 코드는<br/>
```
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next

def add(data):
    node = head
    while node.next:
        node = node.next
        
    node.next = Node(data)
    return

def insert(data, new_data):
    node = head
    
    try:
        while node.data != data:
            node = node.next
        
    except AttributeError:
        print('찾는 코드가 없습니다.')
        return
    
    tem = node.next 
    node_new = Node(new_data) 
    node.next = node_new 
    node = node_new
    node.next = tem 
    
    return

def delete(data):
    global head
    
    if head.data == data:
        node = head
        head = head.next
        del node
        return
    
    node = head
    
    try:
        while (node.next).data != data:
            node = node.next
        
    except AttributeError:
        print('찾는 코드가 없습니다.')
        return
        
    temp = (node.next).next
    
    del node.next

    node.next = temp

def node_print():
    node = head
    while node.next:
        print(node.data)
        node = node.next
    print(node.data)
    
    return
```
위와 같다. 이 상태에서 아래의 코드를 실행시켜 보았다.<br/>
```
node1 = Node(1)
node2 = Node(2)
node1.next = node2
head = node1

add(3)
add(5)
add('퍄퍄')
node_print()
print()

insert(3, 4)
insert(4, 4.5)
insert(6, '롸롸')
node_print()
print()

delete(1)
node_print()
print(head.data)
print()

delete(3)
node_print()
print()

delete(3)
delete(2)
delete(4)
node_print()
```
그리고 결과는 아래와 같이 출력되었다.<br/>
```
1
2
3
5
퍄퍄

찾는 코드가 없습니다.
1
2
3
4
4.5
5
퍄퍄

2
3
4
4.5
5
퍄퍄
2

2
4
4.5
5
퍄퍄

찾는 코드가 없습니다.
4.5
5
퍄퍄
```
휴우.. 에러없이 잘 진행되었다. 링크드리스트 내에 값이 없어서 발생하는 오류는 해결된 듯 하다.<br/>
일단은..<br/>

**4편에서 계속..**
