---
layout: post
title:  "[JavaScript] Class"
subtitle:   
date: 2023-08-21 06:18:04 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [JavaScript] Class<br/>
<br/>

JS에서 클래스를 사용하는 법에 대해서 알아보자.<br/>
<br/>

## Class 만들기<br/>
<br/>

```
class Human {
    // 초기값 지정

    constructor(name, age, introduce) {
        this.age = age;
        this.name = name;
        this.introduce = introduce;
    }

    sayMyName() {
        console.log(`이름 : ${this.name}`);
        console.log(`나이 : ${this.age}`);
        console.log(`자기소개 : ${this.introduce}`);
    }
}
```

사실 자바스크립트에서의 클래스는 Object라는 객체가 이미 존재하며<br/>

이 Object라는 객체에서 복사를 해서 보다 객체를 편리하게 사용할 수 있도록 만드는 것이다.<br/>

즉, 해당 객체의 프로토타입을 찾고, 찾지 못할 경우 상위 프로토타입을 계속 찾아나가며,<br/>

제일 상위 프로토타입이 Object인 것이다.<br/>

그래서 초기값을 지정할 때 constructor을 사용하는데<br/>

C++의 생성자 역할을 뭔가 여기서는 constructor가 하는 거 같다.<br/>

this를 이용해서 이 클래스의 속성을 지정할 수 있고 메서드, 함수도 심을 수 있다.<br/>

그래서 위의 클래스를 만들고

```
const Person1 = new Human("김성욱", 29, "안녕?");
const Person2 = new Human("우키킴", 30, "반갑습니다.");

Person1.sayMyName();

Person2.sayMyName();
```

이 코드를 실행하면

```
이름 : 김성욱
나이 : 29
자기소개 : 안녕?

이름 : 우키킴
나이 : 30
자기소개 : 반갑습니다.
```

이렇게 출력된다!<br/>

<Br/>

## 클래스의 상속 (extends)<br/>
<Br/>

```
lass Animal {
    constructor(name){
        this.name = name;
    }

    Speaking() {
        console.log(`묘하게 생긴 ${this.name}입니다.`);
    }
}

class Cat extends Animal {
    constructor(name) {
        // 부모 클래스에 정의된 메서드에 접근 가능
        // 부모 생성자를 호출할 때 사용
        super(name);
    }

    Speaking(){
        super.Speaking();
        console.log("야옹 소리를 내죠.");
    }
}

class Dog extends Animal {
    constructor(name) {
        // 부모 클래스에 정의된 메서드에 접근 가능
        // 부모 생성자를 호출할 때 사용
        super(name);
    }

    Speaking(){
        super.Speaking();
        console.log("멍멍 소리를 내죠.");
    }
}
```

자바 스크립트의 클래스도 물론 상속이 가능하다!<br/>

extends를 사용하면 된다.<br/>

그리고 자바와 마찬가지로 부모 객체에 접근하고자 할때 super을 사용한다.

그래서 super(name)을 통해서 부모의 constructor을 호출하고<br/>

this.name을 입력하게 되는 것이다.<br/>

오버라이딩을 통한 방법도 있긴하다.<br/>

그래서 위의 클래스를 만들고<br/>

```
a_cat = new Cat("김양");

a_cat.Speaking();

a_dog = new Dog("김멍");

a_dog.Speaking();
```

이 코드를 작동시켜보면

```
묘하게 생긴 김양입니다.
야옹 소리를 내죠.
묘하게 생긴 김멍입니다.
멍멍 소리를 내죠.
```

위의 결과가 나온다.