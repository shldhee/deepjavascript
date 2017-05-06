# 스코프

**스코프**란 현재 접근할 수 있는 변수들의 범위를 뜻한다.
어떠한 변수가 스코프 안에 선언되었으면 해당 스코프 안에서는 변수에 접근해서 읽거나 쓸수 있고, 스코프 밖에서는 해당 변수에 접근할 수 없다.

*이슈가 있는 소스*

``` js
<div id="div0">Click Me Div0</div>
<div id="div1">Click Me Div1</div>
<div id="div2">Click Me Div2</div>
<script type="text/javascript">
    var i, len = 3;
    for ( i = 0; i < len; i += 1 ) {
        document.getElementById("div" + i).addEventListener("click", function () {
            alert("You clicked div #" + i );
        }, false);
    }
</script>

// <div> 클릭 후 출력 결과 모두 You clicked div #3
```

* 이 문제는 정확하게 스코프 때문이라기보다는 스코프가 생성되고 유지되는 방법때문에 발생한 일, 다른 말로는 ***클로저*** 때문에 생긴 문제이다.
* `<div>`에 대하여 클릭 이벤트 핸들러를 위한 콜백 함수가 `addEventListener`에 작성되었다.
* 이때 콜백함수는 `var i, len = 3`의 변수들에 계속 접근할 수 있는 스코프를 가진다.
* ***따라서 i가 for 반복문을 통해 3까지 증가한 뒤, 반복문이 끝나도 계속 유지된다. 그러므로 `alert()` 호출 시 i의 최종값인 3을 반환한다.***
* 위의 for 반복문을 돌 때는 별도의 스코프가 생성되지 않고 i는 글로벌 스코프에 존재한다.
* addEventListener()로 콜백 함수를 설정할 때 익명 함수가 선언되면서 이때 스코프가 생성되어 스코프 체인(scope-chain)을 만들게 된다.(p24 그림 참조)
* 각 `<div>`의 클릭 이벤트에 설정되었던 콜백 함수들은 모두 같은 스코프의 변수인 i를 참조한다.

---

## 스코프의 생성

먼저 for-loop 블록의 스코프 생성 여부를 알아보자.

*자바스크립트로 숫자 0에서 9까지 더하여 총합이 16이 넘는 숫자를 구하는 소스*

``` js
for(var i = 0; i < 10; i++) {
    var total = (total || 0) + i;
    var last = i;
    if (total > 16) {
        break;
    }
}

console.log(typeof total !== "undefined");    // true
console.log(typeof last !== "undefined");    // true
console.log(typeof i !== "undefined");    // true
console.log("total === " + total + ", last === " + last); // 21 , 6
```

다른 프로그래밍 언어에서 for-loop의 블록 안에 선언된 변수들은 블록 밖에서 조회하면 에러가 발생
하지만 자바스크립트에서는 모든 값에 접할 수 있다. (ES6 부터는 블록스코프로 바뀌었다.)

자바스크립트의 스코프는 특정 구문이 실행될 때 새로 생성하여 스코프 체인을 생성한다.

* function
* with
* catch

### function 구문의 스코프 생성

``` js
function foo() {
    var b = "Can you access me?";
}
console.log(typeof b === "undefined");
```

foo() 함수 외부에서 내부에 선언된 변수에 접근할 수 없다.
이것으로 ***function 구문을 통해서 스코프가 생성된다는 것을 알수 있다.***

### catch 구문의 스코프 생성

이 두 구문은 괄호 안에 인자로 받은 변수들만 새로운 내부 스코프에 포함되어 그 다음으로 오는 블록 안에서만 접근할 수 있다.
반면 블록 안에서 새로 정의한 변수들은 for-loop와 비슷하게 블록 외부에서도 접근할 수 있다.

``` js
try {
    throw new exception("fake exception");
} catch (err) {
    var test = "can you see me"; // 외부에서 접근 가능
    console.log(err instanceof ReferenceEroor === true); // catch구문 파라미터 err변수는 블록 내부에서 접근 가능 하지만 외부에서 접근할 없다.
}
console.log(test === "can you see me");
console.log(typeof err === "undefined");

// 모두 true
```

with구문도 catch구문과 같이 스코프 생성
with구문은 생략.....하고 싶었으나 해야겠네요

### width 구문을 활용한 문제해결

``` js
<div id="div0">Click Me Div0</div>
<div id="div1">Click Me Div1</div>
<div id="div2">Click Me Div2</div>
<script type="text/javascript">
    var i, len = 3;
    for ( i = 0; i < len; i += 1 ) {
        with ({num: i}) {
            document.getElementById("div" + i).addEventListener("click", function () {
                alert("You clicked div #" + i );
            }, false);
        }
    }
</script>
```

* with구문을 사용해 해결 : 이것은 자바스크립트의 비동기 콜백 함수의 특성과 스코프의 지속성이 합쳐진 결과이다.???
* with 구문은 괄호 안에 있는 파라미터를 활용하여 새로운 스코프를 생성
* num 변수의 값에 i의 값을 부여하고 클릭 이벤트에 대한 콜백 함수를 선언한다.
* 클릭 이벤트의 콜백 함수는 새로운 스코프를 생성한 with의 num을 참조하게 되어 고정적인 num 변수의 값인 0을 참조
* num 변수의 값이 0, 1, 2로 변경 될때마다 새로운 스코프 생성하여 클릭 이벤트 콜백 함수와 각각 연결 된다.

## 스코프의 지속성

자바스크립트에서 이러한 스코포의 지속성이 필요한 이유는 새로운 스코프가 생성되고 스코프 체인을 참조하는 함수(function)를 변수에도 넣을 수 있고, 다른 함수의 인자로 넘겨줄 수도 있으며, 함수의 반환값으로도 활용할 수도 있기 때문이다. ***즉, 지금 함수가 선언된 곳이 아닌 전혀 다른 곳에서 함수가 호출될 수 있어서, 해당 함수가 현재 참조하는 스코프를 지속할 필요가 있는 것이다.***

### 함수를 이용한 문제해결

이러한 지속성을 이해하기 위해 앞의 클릭 이벤트 핸들러 문제를 또 다른 방식으로 해결해보자.

``` js
<div id="divScope0">Click Me Div0</div>
<div id="divScope1">Click Me Div1</div>
<div id="divScope2">Click Me Div2</div>
<script type="text/javascript">
    function setDivClick(index) {
        document.getElementById("divScope" + index).addEventListener(
            "click",
            function () {
                alert("You clicked div #" + index);
            },
            false
        );
    }
    var i, len = 3;
    for (i = 0; i < len; i++) {
        setDivClick(i);
    }
</script>
```

* 이렇게 필요할 때 함수로 분리하는 것은 비동기 처리를 많이 하는 자바스크립트의 특성에서는 중요하게 생각해야 하는 개발방식이다.
* 별도의 function을 추가했으므로 with 구문과 똑같은 개념으로 스코프가 생성되고 지속된다. 따라서 스코프 체인은 with구문과 비슷하게 나온다.
* 하지만 내부적으로 다른점이 있다면, 일반 with가 가지는 모호성??을 베재할 수 있고, 완벽하게 이벤트 처리를 위한 별도의 스코프를 하나 만들어서 사용하게 된다.

### 클로저를 활용한 문제해결

*IIFE로 해결하기*
``` js
<div id="divScope0">Click Me Div0</div>
<div id="divScope1">Click Me Div1</div>
<div id="divScope2">Click Me Div2</div>
<script type="text/javascript">
    var i, len = 3;
    for (i = 0; i < len; i += 1 ) {
        document.getElementById("divScope" + i).addEventListener(
            "click",
            (function (index) {
                return function () {
                    alert("You clicked div #" + index);
                };
            }(i)),
        false);
    }
</script>
```

* with 구문과는 다르게 구문 안에서 스코프 체인을 function으로 만들고 있다.
* 익명함수(anonymouse function)는 index라는 파라미터를 받으며, 그 파라미터는 `(i)`의 값을 넘겨준다. 즉 index에 i값을 할당

아래와 같이 두 단계로 나눌 수 있다.

``` js
var func = function (index) { };
var returnValue = func(i);
returnValue = (function (index){ })(i));

위 두 줄을 한 줄로 표현하면 마지막 줄과 같다.
```

* 자바스크립트에 자주 등장하는 함수 활용 방법으로 IIFE(Immediate Invoke Function Expression), 즉시 호출 함수
* 이러한 표현 방식은 스코프 체인을 생성해서 클로저를 활용하는데 매우 유용하게 쓰이며, 자바스크립트 라이브러리들에서도 기본으로 활용되는 기법 중 하나이다.
* return문에 새로운 함수를 하나 선언해서 반환한다.
* 반환된 함수는 바로 윗줄 index 변수를 상위 스코프 체인에 추가한 뒤, addEventListener() 함수의 2번째 인자로 들어간다.
