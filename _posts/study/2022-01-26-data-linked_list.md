---
layout: post
title:  "[자료구조] 링크드리스트(Linked_List) 1"
subtitle:   "개요"
date: 2022-01-26 10:19:02 +0900
categories: study
tags: data_structure
comments: true
related_posts:

---


## 링크드리스트란 무엇인가?<br/>

각 노드마다 데이터를 저장하고, 연결 지정해서 만들어낸 리스트.<br/>

### 일반 배열과의 차이점은?<br/>

일반 배열은 선언할 때, 데이터를 넣을 최대 공간을 미리 확보해 두어야 한다.<br/>
반면에 링크드리스트는 필요할 때 마다 공간을 추가해서 데이터를 저장할 수 있다는 장점이 있다.<br/>
<br/>
링크드리스트를 구성하는 요소는 다음과 같다.<br/>

- 노드 : 데이터 저장 단위(데이터 값, 포인터)로 구성
- 포인터 : 각 노드 안에서, 이전이나 다음 노드와의 연결정보를 가지고 있는 공간
- 데이터 저장 공간

그림을 그려보면 아래와 같다.<br/>

![링크드리스트](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-01-26_linked_list.jpg?raw=true)<br/>

각 노드마다 데이터가 저장되어 있고, 포인터는 다음 데이터가 있는 주소를 가리킨다.<br/>
<br/>

**비유를 하면 지하철노선도같다.**<br/>
<br/>
수도권 전철 1호선을 생각해보자.<br/>

- {주소 : '1번' '역명' : '소요산', 이전역 : '', '다음역' : '2번'}
- {주소 : '2번' '역명' : '동두천', 이전역 : '1번', '다음역' : '3번'}
- {주소 : '3번' '역명' : '동두천중앙', 이전역 : '2번', '다음역' : '4번'}
...
- {주소 : '98번' '역명' : '온양온천', 이전역 : '97번', '다음역' : '99번'}
- {주소 : '99번' '역명' : '신창(순천향대)', 이전역 : '98번', '다음역' : ''}

원래 주소는 '경기도 동두천시...'와 같이 표기하고 싶었지만 간략화를 위해 '1번'등으로 표기하였다.<br/>
<br/>
자, 이렇게 지하철은 어떤 주소에 각각 역이 있고 각 역에도 역명, 승강장 형태, 환승 가능여부 등의 정보가 있다. 그리고 이전역은 어디인지, 다음역은 어디인지에 대한 정보도 담겨있다.<br/>
만약 이전역 정보가 없다면 노선의 시점인 것이고, 다음역 정보가 없다면 노선의 종점인 것과 같다.<br/>
<br/>
그리고 같은 역들이 있다고 해도 노선이 어떻게 연결되어있냐, 어느 역이 연결되어 있냐에 따라서 서로 다른 노선을 형성하게 되는 것이다.<br/>
<br/>
링크드리스트도 마찬가지이다. 데이터가 있는 각 주소마다 저장된 데이터, 다른 노드와의 연결정보가 담겨있고, 이 연결정보에 따라 노선이 만들어져 서로 다른 링크드리스트가 형성이 되는 것이다.<br/>
<br/>

## 파이썬 코드로 구현하기.<br/>

```
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
```
노드는 파이썬 코드로 위와 같이 간단하게 구현해볼 수 있다.<br/>

자, 이제 노드를 형성하고 노드와 노드를 연결해보자.<br/>

```
node1 = Node(1)
node2 = Node(2)
node1.next = node2
head = node1
```
- 1번 노드에 1이 저장되었다.
- 2번 노드에 2가 저장되었다.
- 리스트의 시작점은 1번 노드이다.
- 1번 노드의 다음 노드는 2번노드이다.

위와 같은 설정이 완료되었다.<br/>
<br/>
다음으로, 데이터를 추가하고 싶다면 어떻게 구현하면 될까?<br/>

```
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next

    def add(data):
        node = head # head노드에서 탐색 시작.
        while node.next: # 다음 노드에 대한 포인터가 있다면 진행
            node = node.next
        
        # while 구문이 끝난 시점에는 마지막 노드에 와있다.
        node.next = Node(data)
```

이렇게 구현할 수 있다!<br/>

## 직접 한번 활용해보자.<br/>

위에 필자가 링크드리스트를 지하철 노선에 비유했다. 이걸로 지하철 노선을 한번 만들어보겠다.<br/>

```
class Station:
    def __init__(self, name, next=None):
        self.name = name
        self.next = next
    
    def add(name):
        station = start
        while station.next:
            station = station.next
        
        station.next = Station(name)
```
노드랑 비슷하게 역을 형성하고 추가할 수 있도록 코드를 짰다.<br/>

자, 이제 노선을 만들어볼까?<br/>
이번엔 수인분당선에서 망포역 - 고색역 까지의 노선을 만들어보자.<br/>

```
Bundang_K241 = Station('망포')
Bundang_K242 = Station('매탄권선')
Bundang_K241.next = Bundang_K242
Start = Bundang_K241

Bundang_K243 = Station('수원시청')
Bundang_K242.next = Bundang_K243

Bundang_K244 = Station('매교')
Bundang_K243.next = Bundang_K244

Bundang_K245 = Station('수원')
Bundang_K244.next = Bundang_K245

Bundang_K246 = Station('고색')
Bundang_K245.next = Bundang_K246
```

자! 이렇게 수인분당선에서 고색행 열차가 망포역 - 고색역 까지 주행하는 구간을 링크드리스트로 형성했다.
```
print('이번역은 망포역입니다.')
print('고색행 열차의 이후 정차역은 아래와 같습니다.')
station = start
while station.next:
    print(station.name)
    station = station.next
print(station.name)
```
이렇게 코드를 짜서 출력해보면<br/>

![링크드수인분당](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-01-26_linked_list_2.jpg?raw=true)<br/>

Spyder에서 구동해보니 잘 출력되었다!<br/>

2편에서 계속..<br/>