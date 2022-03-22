---
layout: post
title:  "[알고리즘] BFS와 DFS"
subtitle:
date: 2022-02-16 10:14:21 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

# [알고리즘] BFS와 DFS<br/>

## BFS와 DFS란?<br/>

아래 사진과 같이 그래프가 있다고 하자.<br/>

![그래프](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-02-16_bfs_and_dfs_1.jpg?raw=true)
<br/>

여기서 BFS는 너비우선탐색을 가리키며, 그래프를 탐색할 때 같은 레벨에 있는 노드를 모두 탐색한 다음에 다음 레벨로 넘어가서 탐색하는 기법을 말한다. <br/>

위의 경우 그림으로 나타내면 아래와 같다.<br/>
![BFS](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-02-16_bfs_and_dfs_2.jpg?raw=true)
<br/>
그림에선 왼쪽 -> 오른쪽으로 나타났지만 오른쪽 -> 왼쪽으로 탐색해도 된다!<br/>

한편, DFS는 깊이우선탐색을 가리키며, 그래프를 탐색할 때 한 leaf node에 도달할 때 까지 다음 레벨로 넘어가며, leaf node에 도달할 경우 방문했던 길을 되돌아가서 자식노드 중 방문하지 않은 노드가 있는 노드를 방문하여 다시 leaf node가 나타날 때 까지 탐색하는 기법이다.<br/>

마찬가지로 위의 경우 그림으로 나타내면 아래와 같다.<br/>
![DFS](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-02-16_bfs_and_dfs_3.jpg?raw=true)
<br/>
그림은 왼쪽가지부터 탐색하는 구조로 나타났지만 마찬가지로 오른쪽가지부터 탐색하는 것도 무방하다.<br/>
<br/>

## 그래프를 파이썬으로 구현하기<br/>

참고링크 : [잔재미코딩_BFS](https://fun-coding.org/Chapter18-bfs-live.html)<br/>

전체 그래프를 Dictionary로 구성하고, 각 노드를 Key로 지정하고, Value는 그 노드랑 연결된 노드를 담은 리스트로 지정하는 구조로 할 수 있다.

```
graph = dict()

graph['A'] = ['B', 'C']
graph['B'] = ['A', 'D', 'E']
graph['C'] = ['A', 'F', 'G', 'H']
graph['D'] = ['B', 'I']
graph['E'] = ['B']
graph['F'] = ['C', 'J']
graph['G'] = ['C']
graph['H'] = ['C']
graph['I'] = ['D']
graph['J'] = ['F']
```

즉, 맨 위에 그려진 그림을 딕셔너리, 리스트를 활용해서 위와 같이 나타낼 수 있다.<br/>
<br/>

## 파이썬 코드로 BFS 구현하기.<br/>

```
def bfs(graph, start_node):
    visited, need_visit = list(), list()
    need_visit.append(start_node)
    
    while need_visit:
        node = need_visit.pop(0)
        if node not in visited:
            visited.append(node)
            need_visit.extend(graph[node])
    
    return visited
```

사용자 지정함수에서 graph와 start 노드를 입력 값으로 받고, 방문한 노드와 방문해야할 노드를 기록하는 리스트를 각각 visited, need_visit로 기록한다.<br/>

방문해야할 리스트에 시작 노드를 추가하면 while 구문에서 노드 내의 방문해야할 노드를 탐색하며 visited에 방문한 노드를 추가한다.

```
# Test Code
print(bfs(graph, 'A'))

# Result
['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J']
```

위에 나타낸 그래프의 BFS 탐색 순서대로 나타났다.<br/>
<br/>

## 파이썬 코드로 DFS 구현하기.<br/>

참고링크 : [잔재미코딩_DFS](https://fun-coding.org/Chapter18-dfs-live.html)

```
def dfs(graph, start_node):
    visited, need_visit = list(), list()
    need_visit.append(start_node)
    
    while need_visit:
        node = need_visit.pop()
        if node not in visited:
            visited.append(node)
            need_visit.extend(graph[node])
    
    return visited
```

위의 BFS랑 흐름은 비슷하다. 다만, node에서 방문해야할 노드를 pop할 때 index가 차이가 있다.<br/>
<br/>


## 두 코드의 차이?<br/>

지금 우리는 Value의 리스트 안에 0번 인덱스에는 부모노드를, 나머지에는 자식노드를 연결해두었다.<br/>

만약에 pop(0)이 진행된다면 부모노드쪽을 불러오게 되는 것이며,
아래와 같은 흐름이 진행된다.

- 부모노드의 연결된 리스트를 불러온다.
- 0번 인덱스 쪽에는 부모노드가 있다.
- node 변수에 부모노드의 연결된 리스트 목록을 불러온다.
- need_visit 리스트에 부모의 자식노드들이 추가된다.
- need visit 리스트에서 부모노드는 이미 방문했으므로 자동으로 자식노드를 탐색한다. (if node not in이 그 역할을 함.)
- 부모의 자식노드는 자신과 자신의 형제노드이다.
- 고로 형제노드들을 탐색하는 BFS가 진행된다.

반면, pop()이 진행된다면?

- 자식노드의 연결된 리스트를 불러온다.
- 끝번 인덱스 쪽에는 자식노드가 있다.
- node 변수에 자식노드의 연결된 리스트 목록을 불러온다.
- need_visit 리스트에 자식노드의 부모노드와 자식노드들이 추가된다.
- need visit 리스트에서 부모노드는 이미 방문했으므로 자동으로 자식노드를 탐색한다. (if node not in이 그 역할을 함.)
- 자식이 없을 때 까지, 즉 Leaf Node에 도달할 때 까지 먼저 자식노드들을 계속 탐색하게 된다.
- 고로 DFS가 진행된다.
<br/>

주의사항은 연결된 노드를 리스트에

```
['부모노드', '자식노드1', '자식노드2', ...]
```

형태로 연결했기 때문에 pop의 인덱스를 조절하는 것으로 dfs와 bfs 구조를 바꿀 수 있다는 점을 기억해야겠다.<br/>

## 종합 코드

```
def bfs(graph, start_node):
    visited, need_visit = list(), list()
    need_visit.append(start_node)
    
    while need_visit:
        node = need_visit.pop(0)
        if node not in visited:
            visited.append(node)
            need_visit.extend(graph[node])
    
    return visited


def dfs(graph, start_node):
    visited, need_visit = list(), list()
    need_visit.append(start_node)
    
    while need_visit:
        node = need_visit.pop()
        if node not in visited:
            visited.append(node)
            need_visit.extend(graph[node])
    
    return visited

graph = dict()

graph['A'] = ['B', 'C']
graph['B'] = ['A', 'D', 'E']
graph['C'] = ['A', 'F', 'G', 'H']
graph['D'] = ['B', 'I']
graph['E'] = ['B']
graph['F'] = ['C', 'J']
graph['G'] = ['C']
graph['H'] = ['C']
graph['I'] = ['D']
graph['J'] = ['F']

print(bfs(graph, 'A'))
print(dfs(graph, 'A'))
```

이번에 작성한 코드를 합치면 위와 같다.<br/>
리스트와 Dictionary가 제일 속도가 빠를 것이라 생각이 들지만, 이 방법 말고도 Class로 구현을 시도할 수 있을 것 같다. 도전해봐야지.<br/>