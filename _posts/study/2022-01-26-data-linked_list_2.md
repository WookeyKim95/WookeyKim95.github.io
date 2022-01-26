---
layout: post
title:  "[자료구조] 링크드리스트(Linked_List) 2"
subtitle:   "개요"
date: 2022-01-26 12:29:22 +0900
categories: study
tags: data_structure
comments: true
related_posts:

---

### 링크드리스트의 장단점은?<br/>

- 장점
    + 일반 ArrayList와 달리, 배열공간을 미리 할당하지 않아도 된다.

- 단점
    + 연결 정보를 위한 별도의 데이터 공간이 필요해서 공간 효율이 좋지는 않다.
    + 연결 정보를 찾는데 시간이 걸려 접근 속도가 느리다.
    + 중간에 데이터를 추가하거나 삭제시, 연결 정보를 수정하는 부가작업 필요.


### 링크드리스트의 중간에 데이터 추가하기.<br/>


![링크드리스트추가](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-01-26_linked_list_3.jpg?raw=true)<br/>

위의 그림과 같이, 링크드리스트의 중간에 데이터를 추가하는 과정은 원하는 위치를 설정한 뒤, 위치의 이전에 있는 노드의 다음 노드의 주소를 새로운 노드의 주소로, 그리고 새로운 노드의 다음 노드의 주소를 위치의 다음에 있는 노드의 주소로 설정하는 방식이다.<br/>

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
```
자, 이전 포스트에서는 다음과 같이 코드가 작성되어있는 상태였다. 여기에 특정 데이터를 가진 노드의 뒤에 노드를 추가하는 코드를 작성해보자.<br/>

data를 가진 노드 뒤에 new_data를 추가하고 싶다면?<br/>
```
# 노드를 추가할 위치를 잡기 위한 데이터 입력

def insert(data, new_data):
    node = head
    while node.data != data:
        node = node.next
    
    # 만약 data를 가진 노드에 도착하면 아래의 코드가 진행된다.
    tem = node.next # 우선 기존의 다음 노드 주소를 임시 변수에 저장
    node_new = Node(new_data) # 새로운 노드 생성
    node.next = node_new # data를 가진 노드의 다음 주소를 새로운 노드의 주소로 변경
    node = node_new
    node.next = tem # 새로운 노드의 다음 주소를 기존의 다음 노드 주소로 연결하여 연결 완료.

```

이렇게 하면 구현이 가능하다.<br/>
```
def node_print():
    node = head
    while node.next:
        print(node.data)
        node = node.next
    print(node.data)
    
    return
```
그리고 링크드리스트의 노드들을 출력하기 위한 코드를 짜고<br/>
```
node1 = Node(1)
node2 = Node(2)
node1.next = node2
head = node1

add(3)
add(5)
insert(3, 4)
insert(4, 4.5)

node_print()
```
이 코드들을 실행해보았다.<br/>

```
# 출력결과
1
2
3
4
4.5
5
```
Spyder에서 구동해보니 잘 나온다!<br/>

### 링크드리스트의 데이터 삭제하기.<br/>

자, 그러면 삭제는 추가와 반대과정으로 노드를 재연결 해야할 것이다.<br/>
![링크드리스트제거](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-01-26_linked_list_4.jpg?raw=true)<br/>

위의 그림과 같이 삭제할 노드의 이전 노드의 node.next를 삭제할 노드의 다음 노드로 설정하면 된다.
그 뒤에 노드의 삭제를 실행하면 된다.

코드로 구현하면 아래와 같다.<br/>
```
def delete(data):
    node = head
    while node.next != data:
        node = node.next
    
    # 다음 노드가 삭제할 노드일 때
    # 다음 다음 노드를 다음 노드로 지정해 주면 된다.
    # 이 후 다음 노드를 삭제하기 위해서 임시변수에 저장해주자.
    temp = (node.next).next
    
    # 그 후에 다음 노드를 삭제시킨다.
    del node.next

    # 그리고 다음 노드의 주소를 임시 저장했던 주소를 넣어준다.
    node.next = temp
```
위에 있던 코드에 def delete(data)함수를 추가한 뒤, 아래의 코드를 다시 실행해보았다.
```
node1 = Node(1)
node2 = Node(2)
node1.next = node2
head = node1

add(3)
add(5)
insert(3, 4)
insert(4, 4.5)

node_print()
print()
delete(3)

node_print()
```
그리고 실행을 한 결과는 아래와 같았다.<br/>
```
# 출력 결과
1
2
3
4
4.5
5

1
2
4
4.5
5
```
원하던 대로 3이 삭제되었다.<br/>

**하지만 만약에 삭제하고자 하는 데이터가 맨 앞에 있다면?(head)**<br/>
<br/>
head를 새로 지정한 뒤에 삭제를 진행해주어야 한다.<br/>
```
def delete(data):
    global head
    
    if head.data == data: # 삭제하려는 데이터가 head라면?
        node = head
        head = head.next # 새로운 head 지정
        del node # 원래 head node 삭제
        return # 아래쪽의 코드가 실행되지 않도록 함수 종료.
    
    # head가 아니라면 이쪽으로 데이터가 넘어온다.

    while (node.next).data != data:
        node = node.next
    
    temp = (node.next).next
    
    del node.next

    node.next = temp
```
위와 같이 코드를 짤 수 있다.<br/>

**헉!! 저녁에 위 코드에서 오류사항이 있다는 것을 발견하였다. 오류 수정은 3편에서!**<br/>

```
node1 = Node(1)
node2 = Node(2)
node1.next = node2
head = node1

add(3)
add(5)
insert(3, 4)
insert(4, 4.5)

node_print()
print()
delete(1)

node_print()
```
이 코드를 실행해보았고,<br/>
```
# 출력결과
1
2
3
4
4.5
5

2
3
4
4.5
5
```
음! 잘 출력되었다!<br/>

**3편에서 계속..**