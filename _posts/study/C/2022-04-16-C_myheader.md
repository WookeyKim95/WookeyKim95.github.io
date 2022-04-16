---
layout: post
title:  "[C] 나만의 헤더파일 만들기"
subtitle:
date: 2022-04-16 09:37:14 +0900
tags: C
categories: study
comments: true
related_posts:

---

# [C] 나만의 헤더파일 만들기<Br/>

파이썬에서 import를 하는 것처럼 C언어에서는 필요한 라이브러리를 부를 때 #include과정을 한다.<br/>

```
#include <stdio.h>
#include <string.h>
```

그러고보니 위와 같은 stdio.h같은 필요한 함수가 들어있는 파일을 헤더파일이라고 한다.<br/>

그렇다면 우리만의 함수를 모아놓은 헤더파일을 만들 수 있을까?<br/>

물론 있다.<br/>

<br/>

## 나만의 헤더파일 만들기<br/>
<br/>

```
#include <stdio.h>
#include <string.h>

#pragma once // include가 중복되지 않도록 막는 역할 (include를 여러번 해도 한번만 include 되도록 하는 역할.)

#define TRUE 1
#define FALSE 0

void clear_buffer();
void remove_enter(char* in);

void clear_buffer() {
    while (getchar() != '\n');
}

void remove_enter(char* in) {
    // fgets 사용으로인해 입력된 \n을 \0으로 변환하기 위한 코드
    int len = strlen(in);
    in[len - 1] = '\0';
}
```

필자가 만든 헤더파일이다. <br/>

remove_enter는 fgets함수를 사용할 때 엔터까지 저장을 받는 것을 방지하기 위해 사용하는 사용자 지정함수이고,<br/>
clear_buffer는 엔터를 입력받을 때 까지 입력을 받을 수 있게 buffer를 초기화하는 함수이다.<br/>

헤더파일의 이름을 myheader.h라 한 다음에 이 함수를 정의하고 저장만 하면<br/>
같은 폴더내에 있는 프로젝트에서 사용할 수 있게 된다.<br/>

여기서 #pragma once는 include가 여러번 입력돼도 한번만 include 되도록 하는 중요한 역할을 한다.<br/>
필수코드이므로 꼭 입력해두자.<br/>
<br/>

## 나만의 헤더파일 사용하기.<br/>
<br/>

파이썬에서는 from ... import * 형식으로 사용이 가능했다.<br/>

그렇다면 C언어 코드에서 어떻게 사용할 수 있을까?<br/>

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "myheader.h" // 프로젝트 내에서 include하고 싶은 경우에는 큰따옴표로 include 하기!

#pragma warning (disable : 4996)
```

C코드 맨 위에 보통 작성하는 include 파일이다.<br/>

파이썬의 내장함수처럼 C에서 기본적으로 설치된 헤더파일은

```
#include <stdlib.h> 
```

위와 같은 형식으로 불러오지만,<br/>
<br/>

사용자가 정의한 헤더파일은

```
#include "myheader.h"
```

위와 같이 큰 따옴표를 이용해야한다.<br/>
