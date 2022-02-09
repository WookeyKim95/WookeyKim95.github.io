---
layout: post
title:  "[자료구조] 링크드리스트(Linked_List) 5"
subtitle:   "개요"
date: 2022-01-27 09:21:46 +0900
tags: data_structure
categories: study
comments: true
related_posts:

---

## 이중 링크드리스트로 지하철 안내방송 만들기<br/>
<br/>
지난 4편에서 이중 링크드리스트를 만들고, 머리부터 탐색하는 기능, 꼬리부터 탐색하는 기능을 코드로 구현해보았다.<br/>
<br/>
이걸로 지하철 안내방송을 한번 출력해보려고한다.<br/>
<br/>

### 1. 각 정류장이 가지는 정보.<br/>

우선 각 지하철 역에는 역의 고유번호가 있다.<br/>

ex) 왕십리역 : 208(2호선), 540(5호선), K210(수인분당선), K116(경의중앙선)<br/>

그리고 환승이 가능하다면 몇 호선과 환승이 가능한지, 타고 내릴 때 열리는 전동차 문은 어느 쪽인지에 대한 정보도 있어야한다.<br/>

그래서, Dictionary를 활용하여 이중 링크드리스트에 Dictionary를 넣는 자료구조 형태로 구현하고자 한다.<br/>

이 때, 수인분당선의 왕십리역에 대한 Dictionary는 다음과 같이 형성이 가능하다.

```
K210 = {'name':'왕십리', 'door':'오른쪽', '환승':[2, 5, '경의중앙']}
```

### 2. 이중 링크드리스트 코드 만들기.<br/>

사실 지난 4편에서의 코드를 가져온 뒤, 일부 수정 후 추가를 해주면 된다.<br/>
node를 station으로, data를 id_num으로 바꾸었다.<br/>

```
class Station:
    def __init__(self, id_num, prev=None, next=None):
        self.id_num = id_num
        self.prev = prev
        self.next = next

class Station_Manage:
    def __init__(self, id_num):
        self.head = Station(id_num)
        self.tail = self.head
    
    def add(self, id_num):
        if self.head == None:
            self.head = Station(id_num)
            self.tail = self.head
        else:
            station = self.head
            while station.next:
                station = station.next
                
            new = Station(id_num)
            station.next = new
            new.prev = station
            self.tail = new
            
            return

    def station_print_head(self):
        station = self.head
        while station:
            print(station.id_num)
            station = station.next
            
        return
    
    def station_print_tail(self):
        station = self.tail
        while station:
            print(station.id_num)
            station = station.prev
            
        return


    def insert(self, id_num, new_id_num):
        station = self.head
        
        try:
            while station.id_num != id_num:
                station = station.next
            
        except AttributeError:
            print('찾는 역이 없습니다.')
            return
        
        if station == self.tail:
            station.next = Station(new_id_num)
            station.next.prev = station
            self.tail = station.next
            return
        else:
            tem = station.next 
            station_new = Station(new_id_num) 
            station.next = station_new
            station_new.prev = station
            station = station_new
            
            station.next = tem
            station.next.prev = station
            
            return
        
    def insert_prev(self, id_num, new_id_num):
        station = self.tail
        
        try:
            while station.id_num != id_num:
                station = station.prev
            
        except AttributeError:
            print('찾는 역이 없습니다.')
            return
        
        if station == self.tail:
            station.prev = Station(new_id_num)
            station.prev.next = station
            self.tail = station.prev
            return
        
        else:
            tem = station.prev
            station_new = Station(new_id_num) 
            station.prev = station_new
            station_new.next = station
            station = station_new
            
            station.prev = tem
            station.prev.next = station
            
            return

    def delete(self, id_num):
        
        if id_num == self.head.id_num:
            station = self.head
            self.head = self.head.next
            del station
            return
        
        elif id_num == self.tail.id_num:
            station = self.tail
            self.tail = self.tail.prev
            del station
            return
        
        station = self.head
        
        try:
            while (station.next).id_num != id_num:
                station = station.next
            
        except AttributeError:
            print('찾는 역이 없습니다.')
            return
        
        temp = (station.next).next
       
        del station.next

        station.next = temp
        station.next.prev = station
        
        return
```
### 3. 노선에 데이터 추가하기.<br/>

여기서 추가할 기능은 각 역에 도착할 때마다 안내방송을 하는 기능, 그리고 환승정보가 있다면 몇호선으로 갈아탈 수 있다는 방송을 할 것이다.<br/>
<br/>
간단하게 실제 노선도 중에서 신분당선의 강남역 ~ 정자역 구간, 분당선의 죽전 ~ 야탑역 구간, 3호선의 양재 ~ 수서 구간을 따와서 노선을 만들어보자.<br/>
<br/>
그렇다면 정류장 데이터를 아래와 같이 형성할 수 있다.

```
# 신분당선 강남 ~ 정자 구간

D07 = {'name':'강남', 'door':'왼쪽', '환승':[]}
D08 = {'name':'양재', 'door':'왼쪽', '환승':[3]}
D09 = {'name':'양재 시민의 숲', 'door':'왼쪽', '환승':[]}
D10 = {'name':'청계산 입구', 'door':'왼쪽', '환승':[]}
D11 = {'name':'판교', 'door':'왼쪽', '환승':[]}
D12 = {'name':'정자', 'door':'왼쪽', '환승':['분당']}

# 분당선 죽전 ~ 야탑 구간
K233 = {'name':'죽전', 'door':'오른쪽', '환승':[]}
K232 = {'name':'오리', 'door':'왼쪽', '환승':[]}
K231 = {'name':'미금', 'door':'왼쪽', '환승':[]}
K230 = {'name':'정자', 'door':'왼쪽', '환승':['신분당']}
K229 = {'name':'수내', 'door':'왼쪽', '환승':[]}
K228 = {'name':'서현', 'door':'왼쪽', '환승':[]}
K227 = {'name':'이매', 'door':'왼쪽', '환승':[]}
K226 = {'name':'야탑', 'door':'왼쪽', '환승':[]}

# 3호선 양재 ~ 수서 구간
S342 = {'name':'양재', 'door':'오른쪽', '환승':['신분당']}
S343 = {'name':'매봉', 'door':'오른쪽', '환승':[]}
S344 = {'name':'도곡', 'door':'오른쪽', '환승':[]}
S345 = {'name':'대치', 'door':'왼쪽', '환승':[]}
S346 = {'name':'학여울', 'door':'오른쪽', '환승':[]}
S347 = {'name':'대청', 'door':'오른쪽', '환승':[]}
S348 = {'name':'일원', 'door':'오른쪽', '환승':[]}
S349 = {'name':'수서', 'door':'왼쪽', '환승':[]}
```

이제 노선을 형성할 링크드리스트를 만들고 데이터를 추가하여 노선을 형성하자.<br/>

```
# 3호선
Line_3 = Station_Manage(S342)
Line_3.add(S343)
Line_3.add(S344)
Line_3.add(S345)
Line_3.add(S346)
Line_3.add(S347)
Line_3.add(S348)
Line_3.add(S349)

# 분당선
Line_K2 = Station_Manage(K233)
Line_K2.add(K232)
Line_K2.add(K231)
Line_K2.add(K230)
Line_K2.add(K229)
Line_K2.add(K228)
Line_K2.add(K227)
Line_K2.add(K226)

# 신분당선
Line_D = Station_Manage(D07)
Line_D.add(D08)
Line_D.add(D09)
Line_D.add(D10)
Line_D.add(D11)
Line_D.add(D12)
```

그리고 위에 만들었던 station_print_head()함수를 사용하면<br/>

```
Line_3.station_print_head()
print()
Line_K2.station_print_head()
print()
Line_D.station_print_head()
```
```
# 출력 결과
{'name': '양재', 'door': '오른쪽', '환승': ['신분당']}
{'name': '매봉', 'door': '오른쪽', '환승': []}
{'name': '도곡', 'door': '오른쪽', '환승': []}
{'name': '대치', 'door': '왼쪽', '환승': []}
{'name': '학여울', 'door': '오른쪽', '환승': []}
{'name': '대청', 'door': '오른쪽', '환승': []}
{'name': '일원', 'door': '오른쪽', '환승': []}
{'name': '수서', 'door': '왼쪽', '환승': []}

{'name': '죽전', 'door': '오른쪽', '환승': []}
{'name': '오리', 'door': '왼쪽', '환승': []}
{'name': '미금', 'door': '왼쪽', '환승': []}
{'name': '정자', 'door': '왼쪽', '환승': ['신분당']}
{'name': '수내', 'door': '왼쪽', '환승': []}
{'name': '서현', 'door': '왼쪽', '환승': []}
{'name': '이매', 'door': '왼쪽', '환승': []}
{'name': '야탑', 'door': '왼쪽', '환승': []}

{'name': '강남', 'door': '왼쪽', '환승': []}
{'name': '양재', 'door': '왼쪽', '환승': [3]}
{'name': '양재 시민의 숲', 'door': '왼쪽', '환승': []}
{'name': '청계산 입구', 'door': '왼쪽', '환승': []}
{'name': '판교', 'door': '왼쪽', '환승': []}
{'name': '정자', 'door': '왼쪽', '환승': ['분당']}
```
데이터가 잘 추가되었다.<br/>

### 4. 안내방송 멘트 출력하기.<br/>

```
def station_print_head(self):
def station_print_tail(self):
```
위에 있었던 링크드리스트 출력함수를 활용할 수 있다.<br/>
출력하는 부분에서 안내방송이 흘러나오도록 하면 되겠지!<br/>
<br/>
그렇다면 각 역별로 안내방송이 나오도록 새로운 함수를 지정해줘야한다.<br/>

```
def arrival(id_num):
    print('이번역은 ' + id_num['name'] + ', ' + id_num['name'] + '역 입니다.')
    print('내리실 문은 ' + id_num['door'] + '입니다.')
```
함수의 이름은 arrival로 했고, input은 역의 데이터를 담은 Dictionary이다.<br/>
저기서는 현재 station의 정보, 즉 station.id_num이 들어간다.<br/>

```
if len(id_num['환승']) > 0:
        for i in id_num['환승']:
            if type(i) == int:
                print(str(i)+'호선', end=' ')
            elif type(i) == str:
                print(i+'선', end=' ')
        print('으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.')

```
그리고 환승할 수 있는 호선이 있으면 환승 안내 방송도 한다.<br/>

Dictionary안에 환승 가능 노선 데이터가 리스트형태로 저장되었으므로, 리스트의 길이가 0보다 크다는 것은 곧 환승이 가능하다는 의미이니 환승 안내방송을 출력하도록 한다.<br/>
<br/>
또한, 노선이 3호선처럼 번호인 경우도 있고, 신분당선, 분당선처럼 문자인 경우도 있으니 구분해서 출력하도록 유도하였다.<br/>

```
print('This stop is '+ id_num['name'] + ', ' + id_num['name'] + '.')
    
    if id_num['door'] == '왼쪽':
        print('The door opens on the left.')
    elif id_num['door'] == '오른쪽':
        print('The door opens on the right.')

    if len(id_num['환승']) > 0:
        print('You can transfer to ', end=' ')
        for i in id_num['환승']:
            if type(i) == int:
                print('line no.' + str(i), end=' ')
            elif type(i) == str:
                print(i+' line', end=' ')
```
한글로 안내했으면 영어 안내멘트도 빼놓을 수 없다.<br/>
하지만 차이점이라면 어느 쪽 문이 열리는지 여부는 한글문자로 저장됐기 때문에, 왼쪽인지 오른쪽인지 구분하는 과정을 출력해서 영어 안내멘트가 나오도록 만들었다.<br/>

```
def arrival(id_num):
    print('이번역은 ' + id_num['name'] + ', ' + id_num['name'] + '역 입니다.')
    print('내리실 문은 ' + id_num['door'] + '입니다.')
    print()
    
    if len(id_num['환승']) > 0:
        for i in id_num['환승']:
            if type(i) == int:
                print(str(i)+'호선', end=' ')
            elif type(i) == str:
                print(i+'선', end=' ')
        print('으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.')
        print()
        
    print('This stop is '+ id_num['name'] + ', ' + id_num['name'] + '.')
    
    if id_num['door'] == '왼쪽':
        print('The door opens on the left.')
    elif id_num['door'] == '오른쪽':
        print('The door opens on the right.')
    print()
    if len(id_num['환승']) > 0:
        print('You can transfer to ', end=' ')
        for i in id_num['환승']:
            if type(i) == int:
                print('line no.' + str(i), end=' ')
            elif type(i) == str:
                print(i+' line', end=' ')
    
        print('\n')
    print('----------------------------')
    print()

    return
```
지금까지 작성된 코드는 다음과 같다. 역마다 구분해서 이쁘게 보일 수 있도록 부분마다 print()를 추가하였다.<br/>
```
def station_oper_head(self):
        station = self.head
        while station:
            arrival(station.id_num)
            station = station.next
            
        return
```
그리고 머리부터 운행하는 함수에 arrival(station.id_num)을 넣어주었다.<br/>

```
Line_3.station_oper_head()
```
3호선을 머리부터 운행해보았다.<br/>

```
# 출력 결과
이번역은 양재, 양재역 입니다.
내리실 문은 오른쪽입니다.

신분당선 으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.

This stop is 양재, 양재.
The door opens on the right.

You can transfer to  신분당 line 

----------------------------

이번역은 매봉, 매봉역 입니다.
내리실 문은 오른쪽입니다.

This stop is 매봉, 매봉.
The door opens on the right.

----------------------------

이번역은 도곡, 도곡역 입니다.
내리실 문은 오른쪽입니다.

This stop is 도곡, 도곡.
The door opens on the right.

----------------------------

이번역은 대치, 대치역 입니다.
내리실 문은 왼쪽입니다.

This stop is 대치, 대치.
The door opens on the left.

----------------------------

이번역은 학여울, 학여울역 입니다.
내리실 문은 오른쪽입니다.

This stop is 학여울, 학여울.
The door opens on the right.

----------------------------

이번역은 대청, 대청역 입니다.
내리실 문은 오른쪽입니다.

This stop is 대청, 대청.
The door opens on the right.

----------------------------

이번역은 일원, 일원역 입니다.
내리실 문은 오른쪽입니다.

This stop is 일원, 일원.
The door opens on the right.

----------------------------

이번역은 수서, 수서역 입니다.
내리실 문은 왼쪽입니다.

This stop is 수서, 수서.
The door opens on the left.

----------------------------
```
깔끔하게 출력되었다.<br/>

여기다가 시점이면 도착 안내방송대신 출발 안내방송을 하면 될 것이고, 종점이면 종착 안내방송을 하면 좋을 것 같다.<br/>

```
# 출발 안내방송

def depart_start(id_num):
    
    print('고객 여러분, 안녕하십니까?')
    print('우리 열차는 ' + id_num['name'] + ', ' + id_num['name'] + '행 열차입니다.')
    print()
    print('This train bounds for ' + id_num['name'] + ', ' + id_num['name'], end='.\n')
    print('Thank you!')
    print()
    print('----------------------------')
    
    return
```
우선, 출발역 전용 안내멘트가 흘러나오는 함수를 만들었다.<br/>

```
def station_oper_head(self):
    station = self.head
    while station:
        if station == self.head:
            depart_start(self.tail.id_num)
            station = station.next
            continue
        arrival(station.id_num)
        station = station.next
        
    return
```

그리고 출발역이라면 `station == self.head`인 경우이므로 그 부분만 따로 출발역 전용 안내멘트가 흘러나오도록 제어문을 생성한다.<br/>

**그리고 주의사항은 출발역에서만 작동해야하므로, station = station.next를 통해 다음 역으로 이동한 다음에 continue를 넣는다.**<br/>

**station = station.next를 안넣으니 무한루프에 빠져버린다..**<br/>

**그리고 열차는 head에서 출발해서 tail을 향하므로, depart_head함수의 input은 self.tail이 가도록 한다.**

```
# 함수 추가 후 출력 결과

고객 여러분, 안녕하십니까?
우리 열차는 수서, 수서행 열차입니다.

This train bounds for 수서, 수서.
Thank you!

----------------------------
이번역은 매봉, 매봉역 입니다.
내리실 문은 오른쪽입니다.

This stop is 매봉, 매봉.
The door opens on the right.
...
```
출력하니 정상적으로 출력되었다.<br/>
<br/>
그 뒤에, 종착역 전용 안내방송을 출력하는 함수도 만들었다.<br/>

```
def arrival_term(id_num):
    
    print('이번역은 우리 열차의 마지막 역인 ' + id_num['name'] + ', ' + id_num['name'] + '역 입니다.')
    print('내리실 문은 ' + id_num['door'] + '입니다.')
    print()
    
    if len(id_num['환승']) > 0:
        for i in id_num['환승']:
            if type(i) == int:
                print(str(i)+'호선', end=' ')
            elif type(i) == str:
                print(i+'선', end=' ')
        print('으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.')
        print()
        
    print('내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.')
    print('오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!')
    print()
        
    print('This stop is '+ id_num['name'] + ', ' + id_num['name'] + ',' + 'The last Station.')
    
    if id_num['door'] == '왼쪽':
        print('The door opens on the left.')
    elif id_num['door'] == '오른쪽':
        print('The door opens on the right.')
    print()
    
    if len(id_num['환승']) > 0:
        print('You can transfer to ', end=' ')
        for i in id_num['환승']:
            if type(i) == int:
                print('line no.' + str(i), end=' ')
            elif type(i) == str:
                print(i+' line', end=' ')
        print('\n')
    
    print('Thank you for using our train!')
    print('Good Bye!')
    print()
    print('----------------------------')
    print()

    return
```

arrival(id_num)함수를 개조해서 만들 수 있다.<br/>
여기다가 종착역이라는 안내방송, 감사인사 등을 추가하면 된다.<br/>

```
def station_oper_head(self):
    station = self.head
    while station:
        if station == self.head:
            depart_start(self.tail.id_num)
            station = station.next
            continue
        elif station == self.tail:
            arrival_term(station.id_num)
            station = station.next
            continue
        arrival(station.id_num)
        station = station.next
        
    return
```
그리고 종착역인 경우는 `station == self.tail`일 때이다.<br/>
이 경우에만 종착역 안내방송을 하도록 제어문을 조정한다.<br/>

그리고, 종착역 안내방송과 일반역 도착 안내방송에서 내리는 문, 환승 정보를 안내하는 코드가 중복된다.<br/>
이 두 부분을 다른 함수로 빼내서 조금 더 깔끔하게 코드를 작성할 수 있을 것이다.<br/>

```
def arrive_ment_kor(id_num):
    
    print('내리실 문은 ' + id_num['door'] + '입니다.')
    print()
    
    if len(id_num['환승']) > 0:
        for i in id_num['환승']:
            if type(i) == int:
                print(str(i)+'호선', end=' ')
            elif type(i) == str:
                print(i+'선', end=' ')
        print('으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.')
        print()
        
    return

def arrive_ment_eng(id_num):
    
    if id_num['door'] == '왼쪽':
        print('The door opens on the left.')
    elif id_num['door'] == '오른쪽':
        print('The door opens on the right.')
    print()
    if len(id_num['환승']) > 0:
        print('You can transfer to ', end=' ')
        for i in id_num['환승']:
            if type(i) == int:
                print('line no.' + str(i), end=' ')
            elif type(i) == str:
                print(i+' line', end=' ')
        print('\n')
        
    return

def arrival(id_num):
    
    print('이번역은 ' + id_num['name'] + ', ' + id_num['name'] + '역 입니다.')
    
    arrive_ment_kor(id_num)
    arrive_ment_eng(id_num)

    print('----------------------------')
    print()

    return

def arrival_term(id_num):
    
    print('이번역은 우리 열차의 마지막 역인 ' + id_num['name'] + ', ' + id_num['name'] + '역 입니다.')
    print()
    
    arrive_ment_kor(id_num)
        
    print('내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.')
    print('오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!')
    print()
    
    print('This stop is '+ id_num['name'] + ', ' + id_num['name'] + ', The last station.')
    arrive_ment_eng(id_num)
    
    print('Thank you for using our train!')
    print('Good Bye!')
    print()
    print('----------------------------')
    print()

    return
```
이렇게 한글 안내멘트, 영어 안내멘트를 출력하는 함수를 따로 만들고, 원래 있던 자리에 안내 방송 기능이 작동하도록 만들면 된다.<br/>

자, 이상태로 3호선에 대해서 station_oper_head()를 동작시켜보았다.
```
Line_3.station_oper_head()

# 출력 결과
고객 여러분, 안녕하십니까?
우리 열차는 수서, 수서행 열차입니다.

This train bounds for 수서, 수서.
Thank you!

----------------------------
이번역은 매봉, 매봉역 입니다.
내리실 문은 오른쪽입니다.

This stop is 매봉, 매봉.
The door opens on the right.

----------------------------

이번역은 도곡, 도곡역 입니다.
내리실 문은 오른쪽입니다.

This stop is 도곡, 도곡.
The door opens on the right.

----------------------------

이번역은 대치, 대치역 입니다.
내리실 문은 왼쪽입니다.

This stop is 대치, 대치.
The door opens on the left.

----------------------------

이번역은 학여울, 학여울역 입니다.
내리실 문은 오른쪽입니다.

This stop is 학여울, 학여울.
The door opens on the right.

----------------------------

이번역은 대청, 대청역 입니다.
내리실 문은 오른쪽입니다.

This stop is 대청, 대청.
The door opens on the right.

----------------------------

이번역은 일원, 일원역 입니다.
내리실 문은 오른쪽입니다.

This stop is 일원, 일원.
The door opens on the right.

----------------------------

이번역은 우리 열차의 마지막 역인 수서, 수서역 입니다.

내리실 문은 왼쪽입니다.

내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.
오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!

This stop is 수서, 수서, The last station.
The door opens on the left.

Thank you for using our train!
Good Bye!

----------------------------
```
양재역에서 출발 안내멘트를 하고, 수서역까지 운행한 뒤, 수서역에서 종착 안내를 잘 출력하였다.<br/>

그리고 수서역에 도착하면 다시 양재역으로 돌아가야지.<br/>

```
    def station_oper_tail(self):
        station = self.tail
        while station:
            if station == self.tail:
                depart_start(self.head.id_num)
                station = station.prev
                continue
            elif station == self.head:
                arrival_term(station.id_num)
                station = station.prev
                continue
            arrival(station.id_num)
            station = station.prev
            
        return
```
이렇게 tail -> head로 search하는 함수를 만들어주면 된다.<br/>
head를 tail로 바꿔주고, head를 tail로, next를 prev로 바꿔주면 된다!<br/>

이번엔 수서역에서 출발해서 양재역으로 운행하는 3호선 열차를 동작시켜보자.<br/>
```
Line_3.station_oper_tail()

# 출력 결과
고객 여러분, 안녕하십니까?
우리 열차는 양재, 양재행 열차입니다.

This train bounds for 양재, 양재.
Thank you!

----------------------------
이번역은 일원, 일원역 입니다.
내리실 문은 오른쪽입니다.

This stop is 일원, 일원.
The door opens on the right.

----------------------------

이번역은 대청, 대청역 입니다.
내리실 문은 오른쪽입니다.

This stop is 대청, 대청.
The door opens on the right.

----------------------------

이번역은 학여울, 학여울역 입니다.
내리실 문은 오른쪽입니다.

This stop is 학여울, 학여울.
The door opens on the right.

----------------------------

이번역은 대치, 대치역 입니다.
내리실 문은 왼쪽입니다.

This stop is 대치, 대치.
The door opens on the left.

----------------------------

이번역은 도곡, 도곡역 입니다.
내리실 문은 오른쪽입니다.

This stop is 도곡, 도곡.
The door opens on the right.

----------------------------

이번역은 매봉, 매봉역 입니다.
내리실 문은 오른쪽입니다.

This stop is 매봉, 매봉.
The door opens on the right.

----------------------------

이번역은 우리 열차의 마지막 역인 양재, 양재역 입니다.

내리실 문은 오른쪽입니다.

신분당선 으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.

내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.
오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!

This stop is 양재, 양재, The last station.
The door opens on the right.

You can transfer to  신분당 line 

Thank you for using our train!
Good Bye!

----------------------------
```
운행이 잘 완료되었다!<br/>

### 5. 운행을 해보자!
```
class Station:
    def __init__(self, id_num, prev=None, next=None):
        self.id_num = id_num
        self.prev = prev
        self.next = next

def arrive_ment_kor(id_num):
    
    print('내리실 문은 ' + id_num['door'] + '입니다.')
    print()
    
    if len(id_num['환승']) > 0:
        for i in id_num['환승']:
            if type(i) == int:
                print(str(i)+'호선', end=' ')
            elif type(i) == str:
                print(i+'선', end=' ')
        print('으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.')
        print()
        
    return

def arrive_ment_eng(id_num):
    
    if id_num['door'] == '왼쪽':
        print('The door opens on the left.')
    elif id_num['door'] == '오른쪽':
        print('The door opens on the right.')
    print()
    if len(id_num['환승']) > 0:
        print('You can transfer to ', end=' ')
        for i in id_num['환승']:
            if type(i) == int:
                print('line no.' + str(i), end=' ')
            elif type(i) == str:
                print(i+' line', end=' ')
        print('\n')
        
    return

def arrival(id_num):
    
    print('이번역은 ' + id_num['name'] + ', ' + id_num['name'] + '역 입니다.')
    
    arrive_ment_kor(id_num)
    arrive_ment_eng(id_num)

    print('----------------------------')
    print()

    return

def depart_start(id_num):
    
    print('고객 여러분, 안녕하십니까?')
    print('우리 열차는 ' + id_num['name'] + ', ' + id_num['name'] + '행 열차입니다.')
    print()
    print('This train bounds for ' + id_num['name'] + ', ' + id_num['name'], end='.\n')
    print('Thank you!')
    print()
    print('----------------------------')
    
    return

def arrival_term(id_num):
    
    print('이번역은 우리 열차의 마지막 역인 ' + id_num['name'] + ', ' + id_num['name'] + '역 입니다.')
    print()
    
    arrive_ment_kor(id_num)
        
    print('내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.')
    print('오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!')
    print()
    
    print('This stop is '+ id_num['name'] + ', ' + id_num['name'] + ', The last station.')
    arrive_ment_eng(id_num)
    
    print('Thank you for using our train!')
    print('Good Bye!')
    print()
    print('----------------------------')
    print()

    return

class Station_Manage:
    def __init__(self, id_num):
        self.head = Station(id_num)
        self.tail = self.head

    def station_oper_head(self):
        station = self.head
        while station:
            if station == self.head:
                depart_start(self.tail.id_num)
                station = station.next
                continue
            elif station == self.tail:
                arrival_term(station.id_num)
                station = station.next
                continue
            arrival(station.id_num)
            station = station.next
            
        return
    
    def station_oper_tail(self):
        station = self.tail
        while station:
            if station == self.tail:
                depart_start(self.head.id_num)
                station = station.prev
                continue
            elif station == self.head:
                arrival_term(station.id_num)
                station = station.prev
                continue
            arrival(station.id_num)
            station = station.prev
            
        return
```

이 포스트에서 작성된 코드는 위와 같다. 이제 3호선, 신분당선, 분당선을 운행시켜보자.<br/>

```
####################### 3호선 양재 - 수서 ############################
Line_3.station_oper_head()

'''
출력 결과

고객 여러분, 안녕하십니까?
우리 열차는 수서, 수서행 열차입니다.

This train bounds for 수서, 수서.
Thank you!

----------------------------
이번역은 매봉, 매봉역 입니다.
내리실 문은 오른쪽입니다.

This stop is 매봉, 매봉.
The door opens on the right.

----------------------------

이번역은 도곡, 도곡역 입니다.
내리실 문은 오른쪽입니다.

This stop is 도곡, 도곡.
The door opens on the right.

----------------------------

이번역은 대치, 대치역 입니다.
내리실 문은 왼쪽입니다.

This stop is 대치, 대치.
The door opens on the left.

----------------------------

이번역은 학여울, 학여울역 입니다.
내리실 문은 오른쪽입니다.

This stop is 학여울, 학여울.
The door opens on the right.

----------------------------

이번역은 대청, 대청역 입니다.
내리실 문은 오른쪽입니다.

This stop is 대청, 대청.
The door opens on the right.

----------------------------

이번역은 일원, 일원역 입니다.
내리실 문은 오른쪽입니다.

This stop is 일원, 일원.
The door opens on the right.

----------------------------

이번역은 우리 열차의 마지막 역인 수서, 수서역 입니다.

내리실 문은 왼쪽입니다.

내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.
오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!

This stop is 수서, 수서, The last station.
The door opens on the left.

Thank you for using our train!
Good Bye!

----------------------------
'''

####################### 3호선 수서 - 양재 ############################

Line_3.station_oper_tail()
'''
출력결과

고객 여러분, 안녕하십니까?
우리 열차는 양재, 양재행 열차입니다.

This train bounds for 양재, 양재.
Thank you!

----------------------------
이번역은 일원, 일원역 입니다.
내리실 문은 오른쪽입니다.

This stop is 일원, 일원.
The door opens on the right.

----------------------------

이번역은 대청, 대청역 입니다.
내리실 문은 오른쪽입니다.

This stop is 대청, 대청.
The door opens on the right.

----------------------------

이번역은 학여울, 학여울역 입니다.
내리실 문은 오른쪽입니다.

This stop is 학여울, 학여울.
The door opens on the right.

----------------------------

이번역은 대치, 대치역 입니다.
내리실 문은 왼쪽입니다.

This stop is 대치, 대치.
The door opens on the left.

----------------------------

이번역은 도곡, 도곡역 입니다.
내리실 문은 오른쪽입니다.

This stop is 도곡, 도곡.
The door opens on the right.

----------------------------

이번역은 매봉, 매봉역 입니다.
내리실 문은 오른쪽입니다.

This stop is 매봉, 매봉.
The door opens on the right.

----------------------------

이번역은 우리 열차의 마지막 역인 양재, 양재역 입니다.

내리실 문은 오른쪽입니다.

신분당선 으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.

내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.
오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!

This stop is 양재, 양재, The last station.
The door opens on the right.

You can transfer to  신분당 line 

Thank you for using our train!
Good Bye!

----------------------------
'''

####################### 분당선 야탑 - 죽전 ############################

Line_K2.station_oper_head()

'''
출력결과

고객 여러분, 안녕하십니까?
우리 열차는 죽전, 죽전행 열차입니다.

This train bounds for 죽전, 죽전.
Thank you!

----------------------------
이번역은 이매, 이매역 입니다.
내리실 문은 왼쪽입니다.

This stop is 이매, 이매.
The door opens on the left.

----------------------------

이번역은 서현, 서현역 입니다.
내리실 문은 왼쪽입니다.

This stop is 서현, 서현.
The door opens on the left.

----------------------------

이번역은 수내, 수내역 입니다.
내리실 문은 왼쪽입니다.

This stop is 수내, 수내.
The door opens on the left.

----------------------------

이번역은 정자, 정자역 입니다.
내리실 문은 왼쪽입니다.

신분당선 으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.

This stop is 정자, 정자.
The door opens on the left.

You can transfer to  신분당 line 

----------------------------

이번역은 미금, 미금역 입니다.
내리실 문은 왼쪽입니다.

This stop is 미금, 미금.
The door opens on the left.

----------------------------

이번역은 오리, 오리역 입니다.
내리실 문은 왼쪽입니다.

This stop is 오리, 오리.
The door opens on the left.

----------------------------

이번역은 우리 열차의 마지막 역인 죽전, 죽전역 입니다.

내리실 문은 오른쪽입니다.

내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.
오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!

This stop is 죽전, 죽전, The last station.
The door opens on the right.

Thank you for using our train!
Good Bye!

----------------------------
'''

####################### 분당선 죽전 - 야탑 ############################

Line_K2.station_oper_tail()

'''
출력 결과

고객 여러분, 안녕하십니까?
우리 열차는 야탑, 야탑행 열차입니다.

This train bounds for 야탑, 야탑.
Thank you!

----------------------------
이번역은 오리, 오리역 입니다.
내리실 문은 왼쪽입니다.

This stop is 오리, 오리.
The door opens on the left.

----------------------------

이번역은 미금, 미금역 입니다.
내리실 문은 왼쪽입니다.

This stop is 미금, 미금.
The door opens on the left.

----------------------------

이번역은 정자, 정자역 입니다.
내리실 문은 왼쪽입니다.

신분당선 으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.

This stop is 정자, 정자.
The door opens on the left.

You can transfer to  신분당 line 

----------------------------

이번역은 수내, 수내역 입니다.
내리실 문은 왼쪽입니다.

This stop is 수내, 수내.
The door opens on the left.

----------------------------

이번역은 서현, 서현역 입니다.
내리실 문은 왼쪽입니다.

This stop is 서현, 서현.
The door opens on the left.

----------------------------

이번역은 이매, 이매역 입니다.
내리실 문은 왼쪽입니다.

This stop is 이매, 이매.
The door opens on the left.

----------------------------

이번역은 우리 열차의 마지막 역인 야탑, 야탑역 입니다.

내리실 문은 왼쪽입니다.

내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.
오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!

This stop is 야탑, 야탑, The last station.
The door opens on the left.

Thank you for using our train!
Good Bye!

----------------------------
'''

####################### 신분당선 강남 - 정자 ############################

Line_D.station_oper_tail()

'''
출력 결과
고객 여러분, 안녕하십니까?
우리 열차는 정자, 정자행 열차입니다.

This train bounds for 정자, 정자.
Thank you!

----------------------------
이번역은 양재, 양재역 입니다.
내리실 문은 왼쪽입니다.

3호선 으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.

This stop is 양재, 양재.
The door opens on the left.

You can transfer to  line no.3 

----------------------------

이번역은 양재 시민의 숲, 양재 시민의 숲역 입니다.
내리실 문은 왼쪽입니다.

This stop is 양재 시민의 숲, 양재 시민의 숲.
The door opens on the left.

----------------------------

이번역은 청계산 입구, 청계산 입구역 입니다.
내리실 문은 왼쪽입니다.

This stop is 청계산 입구, 청계산 입구.
The door opens on the left.

----------------------------

이번역은 판교, 판교역 입니다.
내리실 문은 왼쪽입니다.

This stop is 판교, 판교.
The door opens on the left.

----------------------------

이번역은 우리 열차의 마지막 역인 정자, 정자역 입니다.

내리실 문은 왼쪽입니다.

분당선 으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.

내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.
오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!

This stop is 정자, 정자, The last station.
The door opens on the left.

You can transfer to  분당 line 

Thank you for using our train!
Good Bye!

----------------------------
'''

####################### 신분당선 정자 - 강남 ############################

Line_D.station_oper_tail()
'''
고객 여러분, 안녕하십니까?
우리 열차는 강남, 강남행 열차입니다.

This train bounds for 강남, 강남.
Thank you!

----------------------------
이번역은 판교, 판교역 입니다.
내리실 문은 왼쪽입니다.

This stop is 판교, 판교.
The door opens on the left.

----------------------------

이번역은 청계산 입구, 청계산 입구역 입니다.
내리실 문은 왼쪽입니다.

This stop is 청계산 입구, 청계산 입구.
The door opens on the left.

----------------------------

이번역은 양재 시민의 숲, 양재 시민의 숲역 입니다.
내리실 문은 왼쪽입니다.

This stop is 양재 시민의 숲, 양재 시민의 숲.
The door opens on the left.

----------------------------

이번역은 양재, 양재역 입니다.
내리실 문은 왼쪽입니다.

3호선 으로 갈아타실 고객께서는 이번 역에서 내리시기 바랍니다.

This stop is 양재, 양재.
The door opens on the left.

You can transfer to  line no.3 

----------------------------

이번역은 우리 열차의 마지막 역인 강남, 강남역 입니다.

내리실 문은 왼쪽입니다.

내리실 때에 두고 내리시는 물건이 없는지 다시 한번 확인하시기 바랍니다.
오늘도 이용해주셔서 감사합니다. 안녕히 가십시오!

This stop is 강남, 강남, The last station.
The door opens on the left.

Thank you for using our train!
Good Bye!

----------------------------
'''
```

모두 잘 출력되었다!<br/>

이렇게 링크드리스트를 이용해서 지하철 안내방송을 구현할 수 있었다.

### 7. 마치며<br/>

서울 지하철은 수많은 노선들로 이루어져있고, 그 만큼 많은 Dictionary, 리스트로 이루어져있겠지. 이 글에서는 간단하게 구현해보았는데, 실제 서울교통공사나 코레일 등에서는 얼마나 더 복잡한 기능이 들어가있을지는 모르겠다. 배우다보면 더 알아갈 수 있겠지.