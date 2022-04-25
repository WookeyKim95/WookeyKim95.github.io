---
layout: post
title:  "[Javascript] 배열에 관한 함수 2"
subtitle:  
date: 2022-04-25 09:29:23 +0900
categories: study
tags: web_&_frontend
comments: true
related_posts:

---

# [Javascript] 배열에 관한 함수 2<br/>

## filter, find, include 등..<br/>
<br/>

참고사이트 : [MDN Javascript_Array]("https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array")<br/>
<br/>
<br/>

## filter<br/>
<Br/>

배열에서 조건에 맞는 아이템을 골라 배열 데이터로 반환하는 함수이다.

```
const numbers = [0, 1, 2, 3]
```

위와 같은 배열이 있다고 하면<br/>

```
const a = numbers.filter(number => {
    return number < 3
})
console.log(a)
```

이렇게 사용할 수 있다.<br/>

이렇게 된다면 반환되는 결과는

```
[0, 1, 2]
```
이다.<br/>

위 코드를 조금만 더 다듬으면

```
const a = numbers.filter(number =>  number < 3)
console.log(a)
```
이렇게도 축약이 가능하다. 계속 연습하자.<br/>
<br/>

## find, findIndex<br/>
<br/>

조건에 맞는 아이템이나 아이템의 인덱스를 반환하는 함수이다.

```
const fruits = ['Apple', 'Banana', 'Mango']
```

위와 같은 배열이 있다고 하면 아래와 같이 쓸 수 있다.<br/>

```
const a = fruits.find(fruit => /^A/.test(fruit))
console.log(a)

결과 : Apple
```
설명하면, /^A/는 정규표현식을 이용한 표현으로, 'A'로 시작하는 아이템을 의미한다.<br/>

따라서 위 함수에서 조건으로 부여된 내용은 'A'로 시작하는 아이템이며, 위 배열에서 해당하는 아이템은 Apple이므로 Apple이 find를 통해서 반환된다.<br/>

마찬가지로, find 대신에 findIndex를 적용해보자.<br/>

```
const a = fruits.findIndex(fruit => /^A/.test(fruit))
console.log(a)

결과 : 0
```
조건에 맞는 아이템은 Apple이며, Apple은 0번 인덱스에 있으므로 0번이 반환된다.<br/>

조건에 맞는 아이템을 삭제하거나 그 위치에 새로운 아이템을 넣을 때 유용하다.<br/>
<br/>

## includes<br/>
<br/>

파이썬의 in과 비슷한 함수이다.<br/>

```
const numbers = [0, 1, 2, 3]

console.log(numbers.includes(3))

결과 : true
```

배열 안에 해당 값이 들어있는지 여부를 boolean 형태로 값을 반환한다.<br/>
<br/>

## push, unshift<br/>
<br/>

각각 파이썬의 append와 insert(0) (0번 인덱스에 삽입)에 대응된다고 생각한다.<br/>

```
const numbers = [0, 1, 2, 3]

numbers.push(4)
console.log(numbers)

결과 : [0, 1, 2, 3, 4]

numbers.unshift(-1)
console.log(numbers)

결과 : [-1, 0, 1, 2, 3, 4]
```

즉, push는 맨 뒤에, unshift는 맨 앞에 아이템을 새로 삽입하는 함수이다.<br/>
주의할 점은 원본을 수정시킨다는 점이다.
<Br/>

## reverse<br/>

배열의 데이터를 반대로 뒤집는 함수이다.<br/>

```
const numbers = [0, 1, 2, 3]

numbers.reverse()

console.log(numbers)

결과 : [3, 2, 1, 0]
```

이 함수 역시 push와 unshift처럼 원본을 수정한다. 의도한게 아니라면 새로 변수를 선언해주자.<br/>
<br/>

## splice(a, b)<br/>

여기서 a와 b는 정수이다.<br/>

해석을 먼저 하자면 a번 인덱스로부터 아이템을 b개 지워라. 라는 의미이다.<br/>

```
const numbers = [0, 1, 2, 3, 4, 5, 6]
```

위와 같은 원본이 있다고 하면

```
numbers.splice(2, 1)
```
을 실행하면 위에 있던 배열은 아래와 같이 수정된다.

```
console.log(numbers)

결과 : [0, 1, 3, 4, 5, 6]
```

즉 2번 인덱스로부터 아이템을 1개 지우라고 한 것이므로, 2만 지워지고 나머지는 남는다.<br/>

이 상태에서 계속 지워보자.

```
numbers.splice(2, 3)
```

이렇게 하면 결과는

```
console.log(numbers)

결과 : [0, 1, 6]
```

2번 인덱스로부터 아이템을 3개 지운 결과이다.<br/>
<br/>

### 만약에 지울 개수가 0이라면?<br/>
<Br/>

다시 말해, splice(2, 0) 이런 형식으로 적으면 어떻게 될까?<br/>

```
const numbers = [0, 1, 2, 3]

numbers.splice(2, 0)

결과 : [0, 1, 2, 3]
```

아무런 변화가 없다.<br/>

하지만 여기서 아래와 같이 세 번째 인수를 넣어서 그 자리에 아이템을 넣어줄 수 있다.<br/>

```
numbers.splice(2, 0, 99)

결과 : [0, 1, 99, 2, 3]
```

즉, 2번 인덱스에 99를 새로 추가하라는 명령을 내릴 수 있다.<br/>
<br/>

### 아이템을 지우고 새로 아이템을 넣기<br/>
<br/>

```
const numbers = [0, 1, 2, 3]

numbers.splice(2, 1, 99)

결과 : [0, 1, 99, 3]
```
2번 인덱스의 아이템을 1개를 지우고, 그 자리에 99를 넣으라는 의미로 배열의 내용을 수정해줄 수 있다.<br/>

