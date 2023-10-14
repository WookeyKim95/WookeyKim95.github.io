---
layout: post
title:  "[Embedded] 리눅스 쉘 스크립트"
subtitle:  
date: 2023-10-14 03:48:27 +0900
categories: study
tags: Embedded
comments: true
related_posts:

---

# [Embedded] 리눅스 쉘 스크립트<br/>
<br/>

리눅스 쉘 스크립트 작성에 대해서 알아보자.<br/>

## 우리가 기본적으로 사용하는 ShellScript는?<br/>
<Br/>

아참, ShellScript에 대해서 정리를 해야겠지?<br/>

ShellScript는 우리가 명령어를 입력하면 그 명령어를 실행할 때 어떻게 동작할지 정하는 스크립트이다.<br/>

쉘 스크립트의 확장자는 sh이다.<br/>

PC에서는 주로 Bash를 사용한다. Git Bash할때 그 Bash 맞다.<br/>

하지만 임베디드 환경에서는 주로 Dash라고 하는 쉘 스크립트 방식을 사용한다고 한다.<br/>

물론 Bash도 설치해서 사용이 가능하다!<br/>

- Dash : 경량이며, 기능이 적다,
- Bash : 기능이 많지만 무겁다.

차이점은 이렇게 둘 수 있겠다.<br/>

<br/>


## 쉘 스크립트 작성해보기<br/>
<Br/>

모든 쉘 스크립트의 확장자는 .sh이다.<br/>

```
#! /bin/bash

echo "HI"
```

여기서 맨 윗 줄은 이 스크립트는 bash 스크립트임을 알리는 것이며,<br/>

Dash 방식으로 지정하고자 할 때에는 #! /bin/sh를 적어준다.<br/>

그리고 스크립트를 실행하고자 할 때에는 파일 이름을 a라고 해준다면 아래와 같은 명령어를 입력해준다.<br/>

```
source a.sh
./a.sh
```
그러면 쉘에 HI라고 출력이 된다.<br/>
<br/>

## 쉘 스크립트 입출력<br/>

```
#! /bin/bash

echo "HI"
echo "What's Your Name?"

read ans

echo "HI " $ans
```

간단히 말하면 echo는 출력을, read가 입력받기를 담당한다.<br/>

read에 변수를 같이 써주고, 그 변수를 출력하고자 할 때에는 $를 붙인뒤 변수를 넣는다.<br/>
<br/>

## 쉘 스크립트의 변수<br/>

```
#! /bin/bash

bbq=100
kfc=200

echo $bbq + $kfc
```

여기서 주의할 점!<br/>

위 스크립트의 결과물로 출력되는 것은 어떻게 될까?<br/>

결과는 100+200이다.<br/>

왜냐하면 쉘스크립트 변수는 기본적으로 문자열로 취급하지, 수로 취급하지 않는다!<br/>

그렇기 때문에 300이 아닌 100+200이 나오게 된다.<br/>

```
bbq = 100
```

그리고 변수를 지정할 때 위와 같이 스페이스가 있으면 안된다.<br/>

스페이스없이 붙여야 에러가 발생하지 않는다!<br/>
<br/>

## 산술연산처리<br/>
<br/>

그렇다면 우리가 원하는대로 100+200의 값이 300으로 출력되게 하고 싶다면?<br/>

```
echo $(( bbq + kfc ))
```
와 같이 띄어쓰기를 넣어주고, 괄호 두묶음으로 감싼뒤 앞에 달러표기를 붙이면 된다!<br/>
<Br/>

## Argument 변수<br/>

```
#! /bin/bash

echo $1
echo $2
```

위 스크립트의 이름을 go 라고 했을 때,<br/>

명령어로

```
go HI WK
```

라고 한다면

```
HI
WK
```

와 같이 출력된다.<br/>

우리가 명령어로 ls a l 하는 것과 같이<br/>

argument 변수를 통해서도 입력을 받고, 이에 따라서 어떻게 행동할 지 결정을 할 수 있다.<br/>

위의 산술연산처리와 종합해서 아래와 같은 스크립트를 만들 수 있다.<br/>

```
!# /bin/bash

echo SUM VALUE IS $(( $1 + $2 ))

# 입력명령
source ./go.sh 100 200

# 결과
300
```

## 쉘 명령어를 변수로 넣기<br/>

```
#! /bin/bash

DATE=$(date)

echo $DATE "HI"
```

위와 같이 달러표시를 넣고 괄호로 한번만 감싸주면 쉘 명령어를 변수화 시킬 수 있다.<br/>

```
// 결과 예시
Sun 24 Sep 2023 05:04:09 PM KST HI
```
<br/>

## if문 사용하기<br/>

```
#! /bin/bash

a=KFC

if [ $a = "KFC" ] ;then
    echo "Chicken!"
else if [ $a = "HUT" ] ;then
    echo "Pizza!"
else
    echo "NO!"
fi
```

위와 같이 if문으로 사용할 부분은 대괄호로 감싸고, 끝에 ;then으르 붙여준다.<br/>

그리고 if문이 끝나면 fi로 감싸준다.<br/>

주의할 점은 띄어쓰기는 반드시 지켜주자!<br/>
<Br/>

## 비교연산자 사용하기<br/>

부등호, 등호 대신에 그 자리에 아래와 같은 명령어를 넣어야한다.<br/>

- -lt : less than, ~보다 작으면
- -gt : greater than, ~보다 크면
- -eq : equal, 이것은 문자열 비교이지, 수 비교가 아니라는 것에 주의하라.
- -ne : not equal, 같지 않다면
- -ge : 크거나 같다.
- -le : 작거나 같다.

반면, or과 and를 의미하는 ||와 &&는 똑같이 사용하면 된다.<br/>

```
# 예시

#! /bin/bash

a=5

if [ $a -gt 50 ] || [ $a -eq 5 ] ;then
    echo "LUCKY"
fi
```
결과물로 LUCKY가 잘 출력된다.<br/>

## 배열을 만들고 for문을 이용하기<br/>

소괄호를 이용해서 배열을 만들며, 중괄호를 이용해서 변수화 시킬 수 있다.<br/>

그리고 for문은 아래와 같이 사용한다.<br/>

```
!# /bin/bash

arr=(10 20 30)

for ((i=0; i<3; i++))
do
    echo ${arr[i]}
done
```

