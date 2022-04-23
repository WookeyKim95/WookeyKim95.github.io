---
layout: post
title:  "[Javascript] 배열에 관한 함수 1"
subtitle:  
date: 2022-04-23 18:21:24 +0900
categories: study
tags: web_&_frontend
comments: true
related_posts:

---

# [Javascript] 배열에 관한 함수 1<br/>

## length, forEach, concat 등..<br/>
<br/>

참고사이트 : [MDN Javascript_Array]("https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array")<br/>
<br/>
<br/>

## length<br/>
<Br/>

파이썬의 len과 비슷한 함수이다.<br/>

```
const numbers = [0, 1, 2, 3]
const fruits = ['Apple', 'Banana', 'Mango']
```

위와 같은 배열들이 있다고 하면<br/>

```
console.log(numbers.length) // 반환 값 : 4
console.log(fruits.length) // 반환 값 : 3

console.log([0, 1, 2.length]) // 반환 값 : 3
```

이렇게 사용할 수 있다.<br/>
<br/>

## concat<br/>
<br/>

두 배열을 차례로 하나로 합치는 함수이다.

```
const numbers = [0, 1, 2, 3]
const fruits = ['Apple', 'Banana', 'Mango']
```

역시 위와 같은 배열이 있다고 하면<br/>

```
console.log(numbers.concat(fruits))
```

이 코드의 결과는<br/>

```
[0, 1, 2, 3, 'Apple', 'Banana', 'Mango']
```
이다.<br/>

```
const concat_com = numbers.concat(fruits)
```
이렇게 하면 새로운 변수에다가 두 배열을 합친 값을 저장할 수 있다고 보여진다.<br/>

하지만 주의할 점은 concat함수를 사용한다고 해서 원래 있던 두 배열이 사라지거나 값이 변하는 것이 아니라는 것이다.<br/>

```
const numbers = [0, 1, 2, 3]
const fruits = ['Apple', 'Banana', 'Mango']

console.log(numbers.concat(fruits))
반환 값 : [0, 1, 2, 3, 'Apple', 'Banana', 'Mango']

console.log(numbers)
반환 값 : [0, 1, 2, 3]
console.log(fruits)
반환 값 : ['Apple', 'Banana', 'Mango']
```

이다.<br/>
<br/>

## forEach<br/>
<br/>

어떻게 보면 자바스크립트에서 배열을 다루는데 하이라이트라고 생각이 되는 부분이다.<br/>

우선 사용 예시는 아래와 같다.<br/>

```
const fruits = ['Apple', 'Banana', 'Mango']

fruits.forEach(function (element, index, array){
    console.log(element, index, array)
    }
)
```

우선 결과를 살펴보면

```
Apple 0 ['Apple', 'Banana', 'Mango']
Banana 1 ['Apple', 'Banana', 'Mango']
Mango 2 ['Apple', 'Banana', 'Mango']
```
이다. 이 forEach문을 이해하려면 callback의 개념을 알아야 한다.<br/>
<br/>

### 그럼 여기서 Callback이란?<br/>
<br/>

배열의 각 값에 대해서 실행할 함수로, 아래의 세 가지 인자를 받는다.<br/>

- element : Callback에서 처리할 현재 요소
- index : 현재 처리할 element의 인덱스
- array : 호출된 배열

<Br/>

파이썬의 for문에서도<br/>

```
for i in ['a', 'b', 'c', 'd']
    print(i)

결과
a
b
c
d
```

위와 같이 for문을 이용해서 배열 내 element를 순차적으로 출력할 수 있었다.<br/>

다만, element만 사용한다면 파이썬의 for문과 같은 효력을 발생시키는 것이고,<br/> index와 array까지 지정을 해준다면<br/>

```
a 0 ['a', 'b', 'c', 'd']
b 1 ['a', 'b', 'c', 'd']
c 2 ['a', 'b', 'c', 'd']
d 3 ['a', 'b', 'c', 'd']
```
자바스크립트는 위와 같이 해줄 수 있다는 것이다.<br/>

위 출처에 따르면 element는 필수지만 index와 array는 선택사항이므로 

```
const fruits = ['Apple', 'Banana', 'Mango']

fruits.forEach(function (element){
    console.log(element)
    }
)
```
를 이용해서 파이썬의 for i in.. 과 같은 효과를 낼 수 있다.<br/>

또한, 꼭 element만 사용하는 것이 아니라

```
fruits.forEach(function (item, index, array){
    console.log(item, index, array)
    }
)
```

위와 같이 다른 이름을 사용해줘도 된다. 다만, 매개변수 이름은 꼭 맞춰주자.<br/>
<br/>

## map<br/>
<br/>

이 함수 역시 callback을 사용하는 함수이다.<br/>
forEach와 사용법이 유사한데 차이점은 반환되는 값이 배열이라는 점이다.<br/>

```
const fruits = ['Apple', 'Banana', 'Mango']

fruits.forEach(function (fruit, i){
    console.log(`${fruit}-${i}`)
    }
)

결과
Apple-0
Banana-1
Mango-2
```

위는 forEach문의 예시이고,<br/>

```
const fruits = ['Apple', 'Banana', 'Mango']

const b = fruits.map(function (fruit, i){
    return `${fruit}-${i}`
    }
)

console.log(b)

결과
["Apple-0", "Banana-1", "Mango-2"]
```

forEach는 배열 내 요소 각각에 대해서 조건에 맞춰서 반복적으로 실행한다면,<br/> map은 그 결과를 하나의 배열 형식으로 반환한다고 표현하고 싶다.<br/>

그래서 객체 데이터로 반환하는데 유용하다.<br/>

```
const b = fruits.map(function (fruit, i){
    return {
        id : i,
        name : fruit
        }
    }
)
```

위와 같이 코드를 작성할 수 있으며, 결과는 아래와 같다.

```
[
    {id : 0, name : "Apple"},
    {id : 1, name : "Banana"},
    {id : 2, name : "Mango"}
]
```

여기서 추가해서 화살표 함수를 이용하면<br/>

```
const b = fruits.map(function (fruit, i) => ({
    id : i,
    name : fruit
    })
)
```
와 같이 축약도 가능하다.<br/>