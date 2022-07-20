---
layout: post
title:  "[C++] 구조체, 그리고 str 관련 주요 함수"
subtitle:  
date: 2022-07-20 16:30:27 +0900
categories: study
tags: C++
comments: true
related_posts:

---

# [C++] 구조체, 그리고 str 관련 주요 함수<br/>
<br/>

C언어를 배울 때에도 구조체를 배웠고, C++에서 다시 한번 배웠다.<br/>

내용은 C언어에 있는 것과 같았다.<br/>

```
// 캐릭터를 예시로 들 경우 선언 예

enum class E_job : unsigned char // 열거체 크기 : 자료형의 크기로 1바이트
{
	None, // 직업이 없는 상태
	Knight,
	Archer,
	Magician,
	End
};

struct player
{
	// 이 코드블록 안에 원하는 변수들을 선언한다.
	char	Name[32];
	E_job	Job;
	int	Attack;
	int	Armor;
	int	HP;
	int HP_Max;
	int	MP;
	int MP_Max;
	int	Level;
	char	c1;
	char	c2;
};

```

그래서 정수형 자료에는 아래와 같은 코드로 데이터를 넣어줄 수 있다.<br/>

```
	player	player1; // 구조체 선언

	player1.Job = E_job::Knight;
	player1.Attack = 20;
	player1.Armor = 15;
	player1.HP = 100;
	player1.HP_Max = 100;
```

하지만 아래와 같이 char 문자열에 넣어주려고 하면 에러가 발생한다.<br/>

```
// 에러 발생 코드

// player1.Name = "기사입니다";
```

C++ 에서 "기사입니다"를 작성할 경우 int 처럼 변수를 저장하는게 아니라 특정 메모리에다가 데이터를 작성한 뒤 메모리의 주소를 얻어오는 식으로 데이터를 가져오게 된다.<br/>

즉, 위 코드에서 필자가 시도하려고 했던 것은 int로 비유하면 아래와 같다.

```
	const int	Arr1[10] = {};
	int	Arr2;
	Arr2 = Arr1;
```

Arr1은 배열 주소값이고 Arr2는 변수인데 주소값에다가 변수를 다이렉트로 넣을 수 없다.<br/>

<br/>
<Br/>

그렇다면 어떻게 해야할까?<br/>

player1.Name에 해당하는 주소에다가 "기사입니다" 주소에 있는 값을 복사해주는 식으로 넣어주어야 한다.<br/>

그 기능을 하는 함수가 바로 strcpy_s()이다.

```
// 사용예시

	strcpy_s("주소", "문자열");

	strcpy_s(player1.Name, "기사입니다.");
```

C++은 문자열과 관련된 여러가지 함수들을 제공해준다.<br/>
이 중에 많이 쓰이는 함수들을 모아보면<Br/>


- strcpy_s(A, B) : B 문자열을 A로 복사해주는 함수이다.
- strcat_s(A, B) : A 문자열에 B 문자열을 붙여서 하나의 문자열로 만드는 기능이다.
- strlen(A) : A 문자열의 길이를 얻어오는 함수이다.
- strcmp(A, B) : A, B 두 문자열이 같은지 비교해주는 함수이다.<br/>
이때, 같을 때 0, 길이가 A가 크면 1(양수)을, B가 더 크면 -1(음수)을 반환받는다. 