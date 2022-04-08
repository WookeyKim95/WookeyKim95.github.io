---
layout: post
title:  "대소문자변환 C"
subtitle:   
date: 2022-04-06 09:02:07 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# 대소문자변환 C<br/>

## C의 문자열의 나열<br/>

```
// 출력코드

    for (int j = 0 ; j < 130; j++) {
        printf("%c", j);
    }


123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
```

int = 0 상태로부터 130번째 문자까지 나열한 결과는 위와 같으며,<br/>
같은 알파벳에서 대문자와 소문자 사이의 개수는 32개이다.<br/>

## 대소문자 변환 코드 구현<br/>
<br/>

```
#include <stdio.h>

#pragma warning (disable :4996)

int main() {
for (int i = 0; i < 8; i++) {
        if ('a' <= str[i] && str[i] <= 'z') { // 가능하면 and나 or로 엮자. 'a' <= str[i] <= 'z' 는 지양할 것.
            // 소문자인 경우 대문자로 변환
            str[i] -= 32;   
        }

        else if ('A' <= str[i] && str[i] <= 'Z') { // 가능하면 and나 or로 엮자.
            // 대문자인 경우 소문자로 변환
            str[i] += 32;   
        }
    }
}
```

따라서 구현 코드는 위와 같다.<br/>

그리고 조건문을 작성할때 가능하면
```
'a' <= str[i] <= 'z'
```
와 같은 형태로 적는 것은 지양하자. 버그가 발생한다.<br/>
