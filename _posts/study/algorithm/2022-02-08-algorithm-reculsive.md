---
layout: post
title:  "[알고리즘] 재귀호출"
subtitle:
date: 2022-02-08 10:27:58 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

# [알고리즘] 재귀호출<br/>

## 재귀호출이란?<br/>

중, 고등학교 때 수학의 방정식 문제를 풀 때 이런 경우를 많이 보았을 것이다.<br/>

$ f(x) = 2x + 3 $ 이라고 할 때, $ f(f(f(2))) $ 의 값을 구하시오.<br/>
<br/>
해석을 하면 $ 2(2(2x + 3)+3)+3 $ 의 값을 구하라는 것과 같다.<br/>
그런데, 제일 안쪽에 있는 x의 값에다가 2를 넣었으므로, 계산을 하면 답은 37이 된다.<br/>

이처럼 어떤 함수 안에 인자, 혹은 input data를 다시 같은 함수로 넣어 호출하는 것이라고 재귀호출이라고한다.<br/>

파이썬 기준으로 재귀호출은 최대 1000번까지 가능하다.<br/>

그리고 재귀호출은 후에 동적프로그래밍이나 백트래킹을 구현할 때 많이 쓰인다.<br/>
필자도 백트래킹 알고리즘 문제를 풀 때 자꾸 재귀호출을 할 생각을 안해서 못푼 문제가 많다. (대표적으로 N-Queen)<br/>
그래서 재귀호출하는 것에 익숙해진 다음에 다시 알고리즘 문제를 풀러가야겠다고 생각했다.<br/>

대표적으로 재귀호출을 활용 할 수 있는 기초적인 알고리즘 문제는 다음과 같다.<br/>

- 팩토리얼
- 합하는 경우의 수
- 피보나치수열

파이썬으로 차례차례로 구현을 해보자.<br/>

### 1. 팩토리얼<br/>

팩토리얼은 n!과 같이 표기할 수 있으며, 1부터 n까지 연속된 자연수를 모두 곱한 값이다.<br/>

- 3! = 1 * 2 * 3 = 6
- 5! = 1 * 2 * 3 * 4 * 5 = 120

그러면 이것을 파이썬 코드로 구현하려면?<br/>

n = 1이 될 때 까지 함수를 호출한 다음에, 차례차례로 값을 곱해나가면 된다.<br/>

```
def fact(n):
    if n == 1:
        return 1
    else:
        return n * fact(n-1)
```

예를 들어 fact(3)을 호출하면, else문을 따라서 fact(2)의 값에다가 3을 곱한 값을 반환한다. 그런데 fact(2)가 호출 되었을 경우, 다시 fact(1)의 값에다가 2를 곱한 값을 반환한다. 그리고 fact(1)의 값은 1이므로, 1을 반환한다.<br/>

그래서 fact(3) - fact(2) - fact(1) 순서대로 호출이 진행되며, 반환 값의 순서는 1 - (1 * 2) - (1 * 2 * 3) 이다.<br/>

여기서 재귀함수는 마지막으로 반환된 값만 뽑아내므로 최종 결과는 1 * 2 * 3인 6을 반환한다.<br/>

```
# Test Code

def fact(n):
    if n == 1:
        return 1
    else:
        return n * fact(n-1)

print(fact(3))
print(fact(5))

# 결과 출력
6
120
```
시험한 결과, 결과가 잘 출력되었다.<br/>
<br/>

### 2. 합하는 경우의 수 <br/>

이런 문제가 있다고 해보자.<br/>

자연수 n을 1, 2, 3의 합으로 나타낼 수 있는 경우의 수는 몇 개인가?<br/>
(단, 1+2와 2+1과 같이 순서가 다른 경우는 다른 경우로 본다.)

- 1 : 1 하나 뿐이다.
- 2 : 1+1과 2로 2개이다.
- 3 : 1+1+1과 1+2, 2+1, 3 으로 4개이다.

이렇게 한다면 4는 아래와 같은 경우가 있을 것이다.<br/>

- 1+1+1+1
- 1+1+2
- 1+2+1
- 1+3
- 2+1+1
- 2+2
- 3+1

관찰을 해보면, 4 = 1 + 3인데, 3의 합을 나타낼 수 있는 경우의 수는 4개이다. 그래서 1+..로 시작하는 경우가 4개가 있다.<br/>

또한, 4 = 2 + 2 이며, 2+..로 시작하는 경우는 2개이다.<br/>
마지막으로 4 = 3 + 1에서 3+1로 시작하는 경우는 1개이다.<br/>

그래서 도합 7가지의 경우가 나온다.<br/>

즉, 경우의 수를 나타내는 함수를 f(x)라고 한다면,
```
f(4) = f(3) + f(2) + f(1) = f(4 - 1) + f(4 - 2) + f(4 - 3)
```
여기서 4를 n으로 치환하면
```
f(n) = f(n-1) + f(n-2) + f(n-3)으로 표현이 가능하다.<br/>
```
즉, f(1) + f(2) + f(3)의 조합이 나올 때 까지 계속 호출한 다음에 결과 값을 반환하면 된다.<br/>

그러면 그 과정을 파이썬 코드로 구현해보자.

```
def total(n):
    if n == 1:
        return 1
    elif n == 2:
        return 2
    elif n == 3:
        return 4
    elif n >= 4:
        return total(n-1) + total(n-2) + total(n-3)
```
위와 같이 구현할 수 있다.<br/>
f(1) = 1이고, f(2) = 2, f(3) = 4 이므로 n이 해당 값일 경우 해당 값에 맞는 반환 값을 반환하면 된다.<br/>

만약에, 1, 2, 3, 4의 조합으로 구현하라고 한다면
```
def total(n):
    if n == 1:
        return 1
    elif n == 2:
        return 2
    elif n == 3:
        return 4
    elif n == 4:
        return 7
    elif n >= 5:
        return total(n-1) + total(n-2) + total(n-3) + total(n-4)
```
밑에 부분이 위와 같은 코드로 수정될 것이다.<br/>

맨 처음에 경우의 수를 구하기 쉬운 것의 개수를 구하고 나서 재귀호출로 구현을 하면 될 것 같다.<br/>

```
# Test Code

def total(n):
    if n == 1:
        return 1
    elif n == 2:
        return 2
    elif n == 3:
        return 4
    elif n >= 4:
        return total(n-1) + total(n-2) + total(n-3)

print(total(8))
print(total(4))
print(total(6))

# 결과 값

81
7
24
```

잘 반환 되었다.<br/>
<br/>

### 3. 피보나치 수열<br/>

```
1, 1, 2, 3, 5, 8, 13, 21, 34, ...
```

위와 같이 자신과 자신의 바로 이전의 값을 합한 값이 다음 순서로 오는 수열을 피보나치 수열이라고 한다.<br/>

첫 번째 값은 1이고, 두 번째 값도 1이라고 한다면, 세 번째 값은 2, 네 번째 값은 3이 되는 구조이다.<br/>

그렇다면 만약에 n 번째 값을 구하고 싶다고 하면 첫 번째 값이나 두 번째 값이 반환될 때 까지 함수를 호출하고 나서 차레로 더해나가면 될 것이다.<br/>

그 과정을 파이썬 코드로 구현해보자<br/>

```
def fibonacci(n):
    if n == 1:
        return 1
    elif n == 2:
        return 1
    elif n >= 3:
        return fibonacci(n-1) + fibonacci(n-2)
```

n=1이나 2가 될 때까지 피보나치함수를 계속 호출한다. 그러다가 n=1이나 2가 되어 1이 반환되면 그 이후로 합산을 하기 시작하는 구조이다.<br/>

```
# Test Code

def fibonacci(n):
    if n == 1:
        return 1
    elif n == 2:
        return 1
    elif n >= 3:
        return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(5))
print(fibonacci(8))

# 결과 값

5
21
```
의도한 대로 잘 출력되었다.<br/>

참고로, 코딩 문제에서 피보나치수열의 시작점이 0인 경우도 있고, 두 번째 값이 2인 경우도 있다.<br/>
그 때 마다 상황에 맞춰서 반환 값을 바꿔주면 된다.<br/>
<br/>

### 그러면 반대로 어떤 수는 피보나치 수열의 몇 번째 값인지 구할 수 있을까?<br/>

구할 수 있다!<br/>
**단, 그 어떤 수와 그 이전 번째의 값이 주어져야 한다.<br/>**

예를 들어 21이 피보나치수열의 몇 번째 값인지 구하고 싶다면 그 이전번째 값인 13이 같이 주어져야한다.<br/>

그래서 맨 처음 두 개인 1, 1이 반환될 때까지 함수를 재귀호출하는 방법을 사용할 수 있다.<br/>
그리고, 몇 번째인지 카운트하는 전역변수를 선언하고, 재귀호출할때마다 그 변수의 값을 1씩 올려주면 될 것이다.<br/>

이 과정을 파이썬 코드로 구현해보자.<br/>

```
count = 3 # 재귀호출을 모두 끝마쳤을 때 3번째수가 처음으로 반환되니까.

def fibonacci_rev(a, b):
    global count
    if  a == 1 and b == 0:
        print(1)
        return    
    elif  a == 1 and b == 1:
        print(2)
        return
    elif a == 2 and b == 1:
        print(count)
        count = 3
        return
    else:
        count += 1
        return fibonacci_rev(b, a - b)
```

여기서 조건은 a > b, 즉 a가 피보나치수열의 몇 번째 수인지 구하고자 하는 것이며, b는 그 이전 번째의 수이다.<br/>

fibonacci_rev(b, a - b)를 재귀호출하면서 몇 번째 수인지 세는 변수의 값을 1 씩 올린다.<br/>

```
# Test Code

def fibonacci_rev(a, b):
    global count
    if  a == 1 and b == 0:
        print(1)
        return    
    elif  a == 1 and b == 1:
        print(2)
        return
    elif a == 2 and b == 1:
        print(count)
        count = 3
        return
    else:
        count += 1
        return fibonacci_rev(b, a - b)
        
fibonacci_rev(34, 21)
fibonacci_rev(2, 1)
```
34가 몇 번째 수인지 구하고 싶다. 기존에 알고 있었던 대로라면 34는 9번째 수이므로 올바르게 출력되는지 확인하면 된다.<br/>
또한, 2는 3번째 수라는 것을 알고 있기 때문에 바로 3을 반환하도록 하면 된다.<br/>

```
# 출력 결과
9
3
```
잘 반환 되었다!<br/>

한술 더 떠서, 이전에 구현한 피보나치함수랑 합쳐서 함수와 역함수 기능이 잘 작동하는지 볼 수 있다!

```
# Test Code

count = 3

def fibonacci(n):
    if n == 1:
        return 1
    elif n == 2:
        return 1
    elif n >= 3:
        return fibonacci(n-1) + fibonacci(n-2)

def fibonacci_rev(a, b):
    global count
    if  a == 1 and b == 0:
        print(1)
        return    
    elif  a == 1 and b == 1:
        print(2)
        return
    elif a == 2 and b == 1:
        print(count)
        count = 3
        return
    else:
        count += 1
        return fibonacci_rev(b, a - b)
        
fibonacci_rev(fibonacci(8), fibonacci(7))
fibonacci_rev(fibonacci(26), fibonacci(25))

# 결과 값
8
26
```

잘 작동하였다!<br/>

재귀호출에 대한 기초를 이렇게 공부하였다. 가장 경우의 수를 구하기 쉬운 값을 구하고 나서 그 값이 나올때까지 재귀호출을 반복하는 구조로 알고리즘을 짜는 것을 반복하면 될 것 같다.<br/>

그리고 특정 값이 나올 때까지 재귀호출을 반복하는 것이 트리구조로 되어있는 자료를 탐색할 때 유용하게 쓰일 것 같다.<br/>

여기다가 탐색여부 등의 변수를 추가하는 것을 더 공부해서 DP랑 백트래킹, 탐색 문제를 잘 풀 수 있도록 해야겠다.<br/>

