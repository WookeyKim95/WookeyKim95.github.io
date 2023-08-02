---
layout: post
title:  "[JavaScript] QuerySelector와 Event"
subtitle:   
date: 2023-08-03 05:04:10 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [JavaScript] QuerySelector 활용하기<br/>
<br/>

웹 개발 JS의 하이라이트 중 하나라고 할 수 있는<br/>

QuerySelector을 활용하는 방법을 알아보자<br/>

## QuerySelector란?<br/>
<br/>

```
<body>
 
    <h1>A heading</h1>
    <a href="mcdonald.co.kr">Link Text</a>

    <script>
        // 쿼리셀렉터는 해당 선택자를 만족하는 것 중 단 한가지만 가져온다.
        const aTag = document.querySelector("a");
        aTag.textContent = "naver";
        aTag.href = "https://www.naver.com";
    </script>

</body>
```
쿼리 셀렉터는 조건에 맞는 html태그, 내용을 가져오는 역할을 한다.<br/>

이 쿼리셀렉터를 이용해서 html의 내용을 수정하는 작업을 할 수 있다.<br/>

위의 스크립트는 기존에 맥도날드 코리아에 연결되었던 a태그를<br/>

네이버로 변경하는 태그이다. 텍스트도 네이버로 변경시키고 있다.<br/>
<br/>

## QuerySelectorAll<br/>
<br/>

```
<body>
    <div class="hello"></div>
    <div class="hello"></div>
    <div class="hello"></div>
    <div class="hello"></div>

    <div class="hell"></div>
    <div class="hell"></div>
    <div class="hell"></div>
    <div class="hell"></div>

    <script>
        // 모든 div 가져오기
        const result = document.querySelectorAll("div");

        // hell class를 가지고 있는 div 가져오기
        const hells = document.querySelectorAll(".hell");

    </script>

</body>
```

QuerySelectorAll은 조건에 맞는 모든 태그를 싹다 가져온다.<br/>

위의 코드에서는 div를 모두 가져오면 총 8개의 요소가 가져오게 되고,

hell 이라는 class를 가진 태그를 가쟈오면 총 4개의 요소를 가져오게 된다.<br/>

```
<body>
    <input type="checkbox" name="city" value="서울" checked> 서울
    <input type="checkbox" name="city" value="경기도" checked> 경기도
    <input type="checkbox" name="city" value="제주도"> 제주도

    <script>
        //체크된 것을 가져오기
        const checkedInputs = document.querySelectorAll("input:checked");
        console.log(checkedInputs);

        // name=city인것들을 전부 가져오기
        const nameCityInputs = document.querySelectorAll("input[name=city]");
        console.log(nameCityInputs);
        
        // 값이 서울인 것만 가져오기
        const sInput = document.querySelectorAll("input[value=서울]");
        console.log(sInput);
    </script>
</body>
```

위와 같이 체크된 것만 가져오거나, 값이 서울이거나, 도시 속성을 가진 태그를 모두 가져오게 할 수 있다.<br/>

<br/>

## Element 만들기<br/>
<br/>

```
<body>

    <script>
        // document.createElement
        const button = document.createElement("button");
        button.textContent = "이것은 버튼입니다";

        // 만든 버튼을 document에 넣어주자.
        // append 함수 사용하기
        document.querySelector('body').append(button);
    </script>

</body>
```

createElement툴을 사용하여 만들 수 있다.<br/>

참고로, document.createElement("button")안에 있는 button은 정해져 있는 요소이므로 적절히 골라서 쓰자.<br/>

이렇게 하면 이것은 버튼입니다. 라는 문구를 가진 버튼이 생성된다.<br/>

<br/>

## Event<Br/>
<br/>

하드웨어를 통해서 웹 페이지와 상호작용하는과정, 클릭, 키 누름, 키 뗌 등을 통틀어 모두 이벤트라고 한다.<br/>

이벤트를 이용해서 웹 페이지를 변화시킬 수 있다!<br/>
<br/>

### Click<br/>

```
<body>

button id="btn">눌러보세요</button>

<button onclick="thisisClick()">이것도 눌러보세요</button>

    <script>
        const btn = document.querySelector("#btn");
        // addEventListner("이벤트 동작", "콜백함수")

        function thisisClick()
        {
            alert("자 이게 클릭이야");
        }

        btn.addEventListener("click", thisisClick);

    </script>

</body>
```
addEventListener함수를 통해 태그에 이벤트 발생시 작동할 함수를 부여할 수 있다.<br/>

위 코드는 btn이라는 아이디를 가진 버튼을 클릭했을시

자 이게 클릭이야 라는 경고창이 뜨도록 설계한 코드이다.<br/>

onclick이 있는 버튼도 보일 것이다. 인라인 방식으로 작성된 버튼이다.<br/>

둘 다 똑같은 형식의 버튼이지만<br/>

작성 방법에 차이가 있는 것이다.<br/>

다만 유지보수 측면으로는 인라인 보다는 script를 활용하는 쪽이 더 좋다.<br/>

### Change<br/>
<br/>

```
<body>

<input type="text">
    <div>

    </div>

    <script>
        const inputTag = document.querySelector('input');
        const div = document.querySelector('div');

        inputTag.addEventListener('change', function(e)
        {
            console.log(e);
            // e.target -> 이벤트가 발생한 지점 -> input 태그
            // e.target.value => input의 value
            div.textContent = e.target.value;         
        });

    </script>

</body>
```

위 내용은 입력칸의 내용이 바뀌면 바뀐 내용을 빈 공간에다가 출력하는 코드이다.<br/>

input에 내용이 바뀌는 change이벤트가 발생할 때 마다 바뀌도록 지정하고 있는 것이다.<br/>

change 이벤트를 이용해서 내용을 바꿀 수 있다.<br/>
<br/>

### KeyEvent (KeyUp, KeyDown)<br/>

```
<body>

    <!-- keydown -->
    <input type="text" class="input1">
    <div class="result1">

    </div>

    <script>
        // keydown -> 키보드를 누르는 순간
        const input1 = document.querySelector('.input1');
        const div1 = document.querySelector('.result1');

        input1.addEventListener('keydown', function(e)
        {
            if (e.code == "Enter") // 엔터키를 누른 경우에만 출력됨.
            {
                console.log(e.target.value);
                div1.textContent = e.target.value;
            }
        })

    </script>
    
</body>
```

위 코드는 엔터키를 눌렀을 때 div1에 있는 내용이 바뀌도록 하는 코드이다.<br/>

사실, keydown은 어떤 키를 눌렀는지 값을 들고온다.<br/>

그래서 입력값으로 지정된 e에서 e.code를 통해서 어떤 키를 눌렀는지를 볼 수 있다.<br/>

그리고 e.target.value는 타겟으로 지정된 e는 input1이므로 input1에 어떤 내용이 입력됐는지를 들고오는 역할을 한다.<br/>

e.code == "Enter"일 때 바뀌도록 했으므로 Enter을 누를 때마다 입력한 값으로 div에 띄워질 것이다.<br/>

```
    <input type="text" class="input2">
    <div class="result2">

    </div>

    <script>
        // keyup -> 누른 키보드를 떼는 순간
        // 중복 단어 선택 : Ctrl d
        const input2 = document.querySelector('.input2');
        const div2 = document.querySelector('.result2');
        
        input2.addEventListener('keyup', function(e)
        {
            if (e.code == "Enter") // 엔터키를 누른 경우에만 출력됨.
            {
                console.log(e.target.value);
                div2.textContent = e.target.value;
            }
        })

    </script>
```

위 코드도 마찬가지이다. 다만 차이점은 눌렀던 키를 뗐을 때 값이 변한다는 점이다.<br/>
